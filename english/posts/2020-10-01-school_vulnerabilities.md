---
title: "schools and privacy: don't mix!"
layout: post
---

# schools and privacy: do not mix!
created: 01.10.2020

# webuntis sucks - let me explain why

If you are/ were a student in Austria, you probably came into contact with the company WebUntis (WU) and it's products, mostly known for their timetable manager tool and [security flaws](https://packetstormsecurity.com/files/157998/WebUntis-2020.12.1-Cross-Site-Scripting.html){:target="_blank_"}. So the company's reputation really isn't the best when it comes to security vulnerabilities and how they handle them. But for an austrian enterprise whose headquarters are located in Lower Austria they are actually doing pretty good.  
Or so I thought.  

# most school administrators are ok. some are not

So it turns out that some administrators simply do not read manuals/ best practices or they simply missed to click on a checkbox (which would be a design error by WU). A minor mistake like this can be the reason why the data of 100s of students is publicly available.  
As much as I would like to show you what I mean by that: I won't.  
Why? Because we are talking about data of MINORS, CHILDREN. And this is not a "how-to-get-arrested tutorial". Let me simple say: I found some schools whose WU settings are not optimal for data security. But this factor alone would NOT be as big of a deal if WU didn't implement a shitty way of handling things.

# we will implement some privacy features. not.

If you are logged into your WU account you can see your fellow student's login names. So if someone could login with e.g. a hacked account they could start scraping full names of students. That is very bad. Hence WU recommends that the username should NOT plainly be the someone's full name. Let's take **Maximillian Mustermann** as an example:
```
    username = maximillian.mustermann // bad
    username = must.max // better

```  

WU offers school admins tools to do exactly that. So even if someone hacked into a WU account the intruder would not be able to retrieve a list of fellow student's full names. This is a very smart feature, especially when taking into account that most students use a very simple password for their login and therefore being easy targets for brute forcing. Good job WebUntis!  

_but_  

I accidentally stumbled upon this stupid flaw while playing around with the WebUnits interface. WU has a button for printing out _any_ student's timetable. It loads a new page with a more printable layout - all nice and neat - but it **also**  
displays the whole fucking name of the student.
and their class (can be used to identify age)
and their complete timetable (can be used for all sorts of harmful shit)

# no punchline needed - fuck webuntis!
