These are revision-style notes written as a continuous explanation rather than bullet-point facts. They are designed for understanding first and memorization second.

---

# Semantic Search, Embeddings, Transformers, and Vector Databases

_Based on the Semantic Book Recommender section of the LLM Course_

---

## Introduction: Why Do We Need Embeddings?

When building a semantic book recommender, the first challenge is that computers do not understand language the way humans do. A human can easily recognize that "Roman Empire" and "Ancient Rome" refer to related concepts, but a computer only sees text as a sequence of characters.

To enable a computer to understand meaning, we must convert text into numbers. These numerical representations are called **embeddings**. Embeddings capture the meaning of words, sentences, or documents in a mathematical form so that they can be compared with one another.

The entire recommender system is built upon the idea that texts with similar meanings should have similar numerical representations.

---

# Word Embeddings

The simplest form of embeddings are **word embeddings**.

Imagine placing words such as _queen_, _king_, _woman_, _man_, _girl_, _boy_, and _tree_ in a three-dimensional space. Words with similar meanings would naturally be placed close together, while unrelated words would be placed farther apart.

For example, _queen_ and _king_ would appear near each other because both are associated with royalty. Similarly, _woman_ and _man_ would be close together, and _girl_ and _boy_ would form another nearby pair. In contrast, _tree_ would be located far away because it belongs to a completely different semantic category.

The coordinates assigned to each word in this space are called its **word embedding**. The embedding serves as a compressed mathematical representation of the word's meaning.

In reality, embeddings are not limited to three dimensions. Modern models often use hundreds or even thousands of dimensions. The purpose of these dimensions is not to have an easily interpretable meaning but rather to organize words such that similar words are close together and dissimilar words are far apart.

---

# How Word2Vec Learns Embeddings

One of the earliest successful methods for learning word embeddings was **Word2Vec**.

The tutor specifically discusses the **Skip-Gram architecture**. The central idea behind Skip-Gram is very simple: given a word, predict the words that appear around it.

Consider the phrase:

_"the best things"_

The model is given the word _best_ and asked to predict the surrounding words. In this case, it should predict _the_ before it and _things_ after it.

Initially, the model makes poor predictions because it has not learned anything yet. However, after processing millions of examples, it gradually adjusts its internal weights to become more accurate.

Over time, the model learns that words appearing in similar contexts tend to have similar meanings. For instance, words such as _best_, _worst_, and _funniest_ may occur in similar sentence structures. Because they are used in similar contexts, their embeddings become similar.

This idea can be summarized by one of the most important principles in Natural Language Processing:

> Words that appear in similar contexts tend to have similar meanings.

By repeatedly predicting neighboring words, Word2Vec automatically learns meaningful representations of words without requiring explicit human labeling.

---

# The Limitation of Traditional Word Embeddings

Although Word2Vec was revolutionary, it suffers from an important limitation.

Each word receives only one embedding regardless of context.

Consider the word **bank**.

In the sentence:

_"He checked his bank account."_

the word _bank_ refers to a financial institution.

However, in the sentence:

_"The river bank was peaceful."_

the word _bank_ refers to the side of a river.

Humans immediately understand the difference because we use surrounding words as context. Traditional word embedding models cannot do this effectively because they assign the same vector representation to every occurrence of the word.

This inability to understand context led to the development of Transformer-based models.

---

# Transformers and Contextual Understanding

Modern language models such as ChatGPT, Claude, Gemini, Mistral, and Falcon are all based on the **Transformer architecture**.

Unlike Word2Vec, Transformers do not treat words independently. Instead, they analyze each word in relation to all other words in the sentence.

The first step is similar to Word2Vec: each word is converted into an embedding. However, Transformers also add **positional information** so that the model knows the order in which words appear.

This is important because the meaning of a sentence depends not only on the words present but also on their arrangement.

After adding positional information, the embeddings are processed using a mechanism called **self-attention**.

---

# Self-Attention: The Core Innovation

Self-attention is arguably the most important concept in modern NLP.

The purpose of self-attention is to determine which words are important for understanding the meaning of another word.

Consider the sentence:

_"He checked his bank account."_

To understand the meaning of _bank_, the model should pay attention to words such as _checked_ and _account_. These words strongly suggest that _bank_ refers to a financial institution.

Words like _he_ or _his_ provide much less useful information.

The self-attention mechanism learns these relationships automatically by assigning higher attention scores to important words and lower scores to less relevant ones.

Now consider a different sentence:

_"The river bank was peaceful."_

In this case, the model focuses on _river_ and _peaceful_. As a result, it correctly interprets _bank_ as a geographical feature rather than a financial institution.

This ability to dynamically adjust meaning based on context is what makes Transformers so powerful.

---

# Encoder and Decoder Components

The original Transformer architecture contains two major components:

1. Encoder
2. Decoder

The encoder is responsible for understanding the input sequence. It learns how words relate to one another and creates a rich representation of the sentence.

The decoder uses this representation to generate an output sequence.

