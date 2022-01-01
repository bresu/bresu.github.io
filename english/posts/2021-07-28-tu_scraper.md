---
title: "quick analysis of my fellow applicants"
layout: post
---

# quick analysis of my fellow applicants
created: 20.08.2021

# context
So I am (hopefully) gonna be a Computer Science student at the Technical University of Vienna. Yesterday I did the entry exam and I will find out in a week or so if I am accepted or not. Weeks before the exam all applicants got an e-mail invitation to the "moodle" or "edu" platform, which is basically just the website where you submit your work and find learning resources.
__The site also had an interesting feature which might be privacy-law violating (maybe!)__
In the navigation bar of the website there was a "participants"-tab. I clicked on it and to my surprise:

__You could look up fellow applicants by their first and last name!__

It also offered the option to show __all (1000)__ entries on a single page. Obviously I enabled that and wrote the simplest script to just download it and extract all names.
Now I had a table to work with but the question was: what information can I squeeze out of only just the names?
I quickly realized that first names are a VERY strong indicator of gender. (I know that this a very binary look at it, but the probability of non-binary people being present in this list of about 1000 people is negligible and all my results are just stupid estimations)
So I did some research and it turns out that somebody had made a [gender-guesser](https://pypi.org/project/gender-guesser/){:target="_blank_"} python module that uses data from the American Census and other sources to identify gender by name.
_Pretty neat - Let's get to work_
I let the program run over my data and these are the results (estimations!):

        male: 662
        female: 200
        unknown: 104
        mostly male: 8
        mostly female: 3
        unisex: 2

# interesting

So we see that gender-equality still has a long way to go...
You might ask what "unknown" means. I checked some datapoints and it seems that the data model is very western-centered. So "exotic" (I hate this term) names are not registered in the model. But one can assume that the distribution of gender in the "unknown" category is also roughly 3:2.
# weird things i found
So it turns out that some sick fucks (mostly American researchers) also made a __race-guesser__ which in my opinion is super problematic. As I said before about the gender-guesser: this is just an estimation and it excludes non-binary people or other members of the LGBTQIA+ community. But guessing race????
There are so many factors that need to be taken into account. Just because someone's name is Jack Smith doesn't mean he's white. Name changes happen all the time due to marriages etc. so I really have problems regarding the accuracy of modules like this.

[ WILL UPDATE AT BEGINNING OF NEW SEMESTER ]
