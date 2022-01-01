+++
title= "scraping austria - a painful lesson in webscraping"
descrption = "scraping government sites like a pro - kinda"
author = "Bresu"
date = 2020-06-10
+++
![Österreichische Stadt am See](/pics/scraping_austria/headerImage.png)
Servus! I got the inspiration for this project by a youtube video I watched. In the video, the guy scrapes some embassy websites and automatically sends an e-mail to every single embassy. It's a very entertaining video, you can watch the [video here](https://www.youtube.com/watch?v=Jbix9y8iV38&t=115s). Inspired by the "simple" approach, I thought: Well, embassies are taken, what else is there to scrape? And I wanted something more challenging than scraping 10 accumulating websites that have basically done my job already.
So I came up with something original:

# scraping every single website of every municipality in austria :austria:
The task was determined, now all I needed was a well thought out plan:

1. find a list of ALL municipalities
2. combine each municipality's name with different domains
3. find existing domains
4. look for indicators that the current site is the correct one (and not just a registered, unused one or - even worse - a business site)

_Let's go!_
# finding a list - all hail statistik austria
Finding a complete and up-to-date list wasn't quite as hard as I thought it would be. Turns out that Statistik Austria has some pretty cool stuff on their website, like [this table](http://www.statistik.at/verzeichnis/reglisten/gemliste_knz.xls) which contains every municipality, it's postal code (PLZ) and it's **Gemeindekennziffer** (GKZ).

_What the hell is a GKZ?_

![Confused Dog](/pics/scraping_austria/confusedDoggo.webp)

Glad you asked! It's a unique number assigned to each municipality, much like a postal code number, but still very much different. The GKZ has five digits, while the PLZ has only four. PLZs are an old system which has it's pros and cons, the biggest disadvantage being that PLZs don't have geographical meaningfulness:

* 2384 is the PLZ for Breitenfurt, Lower Austria
* 3012 is the PLZ for Wolfsgraben, Lower Austria (only 4km away from the former)
* Vorarlberg's PLZs all start with 6, which is also Tyrol's first number
* Except South Tyrol's PLZ: starts with 9, which is otherwise Carinthia's!  
<br>
One can see: This system is not good, especially if you want to retrieve geographical data from it.  
That's why Statistik Austria came up with the GKZ

# the genius way of GKZ
As mentioned above the GKZ consists of 5 numbers:
The first representing the state with numbers 1 to 9.
Second and third represent the political districts within the state.
The last two digits represent the municipalities within that district.
So Bisamberg for example has the GKZ: 3 12 01

 - 3: Lower Austria
 - 12: District of Korneuburg
 - 01: Bisamberg[^1]
<br>  
*Way better!*
# combinations and permutations are a pain in the ass
So Austria has roughly 2100 municipalities. That's a lot. Especially if you think about all possible combinations for domains.
'''python
    gemeinde_name = "Bisamberg".lower()

    list_possible_domains = [

        # If the website isn't in HTTPS, trying to access it via HTTPS will throw an error
        "http://www.{gemeinde_name}.gv.at".format(gemeinde_name)

        # Not everybody uses .gv domains
        "http://www.{gemeinde_name}.at".format(gemeinde_name)

        # Turns out: states have domains??? Mostly Tyrol (.tirol), Burgenland (.bgld) and Upper Austria (.ooe)
        "http://www.{gemeinde_name}.bgld.gv.at".format(gemeinde_name)

    ]
'''
For one-worded municipalities this approach works fine but it won't work with such as "Krems an der Donau". Another example are all municipalities with "Sankt", like "St. Georgen im Lavanttal". The work-around I came up with is a very simple one:

    ugly_gemeinde_name_1 = "Krems an der Donau".lower()

    split_gemeinde_name_1 = ugly_gemeinde_name_1.split(" ") // => ['krems','an','der','donau']

    # Then I wrote some very ugly functions that format different possible domain names - I will spare you the details.
    # In the end, the list of possible domains looked like this

    list_of_possible_domains = [

        "http://www.krems.at",
        "http://www.krems-an-der-donau.at",
        "http://www.krems-donau.at",
        "http://www.krems.gv.at",
        "http://www.krems-an-der-donau.gv.at",
        "http://www.krems-donau.gv.at"

    # ... and more ]
# requests and false positives
So after generating all possibilities, I cycled through them and sent a GET request to each one. If the domain doesn't exist, python will throw an error and with a simple try except magic, you can easily catch those and verify existing sites.

# are you 4 real?
So it turned out: just because the site exists doesn't mean that it's the right one. There are business or private sites that use one of the generated domains. I had to check for keywords like "Rathaus", "Gemeindeamt" etc. And even with these checks, I am pretty sure that there are STILL wrong sites in my table - nobody's perfect!

# you got mail
After hours of crawling & scraping I finally had a table to work with for scraping the e-mail adresses. It's basically the same step as before when I checked for keywords, only that this time I was looking for words like "Kontakt" and "Impressum" and then had to parse only the adress without any unwanted text like the "to:" junk. For this step I had to use selenium because many of the sites use an excessive amount of javascript. So I had to render it. But I actually really enjoyed working with selenium - very powerful tool!

Now all that was left to do was to look for errors (there were a LOT) and some general cleanup and encoding issues. I learned a lot while doing this and also had a lot of fun. You can find the data on my [github](https://github.com/bresu/oe_gemeinden).


[^1]: My [hometown](https://www.youtube.com/watch?v=77gKSp8WoRg)!

