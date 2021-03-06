
## [A Persona-Based Neural Conversation Model](https://arxiv.org/abs/1603.06155)


#### Key Points

- Dataset, model and trainning
  - Speaker Model: Twitter FireHouse (Persona) Dataset
  - Speaker-Addressee Model: Television Series Transcripts (American television comedies Friends and The Big Bang Theory) available from [Internet Movie Script Database (IMSDb)](https://www.imsdb.com/).
  - Seq2Seq
  - Beam Search
  - Reranking the generated N-best list using a scoring function that linearly combines a length penalty and the log likelihood of the source given the target [(Li et al. 2016)](/notes/a_diversity-promoting_objective_function_for_neural_conversation_models.md) to avoid generated generic and commonplace responses such as I don’t know.

- Define PERSONA:
  - Character that an artificial agent, as actor, plays or performs during conversational interactions.
  - Composite of elements of identity (background facts or user profile), language behavior, and interaction style.

#### Notes

- For handling the issue of speaker consistency in neural response generation.
  - Speaker Model:
    - A speaker model encodes personas in distributed embeddings that capture individual characteristics such as background information and speaking style.
    - Models the personality of the respondent.
    - Embedding, which encodes speaker-specific information (e.g., dialect, register, age, gender, personal information) that influences the content and style of her responses.
  - Speaker-Addressee Model:
    - A dyadic speaker-addressee model captures properties of interactions between two interlocutors. Models the way the respondent adapts their speech to a given addressee — a linguistic phenomenon known as lexical entrainment (Deutsch and Pechmann, 1982).
    - Training persona vectors directly from conversational data and relevant side-information, and incorporating these directly into the decoder.

![Speaker Model](/images/speaker_model.png)

_Figure 1: Illustrative example of the Speaker Model introduced in this work. Speaker IDs close in embedding space tend to
respond in the same manner. These speaker embeddings are learned jointly with word embeddings and all other parameters of
the neural model via backpropagation. In this example, say Rob is a speaker clustered with people who often mention England
in the training data, then the generation of the token ‘england’ at time t = 2 would be much more likely than that of ‘u.s.’. A
non-persona model would prefer generating in the u.s. if ‘u.s.’ is more represented in the training data across all speakers._

- **The model learns speaker representations based on conversational content produced by different speakers, and speakers producing similar responses tend to have similar embeddings, occupying nearby positions in the vector space.**

- The training data of speakers nearby in vector space help increase the generalization capability of the speaker model. Even if speaker j was never asked the same question, this answer can help influence a good response from speaker j, and this without explicitly labeled geo-location information.

- A domain adaption strategy where we first trained a standard Seq2Seq models using a much larger OpenSubtitles (OSDb) dataset. We run 10 iterations over the training set. We initialize word embeddings and LSTM parameters in the Speaker Model and the SpeakerAddressee model using parameters learned from OpenSubtitles datasets. User embeddings are randomly initialized from [−0.1, 0.1]. We then ran 5 additional epochs until the perplexity on the development set stabilized.

## Results

- We see that our system actually does better than [(Li et al. 2016)](/notes/a_diversity-promoting_objective_function_for_neural_conversation_models.md), and we attribute the improvement to a larger training corpus, the use of dropout during training, and possibly to the “conversationalist” nature of our corpus.

- We suspect that this is primarily due to the relatively small size of the dataset where the interactive patterns might not be fully captured

- Smaller values of perplexity are observed for the Television Series dataset than the Twitter dataset, the perplexity of which is over 40, presumably due to the noisier nature of Twitter dialogues.

- The model tends to generate specific responses for different people in response to the factual questions.

## Conclusions

- We have demonstrated that by encoding personas in distributed representations, we are able to capture personal characteristics such as speaking style and background information. In the Speaker-Addressee model, moreover, the evidence suggests that there is benefit in capturing dyadic interactions.

- Our ultimate goal is to be able to take the profile of an arbitrary individual whose identity is not known in advance, and generate conversations that accurately emulate that individual’s persona in terms of linguistic response behavior and other salient characteristics.
