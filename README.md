# Welcome to preply.com scraper project

## The full source code is private since it's a paid software, contact me for more details

### In this page i will talk about some parts of the code including a sample from the source code.

**Starting from this url** : https://preply.com/en/tutor/77027/

The following code is written in **python 3.6.5** and **Selenium** which is a module for scraping and automating.

![Image of preply.com tutor's profile page](https://raw.githubusercontent.com/zowail/Preply-scraper/master/preply%20profile.PNG)

**Basically the full script is parsing all information of all tuors from preply.com but here i will explain how to parse 1 profile data**

**The script will open google chrome using the chrome driver then visits a tutor profile page 
and then will extract any details "Name, Languages Taught, Languages Spoken, Number of Lessons, ...etc"**

**First import the needed modules**
```python
from time import sleep
from selenium.webdriver.common.keys import Keys
from selenium import webdriver
from parsel import Selector
import csv
import os.path
from collections import defaultdict

```

**Then create the function that parses tutor's name**
```python
def get_tutor_name():
    sel = Selector(text=driver.page_source)
    try:
        tutor_name = sel.css('span[class="ecom-name"]::text').extract_first() 
        tutor_name = tutor_name.strip()
    except:
        tutor_name = "None"
        pass    

    return tutor_name 
```

**Get tutor's origin**
```python
def get_tutor_origin():
    sel = Selector(text=driver.page_source)
    try:
        Origin = sel.css("span.hint.hint--bottom.hint--auto.hint--auto-width::text").extract_first()
        Origin = (str(Origin)).strip()
    except:
        Origin = "None"
        pass    

    return Origin 
```

**Get Languages Taught**
```python
def get_languages_taught():
    try:
        languages_taught_list = []

        languages_taught_web_element= driver.find_element_by_xpath('//*[@id="search"]/div[1]/div[1]/div/div/div[1]/div[2]/ul/li[1]/div[2]')
        languages_taught_string = str( (languages_taught_web_element).text )

        languages_taught_string = languages_taught_string.replace("Teaches", "")

        # if "," is found in the string so we have more than one language
        if "," in languages_taught_string:
            languages_taught_list = languages_taught_string.split(",")

            for i in range(len(languages_taught_list)):
                languages_taught_list[i] = languages_taught_list[i].strip()
                #languages_taught_list[i] = languages_taught_list[i] [ : languages_taught_list[i].find(" ")]
            
            return languages_taught_list

        else:
            languages_taught_string = ( str(languages_taught_string) ).strip()
            #languages_taught_string = languages_taught_string [ : languages_taught_string.find(" ")]

            languages_taught_list.append(languages_taught_string)

            return languages_taught_list
    except:
        languages_taught_list = []
        return languages_taught_list
        pass

```

**Get tutor's languages spoken**
```python
def get_languages_spoken():
    try:

        all_languages_list = []

        # find web element with the text Speaks
        web_element_with_speaks_word = driver.find_element_by_xpath("//*[contains(text(), 'Speaks')]")

        # get the parent of the web element
        parent_of_web_element_with_speaks_word = web_element_with_speaks_word.find_element_by_xpath("..")

        #find all text from the web element
        all_languages_string = parent_of_web_element_with_speaks_word.text

        # remove "Speaks" word
        all_languages_string = all_languages_string.replace("Speaks","")


        if "\n" in all_languages_string:
            # convert the string into list, separate the words by \n
            all_languages_list = all_languages_string.split("\n")

            # remove the "." at the beginning of any language from number 2 to the end
            # and remove the spaces at the beginning and the end if found
            for i in range(len( all_languages_list)):
                if i != 0:
                    all_languages_list[i] = all_languages_list[i][1:]
                    all_languages_list[i] = (all_languages_list[i]).strip()

            # get only the Language name without the level of the language
            for i in range(len( all_languages_list)):
                all_languages_list[i] = (all_languages_list[i]).strip()
                all_languages_list[i] = all_languages_list[i] [ : all_languages_list[i].find(" ")]

            return all_languages_list
        
        # if only one language is found
        else:
            all_languages_string = str(all_languages_string).strip()
            all_languages_string = all_languages_string [ : all_languages_string.find(" ")]

            all_languages_list.append(all_languages_string)

            return all_languages_list

    except:
        all_languages_list = []
        return all_languages_list
        pass

```

**Finally go to the page and use the functions**
```python
driver = webdriver.Chrome('C:\chromedriver\chromedriver.exe')

driver.get("https://preply.com/en/tutor/77027/")

Name = get_tutor_name()

Origin = get_tutor_origin()

Languages_Taught = get_languages_taught()

Languages_Spoken = get_languages_spoken()
```

![Image of VS Code Output](https://raw.githubusercontent.com/zowail/Preply-scraper/master/preply%20profile%20data.PNG)
