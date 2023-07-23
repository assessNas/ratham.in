# Designing "assessments.nas", a online dynamic assessment

### Context

Assume the question paper is a directed graph with a single root node where an answer to all questions(nodes) in a given path can decide what the next question will be. Like a directed graph, it would have many possible end nodes when the assessment ends. A question can have any number of options. All questions are objective. There can be one or more right answers to a question. Right answer adds positive marks to the total, a negative answer gives negative marks to the total. 

####  SRS - (Software Requirement Specification)
1. Sample assessment directed graph structure stored in DB
2. All DB structures/tables you would use and dummy data in each of them
3. All core backend data structures you would need to implement /next batch API
4. You explaining all API full urls that would be called by FE to support the following user actions 
    - Show the current batch of questions. A batch represents the max number of questions, lets say Q1…QN. N is defined as …Q2 is always shown whenever Q1 is shown no matter what the Q1's answer user chooses, Q3 is always shown no matter what Q2's answer is…QN is always shown no matter what QN-1 answer is. 
    -  Whatever gets shown after QN is influenced by whatever has been answered to all or a few of the questions that were shown from the root of the directed graph till QN.
    - Answer each question by choosing an option from the multiple choice (Single or multiple select, assume no subjective Qs)
    - Show next batch of questions
    - Show previous batch
    - The assessment auto ends when the leaf is reached. FE or User won't know which is the last question.   He would only after he presses next. 
    - Score gets shown once assessment ends.

...
