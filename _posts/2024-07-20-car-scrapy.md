---
title: Car Scrapy 
date: 2024-07-20 12:00:00 -0400
categories: [Projects, Coding, Python]
tags: [projects, coding, python, web scraping, selenium, beautiful soup]     # TAG names should always be lowercase
---

![Image Courtesy of Mecum Classic Car Auction ](assets/car-scrapy/carAuctionClassic.png){: width="1200" height="630" }


# What is Car Scrapy and why do I need it? 

I've been working on a project that helps me out at work. I work as a wholesaler for a car auction. Once we perform our inspections, our dealers set their 'Floor Plans' (lowest price they are willing to take) and then launch the auctions to be bid on. 

Not every auction results in a sale, some bids are off by thousands of dollars from where our clients need to be. However, there are an equal number of auctions that come just a few hundred dollars short of the floor price. 

That's what this program is supposed to help with. Finding all the auctions that come just millimeters away from transitioning into a sale. I need a way to quickly,  easily, and totatlly see all the auctions that we have ran in the previous month that have not resulted in a sale. Currently, with the way our company is set up, we have to manually search every individual auction by VIN or by selling dealership. This is incredibly tedious and we can only see one vehicle at a time. That's where car_scrapy2 comes in. It is a Selenium based web-scraper with the goal of extracting the info I need and present it in a human readable format. 

