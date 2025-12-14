# [#015] REQ-NF-001-PERF-001: API 성능 목표 검증을 위한 k6 부하 테스트

## 📋 Issue Metadata
- **Issue Number**: #015
- **Epic**: EPIC 3 - Non-Functional & Operations
- **Type**: `non-functional`
- **Component**: `testing`, `performance`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ❌ No (Depends on #009)

## 🎯 목적
API 성능 목표(P99 < 200ms)를 검증하기 위한 부하 테스트를 구축합니다.

## 📝 설명
k6를 사용하여 핵심 API 엔드포인트의 성능을 측정하고, 목표 지표 달성 여부를 검증하는 자동화된 부하 테스트 시나리오를 구현합니다.

## ✅ 범위 (In-Scope)
- k6 테스트 스크립트 작성
- 핵심 API 시나리오 테스트
- 성능 목표 검증 (P99 < 200ms)
- 동시 사용자 부하 테스트
- 결과 리포트 생성

## ❌ 제외 범위 (Out-of-Scope)
- 장기 Endurance 테스트
- Chaos Engineering
- 실제 프로덕션 부하 테스트

## 🔨 구현 힌트
1. **Project Structure**:
   ```
   performance-tests/
   ├── k6/
   │   ├── scenarios/
   │   │   ├── project-creation.js
   │   │   ├── wizard-answers.js
   │   │   ├── document-generation.js
   │   │   └── export-document.js
   │   ├── utils/
   │   │   ├── config.js
   │   │   └── helpers.js
   │   └── run-all-tests.sh
   └── results/
       └── reports/
   ```

2. **k6 Test Script - Project Creation**:
   ```javascript
   // scenarios/project-creation.js
   import http from 'k6/http';
   import { check, sleep } from 'k6';
   import { Rate, Trend } from 'k6/metrics';
   
   const errorRate = new Rate('errors');
   const projectCreationDuration = new Trend('project_creation_duration');
   
   export const options = {
       stages: [
           { duration: '30s', target: 10 },  // Ramp-up
           { duration: '1m', target: 50 },   // Steady load
           { duration: '30s', target: 100 }, // Peak load
           { duration: '30s', target: 0 },   // Ramp-down
       ],
       thresholds: {
           'http_req_duration': ['p(99)<200'], // P99 < 200ms
           'http_req_duration': ['p(95)<150'], // P95 < 150ms
           'http_req_failed': ['rate<0.01'],   // Error rate < 1%
           'errors': ['rate<0.01'],
       },
   };
   
   export default function () {
       const url = `${__ENV.BASE_URL}/api/v1/projects`;
       const payload = JSON.stringify({
           title: `Test Project ${Date.now()}`,
           templateId: 1,
       });
       
       const params = {
           headers: {
               'Content-Type': 'application/json',
           },
       };
       
       const res = http.post(url, payload, params);
       
       const success = check(res, {
           'status is 201': (r) => r.status === 201,
           'response time < 200ms': (r) => r.timings.duration < 200,
           'response has project id': (r) => r.json('data.id') !== undefined,
       });
       
       errorRate.add(!success);
       projectCreationDuration.add(res.timings.duration);
       
       sleep(1);
   }
   ```

3. **k6 Test Script - Document Generation**:
   ```javascript
   // scenarios/document-generation.js
   import http from 'k6/http';
   import { check, sleep } from 'k6';
   
   export const options = {
       scenarios: {
           document_generation: {
               executor: 'constant-vus',
               vus: 10,
               duration: '2m',
           },
       },
       thresholds: {
           'http_req_duration{endpoint:generate}': ['p(99)<5000'], // 5s for async operation
           'http_req_failed': ['rate<0.05'], // 5% tolerance for async ops
       },
   };
   
   export function setup() {
       // Create test project with wizard answers
       const projectRes = http.post(`${__ENV.BASE_URL}/api/v1/projects`, 
           JSON.stringify({ title: 'Perf Test Project' }));
       const projectId = projectRes.json('data.id');
       
       // Add wizard answers
       http.post(`${__ENV.BASE_URL}/api/v1/projects/${projectId}/wizard/answers`,
           JSON.stringify({ /* mock answers */ }));
       
       return { projectId };
   }
   
   export default function (data) {
       const projectId = data.projectId;
       
       // Trigger document generation
       const genRes = http.post(
           `${__ENV.BASE_URL}/api/v1/projects/${projectId}/generate`,
           null,
           { tags: { endpoint: 'generate' } }
       );
       
       check(genRes, {
           'generation started': (r) => r.status === 202,
       });
       
       // Poll for completion
       let completed = false;
       let attempts = 0;
       while (!completed && attempts < 60) {
           sleep(1);
           const statusRes = http.get(
               `${__ENV.BASE_URL}/api/v1/projects/${projectId}/generation-status`
           );
           
           if (statusRes.json('data.status') === 'COMPLETED') {
               completed = true;
               check(statusRes, {
                   'generation completed': () => true,
               });
           }
           attempts++;
       }
   }
   ```

4. **k6 Test Script - Wizard Answers**:
   ```javascript
   // scenarios/wizard-answers.js
   import http from 'k6/http';
   import { check } from 'k6';
   
   export const options = {
       scenarios: {
           constant_load: {
               executor: 'constant-arrival-rate',
               rate: 100, // 100 requests per second
               timeUnit: '1s',
               duration: '1m',
               preAllocatedVUs: 50,
               maxVUs: 100,
           },
       },
       thresholds: {
           'http_req_duration': ['p(99)<200'],
       },
   };
   
   export default function () {
       const projectId = Math.floor(Math.random() * 1000) + 1;
       const url = `${__ENV.BASE_URL}/api/v1/projects/${projectId}/wizard/answers`;
       
       const payload = JSON.stringify({
           stepId: 'step1',
           questionId: 'q1',
           answerValue: 'Performance test answer',
       });
       
       const res = http.post(url, payload, {
           headers: { 'Content-Type': 'application/json' },
       });
       
       check(res, {
           'status is 200 or 404': (r) => [200, 404].includes(r.status),
           'response time < 200ms': (r) => r.timings.duration < 200,
       });
   }
   ```

5. **Run Script**:
   ```bash
   #!/bin/bash
   # run-all-tests.sh
   
   export BASE_URL="http://localhost:8080"
   
   echo "Running Project Creation Test..."
   k6 run --out json=results/project-creation.json scenarios/project-creation.js
   
   echo "Running Wizard Answers Test..."
   k6 run --out json=results/wizard-answers.json scenarios/wizard-answers.js
   
   echo "Running Document Generation Test..."
   k6 run --out json=results/document-generation.json scenarios/document-generation.js
   
   echo "Generating HTML Report..."
   k6 report results/*.json --out html=results/performance-report.html
   ```

6. **CI/CD Integration**:
   ```yaml
   # .github/workflows/performance-test.yml
   name: Performance Tests
   
   on:
     pull_request:
       branches: [main, develop]
   
   jobs:
     performance-test:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         
         - name: Start Backend
           run: docker-compose up -d
         
         - name: Wait for Backend
           run: sleep 30
         
         - name: Install k6
           run: |
             sudo gpg -k
             sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
             echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
             sudo apt-get update
             sudo apt-get install k6
         
         - name: Run Performance Tests
           run: |
             cd performance-tests/k6
             ./run-all-tests.sh
         
         - name: Upload Results
           uses: actions/upload-artifact@v3
           with:
             name: performance-results
             path: performance-tests/k6/results/
   ```

## ✅ 완료 조건
- [ ] k6 테스트 스크립트 작성 완료
- [ ] 모든 핵심 API 시나리오 커버
- [ ] P99 < 200ms 목표 검증
- [ ] 동시 사용자 시나리오 테스트
- [ ] CI/CD 통합 완료
- [ ] 성능 리포트 자동 생성
- [ ] 성능 테스트 문서 작성

## 🔗 의존성
- **Depends on**: #009 (문서 생성 API 필요)
- **Blocks**: None

## 🧪 테스트 시나리오
### Scenario 1: Project Creation
- **Load**: 100 concurrent users
- **Duration**: 2 minutes
- **Target**: P99 < 200ms

### Scenario 2: Wizard Auto-save
- **Load**: 100 requests/second
- **Duration**: 1 minute
- **Target**: P99 < 200ms

### Scenario 3: Document Generation
- **Load**: 10 concurrent generations
- **Duration**: 2 minutes
- **Target**: P99 < 5000ms (async operation)

## 📅 로드맵
- **Phase**: Phase 5 (NFR & QA)
- **Parallel Group**: None (Final validation)

## 🏷️ Labels
`epic-3`, `non-functional`, `performance`, `testing`, `priority-must`

## 📋 Performance Targets
```yaml
api_latency:
  - Project Creation: P99 < 200ms
  - Wizard Answers: P99 < 200ms
  - Document Status: P99 < 100ms
  - Export Trigger: P99 < 200ms

throughput:
  - Concurrent Users: >= 100
  - Requests/Second: >= 500

reliability:
  - Error Rate: < 1%
  - Timeout Rate: < 0.1%
```

## 📋 Checklist
- [ ] k6 installed and configured
- [ ] All test scenarios implemented
- [ ] Thresholds configured correctly
- [ ] CI/CD pipeline integrated
- [ ] Results analyzed and documented
- [ ] Performance bottlenecks identified
- [ ] Optimization recommendations provided

---
**Related Tasks**: #009
**Execution Order**: Phase 5 - Final Validation (After #009)

