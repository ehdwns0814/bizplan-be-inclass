# [#012] REQ-FUNC-012-BE-001: 재무 추정 및 유닛 이코노믹스 계산 엔진

## 📋 Issue Metadata
- **Issue Number**: #012
- **Epic**: EPIC 2 - 실패 회피 Job (To avoid failure)
- **Type**: `feature`
- **Component**: `backend`, `financial`, `calculation`
- **Priority**: `Should`
- **Estimated Effort**: L
- **Parallelizable**: ✅ Yes (Can develop with #011)

## 🎯 목적
재무 데이터를 기반으로 손익계산서, 유닛 이코노믹스(CAC, LTV 등)를 계산하는 엔진을 구현합니다.

## 📝 설명
사용자가 입력한 재무 정보를 바탕으로 자동으로 재무 지표를 계산하고, 유닛 이코노믹스 분석을 제공하는 계산 엔진을 구축합니다.

## ✅ 범위 (In-Scope)
- 재무 데이터 입력 API (`POST /api/v1/projects/{id}/financial-data`)
- 손익계산서 자동 계산
- 유닛 이코노믹스 계산 (CAC, LTV, LTV:CAC Ratio, Payback Period)
- 재무 예측 (3년치)
- 시각화 데이터 생성

## ❌ 제외 범위 (Out-of-Scope)
- 복잡한 세무 계산
- 다국가 회계 기준
- 실시간 시장 데이터 연동

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   api/
     ├── FinancialController
     └── dto/
         ├── FinancialDataRequest
         ├── FinancialReportResponse
         └── UnitEconomicsResponse
   business/
     ├── service/FinancialCalculationService
     ├── service/UnitEconomicsService
     └── domain/FinancialData
   data/
     ├── entity/FinancialDataEntity
     └── repository/FinancialDataRepository
   ```

2. **API Endpoints**:
   - `POST /api/v1/projects/{projectId}/financial-data` - 재무 데이터 저장
   - `GET /api/v1/projects/{projectId}/financial-report` - 손익계산서 조회
   - `GET /api/v1/projects/{projectId}/unit-economics` - 유닛 이코노믹스 조회
   - `GET /api/v1/projects/{projectId}/financial-forecast` - 재무 예측 조회

3. **Database Schema**:
   ```sql
   CREATE TABLE financial_data (
     id BIGSERIAL PRIMARY KEY,
     project_id BIGINT NOT NULL REFERENCES projects(id),
     year INT NOT NULL,
     revenue DECIMAL(15, 2),
     cogs DECIMAL(15, 2),
     marketing_cost DECIMAL(15, 2),
     operating_cost DECIMAL(15, 2),
     customer_acquisition_count INT,
     customer_retention_rate DECIMAL(5, 2),
     created_at TIMESTAMP,
     updated_at TIMESTAMP
   );
   ```

4. **Unit Economics Calculations**:
   ```java
   @Service
   public class UnitEconomicsService {
       
       public BigDecimal calculateCAC(BigDecimal marketingCost, Integer newCustomers) {
           // CAC = Total Marketing Cost / New Customers
           return marketingCost.divide(new BigDecimal(newCustomers), 2, RoundingMode.HALF_UP);
       }
       
       public BigDecimal calculateLTV(BigDecimal avgRevPerCustomer, 
                                      BigDecimal grossMargin, 
                                      BigDecimal retentionRate) {
           // LTV = (Avg Revenue per Customer * Gross Margin) / Churn Rate
           BigDecimal churnRate = BigDecimal.ONE.subtract(retentionRate);
           return avgRevPerCustomer.multiply(grossMargin).divide(churnRate, 2, RoundingMode.HALF_UP);
       }
       
       public BigDecimal calculateLTVtoCAC(BigDecimal ltv, BigDecimal cac) {
           // LTV:CAC Ratio (should be > 3.0 for healthy business)
           return ltv.divide(cac, 2, RoundingMode.HALF_UP);
       }
       
       public Integer calculatePaybackPeriod(BigDecimal cac, BigDecimal monthlyRecurringRevenue) {
           // Payback Period (months) = CAC / Monthly Recurring Revenue
           return cac.divide(monthlyRecurringRevenue, 0, RoundingMode.UP).intValue();
       }
   }
   ```

5. **Financial Forecast Logic**:
   ```java
   public List<FinancialForecast> generateForecast(FinancialData baseData, int years) {
       // Year-over-year growth assumptions
       // Revenue, cost projections
       // Break-even analysis
   }
   ```

## ✅ 완료 조건
- [ ] API endpoints 구현 완료
- [ ] 재무 계산 로직 구현
- [ ] 유닛 이코노믹스 계산 구현
- [ ] 재무 예측 알고리즘 구현
- [ ] Unit Tests 작성 (계산 로직)
- [ ] Integration Tests 작성 (API)
- [ ] Swagger API Documentation 작성
- [ ] Flyway Migration Script 작성

## 🔗 의존성
- **Depends on**: #006 (Project API 필요)
- **Blocks**: None

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class UnitEconomicsServiceTest {
    @Test
    void calculateCAC_ShouldReturnCorrectValue() {
        // Given
        BigDecimal marketingCost = new BigDecimal("10000");
        Integer newCustomers = 100;
        
        // When
        BigDecimal cac = service.calculateCAC(marketingCost, newCustomers);
        
        // Then
        assertThat(cac).isEqualByComparingTo(new BigDecimal("100.00"));
    }
    
    @Test
    void calculateLTVtoCAC_ShouldBeGreaterThan3_ForHealthyBusiness() {
        // Test business health indicators
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class FinancialControllerTest {
    @Test
    void getUnitEconomics_ShouldReturnCalculations_WhenDataExists() {
        // Test API response
    }
}
```

## 📅 로드맵
- **Phase**: Phase 4 (Special Features)
- **Parallel Group**: Special Features (Can run with #011)

## 🏷️ Labels
`epic-2`, `backend`, `financial`, `calculation`, `feature`, `priority-should`

## 📋 Financial Metrics
```yaml
unit_economics:
  - CAC: Customer Acquisition Cost
  - LTV: Lifetime Value
  - LTV:CAC Ratio: Target > 3.0
  - Payback Period: Target < 12 months
  - Gross Margin: Target > 70%
  - Churn Rate: Target < 5%
```

## 📋 Checklist
- [ ] Build successful (Gradle build)
- [ ] Unit/Integration tests added and passing
- [ ] Code style checked (Checkstyle/Spotless)
- [ ] API Documentation updated (Swagger/OpenAPI)
- [ ] Database migrations tested
- [ ] Calculation accuracy verified
- [ ] Edge cases handled (division by zero, etc.)

---
**Related Tasks**: #006, #011
**Execution Order**: Phase 4 - Parallel (Special Features)

