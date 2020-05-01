# Dacon_Sloan-Digital-Sky-Survey_SDSS
#### 딥러닝 여러 기법을 공부하고 적용함
#### Score = 0.36573(리더보드 상위 21%)

# 활용된 주요 방법론 :
### 1. 첫 노드 수 & 총 layer 갯수 결정

### 2. BatchNormalization
### 3. Learning Rate Scheduler
### 4. Drop Out
### 5. Early Stopping with saving best weight
### 6. Activation Function
### 7. Using Warm Restarts
- ADAMWR : Adam warm restarts
- SGDWR : SGD with restarts

# 위와 같은 결론을 얻기까지 시도한 다양한 방법들 정리
### 1. Gradient Clipping
### 2. Weight Decay
- ADAMW : Adam with weight decay
- SGDW : SGD with weight decay### 6. SWA (Stochastic Weight Average)
### 3. Entity Embedding
출처: https://www.kaggle.com/aquatic/entity-embedding-neural-net#L19
### 4. K-Fold Ensemble
