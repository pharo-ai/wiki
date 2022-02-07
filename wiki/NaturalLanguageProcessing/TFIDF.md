# Term Frequency - Inverse Document Frequency (TF-IDF)

Repository: https://github.com/pharo-ai/tf-idf

## How to use it

Here is a simple example of how you can train a TF-IDF model and use it to assign scores to words. You are given an array of sentences where each sentence is represented as an array of words:

```Smalltalk
sentences := #(
  (I am Sam)
  (Sam I am)
  (I 'don''t' like green eggs and ham)).
```

You can train a TF-IDF model on those sentences:

```Smalltalk
tfidf := AITermFrequencyInverseDocumentFrequency new.
tfidf trainOn: sentences.
```

Now you can use it to assign TF-IDF scores to words:

```Smalltalk
tfidf scoreOf: 'Sam' in: #(I am Sam). "0.4054651081081644"
tfidf scoreOf: 'I' in: #(I am Sam). "0.0"
tfidf scoreOf: 'green' in: #(I am green green ham). "2.1972245773362196"
```

`scoreOf:` is the multiplication between `tf` (term frequency) and `idf` (inverse document frequency).

```st
AITermFrequencyInverseDocumentFrequency >> scoreOf: aWord in: aDocument

	| tf idf |
	tf := self termFrequencyOf: aWord in: aDocument.
	idf := self inverseDocumentFrequencyOf: aWord.
	^ tf * idf
```

You can also encode any given text with a TF-IDF vector, which will contain a TF-IDF score for each word from the vocabulary of unique words extracted from sentences on which TF-IDF was trained:

```Smalltalk
tfidfVector := tfidf vectorFor: #(I am green green ham).
"#(0.0 0.0 0.4054651081081644 0.0 0.0 0.0 2.1972245773362196 1.0986122886681098 0.0)"
```

Those vectors can be used to find semantic similarities between different texts.

We access the vocabulary:

```st
vocabulary := tfidf vocabulary.
"#(#I #Sam #am #and 'don''t' #eggs #green #ham #like).
```

Map the vocaculary with the score of the word for one sentence:

```st
tfidfVector := tfidf vectorFor: #(I am green green ham).
vocabulary := tfidf vocabulary.

vocabulary
	with: tfidfVector
	collect: [ :word :scoreOfTheWord | word -> scoreOfTheWord ]

"{  #I->0.0.
	#Sam->0.0.
	#am->0.4054651081081644.
	#and->0.0. 'don''t'->0.0.
	#eggs->0.0. #green->2.1972245773362196.
	#ham->1.0986122886681098.
	#like->0.0 }"
```