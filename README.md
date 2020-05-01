# Dacon_Sloan-Digital-Sky-Survey_SDSS
#### 딥러닝 여러 기법을 공부하고 적용함
#### Score = 0.36573(리더보드 상위 21%)

# 활용된 주요 방법론 :
### 1. 첫 노드수 & 총 layer 갯수 결정
N_h = N_s / a*(N_i+N_o)
### 2. BatchNormalization
- initializer와 DropOut을 굳이 사용하지 않아도 될 만큼 과적합방지에 뛰어나다.
- 배치사이즈가 너무 작은 경우에는 합리적인 정규화를 이끌어내지 못하므로 배치크기가 적당한 경우에 사용하자.
### 3. Learning Rate Scheduler
- 단일 learning rate로 학습하는 건 적합속도, 정확도 면에서 다 안 좋음
- (reducelronPleteau)와 같이 일정 기간(red_patience) 기다렸다가 lr을 감소시키는 방법도 있고
- 코사인 스케쥴러도 있음
### 4. Drop Out
- 특정 layer에서 노드가 제 기능을 하지 못하게 하여 
### 5. Early Stopping with saving best weight

### 6. Activation Function

### 7. Using Warm Restarts
- ADAMWR : Adam warm restarts
- SGDWR : SGD with restarts

# 위와 같은 결론을 얻기까지 시도한 다양한 방법들 정리
### 1. Gradient Clipping
- clipnorm : 가중치 제한 두기
- 보통 1로 설정하는 듯
- 학습속도가 느려져도 상관이 없는 경우 활용이 가능하다고 생각됨.
### 2. Weight Decay
- ADAMW : Adam with weight decay
- SGDW : SGD with weight decay
### 3. Entity Embedding
출처: https://www.kaggle.com/aquatic/entity-embedding-neural-net#L19
### 4. K-Fold Ensemble
### 5. SWA (Stochastic Weight Average)