Link to the Github Repository: 
[https://github.com/hse97/car_scrapy](https://github.com/hse97/car_scrapy)

Here's a quick rundown of how it all works: 

## The  Setup

The projects leverages several python libraries: 

* Selenium: Used for web interactions. Such a powerhouse of a program that mimics human navigation through the web. 

* Pandas: Processes the data collected by Selenium to be exported into a CSV.  

* BeautifulSoup: For handling HTML and navigating to specific information on the webpage.

![Import Code](assets/car-scrapy/importsCode.png){: width="600" height="315" }

Breaking this down line by line:  

`import csv` and 
`import pandas as pd` to handle and process reading and writing CSVs. 

`from selenium import webdriver` is importing Selenium. It's the main driving force behind this project, as it's a very powerful tool for automating interactions through a web browser. We'll discuss why I chose this path in a bit. 

`webdriver` manages the browswer interactions

`By` allows us to search "By ID" or "By XPATH"

`WebDriverWait` is just letting us wait for elements to load

`expected_conditions (EC)` defines common conditions when using Wait

`TimeoutExceptions and NoSuchElementExceptions` handle cases where the page doesn't load or what we're looking for is not present on the page 

`Keys` lets us simulate keyboard presses 

`from bs4 import BeautifulSoup` is a pretty well known library for handling and parsing HTML code when web scraping. 

`import time` just the standard Python time library 


## File Path and Data Storage 

From our internal site, I am allowed access to a CSV of every auction ID that was created and the associated vehicle and VIN. However, this CSV does not include floor pricing or bid history. If it did, this project would be trivial. 

Instead I need to take the data from this provided CSV, in this case we used VIN since we reuse Auction IDs but the VIN of a vehicle is immutable. 

![CSV File Path](assets/car-scrapy/csvFilePath.png){: width="600" height="315" }

I simply have a directory within the project folder that holds the downloaded CSV from my internal company's website as well as a directory that will hold the combined CSV data that I scrape from the website. 


## Navigating to the Dashboard 

This function is primarily just used to open the browser and take us to the login-page, while ensuring that the page loads properly. 

![Nav. to Dashboard](assets/car-scrapy/naviToDash.png){: width="600" height="315" }


It gets passed both the Web Driver as well as a set URL pre-defined that links to my company's dashboard. 

`driver.get(url)` navigates to the specific URL of my company's internal dashboard. 

`WebDriverWait` waits 60 seconds for an element on the page that is a search field with an element ID of 'filterVin'. I chose 60 seconds because this just takes me to the dashboard. The program waits while I manually login. The reason why I have to manually login is because my company, thankfully, uses 2FA with Okta which requires a unique token to be granted access to the dashboard. This wait allows me to login and get passed 2FA. 60 seconds is enough time for that, and if I were to walk away the program would gracefully close. 

`TimeoutException` just handles a situation where the page doesn't load. Either for it being down or connectivity issues. 

## Manual Login 

Due to the adforementioned two-factor autenthication, the script needs to pause to allow a person to login with their details. This is a huge annoyance, as it prevents any potential for automatically updating our list without at least some human intervention. However, as it is a security issue, I understand why 2FA is necessary. And because of this 2FA, I feel comfortable sharing this project publically as the data is secure. No one who shouldn't have access to our internal data will be able to even with this program. 

![Manual Login](assets/car-scrapy/manualLogin.png){: width="600" height="315" }

`input()` is the dumbest way to implement a pause into a program, but since I am the only one who is going to be running this, I don't care. It works. It's dumb, it's not good code, but it works. Sue me. 

## Search By Vin 

An early issue I ran into with this was searching by Auction ID. I genuinely did not know that we re-use our internal Auction IDs. Initially this program used Aucion ID to search for cars, but I found out very quickly that I was pulling data from years ago rather then within the previous month. And cars at dealerships I know they would not sell. 

To get around this I used a vehicles VIN to find the auction history. A vehicle has a set VIN that will never change, and because of this it is easy to find the exact car I'm looking for.

![Search by Vin](assets/car-scrapy/searchByVin.png){: width="600" height="315" }

This function is passed the Web Driver, the VIN, Selling_dealership, and vehicle for organization and human legibility reasons. 

`search_field.clear()` clears the VIN search field 

`search_field.send_keys(vin)` pastes the VIN pulled from the given CSV into the search field 

`search_button.click()` actually clicks the search button 

`no_sale_links` just checks if there is a hyperlink on the page called 'No Sale'. This is used to check if an auction for the vehicle even exists. The given CSV doesn't discriminate against cars with auctions and cars that were simply put into our system and never launched. Since we are only looking for cars with an auction, this variable acts as a check to make sure the VIN/Vehicle has an associated auction. If there is a hyperlink that says 'No Sale' present on the page, then we begin click_and_scrape. 


## Click and Scrape 

Now we're finally at the point where the program can do its thing. We're passed the 2FA-Login, we have found the auction associated with the given VINs, and we're good to go onwards. Oh wait. The information I need is in a popup modal and not just displayed on screen as plain text. Awesome. 

This is where the program really got difficult. I was expecting the data to be just displayed on screen but unfortunately it's hidden behind a popup modal which doesn't load the data I need until the popup modal is loaded. 

This is why I needed to use Selenium. It seemed overkill to me at first because I was sure I would just be able to use BeautifulSoup to parse the website entirely and extract what I need from that. But because it is hidden, I needed a way to interact with hyperlinks on the page. Specifically a hyper-link that says 'No Sale'. Selenium was the most versatile and easy to implement solution I could find.

![Click and Scrape](assets/car-scrapy/click_and_scrape.png){: width="600" height="315" }

The function is passed the Web Driver, the no_sale_link hyper text location, and provided VIN, seller, and vehicle. 

`no_sale_link.click` clicks on the 'No Sale' hyperlink to get the popup modal to appear 

`WebDriverWait` simply waits a second for the elements to load. Once the element is loaded entirely it goes on. 

`popup_html` and `soup` are used to grab and hold the inner HTML of the modal popup and Soup just holds the parsed HTML. 

`orig_closing_price` searches the parsed HTML for the highest bid we have in our history for a given auction and saves it 

`floor_price` does the same for the seller's floor pricing. 

`combine_data()` recombines the VIN, Vehicle, Seller, Floor Price, and Orig. Closing Price/Highest Bid.

`time.sleep(1)` since this is a webpage connected over the internet these wait commands are to ensure that the page is fully loaded. 

`driver.find_element(By.CSS_SELECTOR, 'body').send_keys(Keys.ESCAPE)` Okay this I have to rant about for a second. I spent hours trying to figure out a way to close the popup modal. Iniitally I tried to just use Selenium to click on the close button, there are two of them. For whatever reason, I could never get it to work. No matter how much wait time I would give the program, it would always try to click instantly, before the popup was even loaded. And it would never work. I spent hours trying to figure out what I was doing wrong. And I couldn't. 

Well it turns out you can just close the popup by pressing escape. Found that out and just said screw it that's what we're doing. And it works. Again, I am going to be the only one who runs this I don't really care if it's not proper it works. 

But now we have all the data I needed. The program launches a browswer, takes me to the login in, waits for me to login because of 2FA/Security reasons, and then automatically searches by VIN of every vehicle I need, and then scrapes the information I needed. Now we just need to combine and save it all. 

## Combing and Saving 

Combining the data is very easy, especially since we're ouptutting it as a CSV. Just simply appending all the data together works. 

![Combine Data](assets/car-scrapy/combineData.png){: width="600" height="315" }

Saving the data is also easy. Just using the CSV library from earlier to save the previously appended data as an actual CSV file. 

![Save Combined Data](assets/car-scrapy/saveCombinedData.png){: width="600" height="315" }

## Main Function 

And now to orchestrate the entire process!!!

![Main Function](assets/car-scrapy/carscrapyMain.png){: width="600" height="315" }

Here were actually initialize the webdriver, in this case I use FireFox exclusively so that's what I used for the script. Makes giving it to my coworkers to run kinda difficult, but if they can't figure it out, that's on them. 

It starts with a simple if not statement, because if I can't even get to the dashboard to login then the entire program will crash. This is simply a graceful check for that event. 

`df = pd.read_csv()` just uses Pandas to extract information from the CSV provded by my internal website. 

I then iterate over df and pull specific data that it pulled from the CSV. It grabs the VIN, Selling Dealership, and Vehicle. And then it calls search_by_vin and passes all that information to go on a wild ride. 

Once that process is complete it saves, quits the driver, and then we are golden!!! 

## Conclusion 

Working on the Car Scrapy project has been a real eye-opener. I learned a ton about web scraping, especially the tricky parts like dealing with dynamic content hidden behind popups. Using Selenium for browser automation and BeautifulSoup for parsing HTML turned out to be the perfect combo, letting me automate a task that used to take forever.

This project has been a game-changer for my job as a car auction wholesaler. Instead of manually searching through each auction, I can now quickly find the ones that almost sold but just missed the mark. This saves me hours of tedious work and makes sure I don't overlook any potential sales.

All in all, Car Scrapy has made my workflow way more efficient and showed me how powerful coding can be in solving real-world problems. It's been awesome seeing how automation can make such a big difference, and I'm pumped to keep building and improving projects like this that make my life easier.
