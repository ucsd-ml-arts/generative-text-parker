# Project 1 Generative Text

Parker Addison, pgaddiso@ucsd.edu

## Abstract

*Include your abstract here. This should be one paragraph clearly describing your concept, method, and results. This should tell us what architecture/approach you used. Also describe your creative goals, and whether you were successful in achieving them. Also could describe future directions.*

The goal of my project is to create a "verbosifier"—something that is in stark contrast to the text-summarative capabilities that GPT-2 is often cited for.  At the same time, I don't intend to allow GPT-2 the generative freedom that it is often given when applied to text-generative tasks.  No, a verbosifier should accomplish what any highschooler horrified by a large word count could only dream of: **given some writing, say the same thing but differently**.

This will involve some careful—though intentional—misuse of GPT-2.  I am experimenting with temperature of conditional outputs of course, but also with ways to abuse the overfitting that occurs when finetuning is performed on a small corpus.  Can I find a number of training steps that manages to skirt the line between pure replication and creative freedom?  We'll see.

## Model/Data

Briefly describe the files that are included with your repository:
- trained models
- training data (or link to training data). what is your corpus?

## Code

Your code for generating your project:
- training_code.py or training_code.ipynb - your training code
- generative_code.py or generative_code.ipynb - your generation code

## Results

- Documentation of your generative text in an effective form. A file with your generated text (.pdf, .doc, .txt). 

## Technical Notes

Any implementation details or notes we need to repeat your work. 
- Does this code require other pip packages, software, etc?
- Does it run on some other (non-datahub) platform? (CoLab, etc.)

## Reference

References to any papers, techniques, repositories you used:
- Papers
  - [This is a paper](this_is_the_link.pdf)
- Repositories
- Blog posts
