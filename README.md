# Reddit Search Engine
A keyword-based search engine for raw data grabbed from Reddit

# Abstract

The site known as reddit is widely known for its inside communities that connect users with
similar interest to posts on its site where they can share information and see posts by other users
based on their popularity within that community. We wanted to create a search engine that gives
users results that are better tailored to their search, without the need to add a parameter into
google as we have noticed many users do. After our base implementation and running a limited
amount of tests we came to the conclusion that our search engine does possibly give more
accurate results based on sorting but it also has much to expand and improve on for the future.

# Acknowledgement

This project uses [aliparlakci/bulk-downloader-for-reddit](https://github.com/aliparlakci/bulk-downloader-for-reddit) and it would have been extremely difficult without it.



# Data collection

ABDFR fetches directly from the Reddit API and has built in redundancies which makes it a more attractive tool than iterating our
own in the short time that we had. Windows Powershell and Python 3 were used to
install the tool using the command: directly𝑝𝑦𝑡ℎ𝑜𝑛3 − 𝑚 𝑝𝑖𝑝 𝑖𝑛𝑠𝑡𝑎𝑙𝑙 𝑏𝑑𝑓𝑟 –𝑢𝑝𝑔𝑟𝑎𝑑𝑒
into our machines.

# Initial setup

Several things had to be taken care of before beginning work, mostly in initializing the
dependencies in which this project was built on. We imported python packages such as
pandas for its dataframe tools and numpy for certain calculations. For our function to
process the raw json files we used three utilities: glob, os, and json. We also used nltk for
natural language processing as well as datetime to calculate decay factor and drive, a
Colab utility to interface directly with Google’s Drive. In each step, we saved the
progress into a new processed csv file so as to make it easier for us to resume work in
each block without having to run all the commands from the beginning. All we needed to
do was run the setup and then any particular block.

This version of the project has been modified such that it does not need connection to Google Drive, but rather uses files available in this repo.

# Dataset conversion
 We used a function to fetch all the raw files in the particular folder with a
.json extension and added it to a python data structure. It was then converted into a native
pandas dataframe and saved into a .csv file so we do not have to rerun the processing step
from the beginning.

# Cleaning the data
Steps were taken to clean the data to avoid issues in the future. Null values were replaced
with empty strings such as “ “ so as to avoid any issues in processing and certain
columns, such as boolean columns that reddit uses to markdown posts were removed as
they offered no relevance in the project. These include but are not limited to “over_18”,
“spoiler”, or “pinned”.

The download also marked the timestamp in a raw UNIX format and we used a pandas
function to convert it into a readable general format. We left the timezone as the default
UTC.

# Language pre-processing
We applied several language pre-processing techniques that were learned in class. Before
that, however, we combine the title of the post and the body of each post to make one
new column entry to create a full representation of the text in that submission. We then
normalized it by converting all letters into a lowercase and performed stemming. We then
tokenized the text and separated each word into a python list. We then wrote a function to
store a vocabulary of stopwords and iterated through each list to remove the stopwords
and other unused symbols. We also converted it into a set and used its natural property to
remove duplicates.

# Calculating decay factor
We included an additional column that factors in exponential decay to adjust each
submissions’ score. We figured that as some of these submissions go back to 2015, the
contents might not be as relevant as ones fetched in recent days. So we used a common
decay formula

𝑒−𝑎 (𝑐𝑢𝑟𝑟𝑒𝑛𝑡 𝑡𝑖𝑚𝑒 − 𝑟𝑎𝑡𝑖𝑛𝑔 𝑡𝑖𝑚𝑒 𝑤ℎ𝑒𝑟𝑒 𝑒 = 𝑟𝑎𝑡𝑖𝑛𝑔, 𝑎𝑛𝑑 𝑎 𝑖𝑠 𝑎 𝑐𝑜𝑛𝑠𝑡𝑎𝑛𝑡

We then used a python library to fetch today’s date and time and perform adjustments
everytime the colab project is run. This results in older posts getting a slightly lower point
compared to newer entries based on how old they are.

# Search engine implementation
Our implementation of a search engine is essentially a keyword search that uses a partial
best match model. It searches through the keyword column for each row and finds if it
finds at least a partially matching term in the query. We had planned to do query
expansion but did not find ample time to implement it properly.
We did however find time to create a basic terminal interface that will parse your input
and accept several arguments. A basic flowchart detailing how it works is included
below:

![image](https://user-images.githubusercontent.com/48269287/211879933-34512161-969e-4b01-bb21-54823b12b1be.png)

Upon initializing the script, the program starts with a try catch block and a boolean
condition that is by default true to ensure the program keeps running. The variables
including the boolean are reset every run to ensure that the results stay consistent. Upon
submission of the query, the program checks if the user wants to request “-help” and then
“-exit”, this is to ensure that if the user would like to search for the query “help”, they can
do so. It then parses the input and separates the entries into a list, with the different
argument separated by the dash prefix.

The program then runs checks for redundancy, verifying that there is an additional
argument aside from the query and if there is a query in itself, then sets boolean values
accordingly, for example if the user would like to sort by decayed rating, then they can
input either - or - . A switch case statement would have been preferable to an if𝑑 𝑑𝑒𝑐𝑎𝑦
else, but python’s implementation does not seem to allow for multiple switches for one
output at the moment.

If the checks are valid and there is an argument, then the query is then passed on the
search function which goes through the dataframe. It then stores the results into a
separate search result dataframe, which is then sorted according to the argument and
boolean functions, but by default it sorts based on the points in descending order. As it is
a terminal, we could not use panda’s dataframe visualization and thus used print
statements to output the results neatly. However, due to some of these results being able
to reach 30 posts, we also included a string argument to limit the number of output posts
so as not to clutter the terminal unnecessarily. This is taken into account when doing the
boolean checks
