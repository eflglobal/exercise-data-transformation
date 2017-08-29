# Data Transformation Exercise
The company where you work has created an online survey.
Whenever someone completes this survey, their responses and some metadata are collected and sent to the backend as a list of JSON objects.

After validation, this data must be transformed so it can be correctly stored in the database.

**Your task is to design and build a process to transform the incoming data from [`payload.json` (included in this GitHub repo)](payload.json) so that it can be imported into the `assessmentResponse` table described below.**

- You must use Python (any version) to complete this exercise.
- You may use any frameworks/ORMs/libraries/etc. that you are comfortable with.
- Your solution does not actually have to import anything into a SQL database,
  but it must produce something that would be trivially easy to import.

## Model
Here's the model that you have to work with.
Unfortunately, it's part of the legacy application and doesn't quite match the structure of the data from the new survey application.
But, lots of existing systems rely on it, so you'll just have to find a way to make the data fit.

```sql
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

## Example Data
To give yourself an idea of how you want it to look once saved in the database, you manually transformed one of the questions/steps.

### Input JSON
```json
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

### Transformed Values
```text
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

# Hints
- Some of the observations in `payload.json` have attributes that don't map nicely to `assessmentResponse`.
  Your code should still try to import these values... somehow.
- You may need to make assumptions about the meaning/purpose of some of the data.
  As long as you document it somewhere (and it's... you know, _plausible_ :slightly_smiling_face:), we'll play along.
- This is a programming exercise with a time limit, so focus your attention on the areas that will best showcase your skills and talents, while still completing as many of the requirements as possible.
  An amazing solution that's 75% complete is better than an okay solution that's 100% complete.
