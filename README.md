# exercise-data-transformation

The company where you work has created an online survey. Whenever someone takes this survey, the responses and some meta-data about it are collected and sent to the backend as a list of JSON objects.

After validation, this data must be transformed so it can be correctly stored in the database. You've been tasked with writing the transformation process to make this incoming data be ready for storing in your database.

## About the data

For storing the data in the `assessmentResponse` table, you have built the following Django model:

```
class Assessmentresponse(models.Model):

    # The name of the variable.
    questioncode = models.TextField(null=False, blank=False,
                                    db_column='questionCode')

    # The value that the applicant entered/selected; often a label or other
    # "human-readable" value.
    #
    # Note that this value should only be None if the question was skipped.
    questionresponse = models.TextField(null=True, blank=True,
                                        db_column='questionResponse')

    # The name of the section in which the question appeared (e.g., in Dooblo
    # these are called Chapters).
    section = models.TextField(null=True, blank=True, db_column='section')

    # The amount of time (cumulative) that the applicant spent on this
    # question.
    timeelapsed = models.IntegerField(max_length=4, null=True,
                                    blank=True, db_column='timeElapsed')

    # The number of times the applicant saw this question (can be > 1 if the
    # applicant backtracks in the test).
    viewscount = models.SmallIntegerField(max_length=4, null=True,
                                        blank=True, db_column='viewsCount')

    # Captures the unique response values that the applicant entered for the
    # question (in case he changed his response).
    responsesequence = models.TextField(null=True, blank=True,
                                        db_column='responseSequence')

    class Meta(object):
        db_table = 'assessmentResponse'
```

Also, you've already manually transformed one of the questions/steps to give yourself an idea of how you want it to look once saved on the database.

### Input Data:
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
      "viewCount":        1,
    },

    "work_integrity": {
      "responseValue":    "3",
      "responseSequence": ["2", "3", "4", "3"],
      "timeElapsed":      48,
      "viewCount":        5,
    },

    "personal_honesty": {
      "responseValue":    "1",
      "responseSequence": ["5", "1"],
      "timeElapsed":      17,
      "viewCount":        2,
    }
  },

  "metas": {
    "timeElapsed":    486,
    "helpBarClicks":  3,
    "viewCount":      1
  }
}
```

### Output Data:
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
  - The values and fields are valid, and the former are consistent between steps/questions.
  - Variables with the same name have same type/purpose (i.e `clickCount` is always an integer
    representing the number of times the person clicked on something).

Your task is to write a program to transform the incoming data from [`payload.js` (included in this GitHub repo)](payload.js) to the table structure described by the `Assessmentresponse` model.
