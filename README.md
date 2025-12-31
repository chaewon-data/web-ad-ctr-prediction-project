## 📌 프로젝트 개요
광고 클릭률(CTR)을 예측하는 AI 모델 개발 프로젝트입니다. 7일간의 웹 로그 데이터를 기반으로 하루 동안의 광고 클릭 여부를 예측하는 이진 분류 문제를 해결했습니다.

- **프로젝트 기간**: 2025.11 ~ 2025.12
- **대회**: [Dacon - 웹 광고 클릭률 예측 AI 경진대회](https://dacon.io/competitions/official/236258/overview/description)
- **최종 결과**: Public Leaderboard **27위 / 1,150명 (상위 2.3%)**, AUC **0.78079**

---

## 📊 데이터
**데이터 규모**
- Train: 28,605,391 rows × 40 features (약 42GB)
- Test: 4,538,541 rows × 39 features (약 7GB)
- Click rate: 0.1947 (클래스 불균형)

**데이터 다운로드**
> 데이터는 용량이 커서 GitHub에 포함되지 않았습니다.  
> [대회 페이지](https://dacon.io/competitions/official/236258/data)에서 다운로드하세요.

---

## 📋 코드 구조

### 1. 환경 설정 및 라이브러리
- 필수 라이브러리 import (pandas, numpy, sklearn, xgboost)
- 경고 메시지 필터링

### 2. 데이터 로드 및 변환
- CSV → Parquet 변환으로 메모리 효율화
- Snappy 압축 적용

### 3. 메모리 최적화
**초기 메모리 사용량**
- Train: 41.97 GB
- Test: 6.63 GB

**최적화 기법**
1. **ID 컬럼 변환**: `object` → `int32`
2. **범주형 변수 변환**: `object` → `category`
   - Train: 41.97 GB → 6.20 GB (85.2% 감소)
   - Test: 6.63 GB → 1.06 GB (84.0% 감소)
3. **수치형 다운캐스팅**: `float64` → `float32`, `int64` → downcast
   - Train: 6.20 GB → 4.58 GB (26.2% 추가 감소)
   - Test: 1.06 GB → 0.83 GB (22.3% 추가 감소)

**최종 메모리 사용량**
- Train: **41.97 GB → 4.58 GB (89.1% 감소)**
- Test: **6.63 GB → 0.83 GB (87.5% 감소)**

### 4. 피처 엔지니어링
**Target Encoding**
- K-Fold Cross-Validation (5-fold) 적용으로 overfitting 방지
- Smoothing (α=10) 적용
- 범주형 변수 22개 인코딩

### 5. 모델링
**Train/Validation Split**
- Train: 22,884,312 rows (80%)
- Validation: 5,721,079 rows (20%)
- Stratified sampling으로 클래스 불균형 유지

**XGBoost 하이퍼파라미터**
```python
{
    'n_estimators': 1000,
    'learning_rate': 0.05,
    'max_depth': 8,
    'subsample': 0.8,
    'colsample_bytree': 0.8,
    'reg_alpha': 0.1,
    'reg_lambda': 1,
    'tree_method': 'hist',
    'device': 'cuda'
}
```

**학습 결과**
- 학습 시간: 1.7분 (GPU 가속)
- Validation AUC: **0.7820**
- 안정적인 학습 곡선 (0.7368 → 0.7820)

### 6. 예측 및 제출
- Test 예측값 평균: 0.1959 (실제 Click rate와 유사)
- Submission 파일 생성

---

## 🏆 최종 결과
- **Public Leaderboard AUC: 0.78079**
- **최종 순위: 27위 / 1,150명 (상위 2.3%)**
