# Dacon_code_similarity
## 05.28
전날 토크나이징 작업을 해놓은 최종적인 데이터셋(train 330만, val 22만) 으로 학습을 시작하였다. 
기존 private score : 0.947

## 05.29
330만 개의 data를 8시간 돌린 결과 val_loss : 0.02, val_accuracy : 0.912 정도 나왔는데 score는 0.85정도로 오히려 떨어졌다.
예상되는 원인은,
1) 300개 이하 토큰을 버려 max_len 512->256이 된 점
2) 1 epoch을 조금 넘길때까지 학습하여 생긴 overfitting
3) pair 구성 시 점수를 기준으로 정렬하여 사용하였는데 그 과정에서 중복되는 데이터가 많이 발생.
4) batch size가 32 -> 128로 늘었다. 
우선 0.947 score가 나온 90만개 data로 한 실험을 동일한 파라미터를 적용하여 점수를 확인해본다.
점수가 0.94에 가깝게 나온다면 overfitting이 난것이고 점수가 똑같이 0.85수준으로 나온다면 파라미터(토큰과 max_len) 문제인 것이다.

추가적으로 중복 데이터가 많은지 확인해보아야겠다. ~~train loss와 val loss 모두 이쁘게 내려갔는데 무엇이 문젠지 모르겠다.~~

==> ~~점수가 잘 나온 경우를 보니 train loss가 시작부터 val_loss보다 계속 큰 상태로 내려갔고 earlystopping이 걸린 부분에서야 겨우 크로스되었다. 문제점을 찾은 것 같다.~~

![화면 캡처 2022-05-29 213012](https://user-images.githubusercontent.com/37128004/170868396-e26f915d-c8dc-4f72-93ff-6ee57c328a17.png)

TPU를 적용한다고 적용했는데 속도는 전혀늘지 않고 batch_size만 늘리는 용도로 사용중이다. 
Trainer 와 TrainingArgument API 사용해서 훈련하려다보니 커스터마이징이 불편해서 속도 개선방법을 계속 찾지 못한다면 직접 짜는 방향으로 틀어야겠다. 

## 5.30
토크나이징 과정에서 토큰 갯수가 300개 이하인 데이터를 버리고 사용했는데 모델링 과정에서 MAX_LEN을 256으로 두고 실험하고 있었다.
거의 반정도만 보고 학습을 하고 있던 것이라 acc가 나오지 않았던 것으로 추정된다. 

## 5.31
MAX_LEN을 512로 바꾸고 실험한 결과 accuracy가 소폭 상승했으나 기대한 수준은 아니었다. 최고점이 나왔던 경우는 data 자체가 graphcode-bert 모델로 토크나이징 했었다. 
따라서 토크나이징과 pair 구성에 문제가 있었다는 가설 하에 이번엔 huggingface/CodeBERTa-small 말고 microsoft/graphcodebert-base 를 이용하여 토크나이징하고 pair를 구성해보았다. train 데이터 구성에만 34시간이 걸리는 3중 for문을 돌려야 했다.

## 6.1
negative pair를 만드든 과정에 문제가 있음을 확인하고 새로운 방법으로 데이터를 가져왔다. 

## 6.2


