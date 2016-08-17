# Data Transformation Exercise

The company where you work has created an online survey. Whenever someone takes this survey, the responses and some meta-data about it are collected and sent to the backend as a list of JSON objects.

After validation, this data must be transformed so it can be correctly stored in the database. You've been tasked with writing the transformation process to make this incoming data be ready for storing in your database.

## Model
The application you are working on already has an existing model to store response data in a relational database.  Unfortunately, it's part of the legacy application and doesn't quite match the structure of the data from the new survey application.  Lots of existing systems rely on it, however, so you'll just have to find a way to make the data fit.

Here's the model that you have to work with:

```
CREATE TABLE assessmentResponse (
  id integer PRIMARY KEY,
  questionCode varchar(104),
  questionResponse varchar(104),
  section varchar(104),
  timeElapsed smallint,
  viewsCount smallint,
  responseSequence varchar(104)
);
```

Note:  `assessmentResponse.id` should auto-increment (exact implementation depends on which SQL/ORM backend you are using).

## Example Data
To give yourself an idea of how you want it to look once saved in the database, you manually transformed one of the questions/steps.

### Input JSON
```
{
  "uid":      "b1435d5771c14c2498642f07ce04b087",
  "step":     "likert",
  "sequence": 1,

  "observations": {
    "cheat_customer": {
      "responseValue":    "5",
      "responseSequence": ["5"],
      "timeElapsed":      8,
      "viewCount":        1
    },

    "work_integrity": {
      "responseValue":    "3",
      "responseSequence": ["2", "3", "4", "3"],
      "timeElapsed":      48,
      "viewCount":        5
    },

    "personal_honesty": {
      "responseValue":    "1",
      "responseSequence": ["5", "1"],
      "timeElapsed":      17,
      "viewCount":        2
    }
  },

  "metas": {
    "timeElapsed":    486,
    "helpBarClicks":  3,
    "viewCount":      1
  }
}
```

### Database Records Created
```
+-----------------------+------------------+-------------+-------------+------------+----------------------+
| questionCode          | questionResponse | section     | timeElapsed | viewsCount | responseSequence     |
+-----------------------+------------------+-------------+-------------+------------+----------------------+
| cheat_customer        | 5                | likert      |           8 |          1 | ["5"]                |
| work_integrity        | 3                | likert      |          48 |          5 | ["2", "3", "4", "3"] |
| personal_honesty      | 1                | likert      |          17 |          2 | ["5", "1"]           |
| _likert               | NULL             | likert      |         486 |          1 | NULL                 |
| _likert_helpBarClicks | 3                | likert      |        NULL |       NULL | NULL                 |
+-----------------------+------------------+-------------+-------------+------------+----------------------+
```

## More about your task

When it reaches the start of your process, the data has already been validated so you can assume:

  - The JSON structure is valid.
  - Attributes with the same name always have the same type/purpose (e.g., `clickCount` is always an integer representing the number of times the person clicked on something).

Your task is to write a program to transform the incoming data from [`payload.json` (included in this GitHub repo)](payload.json) to the table structure described by the `assessmentResponse` model.

**You may use any language/framework to solve this exercise.  Use whatever database/ORM that you are most comfortable with.**

# Hints

- Some of the observations in `payload.json` have attributes that don't map nicely to `assessmentResponse`.  Your code should still try to import these values... somehow.
- You may need to make assumptions about the meaning/purpose of some of the data.  As long as you can justify it, we'll play along.
- Since this is a programming exercise with a time limit, keep in mind that a great solution that's 80% complete is better than an okay solution that's 100% complete.

