---
title : "Pretrained Language Model"



excerpt: "PLM"

categories:
  - NLP
tags:
  - [Machine Learning,NLP,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
## PLM(Pretrained Language Model)

A PLM (Pretrained Language model) consisting of Billion unit parameters is shared as an open-source. Since the advent of Bert, attempts have been made to learn language representations and fine-tuning them for various downstream tasks. And, large research institutes (Ex. Google, DeepMind, OpenAI….etc) continue to publish large-scale PLM papers. I used to think I needed to be able to build large-scale PLMs, but I've changed my mind these days. In the world, various PLMs are already shared as open sources. Their modules are similar, but each has different trained configurations and learned tasks. Not only that but training the PLM causes huge CO2 emissions. The Earth's carbon problem is already serious, and with the AI boom, if you and I try to make a Pretrained Model, it will only get worse. If you are well aware of the differences between these models, you can find a solution that suits your situation without learning PLM. (This article uses lecture materials from the 2nd session of Naver BoostCamp AI Tech.)

2021-12-27 Bert, Roberta, 

## Bidirectional Encoder

![image](https://user-images.githubusercontent.com/50165842/147475191-30162c43-7a87-45cc-b26c-3fafe2ea11ea.png)

Bidirectional Encoder uses only the Encoder part from the structure of the existing Transformer. First, let's look at the most representative Bert. 

### Bert

![image](https://user-images.githubusercontent.com/50165842/147475435-ab04d211-a126-4227-b6ed-b8d192aa87eb.png)

As mentioned above, Bert (Bidirectional Encoder Representations from Transformers) is a model created using Transformer's Encoder structure. By giving Sentence1 and Sentence2 as inputs, we performed the Next Sentence Prediction Task to guess whether two sentences are consecutive sentences.

![image](https://user-images.githubusercontent.com/50165842/147476098-25a718b0-faf7-40e9-aa7c-429585b050fb.png)

Then, it performs the task of predicting it by putting a Mask in the token. This image, taken from the BoostCamp lecture material. Judging from the image, it is to make a hole and restore it. However, it is difficult to say that Bert's Masked Token Prediction realizes a true Bidirectional Encoder. Because predicting Mask tokens is independent. Let's take an example to illustrate this.

```python
A = ['일론' ,'머스크는'  ,'테슬라의' ,'Co-Founder이다' ]
Masked_A [ 'MASK' , 'MASk' ,'테슬라의' ,'Co-Founder이다' ]
```

When these two are masked, each independently predicts `'Elon'' and `'Musk''. In fact, when the preceding word is ''Elon'', it will most likely be ''Musk''. That is, `'Elon'` and `'Musk'` are not independent, but they are predicted independently. Nevertheless, it can be said that Bert was a sensational model when it first came out, as it performed well in a variety of DownStream tasks.

```python
 A = ['일론' ,'주커버그는' '테슬라의' , 'Co-Founder이다' ]
```

Bert also randomly swaps out tokens. This is called random replacing.

Bert randomly selected 15% of the tokens of the input sequence, replacing 80% with a mask, replacing 10% randomly, and leaving 10% unchanged.

In other words, Bert performed the Next Sentence Prediction and Masked LM tasks as pretraining tasks.

### Roberta

Roberta (A Robustly Optimized BERT Pretraining Approach) is a model that thought about how to learn Bert.

| Bert          | Roberta         |
| ------------- | --------------- |
| NSP           | WithoutNSP      |
| 128           | 512             |
| Fixed Masking | Dynamic Masking |
| 256           | 2K ,8K          |
| 16gb          | 160gb           |

- First, Roberta's paper removed the NSP task and pretrained it based on the paper that raised the question of whether the NSP task was important to Bert's performance.
- Also, bert trained 90% of the data with a 128-length token sequence, whereas roberta trained with a 512-length token sequence.
- Bert learned to predict the same mask token by using fixed masking every epoch, while Roberta used dynamic masking to change the mask position every epoch.
- Bert used a batch size of 256, while roberta used a batch size of 2K, 8K.
- Bert said he used around 16gb, but Roberta used 160gb of corpus. This leads to more data and more training time.

![image](https://user-images.githubusercontent.com/50165842/147479411-ac8c7489-ef64-41de-b0bb-6c1e9138c2a5.png)



위 와 같은 결과가 나왔다고 논문에 적혀 있습니다.

## Autoregressive Decoder



### GPT2



## Encoder-Decoder



### XLM



### T5