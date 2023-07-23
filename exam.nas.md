Backend Architecture Design of `assessments.nas` or `exam.nas`

## Database Schema:

For the DB, I decided to opt for a NoSQl DB, or a key-value pair DB. The nature of the assessment, retrieval of data, the immutability/structure of the data,the horizontal scalability, high fz of reads over writes  has all influenced in my decision to go with a non-traditional approach of NoSQL over SQL DB. 

For simplicity and better designing of Schema, I have decided to narrow my choice of NoSQLDB to MongoDB.
To represent the directed graph structure of the assessment, I intent to use 

1. Questions Collection:
```
{
  "_id": "ObjectId",
  "questionText": "String",
  "options": ["String"],
  "correctAnswers": ["String"],
  "marksForCorrectAnswer": "Number",
  "marksForIncorrectAnswer": "Number",
  "nextQuestionId": "ObjectId" // Reference to the next question's _id in the graph
}
```
2. Assessments Collection:
```
{
  "_id": "ObjectId",
  "userId": "String",
  "currentQuestionId": "ObjectId",
  "isComplete": "Boolean",
  "score": "Number"
}
```
Core Backend Data Structures: involves storing a linked list
```
1. Question Model:

class Question {
  constructor(_id, questionText, options, correctAnswers, marksForCorrectAnswer, marksForIncorrectAnswer, nextQuestionId) {
    this._id = _id;
    this.questionText = questionText;
    this.options = options;
    this.correctAnswers = correctAnswers;
    this.marksForCorrectAnswer = marksForCorrectAnswer;
    this.marksForIncorrectAnswer = marksForIncorrectAnswer;
    this.nextQuestionId = nextQuestionId;
  }
}
```
2. Assessment Model:
```
class Assessment {
  constructor(_id, userId, currentQuestionId, isComplete, score) {
    this._id = _id;
    this.userId = userId;
    this.currentQuestionId = currentQuestionId;
    this.isComplete = isComplete;
    this.score = score;
  }
}
```

## Backend APIs

Assuming the backend is designed using RESTful APIs, here are the API endpoints and their functionalities:

1. POST /api/assessments/start

-   Request: Create a new assessment session for a user.
-   Response: Returns the first batch of questions (Q1...QN) along with their details.

2. POST /api/assessments/:assessmentId/questions/:questionId/answer

-   Request: Submit the user's answer for a specific question in the assessment.
-   Response: Returns the updated assessment with the next batch of questions (Q2...QN+1) after the current question.

3. GET /api/assessments/:assessmentId/score

-   Request: Retrieve the final score of the assessment.
-   Response: Returns the final score of the assessment.

4. GET /api/assessments/:assessmentId/batch/:batchNumber

-   Request: Retrieve a specific batch of questions.
-   Response: Returns the questions (Q1...Q(batchNumber)) along with their details.

5. GET /api/assessments/:assessmentId/nextBatch

<b>Caveat: this could involve building stateful web servers that cannot be horizontally scaled! A better approach would be rely on pagination queries</b>

-   Request: Retrieve the next batch of questions after the current batch.
-   Response: Returns the next set of questions (Q(N+1)...Q(N+batchSize)) along with their details.

6. GET /api/assessments/:assessmentId/previousBatch

<b>Caveat: this could involve building stateful web servers that cannot be horizontally scaled! A better approach would be rely on pagination queries</b>

-   Request: Retrieve the previous batch of questions before the current batch.
-   Response: Returns the previous set of questions (Q(N-batchSize)...Q(N-1)) along with their details.

Note: The "batchSize" parameter could be defined based on the number of questions you want to show in each batch. It can be augmented into a pagination query!

With this architecture and APIs, the frontend can interact with the backend to handle the user actions you mentioned, such as showing the current batch of questions, answering questions, showing the next and previous batches, and displaying the final score once the assessment 