For example, in machine translation, the encoder processes an English sentence while the decoder generates the equivalent sentence in German.

A useful way to remember this distinction is:

- Encoder = Understand
- Decoder = Generate

---

# Types of Transformer Models

Over time, different variations of the Transformer architecture have emerged.

**Encoder-Decoder Models** are commonly used for translation and other sequence-to-sequence tasks.

**Decoder-Only Models** focus on text generation. Models such as GPT, Claude, and Gemini belong to this category.

**Encoder-Only Models** specialize in understanding text rather than generating it. These models are particularly useful for tasks such as classification, retrieval, and semantic search.

The semantic recommender project primarily relies on encoder models.

---

# RoBERTa and Masked Language Modeling

One popular encoder model is **RoBERTa**.

RoBERTa is trained using a technique called **Masked Language Modeling**.

During training, certain words are hidden from a sentence and the model must predict the missing words.

For example:

_"I grew up in Paris, so I speak [MASK] and English."_

The model should predict _French_.

To solve this task, the model must understand grammar, context, and relationships between words. After processing enormous amounts of text, it develops a sophisticated understanding of language.

The original RoBERTa model was trained on approximately 160 GB of text, allowing it to build rich internal representations of language.

---

# From Word Embeddings to Document Embeddings

Word embeddings represent individual words. However, a book recommender needs to compare entire book descriptions.

This leads to the concept of **document embeddings**.

A document embedding is a vector representation of an entire piece of text, such as a sentence, paragraph, article, or book description.

When a book description is passed through an encoder model, the model produces a document embedding that captures the overall meaning of that description.

Descriptions discussing romance, history, psychology, or crime will occupy different regions of the embedding space because their meanings differ.

---

# Vector Databases

Once document embeddings have been generated, they must be stored somewhere.

This is the role of a **vector database**.

A vector database stores:

- The embedding vector
- An identifier
- Additional metadata

For example, a vector may be associated with a book title, author name, genre, or summary.

The vector itself is not useful to humans. The associated metadata allows us to retrieve meaningful information once a match has been found.

---

# Semantic Search Using Embeddings

Suppose a user searches for:

_"Books about the Roman Empire"_

The query is passed through the same encoder model used to create the book embeddings.

This produces a query embedding.

The system then compares the query embedding with all book embeddings stored in the vector database.

If a particular book description discussing Roman history lies close to the query vector in embedding space, it will receive a high similarity score and be returned as a recommendation.

This approach is called **semantic search** because it searches by meaning rather than by exact keyword matching.

---

# Cosine Similarity

To determine how close two vectors are, we use a similarity metric.

The tutor mentions **Cosine Similarity**, which is one of the most widely used metrics for comparing embeddings.

Cosine similarity measures the angle between two vectors.

If two vectors point in nearly the same direction, the similarity score approaches 1, indicating strong semantic similarity.

If the vectors are unrelated, the score approaches 0.

If they point in opposite directions, the score approaches -1.

By ranking vectors according to cosine similarity, we can identify the books whose meanings most closely match the user's query.

---

# Why Vector Databases Are Necessary

A naive implementation would compare the query vector against every vector in the database.

If there are one million books, one million comparisons must be performed for every search.

This is inefficient because the search time grows linearly with the size of the database.

Vector databases solve this problem by creating specialized indexes that group similar vectors together. During search, only the most promising regions of the embedding space are explored.

This dramatically reduces search time while maintaining high accuracy.

Most vector databases use approximate nearest-neighbor algorithms, which trade a small amount of accuracy for significant gains in speed.

---

# The Complete Semantic Recommender Pipeline

The semantic book recommender follows a straightforward pipeline.

Book descriptions are first passed through an encoder model to generate document embeddings. These embeddings are then stored inside a vector database.

When a user submits a query, the same encoder model converts the query into an embedding. The vector database compares this query embedding with stored book embeddings using a similarity metric such as cosine similarity.

The most similar embeddings are retrieved, and their associated metadata is used to present book recommendations to the user.

Thus, the system recommends books based on meaning rather than exact keyword matches, which is the essence of semantic search.

---

## One-Page Revision Summary

**Word Embeddings** represent the meaning of individual words as vectors.

**Word2Vec** learns embeddings by predicting surrounding words and relies on the principle that words appearing in similar contexts have similar meanings.

**Traditional word embeddings fail to capture context**, meaning a word like _bank_ receives the same representation regardless of its meaning.

**Transformers solve this problem using self-attention**, allowing each word to consider surrounding words when determining its meaning.

**Encoder models** such as RoBERTa are trained to understand language through masked language modeling.

**Document embeddings** extend the idea of embeddings from individual words to entire sentences, paragraphs, or book descriptions.

**Vector databases** store document embeddings and enable efficient similarity search.

**Cosine similarity** measures how close two embeddings are in vector space.

**Semantic search** works by comparing the embedding of a user query with the embeddings of stored documents and returning the most similar results.

This is the theoretical foundation behind modern recommendation systems, retrieval systems, RAG applications, and AI-powered search engines.
