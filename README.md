# Project 1 Generative Text

Parker Addison, pgaddiso@ucsd.edu

## Abstract

*Include your abstract here. This should be one paragraph clearly describing your concept, method, and results. This should tell us what architecture/approach you used. Also describe your creative goals, and whether you were successful in achieving them. Also could describe future directions.*

I will be generating docstrings for Python functions based on the source code of the function.  I may decide to call this model *"Func2Doc"*.

Python functions are encouraged to include a [docstring](https://www.python.org/dev/peps/pep-0257/)—a block of text following the function definition which is provided so that users of the code can understand the purpose and use of the function.  This text is ignored by the computer when running the code, and exists primarily for human use.  That being said, many (not all) docstrings follow specific formats that are compatble with automatic documentation webpage generators such as [Sphinx](https://www.sphinx-doc.org/en/master/), though there are a few different flavors such as the [Sphinx flavor](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html#the-sphinx-docstring-format) of docstrings, the [NumPy flavor](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html#example-numpy), and the [Google flavor](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html).

Corpus creation should be straightforward, since Python code has a specific whitespace-delimited format which can be parsed to extract function definitions, their internal code, and their docstrings.

This model will likely rely on seq2seq as its general architecture, and will produce a mapping from function source code to docstring text.

A docstring is the part of the code below surrounded by triple quotation marks, `"""`.  The following is an example of a Sphinx-flavored docstring.
```python
def my_function(data=None):
  """
  Takes in a collection of data and returns the results of performing something else on it.
  
  :param data: A collection of data, defaults to None
  :type data: iterable, list, array-like
  ...
  :raises Exception: If no data is provided, an Exception will be raised
  ...
  :return: The value given by doing something else on the collection of data
  :rtype: int
  
  """
  
  # Some useful comments, some useful code
  
  if data=None:
    raise Exception("You must provide some data!")
    
  value = do_something_else(data)
  
  return value
```

In this case, the model would be trained on the mapping of `'\n# Some useful comments, some useful code\n\nif data=None:\n    raise Exception("You mu...'` → `'Takes in a collection of data and returns the results of performing something else on it.\n\n:param data: A collection of da...'`

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
