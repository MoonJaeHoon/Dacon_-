# Dacon_Sloan-Digital-Sky-Survey_SDSS
* Score = 0.36573(리더보드 상위 21%)
* Dacon 프로필 : https://dacon.io/myprofile/226309/overview/
* 딥러닝 여러 기법을 공부하고 적용함

# Data Description
* 데이터출처 : https://newfront.dacon.io/competitions/official/235573/data/
## train.csv (199991 rows, 24 columns) / test.csv (10009 rows, 22 columns)

* ID : Unique ID
* type : Source type : 천체의 분류 (예측을 해야 하는 변수입니다.)
* fiberID : 관측에 사용된 광섬유의 구분자
다음 밝기 변수들은 변수명에 u, g, r, i, z 첨자가 붙어있다.
해당 알파벳들은 파장대를 의미하며, 각각
u : Ultraviolet
g : Green
r : Red 
i : Near Infrared
z : Infrared
* psfMag : (Point spread function magnitudes) 먼 천체를 한 점으로 가정하여 측정한 빛의 밝기입니다.
* fiberMag : (Fiber magnitudes) 3인치 지름의 광섬유를 사용하여 광스펙트럼을 측정합니다. 광섬유를 통과하는 빛의 밝기입니다.
* petroMag : (Petrosian Magnitudes) 은하처럼 뚜렷한 표면이 없는 천체에서는 빛의 밝기를 측정하기 어렵습니다. 천체의 위치와 거리에 상관없이 빛의 밝기를 비교하기 위한 수치입니다.
* modelMag : (Model magnitudes) 천체 중심으로부터 특정 거리의 밝기입니다.


# 활용된 주요 방법론 :
### 1. 첫 노드수 & 총 layer 갯수 결정
- N_h = N_s / a*(N_i+N_o)
### 2. BatchNormalization
- initializer와 DropOut을 굳이 사용하지 않아도 될 만큼 과적합방지에 뛰어나다.
- 배치사이즈가 너무 작은 경우에는 합리적인 정규화를 이끌어내지 못하므로 배치크기가 적당한 경우에 사용하자.
### 3. Learning Rate Scheduler
- 단일 learning rate로 학습하는 건 적합속도, 정확도 면에서 다 안 좋음
- (reducelronPleteau)와 같이 일정 기간(red_patience) 기다렸다가 lr을 감소시키는 방법도 있고
- 코사인 스케쥴러도 있음
### 4. Drop Out
- 특정 layer에서 노드가 제 기능을 하지 못하게 하여 학습시켜서 
- 적합시킨 모델을 사용할 때에는 모든 노드를 다 사용하여 Predict함.
### 5. Early Stopping with saving best weight
- 과대적합 방지
### 6. Activation Function
- Relu vs LeakyRelu vs Elu vs Gelu
- 여기서 사용하진 않았지만, 반응변수가 (Boundary)하한과 상한이 정해진 연속형변수일 때 tanh()*a로 사용자정의 함수를 사용이 가능함.
- 몇 번의 딥러닝 경험에 비추어 본 결과, Relu와 LeakyRelu 보다는 Elu가 더 좋다고 느껴짐
- Gelu는 경우에 따라 잘 기능할 때도 있음
- 일반적으로 Gelu가 Elu보다 learningrate를 더 적게 설정해야 한다고 느꼈음.(개인적인 생각)
### 7. Using Warm Restarts
- ADAMWR : Adam warm restarts
- SGDWR : SGD with restarts
- 처음부터 매우 낮은 lr을 가지고 적합하기보다는 보다 높은 lr로 WarmUp Step을 가진 후 모델링을 진행하는 것이 필요하다고 생각됨.

# 위와 같은 결론을 얻기까지 시도한 다양한 방법들 정리
### 1. Gradient Clipping
- clipnorm : 가중치 제한 두기
- 일반적으로 1로 설정하는 듯
- 학습속도가 느려져도 상관이 없는 경우 Gradient Exploding 방지를 위해 활용이 가능하다고 생각됨.
### 2. Weight Decay
- ADAMW : Adam with weight decay
- SGDW : SGD with weight decay
### 3. Entity Embedding
- 출처: https://www.kaggle.com/aquatic/entity-embedding-neural-net#L19
- 범주의 갯수가 너무나 많은 Categorical Variable인 FiberID 때문에 고려한 방법이다
- 너무나 많은 범주들을 가진 범주형변수를 설명변수로서 그대로 모델에 투입하면 모델의 정확도가 떨어지는 경우가 있다.
- 실제로 이 데이터에서는 범주형 변수 FiberID를 그대로 투입하였을 때, FiberID를 제외하고 모델링한 결과보다 좋지 못했다.
- EntityEmbedding은 설명변수들 중 범주형변수가 Embedding layer를 통과하게 만들어 임베딩시킨 후 모델링하는 방법론이다.
- Embedding을 거친 결과, FiberID를 원래 그대로 투입시켰을 때보단 좋은 결과를 보였지만, 그래도 이 변수를 제외하고 모델링하였을 때보다는 좋지 못했다.
- 다음에 너무나 많은 범주를 가진 범주형 변수를 또 만나게 된다면, integer 형태로 고려해보기도 할 것이다. (대회 참여 당시 이건 생각지 못했음..)
- 딥러닝은 아니지만, 실제로 LightGBM Document에서는 너무나 많은 범주를 가진 Categorical Variable은 Integer형태로 고려하여 모델에 투입시키는 것을 추천하고 있다.
### 4. K-Fold Ensemble
- 이 데이터에서는 시간이 부족해 사용해보지 않았지만, 더 높은 정확도를 가져다 줄 수 있을 것이다.
### 5. SWA (Stochastic Weight Average)
- global minima를 찾는 것이 우리 목표인데, 이를 local minima들을 이용하여 찾아내겠다는 방법론이다.
- 나의 직관으로 이해한 설명은 다음과 같다.
- local minima들 각각을 도출해낸 weight들을 저장해놓고, 이를 Weighted Average를 이용해 New weight를 생성해낸다.
- New Weight를 통해 local minima들보다 더 훌륭한 minima를 찾아낼 수 있을 것이다.
