# Multilingual Unsupervised and Supervised Embeddings (MUSE)

MUSE is a pre-trained model for machine translation with no parallel data.

## Model

The model is composed of an encoder and a decoder. Given a sequence of $m$ words in a particular language, the encoder computes a sequence of $m$ hidden states via word embeddings. The decoder takes the latent representation of the word sequence, and produces a new word sequence in the target language. For that, the decoder iteratively takes as input the previously generated word, in an auto-regressive manner, updates its internal state, and returns the word that has the highest probability of being the next one. This process is repeated until the decoder generates a stop symbol.

In MUSE, the encoder is a bidirectional-LSTM and the encoder is an LSTM.

## Method

Consider a dataset of sentences in the source domain, $\mathcal{D}\_{src}$, and a dataset of sentences in the target domain, $\mathcal{D}_{tgt}$. These datasets do not have to correspond to each other. The encoder and decoder are trained by reconstructing a sentence in one domain, given a noisy version of the same sentence in the same or in the other domain.

### Denoising Auto-Encoding

Training an auto-encoder naively makes the model simply output copies of the input, without learning the language structure. To prevent this, the authors add noise to the input sequences before being fed to the model, which means the model can no longer simply output the received sequence. Two kinds of noise were considered. First, each word in the sentence is dropped with probability $p_{wd}$. Then, the input sequence is shuffled, by applying a random permutation $\sigma$ to the input sequence.

### Cross-Domain Training

The model should be constrained to map an input sequence from a source/target domain $l_1$ to the target/source domain $l_2$, as that is the desired task at test time. The principle here is to sample a sentence $x\in\mathcal{D}_{l_1}$, and to generate a corrupt translation of this sentence in $l_2$. The corrupt translation is obtained by applying the current translation model. Then this version receives the noise strategy described above, and the goal is to reconstruct the original sentence in $l_1$, from the noisy sentence in $l_2$.

### Adversarial Training

The authors would like the model to output features in the same space regardless of the language of the input sequence. For that, they introduced a discriminator network, tasked with classifying between the encodings of source sentences and the encodings of target sentences. The discriminator operates on the output of the encoder and produces a binary prediction about the language of the encoder input sequence. The encoder is trained to fool the discriminator, thus aligning the representations between source and target language encodings.

## References

- [MUSE: Multilingual Unsupervised and Supervised Embeddings](https://github.com/facebookresearch/MUSE)