# [#014] REQ-NF-012-OPS-001: 구조화된 로깅 및 Prometheus/Grafana 모니터링

## 📋 Issue Metadata
- **Issue Number**: #014
- **Epic**: EPIC 3 - Non-Functional & Operations
- **Type**: `non-functional`
- **Component**: `backend`, `ops`, `monitoring`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ✅ Yes (Can develop with #013, #015)

## 🎯 목적
운영 모니터링을 위한 구조화된 로깅 및 메트릭 수집 시스템을 구축합니다.

## 📝 설명
JSON 형식의 구조화된 로그를 생성하고, Prometheus/Grafana를 통한 메트릭 모니터링 환경을 구축하여 시스템 상태를 실시간으로 파악합니다.

## ✅ 범위 (In-Scope)
- 구조화된 JSON 로깅 (Logback)
- Prometheus 메트릭 수집
- Grafana 대시보드 구성
- Custom Metrics 정의
- Health Check 엔드포인트
- Application Metrics (JVM, HTTP, DB)

## ❌ 제외 범위 (Out-of-Scope)
- Distributed Tracing (Zipkin/Jaeger)
- Log Aggregation (ELK Stack)
- APM (Application Performance Monitoring)

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   global/
     ├── config/
     │   ├── LoggingConfig
     │   └── MetricsConfig
     └── monitoring/
         ├── CustomMetrics
         └── HealthIndicators
   ```

2. **Structured Logging**:
   ```xml
   <!-- logback-spring.xml -->
   <configuration>
       <appender name="JSON" class="ch.qos.logback.core.ConsoleAppender">
           <encoder class="net.logstash.logback.encoder.LogstashEncoder">
               <includeMdcKeyName>request_id</includeMdcKeyName>
               <includeMdcKeyName>user_id</includeMdcKeyName>
               <customFields>{"service":"bizplan-backend","env":"${SPRING_PROFILES_ACTIVE}"}</customFields>
           </encoder>
       </appender>
       
       <root level="INFO">
           <appender-ref ref="JSON" />
       </root>
   </configuration>
   ```

3. **Logging in Code**:
   ```java
   @Slf4j
   @Service
   @RequiredArgsConstructor
   public class DocumentGenerationService {
       
       public GeneratedDocument generateDocument(Long projectId) {
           MDC.put("project_id", projectId.toString());
           MDC.put("request_id", UUID.randomUUID().toString());
           
           log.info("Starting document generation", 
                    kv("project_id", projectId),
                    kv("action", "generate_document"));
           
           try {
               // Business logic
               log.info("Document generation completed successfully",
                        kv("project_id", projectId),
                        kv("duration_ms", duration));
               return document;
           } catch (Exception e) {
               log.error("Document generation failed",
                         kv("project_id", projectId),
                         kv("error", e.getMessage()),
                         e);
               throw e;
           } finally {
               MDC.clear();
           }
       }
   }
   ```

4. **Custom Metrics**:
   ```java
   @Component
   public class CustomMetrics {
       private final MeterRegistry meterRegistry;
       
       private final Counter documentGenerationCounter;
       private final Timer documentGenerationTimer;
       private final Gauge activeGenerationsGauge;
       
       public CustomMetrics(MeterRegistry meterRegistry) {
           this.meterRegistry = meterRegistry;
           
           this.documentGenerationCounter = Counter.builder("bizplan.document.generation.total")
               .description("Total number of document generation requests")
               .tag("status", "success")
               .register(meterRegistry);
           
           this.documentGenerationTimer = Timer.builder("bizplan.document.generation.duration")
               .description("Document generation duration")
               .register(meterRegistry);
           
           this.activeGenerationsGauge = Gauge.builder("bizplan.document.generation.active", 
                                                        activeGenerations, AtomicInteger::get)
               .description("Active document generations")
               .register(meterRegistry);
       }
       
       public void recordGeneration(boolean success, long durationMs) {
           documentGenerationCounter.increment();
           documentGenerationTimer.record(durationMs, TimeUnit.MILLISECONDS);
       }
   }
   ```

5. **Health Checks**:
   ```java
   @Component
   public class LLMServiceHealthIndicator implements HealthIndicator {
       
       @Autowired
       private WebClient llmWebClient;
       
       @Override
       public Health health() {
           try {
               llmWebClient.get()
                   .uri("/health")
                   .retrieve()
                   .toBodilessEntity()
                   .block(Duration.ofSeconds(5));
               
               return Health.up()
                   .withDetail("llm_service", "available")
                   .build();
           } catch (Exception e) {
               return Health.down()
                   .withDetail("llm_service", "unavailable")
                   .withException(e)
                   .build();
           }
       }
   }
   ```

6. **Prometheus Configuration**:
   ```yaml
   # application.yml
   management:
     endpoints:
       web:
         exposure:
           include: health,info,prometheus,metrics
     metrics:
       export:
         prometheus:
           enabled: true
       distribution:
         percentiles-histogram:
           http.server.requests: true
     endpoint:
       health:
         show-details: always
   ```

7. **Grafana Dashboard JSON** (샘플):
   ```json
   {
     "dashboard": {
       "title": "BizPlan Backend Monitoring",
       "panels": [
         {
           "title": "Document Generation Rate",
           "targets": [
             {
               "expr": "rate(bizplan_document_generation_total[5m])"
             }
           ]
         },
         {
           "title": "API Latency P99",
           "targets": [
             {
               "expr": "histogram_quantile(0.99, http_server_requests_seconds_bucket)"
             }
           ]
         }
       ]
     }
   }
   ```

## ✅ 완료 조건
- [ ] JSON 로깅 설정 완료
- [ ] Prometheus 메트릭 엔드포인트 활성화
- [ ] Custom Metrics 구현
- [ ] Health Check 구현
- [ ] Grafana 대시보드 작성
- [ ] 로그 레벨별 필터링 설정
- [ ] 모니터링 문서 작성

## 🔗 의존성
- **Depends on**: #006 (기본 API 필요)
- **Blocks**: None

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class CustomMetricsTest {
    @Test
    void recordGeneration_ShouldIncrementCounter() {
        // Test metrics recording
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class MetricsEndpointTest {
    @Test
    void prometheusEndpoint_ShouldExposeMetrics() {
        mockMvc.perform(get("/actuator/prometheus"))
               .andExpect(status().isOk())
               .andExpect(content().string(containsString("bizplan_document_generation_total")));
    }
}
```

## 📅 로드맵
- **Phase**: Phase 5 (NFR & QA)
- **Parallel Group**: NFR (Can run with #013, #015)

## 🏷️ Labels
`epic-3`, `non-functional`, `ops`, `monitoring`, `priority-must`

## 📋 Metrics to Monitor
```yaml
system_metrics:
  - jvm.memory.used
  - jvm.threads.live
  - jvm.gc.pause
  - system.cpu.usage

application_metrics:
  - bizplan.document.generation.total
  - bizplan.document.generation.duration
  - bizplan.document.generation.active
  - bizplan.llm.api.calls
  - bizplan.export.total

http_metrics:
  - http.server.requests (latency, count, errors)
  - http.client.requests (for LLM calls)

database_metrics:
  - hikaricp.connections.active
  - hikaricp.connections.pending
  - spring.data.repository.invocations
```

## 📋 Dependencies (Gradle)
```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-actuator")
    implementation("io.micrometer:micrometer-registry-prometheus")
    implementation("net.logstash.logback:logstash-logback-encoder:7.4")
}
```

## 📋 Checklist
- [ ] Structured JSON logging enabled
- [ ] Prometheus endpoint accessible
- [ ] Custom metrics implemented
- [ ] Health checks configured
- [ ] Grafana dashboard created
- [ ] Alert rules defined
- [ ] Log retention policy set

---
**Related Tasks**: #006, #013, #015
**Execution Order**: Phase 5 - Parallel (NFR)

