# No information may be an important piece of information

A few years back, I have learnt a very valuable lesson in a data migration project at a health care organisation.

There was a table called *patient* and within the patient table there was a field called *allergies*. The allergies field contained free text like "allergic to peanuts", etc. 

As part of a cleansing rule, we've decided to filter out anything that was not an allergy. For instance "NIL KNOWN" would not make it to the destination system as it was not an allergy.

The decision turned out to be a mistake because "NIL KNOWN" meant something that's valuable to the business.

The allergies field was used to store answers from a patient survey form. Therefore there was an obvious difference between **NOT** answering a question and answering *NO* to a question.

Let's now look at the possible consequences of the data migration process:

### 1. Migrating "NIL KNOWN"
The nurse could safely carry out care activities without delay.

### 2. Not migrating "NIL KNOWN"
The nurse might take extra time to confirm that the patient has no food, medical or physical allergies before carrying out care activities.

Lesson leant.
