---
title : "What is Attention?Why softmax in hidden layer output?"



excerpt: "Attention and Explicit Memory"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Attention,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

It is not an exaggeration to say that the natural language domain in deep learning has risen rapidly after the publication of Attention is all you need in 2017 and Bert in 2019. With the advent of the architecture called Transformer, fine-tuning like VISION is possible in the NLP field, and various downstream tasks can be performed. The architecture called Transformer is composed of multiple multihead attention, and multihead attention is composed of a module called attention. The Attention module has one major difference from other deeplearning modules. Even though it is a hidden layer, the softmax function is used as the activation function. The softmax function transforms the inputs into a probability space. Since the statistics-based model needs to calculate the probability, it was used to find the probability in the output layer. About this, I asked several communities, but no one knew about it. While I was giving up, I happened to be able to confirm that about 5-6pg was allocated and described in the [Deep Learning](https://www.deeplearningbook.org/)(by Ian GoodFellow) book. (If you don't know, look for the book!) .and ,[Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow: Concepts, Tools, and Techniques to Build Intelligent Systems: ](https://www.amazon.com/Hands-Machine-Learning-Scikit-Learn -TensorFlow/dp/1492032646)(by [Aurelien Geron](https://tensorflow.blog/tag/aurelien-geron/)) gave me a more detailed understanding of the Attention concept. I would like to discuss this. (Excluding formulas as much as possible.) (This article focuses on the Attention module rather than the Transformer Architecture, so the description of the transformer architecutre is intentionally omitted.)



## What is Attention ?

![image](https://user-images.githubusercontent.com/50165842/148772559-4c347b53-56e5-45d1-8cbe-5d1ebf479273.png)



To understand Attention Mechanism, it is necessary to know how the Encoder-Decoder Model (Seq2Seq Model) calculated the output before Attention. Before attention, the rnn series model calculated the output directly after linear transformation. On the left is the process of calculating the sequence output before attention. The example above is the process of translating the French sentence 'je bois du lait' from the English sentence 'milk drink I'. You can see that from `milk` to `lait` takes a very long time. Attention algorithm is an algorithm that solves how to make the context of the word `milk` short to `lait`. The attention algorithm is [1409.0473.pdf (arxiv.org)](https://arxiv.org/pdf/1409.0473.pdf) by[Dzmitry Bahdanau](https://arxiv.org/search/cs?searchtype=author&query= Bahdanau%2C+D), , [Kyunghyun Cho](https://arxiv.org/search/cs?searchtype=author&query=Cho%2C+K), [Yoshua Bengio](https://arxiv.org/search /cs?searchtype=author&query=Bengio%2C+Y). Algorithm introduced in . In the figure on the right, the algorithm they introduced is to create an architecuture of the model so that the decoder pays attention to a specific input word. It can be seen that the output of the hidden state $$h(i)$$ is calculated through the operation with the weight $$\alpha_{(t,i)}$$. weight $$\alpha_{(t,i)}$$ is the weight in the `t-th` decoder of the `i-th` encoder. For example, $$\alpha_{(3,2)}$$ will attract relatively more attention than $$\alpha_{(3,0)} , \alpha_{(3,0)} $$ no see. And, every `t-th`, the decoder will concatenate (additive) the encoder output and hidden state. Since Bahdanau Attention goes through the Time Distributed Layer, if the input sequence is n, the complexity of $$ O(n**2) $$ is calculated. If the output sequence is very long, quadratic order is critical, but it has little effect since the output is countable in thousands.

Now let's see how to calculate $$\alpha_{(t,i)}$$ . For example, when calculating $$\alpha_{(t,i)}$$ , the hidden state $$h(t-1)$$

and concatante. After that, $$e_{(t,i)}$$ is calculated through TimeDistributed (Dense Layer) (Time Distributed means parallel operation with a matrix). $$e_{(t,i)}$$ is a value indicating how aligned with the previous hidden state. ( When there are two sequences, it is convenient to think of the alignment score as a score indicating how continuous the two sequences are. [Alignment and Alignment Score (titech.ac.jp)](https://www.gsic.titech.ac .jp/supercon/supercon2004-e/alignmentE.html)) Then, a weighted sum is computed to give weight to which $$y_{(i)}$$ will receive more attention. At this time, the weighted sum is calculated using softmax. It is also called Bahdanau Attention or concatenate (additive) attention, after the main author Bandanau.

In addition to this, there is also Luong eAttention published in a paper at [[1508.04025\] Effective Approaches to Attention-based Neural Machine Translation (arxiv.org)](https://arxiv.org/abs/1508.04025). Since the purpose of Luong Attention was to find simliarity, similarity was calculated using dotproduct instead of TimeDistributed Layer (Dense). This is also called Luong Attention, or multiplicative attention. They calculate the simliarity score and calculate $$\alpha_{(t,i)}$$ through the softmax layer as in Bahdanau Attention. The peculiarity of these is that $$h(t)$$ is used instead of $$h(t-1)$$ for the decoder's hidden state. In addition to the dotproduct method, they proposed a general method that passed the TimeDistributed layer. And, they compared the performance of this method with the concatanate method, and the dotproduct, general method showed better performance than the concatanate method. Therefore, the concatanate method is not used well at present. Actually, Transformer also uses dotproduct method. These three things can be summarized in formulas as follows.
$$\tilde {h(t)} = \sum_{i=1} {y_i \alpha_{(i,t)}}$$
$$\alpha_{(i,t)} = {{exp{( e_{(i,t)}) }} \over {\sum_{i=1}{exp{ (e_{(i,t)} ) } }}}$$

$$e_{(i,t)} = \begin{cases} h_{(t)} y_{(i)} & \qquad dot \\ h_{(t)}W y_{(i)} & \qquad general \\ v^TW_a[h_{(t-1)};y_{(i)}] & \qquad concatanate \end{cases}$$




### Self-Attention



![image](https://user-images.githubusercontent.com/50165842/148861399-92bcde71-7df9-4ef0-bb30-8ed70f6f5eaa.png)



multiheadAttention is a module constituting the transformers module introduced in the AttenionisAllyouneed paper. Before defining .Attention, let's take an example of how Attention calculates a score. For example, {"Subject" : "Choi Woongjun" , "Verb" : "I ate rice." , "where" : "at home"} Let's assume we have such a dictionary. When the sentence 'Choi Woong-jun ate rice' appears, the information 'where' should appear next. To know this, we will look up the keyword 'where' in the dictionary, and from there we will get the value 'at home'. And, I will complete the sentence 'Choi Woong-jun ate at home'. Here, using a query that considers the key, value, and context of this dictioary is a self-attention module that achieves multihead attention.

However, it is difficult to use it to directly implement it. Because the inside of attention is a vector representation, it is not possible to select and use the key ‘where’ among the keys ‘subject’, ‘verb’, and ‘where’. If so, how should we utilize this vector representation? Here, it determines which `key` value to give more attention to by finding the similarity between `query` and `key` values, which are contextual information. To calculate the similarity, dot-product is used. Then, we use the softmax function to convert it to a weight with sum equal to 1. Using this, we get the value of . Here, the weight of the vector expressing the key ‘where’ will be close to 1, and the weights of the vectors expressing ‘subject’ and ‘verb’ will be close to 0 for the rest. When calculating the weighted sum for this, it will approximate the vector value representing 'at home', which is the value of 'where'. Expressing this as a formula is as follows.
$$Attention(Q,K,V) = {softmax({{QK^T} \over {\sqrt{d_{keys}}}}) } \cdot V$$

Let's comment on the attention formula in more detail.

- Q is a matrix with the shape of $$[n_{queries}, d_{keys}]$$.
- K is a matrix with the shape of $$[n_{keys} ,d_{keys}] $$ .
- V is a matrix with the shape of $$[n_{keys} , d{values}] $$.
- The shape of $$ QK^T $$ is $$[n_{queries} :n_{keys} ] $$ . And, the output of the softmax function also has the same shape. And, every row is in the form of a weighted sum with sum equal to 1. The shape of $$ QK^T$$ will be $$[n_{queries} :d_{values} ] $$ . You can think of each row as the result of one query.
- It is said that the scaling factor $$\sqrt{d_{keys}$$ prevents the softmax from growing so that it can be updated by calculating a small gradient value.
- Actually, when implementing MaskedMultiheadAttention, it uses a technique that adds a very large negative value to each self-attention in the decoder, which is quite useful.

And, in Transformers, there are encoder and decoder parts. Here, the encoder calculates from the input sentences with the same query, key, and value. Also, the decoder calculates the query, key, and value with the target sentence. However, in the case of the decoder, $$ P(y_{t}|y_{<t}) $$ has to be calculated, so as mentioned above, when calculating the softmax value using a very large negative value, the weight of the latter values is It makes it close to 0. And, the MultiheadAttention Layer comes out of the decoder, K and V use the vector representation (dictionary) created by the encoder, and Q uses the vector calculated from the maskedmulitheadattention.

### Multihead-attention

![image](https://user-images.githubusercontent.com/50165842/148869343-08d9b859-0a3d-4438-9015-4002213dfc61.png)

So, why use multiple attentions instead of one? For example, let me explain. ' Choi Woong-jun went to his cafe to study because he couldn't concentrate at home. There is a sentence called `. At this time, {"subject" : "Choi Woong-jun" , "verb" : "I went to study." , "where" : "in a cafe"} Such a dictionary will be created. You can get a value of 'in cafe' for a key value of 'where'. However, it is not possible to answer the key value of ‘why did you go to the cafe’ with the existing dictionary. A sentence contains not only this but also a lot of contextual information. A single attention will only contain information from one aspect. Therefore, we want to express various information of the sequence by projecting it to multiple vector spaces with multiple attentions.

## Why Attention Weighted Sum? -> Detailed

We haven't explained yet why Attention computes the weighted sum. It wasn't made because researchers just wanted to try it out when they made it. To understand this, we need to understand how the human brain remembers.

Human beings are ambiguous and in the realm of the unconscious, and have a very complex structure of memory. For example, ‘how a person walks’ and ‘how a person puts on a jacket’. These memories are the unconscious actions that we naturally do in our daily life. However, if you look deeply into these memories, they are ambiguous because they are very complex. It is called implicit memory because people do not remember it consciously. On the other hand, there are very distinct, explicit memories. For example, 'cola' and 'apple' have clear images. Also, 'you have to take the medicine at 8 in the morning' is an action that a person consciously remembers. Because a person can remember while consciously, it is called explicit memory. To catch only the concept, only approximate differences are mentioned.)

Neural networks are specialized in storing implicit memory. However, it was far from being able to store explicit memory. Stochastic Gradient Descent calculates the input as a feedforward, and it is updated after going through several layers, rather than being saved immediately. As these inputs go through the hidden layer and enter the input of another hidden layer, they are different from the original input, and when the gradient descent is calculated and updated later, the input information at that moment will not be updated. I tried to interpret the verse in my own way, but I don't know if it is correct. I interpreted the verse corresponding to 10.12 418pg in the original text.) . [Nueral Turing Machine](https://arxiv.org/pdf/1410.5401.pdf) argues that this is a problem caused by the absence of human-like working memory.

### Working Memory?

![A Blackboard Full of Mathematical Formulas. Educational Concept Background  Stock Image - Image of complicated, education: 174283309](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRurJWypZIrq1oBcsypUfLwEMJwk813BrjcsA&usqp=CAU)

Working Memory is a place where memories are temporarily stored when a person learns. When the teacher is teaching a class, he writes something on the blackboard and explains it to the students. And, over time, other content becomes less. The previous content will be erased by the teacher with an eraser. Even when people study, they remember what they studied at that moment, but as time passes, they forget it.

A person can use the working memory of that moment to decide what to do next. It decides at every moment what information to pull from working memory and what new information to write to working memory. Humans do not automatically react when input comes in, but have the ability to reason and act. Explicit Memory came out wondering if it was possible to give the neural net this ability.



### Memory Cell 



![image](https://user-images.githubusercontent.com/50165842/148873967-48e6136a-6c62-4bcd-9693-7a64bcd72568.png)



If you look at the picture, there is a Neural Net, and the Neural Net performs some task. At the same time, when an external input comes in, the controller (nueral net) writes to the memory, and when it needs to output an output, it reads the memory and makes an output. There is a part that Ian Goodfellow explains in the video, and I will explain this example as it is a good example. For example, I have decided to attend a weekly reading group at 8:30 am tomorrow. Now, in Ian's meorycell, the gating function (actviation function) is set to 1 in "Tomorrow 8:30 am reading group". Suddenly, at 9pm today, I get a call from my boss. "Ian, I have to attend the investment briefing tomorrow at 9 am" , "oh my god", and the gating function (actviation function) in the part corresponding to "Tomorrow 8:30 am reading group" of Ian's memory cell is close to 0, and the gating function (activation function) of the part corresponding to "tomorrow 9 am meeting" approaches to 1. In other words, writing is done as it enters the input from the memory cell, and depending on the input, the activation approaches 1 or 0 depending on the input. Memory is read when external output is needed. This task network 'learns' how to 'control' memory. I think softmax is used because it has to activate some memory and other memories can be deactivated depending on the other memory.

At first, such an attempt was made with an architecture called Memory Network. However, the memory network had to learn how to use this memory with supervised learning. Therefore, an architecture called neural turing machine has emerged to complement this. The neural turing machine was able to learn end-to-end without supervised signals for memory cells. This neural turing machine uses a content-based soft-attention mechainism. Since this soft-attention mechanism allows gradient descent optimization, it has become a standard architecuture since the 2015s.

This memory cell can be thought of as the cell state of the GRU and LSTM architecture expanded into a vector. GRU and LSTM are also vectors, but it may sound strange to say that they are extended to vectors. In cell state, it is a set of scalars, but a memory cell is a set of vectors. The difference between GRU, LSTM and memory cell is whether or not to decide what to read and write to the memory. This is the same as when a computer receives a memory address and accesses the memory. Then, why the hell did you use a larger capacity vector in the cell state?

### Why Memory Cell use vector?

Memory cells are referenced by the address location method, and in this case, it was difficult to accurately approximate the integer address for reading and writing with the existing method. Therefore, we tried to solve this problem by simultaneously reading and writing in the memory cell. At the same time, it reads all memory addresses and accesses the most likely address. That is, to read, a value was read from memory with a weighted average and a different value was written to each memory. It is a coefficient for performing operations on memory, allowing us to focus on a small number of memories. In this case, the softmax function was used. Through this , non-zero deriative, optimization is done through gradient descent. The gradient of the coefficient determines which weight to increase or decrease, but the row corresponding to the large value of the coefficient increases the gradient, so it is affected.

You may be wondering what the above has to do with the use of vectors in Memory Cells. The first reason to use a memory cell as a set of vectors is that we have already increased the computation cost to read simultaneously (weighted average). Already, the computation cost has increased to about $$O(N^2) $$, so it is said that this cost is offset by reading the vector. (This part was a little ambiguous. I spent some time thinking about it, but in a neural turing machine , NTM, it was a method to obtain a weighted average by reading the shape (N,M) at the same time without approximating a specific location. Previously, mentioned as a problem One thing I did was to specify the exact integer address. The part to specify precisely is sequential operation, and reading as a vector value seems to have offset the computational cost more because matrix multiplication is performed in parallel operation.) Second, content-based reading is to do it. Existing LSTM and GRU `cell state` creates documents matching a certain pattern from all document information. With sigmoid we just increase the weight if the value is high and decrease the weight if the value is low. However, since the memory network chooses which pattern to find information (coefficient , weighted average), it searches only a few documents to find information (key, value dictionary). For example, to find documents containing the word 'corona', only documents grouped with the keyword 'corona' will be searched. Conversely, location based does not refer to content . This can be viewed similarly to 'Bring the document in the 347th document box'.

If we analogize this to MultiheadAttention in Transformers, it is similar to how multiheadattetntion in Decoder computes $$softmax(QK^T)$$ . In the Transformer, the decoder also represents as a vector what information the decoder will have at time step t. In the Transformer, the corresponding memory cell can be seen as V, and the matrix to obtain a weighted average of this is $$softmax(QK^T)$ You can see it as $. It can be seen that Transformer also obtains a weighted average of V instead of finding the address of the most relevant content.

## Attention in optimization view

What is attention, why it uses softmax as the hidden layer output, why it computes a weighted sum, and why $$Attention(Q,K,V) = {softmax({{QK^T} \over { \sqrt{d_{keys}}}}) } In the form of \cdot V$$ , we checked whether QK and V were matrix multiplication. This time, we will look at what advantages there are over the existing LSTM and GRU in terms of gradient optimization. Unlike LSTM and GRU, Explicit memory performs relatively well in propagation and backpropagation. In seq2seq based on LSTM and GRU, the cell state passing through the hidden layer passes through many layers, so there is a high probability that the gradient will explode or vanish. However, since explicit memory applies an attention mechanism to calculate gradient for each output, it can learn using gradient descent, unlike GRU and LSTM seq2seq, which calculates the context directly instead of using a weighted average. (respectively long duration)





