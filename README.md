# [[aiArt](https://github.com/parkeraddison?utf8=%E2%9C%93&tab=repositories&q=aiArt)] Verbosified Space Rovers
### *Or: Convincing GPT-2 that more is more*

<p align="center">
<img src="./images/example.gif">
</p>

### Website: [lost-in-deep.space](https://lost-in-deep.space)

### Table of Contents:
[Abstract](#abstract)  
[Background](#background)  
[Approach](#approach--implementation)  
[Results](#results)  
[Code](#code)  
[Technical exploration](#technical-exploration)  
[References](#references)  
[Acknowledgements](#acknowledgements)  

### **Abstract:**

In early 2019, OpenAI released a language model called GPT-2 which was widely [sensationalized](https://www.independent.co.uk/life-style/gadgets-and-tech/news/ai-artificial-intelligence-dangerous-text-gpt2-elon-musk-a9192121.html) as being "[too dangerous to release](https://www.theguardian.com/technology/2019/feb/14/elon-musk-backed-ai-writes-convincing-news-fiction)" due to its capability to generate bulk amounts of believable text.  It's precisely that model which I chose to use as the main tool for this project.   [This art project](https://lost-in-deep.space): accomplishes what a word-count-seeking writer could only dream of; is a statement to the needlessly verbose writing style I often find myself embodying; and showcases that the 'scariness' of GPT-2 depends largely on how the model is configured.

In this art piece, I manipulated the GPT-2 model to double the word count of an essay without significantly altering the meaning of the essay.  I also display results which—despite my constraints—still manage to demonstrate the model's frightening creativity.

The final piece was produced by exploring different parameters of the model, and by purposefully misusing finetuning in order to achieve a careful balance right on the edge of overfitting.

<p align="center">
<img src="./images/example.png" width=512px alt="Example from Verbosified Space Rovers">
</p>

## Background:

The GPT-2 model released by [OpenAI](https://openai.com/), as seen in the original blog post, [*"Better Language Models and Their Implications"*](https://openai.com/blog/better-language-models/) [[1]], is a general purpose [unsupervised](https://machinelearningmastery.com/supervised-and-unsupervised-machine-learning-algorithms/)<sup>[1]</sup> language model capable of text summarization, translation, question answering, and—of greatest interest for this art piece—*conditional synthetic text generation*.  Breaking down that last term, this means the model can generate text outputs which strongly depend on a given input.  An example from the original blot post shows an input being a couple sentences which seem to begin a news story, and the GPT-2 model generates text which continues the story.

<p align="center">
<img src="./images/blog-example.png" width=512px alt="Example from "Better Language Models and Their Implications">
</p>

When the model was released, it prompted both hype and fright in the media, with many articles touting it "too dangerous" to release to the public [[2]] (though a look at the blog reveals this word isn't used at all).  This is largely due to its ability to quickly generate bulk amounts of text which sound *believable*, as if it could have been written by a human.  *Talk about a whole new level of fake news!*

The GPT-2 model has a handful of advantages over other text generation models including the ability to maintain context for a longer period of time.  One of the unique advantages of GPT-2 is its dataset: whereas most other models use a fairly homogeneous dataset such as a collection of books or a corpus of Wikipedia articles, GPT-2 was trained on a diverse scrape of weblinks culminating in a 40GB corpus scraped from roughly 8 million unique documents [[1]].  This means that GPT-2 possesses a rather large lexicon, diverse writing styles, and a broad range of general and domain-specific knowledge.

Cutting through the media hype, a quick look at the text generated by GPT-2 reveals plenty of quirks.  While the text may sound *believable*, it still often sounds *unrealistic*.  This is often due to topic divergence and the introduction of (albeit rather creative) new concepts, characters, and sometimes new laws of physics.  If only there were a way to tap into the model's incredible knowledge, while enforcing constraints to prevent it from overextending itself… (*hint hint*)

## Approach / Implementation:

In order to fluff up an essay, we want to alter GPT-2 to allow us to insert a generated sentence between (almost) every human-written sentence…

### Motivation

As noted above, the non-finetuned GPT-2 model has a habit of introducing new topics if it doesn’t have adequate knowledge of the subject included in the prompt.  Here are some thoughts on how to address that, before we dive into this project's approach:

An easy approach to address topic divergence would be to alter two generation parameters.  One could lower the *temperature* generation parameter, which essentially controls how random the output is allowed to be.  And one could place a limit on the *top_k* parameter, which dictates that only the top *k* most probable words can be chosen from during each token generation and thus partially controls how relevant the words are required to be.

However, the model often pulls a switcheroo and still allows itself to diverge by starting the generated text with a reversal of the previous statement!  E.g. the generated text will start with *"But actually, rover's can't transfer huge amounts of scientific data because… \<initiate topic divergence>"*.

### Approach
In order to avoid topic divergence [finetuning](https://flyyufelix.github.io/2016/10/03/fine-tuning-in-keras-part1.html)<sup>[2]</sup> is used.  We want the model to generate sentences related to only one topic, so we try to get the model to think that only that one topic exists!  At the same time we don't want it to lose it's knowledge of the world to the point that the model no longer understands general concepts.  We want to strike a **balance right on the edge of [overfitting](https://en.wikipedia.org/wiki/Overfitting)**<sup>[3]</sup>.

Finetuning GPT-2 is usually run on large corpora, such as the entire works of Shakespeare, but this projects takes a different approach.  Our model's input will be a few sentences of a human-written essay, and we desire the output to be *extremely similar* to the input.  As such, **finetuning is actually performed on the input itself**!

Since this is such a small amount of data being finetuned on, the model overfits very quickly—eventually exactly replicating the input as its output.  Thus, **only a single finetuning step is run**, whereas normally finetuning would require further iterations.

<p align="center">…</p>

The final implementation of the model combines all of the aforementioned approaches:

To develop the final model, a collection of "passages" is created.  Each passage consists of three consecutive sentences which are all part of the same paragraph.  Then, for each passage, GPT-2 was initialized and a single iteration of finetuning was performed on the passage.  Finally, a handful of outputs were generated—with the passage as the input—with a low *top_k* and relatively high *temperature*.  Once everything's put together, this model is effectively constrained to output only words that are likely to pertain to the input topic, but still has boosted randomness in order to avoid repetition and/or replication of the input.  The best looking outputs were manually selected and truncated, then inserted into the essay.

## Results:

Results can be seen [at the website](https://lost-in-deep.space).

## Code:

For the code to run, Tensorflow 1.13, 1.14, or 1.15 must be installed in the Python environment.

Working code can be seen (or downloaded and run) in the notebook [`code/verbosifier-code.ipynb`](https://github.com/parkeraddison/aiArt-verbosified-space-rovers/blob/master/code/verbosifier-code.ipynb)

This notebook does the following steps:
1. Installs [`gpt-2-simple`](https://github.com/minimaxir/gpt-2-simple) and downloads the GPT-2 model
2. Creates *k*-sentence passages from the essay text file (default *k*=3)
3. Finetunes the model on the *i*th passage for one iteration (default *i*=0)
4. Generates text using the *i*th passage as input (default *i*=0)
   - Generation parameters (e.g. *length*, *temperature*, *top_k*) are discussed [here](https://github.com/minimaxir/gpt-2-simple#differences-between-gpt-2-simple-and-other-text-generation-utilities)

**Technical notes:**

- The notebook must be restarted when moving to a new passage
- Note that each model is about 500MB... and that to verbosify a piece of writing the way described above you'll need to train almost as many checkpoints as there are sentences... so make sure to delete these checkpoints once you're done generating for a given passage
- The [gpt-2-simple](https://github.com/minimaxir/gpt-2-simple) package has a hardcoded training data sample size of 1024 tokens. We want to finetune on a single passage... which is no where near that many words. So, I simply duplicated the passage until its file reached or exceeded 1024 words. Is this the best way to accomplish this? Maybe not, but I had a hard time editing the source code without causing errors, so we honestly can't say whether my method was better or worse than adjusting the training data sample size!

## Technical exploration:

Different methods of finetuning were attempted with the following findings:

1. Finetuning on a large corpus of academic writing, magazine writing, and newspaper writing (medium amount of epochs, ~500)
   * The model had a strong desire to talk about topics mentioned in this corpus—did not stay on topic to the prompt
2. Finetuning on the essay that I wanted to verbosify (small amount of epochs, ~1–5)
   * The model generally stayed on topic, but the specific subject discussed still had decent variability
3. Finetuning on (1) followed by (2)
   * The model would generally stay on topic, but would often get derailed by topics from (1)
4. Finetuning on the prompt that I wanted to verbosity (smallest amount of epochs, only 1)
   * The model would generally output very similar information to that contained by the prompt, and would occasionally rely on general knowledge to complete an idea. I ended up solely using this

Different generation parameters were tried with the following findings:

1. Temperature - affects variability of output
   * Low temperatures (0.1–0.4) resulted in low variability (duh) and high likelihood of the output getting caught in a cycle. If the text was off-subject, setting a low temperature did not fix the issue, and instead only cemented the output as slightly off-subject
   * Medium temperatures (0.4–0.7) resulted in moderate variability and low likelihood of the output getting caught in a cycle. This often lead to the output repeating the prompt if the model didn't know much about the subject being discussed
   * High temperatures (0.7–1.0) resulted in good variability. I used primarily a temperature of 0.8, as this allowed for the model to reference its general knowledge about the subject being discussed. If many samples were returning blank or returning a limited output, then bumping the temperature up allowed the model to attempt to bring up a different subject
2. Top-k - allows the model to choose from only the top-k tokens during a generation step
   * Default (0) was horrible since it actually removes all restrictions and allows sampling from all tokens. This meant that the model went off topic very easily
   * Recommended (40) was generally good, since it restricted the model to generating words that were seen in or similar to those seen in the prompt (since I also finetuned on the prompt)
   * Lower (30) was what I used for most of my generations, since it further restricted the model to using words similar to those in the prompt. Lower top-k forces the model to stay on subject, since it isn't allowed to mention unrelated words. A low top-k can be used with a high temperature to very effectively repeat the finetuning data/prompt while still attempting to avoid replication
   * Higher (80) was what I used if many samples were returning blank or the model was having a hard time avoiding replication of the prompt. Doing this returned a bit of creative freedom to the model and allowed it to rely on general knowledge to continue the prompt
3. Length - determines how many tokens are generated
   * I generated 100 tokens for each sample, since putting too small a size often cut off sentences that seemed to have good potential, and too large a size goes off topic or enters a cycle


## References:
[1]: #references
[2]: #references
[3]: #references

### Reader References
1. See this article for the difference between Supervised, Unsupervised, and Semi-supervised learning:  
https://machinelearningmastery.com/supervised-and-unsupervised-machine-learning-algorithms/

2. See the first two sections of this article for an explanation of Finetuning:  
https://flyyufelix.github.io/2016/10/03/fine-tuning-in-keras-part1.html

3. See this article for an explanation of Overfitting:  
https://en.wikipedia.org/wiki/Overfitting

### Project References

1. Radford, Alec and Wu, Jeff and Child, Rewon and Luan, David and Amodei, Dario and Sutskever, Ilya. *"Language Models are Unsupervised Multitask Learners"* and *"Better Language Models and Their Implications"* and *GPT-2*. February 2019.

    Paper:  
https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf

    Blog:  
https://openai.com/blog/better-language-models/

    Code:  
https://github.com/openai/gpt-2

2. Various GPT-2 Articles.  Some are better than others...

   Alex Hern. *"New AI fake text generator may be too dangerous to release, say creators"*. February 2019.  
   https://www.theguardian.com/technology/2019/feb/14/elon-musk-backed-ai-writes-convincing-news-fiction

   Jasper Hamill. *"Elon Musk-founded OpenAI builds artificial intelligence so powerful it must be kept locked up for the good of humanity"*.  February 2019.  
   https://metro.co.uk/2019/02/15/elon-musks-openai-builds-artificial-intelligence-powerful-must-kept-locked-good-humanity-8634379/

   Aaron Mak. *"When Is Technology Too Dangerous to Release to the Public?"*. February 2019.  
   https://slate.com/technology/2019/02/openai-gpt2-text-generating-algorithm-ai-dangerous.html

   Andrew Griffin. *"AI deemed ‘too dangerous to release’ makes it out into the world"*. November 2019.  
   https://www.independent.co.uk/life-style/gadgets-and-tech/news/ai-artificial-intelligence-dangerous-text-gpt2-elon-musk-a9192121.html

   James Vincent. *"OpenAI has published the text-generating AI it said was too dangerous to share"*.  November 2019.
   https://www.theverge.com/2019/11/7/20953040/openai-text-generation-ai-gpt-2-full-model-release-1-5b-parameters

## Acknowledgements:

This project was originally created as a student in [ECE 188: Machine Learning for the Arts](https://roberttwomey.github.io/ucsd-ml-art/) at UC San Diego with the support and tutelage of **Dr. Robert Twomey**.  
https://roberttwomey.github.io/ucsd-ml-art/

The entire process of generating and finetuning GPT-2 was made *simple* by the help of **Max Woolf**'s ([@minimaxir](https://github.com/minimaxir)) *GPT-2-simple* package.  
https://github.com/minimaxir/gpt-2-simple

