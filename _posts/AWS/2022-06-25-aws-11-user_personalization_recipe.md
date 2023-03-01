---
title: "[AWS] AWS Personalize: User-Personalization recipe"
excerpt: 
last_modified_at: 2022-06-25

categories:
  - AWS

tags:
  - AWS
  - Personalize
  - recipe
  - Recommend System

---

# AWS Personalize: User-Personalization recipe

> [AWS Personalize Documentation - User Personalization](https://docs.aws.amazon.com/personalize/latest/dg/native-recipe-new-item-USER_PERSONALIZATION.html)의 내용을 번역, 요약 및 재구성한 포스트입니다.

- 개인화 추천시스템에 최적화

- Interaction, Items, User 데이터에 기반한 추천

- 자동화된 item exploration 제공  

- User implicit feedback(→ Interaction)을 기반으로 추천 자동 조정


Item Exploration: 상호작용(interaction) 또는 관련성(relevance)이 적은 아이템 추천  
{: .notice--info}

<br><br>

## Automatic Updates

자동 업데이트는 비용이 발생하지 않습니다.  
{: .notice--info}

- 최신 모델(Latest solution version)은 **2시간마다 내부적으로 자동 업데이트**합니다.  
→ 모델(Solution Version)을 새로 생성하지않고, 새로운 데이터가 추가됩니다.  
이를통해 최신 Item 정보가 추가되고, User implicit feedback(→ Interaction)에 따라 exploration을 조정합니다.

- 2시간마다 이루어지는 자동 업데이트는 완전한 Full retraining이 아닙니다.  
(→ User behavior 학습 X)  
따라서 매주 새로운 Solution version을 생성 권장합니다.  
(생성시 `trainingMode`를 `Full`로 설정)

- 2시간마다 이루어지는 자동 업데이트가 충분하지 않을 경우  
`trainingMode`를 `UPDATE`로 설정하여 Solution version을 생성합니다.  

<br>

- 자동 업데이트는 `trainingMode`를 `FULL`로 설정한 Latest solution version만 해당됩니다.  
- `trainingMode`를 `UPDATE`으로 생성된 Solution version은 자동 업데이트가 되지 않습니다.
- Dataset을 삭제한 경우 또한 자동 업데이트가 되지 않습니다.  
{: .notice--warning}

<br><br>

## Working with impressions data

- **Impression data:** 유저가 특정 item과 상호작용할 때 보이게되는 Item list  
- **Positive interaction:** ex. clicking, watching, or purchasing  
{: .notice--info}

User-Personalization recipe는 Impression data를 사용합니다.  
(다른 recipe의 경우 Positive interaction만 사용)  

Impression data를 사용하여 item이 얼마나 자주 ignored 되었는지를 기반으로  
새로운 아이템을 적절하게 계산하고, 그에따라 추천 아이템을 변경합니다.  

<br><br>

## Properties and hyperparameters

### Algorithm hyperparameters

HPO tunable: Yes  
{: .notice--info}

#### hidden\_dimension

- 모델에 사용되는 hidden variable의 갯수

- Hidden variable은 ranking score 생성을 위해 유저의 구매 history와 Item 통계를 새로 생성합니다.

- Interaction dataset이 더 복잡한 패턴을 가질 경우 더 큰 수의 hidden variable 지정합니다.

- 더 많은 hidden dimension은 더 큰 dataset과 processing time을 요구합니다.

- 최고의 값을 결정하기 위해서 HPO를 사용하는 것을 권장합니다.

<br>

#### bptt

- **bptt:** back-propagation through time
- **back-propagation through time technique:** RNN algorithm의 weight update 기법  
{: .notice--info}

- back-propagation through time technique 사용여부를 결정합니다.

- delayed rewards을 early event에 연결하기 위한 long-term credit을 위해 bptt을 사용합니다.  
(For example, a delayed reward can be a purchase made after several clicks. An early event can be an initial click.)  
  - delayed rewards: 여러 event를 통해 발생한 최종 reward *(?)*  
  - early event: 최근 event(ex. 클릭 한 번) *(?)*

- 장기적인 영향(long-term effect)를 고려하여 총 reward를 최대화 하는 것은 좋은 방법입니다.

- 장기적인 영향(long-term effects)을 고려하기 위해서는 더 큰 bptt 값을 사용합니다.

- 큰 bptt 값은 더 큰 dataset과 processing time이 필요합니다.

<br>

#### recency\_mask

- Interaction dataset의 최신 인기 동향(Latest popularity trends)를 고려할지 여부를 결정합니다.

- 최근 인기 동향은 interaction dataset 패턴의 갑작스런 변화를 포함할 수 있습니다.

- 최근 이벤트에 더 많은 가중치를 부여하는 모델을 학습하려면 `recency_mask`를 `true`로 설정하세요.

- 과거의 interaction에 동일한 가중치를 부여하는 모델을 학습혀려면 `recency_mask`를 `false`로 설정하세요.  
동일한 가중치를 사용하여 좋은 추천을 얻기 위해서는 더 큰 학습 데이터셋이 필요합니다.

<br><br>

### Featurization hyperparameters

HPO tunable: No  
{: .notice--info}

#### min\_user\_history\_length\_percentile

Short length history를 가진 유저는 **개인의 취향이 아닌, 인기 아이템을 기반**으로 한 패턴이 많이 보입니다.  
이러한 history를 가진 유저 데이터를 제거하는 것이 학습 시 잠재된 데이터의 패턴에 집중할 수 있게 합니다.  
대부분의 유저를 유지하되, edge case만 제거하는 것을 추천합니다.    

<br>

#### max\_user\_history\_length\_percentile

Long length history를 가진 유저는 **noise를 많이 가진 경향**이 있습니다.(ex, 자동화된 interacation하는 로봇)  
이러한 유저를 제한하는 것이 학습의 노이즈를 줄일 수 있습니다.  
대부분의 유저를 유지하되, edge case만 제거하는 것을 추천합니다.  

<br><br>

### Campaign configuration hyperparameters

HPO tunable: No  
{: .notice--info}

#### exploration\_weight

추천시 상호작용(interaction) 또는 관련성(relevance)이 적은 아이템을 얼마나 자주 추천할지 결정합니다  
1에 가까울수록 더 자주 탐색(exploration)하고, 0이면 현재 데이터에 기반한 추천만 진행됩니다.(no exploration)

<br>

#### exploration\_item\_age\_cut\_off

Item이 마지막 상호작용(interaction)으로부터 time frame 기반으로 탐색할 Item을 결정합니다.  
마지막 상호작용(interaction)으로부터 Item 탐색 범위를 정의하기위해 maximum item age을 제공합니다.  
값이 클수록 더 많은 아이템이 탐색(exploration)에 고려됩니다.  

<br><br>

## Reference

- [AWS Personalize Documentation - User Personalization](https://docs.aws.amazon.com/personalize/latest/dg/native-recipe-new-item-USER_PERSONALIZATION.html)
