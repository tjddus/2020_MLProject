# 2020_MLProject

## Motivation
이번 프로젝트를 시작하게 된 계기는 “Neural Style Transfer”를 통해서였다. Style transfer는 content image와 style image를 이용해 해당 style을 갖는 새로운 이미지를 생성하는 방식이다. 그런데 pre_trained 모델에 style image 입력으로 고흐의 그림이 아닌 이미지 사진을 넣게 되면 제대로 된 이미지를 생성하지 못했다. 이를 통해 content image와 style image는 서로 다른 특징을 가지는 것은 아닌지 궁금했고 딥러닝 학습에 따라 그 결과가 달라질 수도 있겠다는 생각을 했다.
 기존에는 Resnet, Vggnet 등의 여러 학습 모델이 개미, 벌과 같이 무언가 형상을 띠는 image를 분류하는 연구가 많이 나왔다. 하지만 style, texture를 분류해주는 딥러닝 모델은 존재하지 않는다. **style, texture를 분류한다는 말이 이상할 수 있지만 우리는 앤디워홀의 작품과 반 고흐의 작품이 서로 다르다고 분류**할 수 있다. 물론 개미, 벌과 같이 형상으로 정의할 수 없지만 그 둘 작품의 색감이나 스타일이 다르다는 것은 느낄 수 있다. 
 
  <img src="https://user-images.githubusercontent.com/30233659/111041109-0209b800-847a-11eb-8783-5c7484f20542.png" width="50%" height="50%">
  
그래서 과연 Convolution network가 이 둘의 색감이나 스타일의 특징을 잘 추출하여 서로 다른 화가가 그린 그림 이라고 분류할 수 있는지가 궁금했다. 우선 이번 프로젝트의 과업은 분류(classifier)가 중점이다. 그래서 imagenet 분류에 중점에 맞춘 연구가 있다. 또한 이번 프로젝트와 autoencoder model 설계에 도움이 된 neural style transfer에 관한 연구가 있다.

## 필요한 라이브러리
  * python
  * pytorch

## 제안 모델
  명화 분류 프로젝트로 변화를 주면서 neural style transfer net에 변형을 주어 autoencoder를 구현하고자 했다. 이번 프로젝트는 크게 **autoencoder와 classifier** 부분으로 나누어 모델을 설계하였다. 명화분류는 Imagenet과 다른 모델로 접근할 필요성을 느꼈다. 사람의 특징 추출에 있어서는 convolution layer를 거듭할수록 귀와 같은 고차원의 특징이 추출되는데 이런 **화가의 명화의 경우 어떤 특징이 추출될지는 전혀 알 수 없기에 색감, 스타일, 이미지를 rare event로 판단하고 autoencoder**를 이용해 잘 압축된 데이터를 얻으려고 했다. 처음 **autoencoder로 학습된 모델의 앞단인 encoder와 redisual layer를 그대로 가지고 와서 classifier의 이미지 특징 추출 부분**으로 사용하고 이후 fully connected layer를 두어 화가를 분류시켰다.
   - autoencoder 구조

   <img src="https://user-images.githubusercontent.com/30233659/111041125-12ba2e00-847a-11eb-809a-6dced8950842.png" width="70%" height="70%">

   - classifier 구조

   <img src="https://user-images.githubusercontent.com/30233659/111041130-18b00f00-847a-11eb-87ca-3c40a4281f35.png" width="70%" height="70%">

## 시도 및 분석

#### try1. batch size 조정
Batch size를 8, 16, 32로 나누어서 학습을 진행했다. 32가 학습속도 및 일반화에서 가장 높은 정확도를 보였다.

#### try2. Autoencoder + classifier
 처음 Autoencoder를 진행하고 나서의 이미지 복원 상태입니다. 이후 최종 모델을 autoencoder+classifier를 합친 새로운 모델을 제시했습니다.
 
   <img src="https://user-images.githubusercontent.com/30233659/111041137-1f3e8680-847a-11eb-9ef1-9d58eba663f5.png" width="70%" height="70%">

#### try3. 전이학습, Autoencoder의 encoder 가중치를 classifier 가중치에 적용
Autoencoder를 통해 pre-trained된 autoencoder의 앞단을 그대로 classifier의 가중치에 그대로 로드시켰다.

#### try4. 성능확인, 분류갯수 48에서 3명으로 줄여 진행
분류갯수가 48명으로 많다보니 모델의 정확도가 전혀 나오질 않아서 3명으로 줄여서 진행했다.

   <img src="https://user-images.githubusercontent.com/30233659/111041158-35e4dd80-847a-11eb-8c1d-000893469a26.png" width="70%" height="70%">
   
   <img src="https://user-images.githubusercontent.com/30233659/111041161-3b422800-847a-11eb-9835-1d784d72dc11.png" width="70%" height="70%">
   
#### try5. Data Augumentation
뒤집기, 회전, 자르기 진행했습니다.

   <img src="https://user-images.githubusercontent.com/30233659/111041141-26fe2b00-847a-11eb-9347-e12a46a3b0d4.png" width="70%" height="70%">
   
## 결론
 결론적으로 이번 프로젝트를 통해 개선할 사항은 **분류하려는 화가의 수를 조금 줄여 model 자체에 집중해보는 시도**를 가져야겠다. 또한 여전히 발생하는 overfitting에 유의하여 fully connected layer에 dropout을 해서 학습시켰는데 성능이 좋지 못하여 이를 적용하지 못했다. 그래서 dropout을 적용해 효과를 볼 수 있는 방법으로 생각해 볼 예정이다. 또한 전체적으로 화가의 작품 수를 균등하게 맞추어 어느 한 쪽으로 분류가 쏠리는 것을 막고자 한다. 

## 인용자료
  실제로 모델의 autoencoder + classifier의 설계는 직접 진행했습니다.
  https://github.com/pytorch/examples/tree/master/fast_neural_style
