# Langchain Expression Language Basics

- LangChain Expression Language is that any two runnables can be "chained" together into sequences.
- The output of the previous runnable's .invoke() call is passed as input to the next runnable.
- This can be done using the pipe operator (|), or the more explicit .pipe() method, which does the same thing.

- Type of LCEL Chains
    - SequentialChain
    - Parallel Chain
    - Router Chain
    - Chain Runnables
    - Custom Chain (Runnable Sequence)