---
layout: post
title: Introduction to Word Embeddings - Pt. 1
categories: NLP

---

                 
## What are they?

Word Embeddings as referred to as vector or tensor representations of text which can then be used by various Machine Learning Algorithms for a variety of tasks. Words and Documents in the form of vectors, sounds  pretty impossible? But then every task accomplished by Deep Learning in recent years had been considered impossible. Word Embeddings are not even recent anymore. [Word2vec](https://en.wikipedia.org/wiki/Word2vec), one of the primary algorithms in word embedding tasks, which we will focus on, was proposed almost 4 years ago. Since then, it has exploded, with a paper coming out everyday on using Word2vec for semantic classification tasks. It’s exceptionally useful and ingenious. So, why wait? Let’s get straight into it.


## The Algorithm

In it’s purest form, word2vec is as simple as it gets. We want to make sure our network understands the meaning of words. The computer needs numbers to determine closeness. Great. So, we represent words as vectors. You got this much from the word2vec name I assume. So, how’s this done?

For that we need to ask ourselves, how do we as humans find out the meaning of a word? If I tell you, “Please bear with me”, you would know that I am telling you to hold up and not referring to the animal “bear”. How did you get that? It’s *context*. The other words next to the word “bear” tell you what my “bear” actually means. So all language and words are is *context*. This is exactly what we need to impart to our model. Context is our building block.

The word2vec model would be a simple Neural Network, which learns the vector representations of the words using their context. We kick-start the network with randomly initialized word vectors. Then using word contexts, we make the network learn what words should be closer by and what should be further apart. For example, words like dogs and cats would occur in similar contexts, whereas dogs and gramophones would not be remotely related. For every group of words fed to it, the network checks how close by their vectors are. An update is performed to get these vectors close by if the words occur together. In the end, ideally the network is able to create a vector space, with a representation for every word in our corpus.
Now, this might seem like it doesn’t make much sense. But the vectors represent some abstract form of understanding the words. Every dimension in the vector represents some piece of key information that was derived from the context, it might not make sense to a person, but the network has managed to learn every aspect of the word it could from the surrounding words.

The picture above perfectly illustrates what I am trying to impart. If a 4 dimensional vector is representing King, Queen, Woman and Princess, the representations might be learned such that the first dimension represents their Royalty Score, the second Masculinity and so on. A human might not be able to decipher what the individual dimensions represent but the model has learn some way to discriminate and compare these words. 


## The Implementation

The complete code for the implementation can be found [here](https://github.com/pranaydeep-af)
So how does the code actually work out. Let’s dive in. The problem at hand being, train a neural network to represent words as vectors using the context. There are two types of algorithms, namely CBOW and Skip-Gram, involved in word2vec, both of these are fairly popular and have their advantages. We will be focusing on using CBOW for our testing. Let’s take an example where we explore how this works. Our corpus (just a fancy word for a collection of sentences or documents) for the example will only consist of 4 sentences.


<div class="message">
Sentence-1 : "The wing of the plane was damaged and the engine had some issues but the pilot landed the plane successfully."

Sentence-2 : "A pilot drives a plane, but a driver drives a car."

Sentence-3 : "Planes are useful for air transport, while cars are useful for land transport."

Sentence-4 : "The driver steered the car through a steep turn"
</div>

Let’s see how we can make  a model based on the CBOW algorithm using these sentences:

###Continuous Bag-of-words (CBOW)

CBOW basically says, that the context decides the word. We use a window of say n-words. When we look at a word, we take n words from before the said word and n words from after the said word, and try to predict which word would come between these words. Too confusing? Let’s explore more with our example. Let’s say my model is looking at Sentence-2 and the word in question, is plane. If I have a context-window of 2, the model takes 2 words before plane, ie. drives and a, and 2 words after plane ie. but and a. These 4 words are the input to the model and the word plane is the label. In reality we will remove words like a and the because they are not contextually relevant. This is called stop words removal.


Let’s make a proper model using CBOW and our sample corpus. First we import all our modules.

{% highlight python%}
In[0]: import keras.backend as K
  ...: from keras.models import Sequential
  ...: from keras.layers import Dense, Embedding, Lambda
  ...: from keras.utils.data_utils import get_file
  ...: from keras.utils import np_utils
  ...: from keras.preprocessing import sequence
  ...: from keras.preprocessing.text import Tokenizer
{% endhighlight %}


Next, we define our text. Tokenize it ie. separate the sentences into individual words. Also, since the model cannot compute using words, we will assign a unique number to each word. 

{% highlight python%}
In [1]: texts = ['The wing of the plane was damaged and the engine had some issues but the pilot landed the plane successfully','A pilot drives a plane, but a driver drives a car','Planes are useful for air transport, while cars are useful for land transport','The driver steered the car through a steep turn']

In [2]: tokenizer = Tokenizer()
   ...: tokenizer.fit_on_texts(texts)
   ...: corpus = tokenizer.texts_to_sequences(texts)
{% endhighlight %}


Let’s see how our corpus looks now:

{% highlight python%}
In [3]: corpus
Out[3]:
[[1, 13, 14, 1, 3, 15, 16, 17, 1, 18, 19, 20, 21, 4, 1, 5, 22, 1, 3, 23],
 [2, 5, 6, 2, 3, 4, 2, 7, 6, 2, 8],
 [24, 9, 10, 11, 25, 12, 26, 27, 9, 10, 11, 28, 12],
 [1, 7, 29, 1, 8, 30, 2, 31, 32]]
{% endhighlight %}



Great, let’s prepare our training data now. For the CBOW model, our input should be n words for either direction and our output would be the middle word. So let’s define our hyper-parameters.

{% highlight python%}
In [4]: nb_samples = sum(len(s) for s in corpus)
   ...: V = len(tokenizer.word_index) + 1
   ...: dim = 30 #No of dimensions each word vector should have
   ...: win_size = 5 #No of words in either direction
{% endhighlight %}

Next, Let’s define a function to convert our current corpus into CBOW input form using our window size.

{% highlight python%}
In [5]: def generate_data(corpus, window_size, V):
    ...:     maxlen = window_size*2
    ...:     for words in corpus:
    ...:         contexts = []
    ...:         labels   = []
    ...:         L = len(words)
    ...:         for index, word in enumerate(words):
    ...:             s = index-window_size
    ...:             e = index+window_size+1
    ...:             
    ...:             contexts.append([words[i] for i in range(s, e) if 0 <= i < L and i != index])
    ...:             labels.append(word)
    ...:
    ...:             x = sequence.pad_sequences(contexts, maxlen=maxlen)
    ...:             y = np_utils.to_categorical(labels, V)
    ...:
    ...:             yield (x, y)
    ...:             
{% endhighlight %}

Almost done, we define our model now.

{% highlight python%}
In [6]: cbow = Sequential()
    ...: cbow.add(Embedding(input_dim=V,output_dim=dim, input_length=win_size*2))
    ...: cbow.add(Lambda(lambda x: K.mean(x, axis=1),output_shape=(dim,)))
    ...: cbow.add(Dense(V, activation='softmax'))
    ...:
In [7]: cbow.compile(loss='categorical_crossentropy', optimizer='adadelta')
{% endhighlight %}

The model is simple. We input a vector of 2 times window length, which in the case is 10. Process it using a dense layer and output a V-length (which is the length of our corpus) one-hot encoded vector indicating the output word. Let’s train the model for a hundred epochs. 

{% highlight python%}
In [8]: for ite in range(100):
    ...:     loss = 0.
    ...:     for x, y in generate_data(corpus, window_size, V):
    ...:         loss += cbow.train_on_batch(x, y)
    ...:     if ite%20==0:
    ...:         print(ite, loss)
    ...:     
    ...:     
(0, 84.92460155487061)
(20, 47.73322546482086)
(40, 18.56218731403351)
(60, 4.56478923559189)
(80, 0.5073482573032379)
{% endhighlight %}

Great, our model has been successfully trained. Are the vectors any good? We can find out. Let’s process the vectors using gensim and see which words the model thinks are closer together.  


{% highlight python%}
In [9]: f = open('vectors.txt' ,'w')
    ...: f.write(' '.join([str(V-1), str(dim)]))
    ...: f.write('\n')
    ...: vectors = cbow.get_weights()[0]
    ...: for word, i in tokenizer.word_index.items():
    ...:     f.write(word)
    ...:     f.write(' ')
    ...:     f.write(' '.join(map(str, list(vectors[i, :]))))
    ...:     f.write('\n')
    ...: f.close()
    ...: w2v = gensim.models.KeyedVectors.load_word2vec_format('./vectors.txt', binary=False)
    ...:

In [10]: w2v.most_similar('driver')
Out[10]:
[ (u'car', 0.6554617881774902),
 (u'drives', 0.6384860277175903),
 (u'steered', 0.5519140958786011),
 (u'turn', 0.5362613201141357),
  (u'pilot', 0.4439489245414734)]

In [11]: w2v.most_similar('pilot')
Out[11]:
[ (u'landed', 0.6166680455207825),
  (u'engine', 0.4601679742336273),
  (u'issues', 0.4028368592262268),
  (u'plane', 0.25351497530937195),
  (u'driver', 0.1687805950641632) ]
{% endhighlight %}

There we go. Our model with so little training data, has learnt that a pilot is more related to engine and plane, and a driver is more related to car and steer.

## How’s this Useful?

Well, there are hundreds of ways I can think of. Let’s start with the obvious and basic ones. Document Classification. This is as simple as it gets. My model as described in the above section has learned to understand what words are alike and what are not. Great. Say, I have a huge bunch of Sci-Fi Novels and a huge bunch of Self-Help books. When trained on these books, my network recognizes that words like, galaxy, supernova, spaceship all occur in similar contexts while words like motivation, determination occur in similar contexts. Now using this model, if you were to convert all your books to vectors, you would find that using a simple clustering algorithm you can sort these books out into Sci-Fi and Self-Help without having any prior knowledge or labeled training data whatsoever. Pretty good, huh? This is just scratching the surface.