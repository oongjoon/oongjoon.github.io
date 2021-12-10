---
title : "Huggingface tutorial: Tokenizer summary"



excerpt: "Tokenizer summary"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

## Huggingface tutorial Series : tokenizer

This article was compiled after listening to the tokenizer part of the Huggingface tutorial series.. [Summary of the tokenizers](https://huggingface.co/docs/transformers/tokenizer_summary)

## What is tokenizer

A tokenizer is a program that splits a sentence into sub-words or word units and converts them into input ids through a look-up table. In the Huggingface tutorial, we learn tokenizers used specifically for transformers-based models. 

![image](https://user-images.githubusercontent.com/50165842/144427223-af53b57a-f908-4b5c-b584-a425113a9cde.png)



## word-based tokenizer

Several tokenizers tokenize word-level units.

It is a tokenizer that tokenizes based on space. When splitting based on space, it becomes as follows.

![image](https://user-images.githubusercontent.com/50165842/144427638-cb31e5ee-6438-484c-bb21-027ea69988e5.png)

You can also create rules that tokenize based on punctuation.

![image](https://user-images.githubusercontent.com/50165842/144427823-345b4540-0a7b-410b-9146-5179cf3f0a96.png)



We will convert this to input_ids in the look-up-tabl

![image](https://user-images.githubusercontent.com/50165842/144427467-50990986-433e-4005-a1eb-70126e6d0e0e.png)

Let's look at the problems of word-based.

### very large vocabulary size



![image](https://user-images.githubusercontent.com/50165842/144538921-1a218243-073e-4f1c-bcc2-aa80e0f3195c.png)

There are words like dogs and dogs. These two words have similar meanings. However, if split based tokenizer is used, words with different ids but similar meaning are mapped to the look-up table. Therefore, you end up with a vocabulary of enormous size. More parameters are needed to embed these many words.

![image](https://user-images.githubusercontent.com/50165842/144539705-dd0a62eb-9140-4ed3-8928-536bf6590804.png)

### fequently word

To limit the number of words, tokenizing mainly on words with high frequency can solve the above problem.

![image](https://user-images.githubusercontent.com/50165842/144539738-7071ea88-ca0a-4eca-b644-138759e0a338.png)

However, in this case, it is not free from the OOV (out of vocabulary) problem.![image](https://user-images.githubusercontent.com/50165842/144540125-a26340ba-d0b1-429f-a58d-049264086b57.png)

A loss of information occurs because there is no word malapropisms in the vocabulary. It can be seen that the representation ability is lowered.![image](https://user-images.githubusercontent.com/50165842/144540337-88941576-3459-4e24-8f8f-792999703c62.png)

UNKNOWN WORDS has the same representation, and it is different from human standards. But when converted to input_ids, multiple different meaning sentences with the same expression may occur. It would be good to understand that information loss occurs because sentences with different meanings have the same representation.

### punctation representation

Although it is similar to the above story, I classified it as a different category.

```python
"Don't you love 🤗 Transformers? We sure do."

["Don't", "you", "love", "🤗", "Transformers?", "We", "sure", "do."]
```

Tokens with sentences and space-based splits. Note that the "Transformers?" and "do." is. These two tokens are followed by a punctuation mark. As in the example of dog and dogs above, there are many combinations of punctuation words, which increases the vocabulary combination. This leads to a large size model. To solve this, a punctuation-based split can be applied.

```python
["Don", "'", "t", "you", "love", "🤗", "Transformers", "?", "We", "sure", "do", "."]
```

However, this method also has its drawbacks. The token "Don't" is tokenized as "Don" ,"'","t". "Don't" means "do not". Therefore, it is desirable to tokenize to "Do" , "n' t". This is where the case starts to get complicated. This is also why each model has its own tokenizer. If different rules (tokenizer) are applied to the same text, different representations (tokenized text) are derived as the result. 

PLM (Pretrained Language Model) does not perform well unless the rules used to tokenize the training data (Tokenizer) are used. 

### summary

Popular libraries of these rule-based tokenizers are [spaCy](https://spacy.io/) and [Moses](http://www.statmt.org/moses/?n=Development.GetStarted). A popular model is [Transformer XL](https://huggingface.co/docs/transformers/master/en/model_doc/transformerxl).The vocabulary size for this model is a whopping 267,735. Usually, big-size vocabulary increases time complexity and memory usage because it causes the size of the embedding layer to grow. Usually, monolingual models have a vocabulary size of less than 50000.





## Character-level

It is a tokenizer that tokenizes character by character. The advantage is that the vocabulary size is small. 

### small size vocabulary

![image](https://user-images.githubusercontent.com/50165842/144560709-91809e82-6e5d-4663-917d-51b7f9e09e37.png)

Out-of-vocabulary issues are relatively rare. ![image](https://user-images.githubusercontent.com/50165842/144560588-ef680f88-3c17-4fe2-8b9c-571c9343a28e.png)

In the example above, we classified malapropisms as UNKNOWN tokens, but the character-level tokenizer tokenizes those words.

### poor representation

There are also downsides to this. First of all, ***<u>it is very difficult to learn a meaningful representation of a single character</u>***. For example, it is easier to learn the representation of the word 'apple' than it is to learn the representation of the character 'a'. As another example, the assumption that 'l' holds a lot of information in the photo above may not be true for all situations. In the case of ideograms such as Chinese characters, the characters themselves often have many meanings. However, for phonetic characters such as Roman characters, Hangul, and English, each character itself does not contain many meanings.

### large size sequence

![image](https://user-images.githubusercontent.com/50165842/144560651-caa6d214-539c-432f-af95-47ab4bd65d0b.png)

Since many tokens are returned, the length of the sequence becomes longer. Most of the transformer series are truncated because the length of the sequence is limited, and the tokenized sentence cannot contain all the information of the original sentence.



## Subword Tokenizer

![image](https://user-images.githubusercontent.com/50165842/145212853-38d249f5-0910-4201-aaf1-7ac7c9ef4ad4.png)

In word-based, many vocabulary books, semantics are similar, and words with different punctations are duplicated a lot. Since it is a large-sized vocab, limiting the vocab size based on the frequency causes an out of vocabulary (OOV) problem. Also, words with the same meaning but different words cannot be included in the vocab, so words with similar meanings can be treated as OOV. At the character level, it is very difficult to learn a meaningful representation of a single character. And, since the size of the sequence is limited in the language model, the problem of truncation occurs. Subword is a tokenizing method that considers both advantages and disadvantages.

Meaningful words should not be split, and rare words should be split into semantic units.

![image](https://user-images.githubusercontent.com/50165842/145217029-0e0e132e-8686-4f79-824a-10a7008f75e9.png)

For example, the word dog is a frequently used word, so it should not be split, and dogs would have to be split into dog and s.

![image](https://user-images.githubusercontent.com/50165842/145213747-c37d16cd-b62b-4464-94d7-624b73125a2c.png)

In the subword method, when tokenizing the word tokenization, it will be tokenized with token and ization.

In other words, ***<u>the subword method helps to separate tokens related to syntactic (grammar) and tokens related to semantic (semantic).</u>***

![image](https://user-images.githubusercontent.com/50165842/145215784-4d4c34c6-1eb0-436a-8408-7c824b66ce05.png)





***<u>The Subword tokenizer identifies whether a word is a start token or an end token.</u>*** For example, Bert uses ## to distinguish between start and end tokens in words. Different tokenizers use different prefixes.

![image](https://user-images.githubusercontent.com/50165842/145217887-2de741ba-cf74-4152-8ad3-37c8834cb225.png)

Besides Bert, some PLMs use various subword tokenizers. Subword tokenizer reduces the size of vocab and specifies which word token it is by using prefix and suffix.



## BPE(Byte-pair-encoding)

BPE was first introduced in [Neural Machine Translation of Rare Words with Subword Units (Sennrich et al., 2015)](https://arxiv.org/abs/1508.07909).

GPT 2, Roberta used space-split tokens or interest, and XLM and Flaubert are models using more complex rule-based tokenizers (Moses). GPT uses Space. BPE goes through the following process:

1. I am using dictionary tokenization as a rule-based tokenizer such as word-based, sentence-based. Then, count the number (frequency) of each word.
2. A basic vocabulary with a sign (letter?) from these words.
3. Two symbols (2-gram) were selected and selected the most. (This is called a merge rule)
4. The desired number of horses (normal size) can be combined.

That is, the desired vocabulary size(number of merges) and base-vocab size become hyperparameters.

Let's take an example.

```python
("hug", 10), ("pug", 5), ("pun", 12), ("bun", 4), ("hugs", 5)
```

After pre-tokenization, the words are separated like this. Here, the base vocab will be "h","u" ,"p","g","n","s","b" .

```python
("h" "u" "g", 10), ("p" "u" "g", 5), ("p" "u" "n", 12), ("b" "u" "n", 4), ("h" "u" "g" "s", 5)
```

Here, the highest frequency of the two symbols becomes "u" + "g" (15+5). So, the first word the tokenizer adds (merge rule) will be "ug".

```python
("h" "ug", 10), ("p" "ug", 5), ("p" "u" "n", 12), ("b" "u" "n", 4), ("h" "ug" "s", 5)
```

The next two symbols with the highest frequency becomes "u" + "n" (16 times). Therefore, "un" is added to the vocabulary.Finally , the vocabs become `["b", "g", "h", "n", "p", "s", "u", "ug", "un", "hug"]` . So, for example, "bug" can be tokenized as "b" and "ug", but in the case of "mug", it will be tokenized as "UNK" and "ug". GPT uses the 478 base vocab size and 40,000 merge rules . 
These values are set as hyperparameters to have a total of 40478 vocabs.

### Byte-level Bpe

Putting all possible basic characters into a base vocab would be quite large. For example, all Unicode characters may be included in the base vocab. However, in GPT2, to have a better base vocab, bytes are used as the base vocab, and the size of the base vocab is 256. However, the base characters are included in the base vocab. According to the literature description, GPT2 can tokenize all text without <unk> symbols by applying a special rule to punctuation marks. GPT2 has a vocab size of 50257, which consists of 256 as the base vocab size, 1 as a special end token, and 50000 learned merge rules.

### wordpiece

### sentencepiece
