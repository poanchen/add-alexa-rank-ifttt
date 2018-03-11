# add-alexa-rank-ifttt
An IFTTT applets that allow the users to Keep track of their site's global or country rank in Alexa. Every time a hit to the API, log it into a Google spreadsheet.

## Why is this useful?
People who own their own site and care about their site ranking, normally, they would just get on to Alexa site multiple times a day and look at the ranking. Some geek people like me might even write a script that does that lol I mean, I am interested in seeing how my site is doing today but I am also interested in seeing how my site is doing in the past 3 months or even a year or more. Currently, a free version of Alexa only provides up to 6 months worth of data for any site with ranking better than a million. Site that records a million and upwards does not even show at all on Alexa beside the ranking for today. As of now, my personal blog [site](https://poanchen.github.io) currently ranked at 1,481,428 as global rank and 271,328 in India. As you can see in the [site](https://www.alexa.com/siteinfo/poanchen.github.io), it has no past data whatsoever because Alexa consider it as lower traffic site. What if somehow I still want to record it? And, not manully but it just records itself and I can come by and check it out anytime. This is why I write this little script that will help me automate all these.

**Disclaimer**: I am using the free version of Alexa API, http://data.alexa.com, apparently used by Alexa toolbar according to [this](https://stackoverflow.com/questions/3676376/fetching-alexa-data) article. For people who wants more data, please visit Alexa's [offical site](https://aws.amazon.com/alexa/) to learn more about it.

Okay enough of writing! Let's get back to work and I will show you how this thing work.

## Getting started

### Prerequisites:
- Python 2.7 or better
- A remote web server that allows you to run a cron job

### Installation
To begin, you first need to create a private applets in IFTTT in [this](https://platform.ifttt.com) site. Next, you would choose *Webhooks* as **if** and *Google Sheets* as **then**. So how it works is that our script will [first](https://github.com/poanchen/add-alexa-rank-ifttt/blob/master/getAlexaRank.py#L26) get your site ranking info from the Alexa API, it would then [parse](https://github.com/poanchen/add-alexa-rank-ifttt/blob/master/getAlexaRank.py#L33) the XML and retrieve relevant information. At the end, assuming everything went successful. It will [trigger](https://github.com/poanchen/add-alexa-rank-ifttt/blob/master/getAlexaRank.py#L38) the IFTTT webhook API to add an entry to the Google Sheets. In case you were wondering how you would get your webhook setting, please visit IFTTT [site](https://ifttt.com/services/maker_webhooks/settings). Thanks to this [site](https://medium.com/glitch/how-to-trigger-multiple-applets-in-ifttt-5877860a76af). I was looking for it all over the places haha as it was not very obvious to look for in IFTTT.com. (I am planning on writing a blog with full instruction on how to do it, stay tuned. Will be posted on https://poanchen.github.io)

### Development environment
Tested on Ubuntu 14.04.X LTS server but theoretically should work on OS X as well.

### Usage
You can simply clone this repo to get the full code. Next, you want to change the config file in order to work with your own settings,
```
import os
config = {}

# config for your site
config["website"] = "https://poanchen.github.io" # change this to be the site that you would like to check

# config for your ifttt
config["iftttApiEndpoint"] = "https://maker.ifttt.com/trigger/%s/with/key/%s"
config["iftttEventName"] = "add_alexa_rank" # change this to your webhook event name
# you should be able to get your IFTTT maker key from the IFTTT webhook site
config["iftttApiMakerKey"] = os.environ['IFTTTAPIMAKERKEY'] 

# config for alexa site
config["alexaCli"] = 10
config["alexaApiEndpoint"] = "http://data.alexa.com/data"
```
As you can see, I posted my code on to GitHub. As a result, I do not want to share my IFTTT maker API key to the whole world. Instead, I saved it as an environment variable, only accessble in the server. (so that it will be save-ish)

In case you were wondering how to set that up, check out my .env.example file,
```
# Set these environment variables
# For Windows, go to https://superuser.com/questions/949560/how-do-i-set-system-environment-variables-in-windows-10
# For Mac OSX, go to https://stackoverflow.com/questions/7501678/set-environment-variables-on-mac-os-x-lion
# For Linux, go to https://www.cyberciti.biz/faq/set-environment-variable-linux

export IFTTTAPIMAKERKEY=XXXXXXXXXXXXXXXX
```
Now, all you need to do is to hook this script up with a cron job and you are done.

This is how I set up my cron job.
```
00 9 * * * cd /path/to/the/add-alexa-rank-ifttt; python getAlexaRank.py >> add-alexa-rank.log; 
date >> add-alexa-rank.log;
```
This cron job will run the script once a day at 9am sharp. It will also record any log to the add-alexa-rank.log for debugging purposes.
