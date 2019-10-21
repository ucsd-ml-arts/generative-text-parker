# Project 1 Generative Text

Parker Addison, pgaddiso@ucsd.edu

## Abstract

Artistically, the input of my project is a high school essay I wrote on Artificial Intelligence in Space Rovers, and the output is the same high school essay with roughly twice the word count.  What I'm creating is a highschooler's dream, and a statement to the needless verbosity and meaningless clutter that high word counts encourage.

I used (rather, abused) OpenAI's GPT-2 model to accomplish my goals.  I wanted to purposefully misuse parts of the model in order to put an end to GPT-2's creativity, and to produce something in stark contrast to the text-summarative capabilities that GPT-2 has been cited for.  This involved a large amount of experimentation with different generation parameters such as `temperature` and `top_k`, as well as exploration of the use and over-use of finetuning.

I tried different methods of finetuning with the following findings:
1. Finetuning on a large corpus of academic writing, magazine writing, and newspaper writing (medium amount of epochs, ~500)
   - The model had a strong desire to talk about topics mentioned in this corpus—did not stay on topic to the prompt
2. Finetuning on the essay that I wanted to verbosify (small amount of epochs, ~1–5)
   - The model generally stayed on topic, but the specific subject discussed still had decent variability
3. Finetuning on (1) followed by (2)
   - The model would generally stay on topic, but would often get derailed by topics from (1)
4. Finetuning on the prompt that I wanted to verbosity (smallest amount of epochs, only 1)
   - AMAZING!  The model would generally output very similar information to that contained by the prompt, and would occasionally rely on general knowledge to complete an idea.  I ended up solely using this
   
I tried different parameters with the following findings:
1. Temperature - affects variability of output
   - Low temperatures (0.1–0.4) resulted in low variability (duh) and high likelihood of the output getting caught in a cycle.  If the text was off-subject, setting a low temperature did *not* fix the issue, and instead only cemented the output as slightly off-subject
   - Medium temperatures (0.4–0.7) resulted in moderate variability and low likelihood of the output getting caught in a cycle.  This often lead to the output repeating the prompt if the model didn't know much about the subject being discussed
   - High temperatures (0.7–1.0) resulted in good variability.  I used primarily a temperature of 0.8, as this allowed for the model to reference its general knowledge about the subject being discussed.  If many samples were returning blank or returning a limited output, then bumping the temperature up allowed the model to attempt to bring up a different subject
2. Top-k - allows the model to choose from only the top-k tokens during a generation step
   - Default (0) was horrible since it actually removes all restrictions and allows sampling from all tokens.  This meant that the model went off topic very easily
   - Recommended (40) was generally good, since it restricted the model to generating words that were seen in or similar to those seen in the prompt (since I also finetuned on the prompt)
   - Lower (30) was what I used for most of my generations, since it further restricted the model to using words similar to those in the prompt.  Lower top-k forces the model to stay on subject, since it isn't allowed to mention unrelated words.  **A low top-k can be used with a high temperature to very effectively repeat the finetuning data/prompt while still attempting to avoid replication**
   - Higher (80) was what I used if many samples were returning blank or the model was having a hard time avoiding replication of the prompt.  Doing this returned a bit of creative freedom to the model and allowed it to rely on general knowledge to continue the prompt
3. Length - determines how many tokens are generated
   - I generated 100 tokens for each sample, since putting too small a size often cut off sentences that seemed to have good potential, and too large a size goes off topic or enters a cycle
    
I played around with these finetuning methods and parameters on a handful of prompts from high school essays, and then got to work doubling the word count of one essay in particular.  In order to do this, I created a collection of "passages" that formed the essay.  Each passage consisted of three consecutive sentences that were all contained in the same paragraph.  (I also played around with using three versus four sentences, but found that three gave better results for my particular high-school-writing-style.)  Then, for each passage, I initialized a GPT-2 model and ran one iteration of finetuning on the passage.  I then generated a handful of samples, using the passage as the prompt.   I would vary the parameters in accordance to how I explain them above.  Finally, I would hand pick some of my favorite generated sentences and add those to my essay.  I found good sentences for most passages using only 10 samples, though some I had to look at up to 40 samples (playing with parameters every 10) in order to find something worthwhile.  Note that the only modifications I made to the samples was adding a period if the output had been truncated.

The end result is (in my opinion) beautiful.  For the most part, the sentences that I selected are very mundane.  They don't pull the essay off topic, they don't detract from its value, and they certainly don't add to it... with the exception being that they add to the word count!  However, there are some instances—particularly in the first few passages—where the creative side of GPT-2 was trying as hard as it could to reveal itself... these instances produced some interesting, amusing, and sometimes terrifying outputs.

Overall, this was a time consuming process when done on an entire essay, but I could see it being somewhat useful, or at least somewhat amusing as an extension to Google Docs.  After all, the only thing the model needs to be finetuned on is the prompt itself, so the training and generation is actually quite quick and very accessible—no need for a large corpus, just a few sentences.


## Model/Data

Briefly describe the files that are included with your repository:
- Training data, complete essay: https://github.com/ucsd-ml-arts/generative-text-parker/blob/master/text/rovers.txt, passages: https://github.com/ucsd-ml-arts/generative-text-parker/tree/master/text/passages
- Checkpoint for 124M model finetuned on `passage0.txt`: **file too large for GitHub without LFS.  The model trains quickly, so just do that instead!**

## Code

Your code for generating your project:
- Training and generating: https://github.com/ucsd-ml-arts/generative-text-parker/blob/master/Project1.ipynb

## Results

- HTML page with results: https://ucsd-ml-arts.github.io/generative-text-parker/, compare to [original](https://drive.google.com/open?id=177hWtGZio96_AQ1mnvBalR3R-nyEq-Nsh7lUBA_hpXA)

## Technical Notes

Any implementation details or notes we need to repeat your work. 
- Uses [gpt-2-simple](https://github.com/minimaxir/gpt-2-simple) for simple finetuning and generating using GPT-2
- Note that each model is about 500MB... and that to verbosify a piece of writing the way described above you'll need to train almost as many checkpoints as there are sentences... so make sure to delete these checkpoints once you're done generating for a given passage
- gpt-2-simple has a hardcoded training data sample size of 1024 tokens.  We want to finetune on a single passage... which is no where near that many words.  So, I simply duplicated the passage until its file reached or exceeded 1024 words.  Is this the best way to accomplish this?  Maybe not, but I had a hard time editing the source code without causing errors, so we honestly can't say whether my method was better or worse than adjusting the training data sample size!
- Note that datahub can usually finetune without issue, but sometimes the instance runs out of VRAM.  In that case just use JupyterHub and request a 1080Ti or another card with greater than 8GB of VRAM

## Reference

References to any papers, techniques, repositories you used:
- [gpt-2-simple](https://github.com/minimaxir/gpt-2-simple)
- [gpt-2-simple's Issues page](https://github.com/minimaxir/gpt-2-simple/issues)
