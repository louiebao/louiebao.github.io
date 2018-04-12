**EDIT:** This is the very first post from my old blog from nearly 10 years ago. As I was reviewing the content, a lot of the arguements still made sense.

# PLUS naming convention

I have always been thinking about the differences between the English and the Chinese language. The Chinese grammar is not as strict or structured as English. The Chinese language emphasizes more on the meaning of the words than the structure of the sentences. 

If we can keep it simple and strip away the "features" of the English written language, then it is pretty easy to come up with a naming convention that just works.

### Present tense vs Past tense

Use programming concepts for example, the present tense is like the base class, whereas the past tense and the present continuous tense are like the derived classes. In order to keep a naming convention as simple as possible, it makes sense to stick with the present tense only when naming things.

### Lower case vs Upper case

Cases are there to improve readability, but in the programming world, we don't READ object names, we simply reference them. Lower case characters are faster to type than upper or mixed cases and they help to reduce if not eliminate case sensitivity issues. Case closed. 

### Under score vs Space

Spaces are used to separate words, and that's a feature you would really want to preserve. However, we all know that putting spaces in object names can cause all sorts of issues. So how do we resolve this dilemma? Well, the problem is partially solved by the TitleCase naming convention, where spaces are removed from the names. I say partially that's because 99% of the time it is fine, but consider this example "ABC1 123XYZ". See, you can't just remove the space and combine the two words, you'll need to make an exception and put something else in between, that's when the underscore comes in. So if you need to use underscores here and there, then why don't you just use underscores to link all words?

### Singular vs Plural

It is another base class vs derived class argument. Why mess with something original and simple by adding extra characters to a name if it yields no further clarity to the meaning of the object?
