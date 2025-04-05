# CheckRoyalCaribbeanPrice
A script that checks if you have cheapest price for beverage package, excursions, internet, etc that you have purchased. Finds all purchased packages on your account, no need to enter them yourself. Can also check the price of a cabin if you provide the Royal Caribbean booking URL (no Royal account needed). You need to run this tool manually, inside a cron job (linux), or [task schedular](https://www.windowscentral.com/how-create-automated-task-using-task-scheduler-windows-10) (windows). If you run Home Assistant, an addon is posted in my [addon repo](https://github.com/jdeath/homeassistant-addons) which can be called automatically.

This is not a hack. Developed only with Firefox and python. All the API calls are public and visible in the Firefox inspector. Everything in this code your browser is doing when you log into the Royal Caribbean website.

~~If anyone can figure out how to get the AccountID programatically, please do a PR. I cannot figure that out.~~ Thanks to anonymous for the fix!

There is a free website that does price checks for beverage packages/excursions and does not log into your account. You have to add your packages manually and it will not find special deals exclusive to your account: https://royalpricetracker.com/ . Consider using that for a simpler solution.  

## Install (Recommended, any Operating System, and you can edit code to your liking)
1. Install python3 (3.12 works fine) `https://www.python.org/downloads/`
1. Download the raw files from this repo or `git clone https://github.com/jdeath/CheckRoyalCaribbeanPrice.git`
1. `cd CheckRoyalCaribbeanPrice`
1. `pip install requests Apprise`

## Install (Not Recommended, Windows 11 Only)
1. Download [CheckRoyalCaribbeanPrice.exe](https://github.com/jdeath/CheckRoyalCaribbeanPrice/releases/download/0.3/CheckRoyalCaribbeanPrice.exe) from releases (made with `pyinstaller -F --collect-all apprise --collect-all bs4 CheckRoyalCaribbeanPrice.py`) Note: Python code in repo may be newer than .exe file

## Edit Config File
Edit `config.yaml` and make sure in the same directory as `CheckRoyalCaribbeanPrice.py` or `CheckRoyalCaribbeanPrice.exe`
```
accountInfo:
  - username: "user@gmail.com" # Your Royal Caribbean User Name
    password: "pa$$word" # Your Royal Caribbean Password
cruises:
  - cruiseURL: "https://www.royalcaribbean.com/checkout/guest-info?sailDate=2025-12-27&shipCode=VI&groupId=VI12BWI-753707406&packageCode=VI12L049&selectedCurrencyCode=USD&country=USA&cabinClassType=OUTSIDE&roomIndex=0&r0a=2&r0c=0&r0b=n&r0r=n&r0s=n&r0q=n&r0t=n&r0d=OUTSIDE&r0D=y&rgVisited=true&r0C=y&r0e=N&r0f=4N&r0g=BESTRATE&r0h=n&r0j=2138&r0w=2&r0B=BD&r0x=AF&r0y=6aa01639-c2d8-4d52-b850-e11c5ecf7146"
    paidPrice: "3833.74"
  - cruiseURL: "https://www.royalcaribbean.com/checkout/guest-info..." # Can have as many URLS and price paid as you want
    paidPrice: "1234.99"   
apprise:  # Optional, see https://github.com/caronc/apprise, can have as many lines as you want.
  - url: "mailto://user:password@gmail.com"
  - url: "whatsapp://AccessToken@FromPhoneID/ToPhoneNo"
```

If you only want to check cruise addons (drink packages, excursions, etc) and do not want emails or check cruise prices, the config file is simpler. Start with this to see if works:
```
accountInfo:
  - username: "user@gmail.com" # Your Royal Caribbean User Name
    password: "pa$$word" # Your Royal Caribbean Password
```

If you only want to check cruise price and do not want emails, the account information is not needed by the tool. Config file can look like this:

```
cruises:
  - cruiseURL: "https://www.royalcaribbean.com/checkout/guest-info?sailDate=2025-12-27&shipCode=VI&groupId=VI12BWI-753707406&packageCode=VI12L049&selectedCurrencyCode=USD&country=USA&cabinClassType=OUTSIDE&roomIndex=0&r0a=2&r0c=0&r0b=n&r0r=n&r0s=n&r0q=n&r0t=n&r0d=OUTSIDE&r0D=y&rgVisited=true&r0C=y&r0e=N&r0f=4N&r0g=BESTRATE&r0h=n&r0j=2138&r0w=2&r0B=BD&r0x=AF&r0y=6aa01639-c2d8-4d52-b850-e11c5ecf7146"
    paidPrice: "3833.74" 
```

## Get Cruise URL (Optional)
1. Go to Royal Caribbean and do a mock booking of the room you have, with the same number of adults and kids
1. Select a cruise and Select your room type/room and complete until they ask for your personal information.
1. At this point, you should see a blue bar at the bottom right of webpage with a price
1. Copy the entire URL from the top of your browser into the cruiseURL field. 
1. Put the price you paid in the paidPrice field
1. Run the tool and see if it works
1. You can add multiple cruiseURL/paidPrice to track multiple cruises or rooms on a cruise
1. If it is lower than you paid for and before final payment date, call your Travel Agent or Royal Caribbean (if you booked direct) and they will reduce the price. Be careful, you will lose the onboard credit you got in your first booking, if the new booking does not still offer it!
1. Only tested on a Garuntee Cabin, where mutliple of same cabin exist for purchase. If you use for a specific room (like "You Pick the Room"), if that room is booked the code will notify you. Have not tried to figure out if I can scan for an available room in the same class. Suggest picking a GTY room for this code and use that as an indicator if your "pick you room" room changed, even though it may not correlate. 
1. If you only want to check the cruise prices, you do not need to have your `accountInfo` and/or `apprise` in your config file, as they are not nessesary.
   
## Apprise (Optional)
1. Review documentation for apprise at: https://github.com/caronc/apprise
1. 99% of people probably have gmail, so you can use the default already setup in the sample config.yaml
1. This will send you an email only if there is a price drop
1. Change username to your gmail username
1. Change password to your gmail password. If you use 2-factor authentication, you need to generate an app password. You cannot use use normal password
1. Documentation to generate ann app password for gmail is here: https://security.google.com/settings/security/apppasswords
1. You can delete the whatsapp line, that is included so you know how to add other services. You can also add more lines for a additional gmail accounts.

## Run
1. `python CheckRoyalCaribbeanPrice.py` (recommended) or `CheckRoyalCaribbeanPrice.exe`
1. It will indicate if you should rebook or if you have the best price
1. It will also tell you if the price has gone up since you purchased (do not rebook in that case!)
1. If you setup apprise, it will notify you via your prefered method(s) if you should rebook

## Output
Will output information on your purchases
```
CONFNUM1: You have the best price for Chacchoben Ruins Exclusive Drive of: 122.99
CONFNUM1: You have the best price for Tabyana Beach Break of: 66.99
CONFNUM2: You have the best price for Deluxe Beverage Package of: 67.99
CONFNUM2: 	Price of Deluxe Beverage Package is now higher: 72.99
CONFNUM2: You have the best price for VOOM SURF + STREAM Internet Package of: 17.99
2025-12-27 VI OUTSIDE 4N: You have best Price of 3612.12 
```
If any of the prices are lower, it will send a notifcation if you setup aprise.

## Automating
1. Linux: Put in a cron job, if running in linux, I am sure you know how!
1. Home Assistant: Use directions in my [repo](https://github.com/jdeath/homeassistant-addons/tree/main/royalpricecheck)
1. Windows: Use windows task schedular
1. Create a basic task. Select a daily trigger, suggest a little before you wake up
1. Action, select "Start a Program"
1. In "Program/script" Select the CheckRoyalCaribbeanPrice.exe file you download from here. Make sure the config.yaml is in same directory as .exe
1. In "Start in (optional)" enter the directory of the .exe/.yaml (you can copy the "Program/script" field, paste it, and remove the CheckRoyalCaribbeanPrice.exe)
1. After clicking finish, you can right click on task, go to triggers, and add more times to trigger the script. Suggest a time right before you get home from work. Twice a day should be sufficient
1. Ensure apprise notifications are working, because the window will close automatically after run.
   
## Notes
1. Confirm price is still lower on website, because it could have gone up since running this bot
1. You need to first cancel your beverage package, shore excursion, internet, etc
1. Wait about 10s
1. Rebook at the lower price.
1. It takes about a week for Royal to refund your credit card, but they charge you new price right away!
1. Enjoy youtube videos on cruising from: `https://www.royalcaribbeanblog.com/`, `https://www.youtube.com/royalcaribbeanblog` and `https://www.youtube.com/@LifeWellCruised`
1. Maybe Matt or Ilana will feature this tool in a video !

# Issues
1. I am probably not going to update much, unless I find an issue. I can only see my own account purchases.
1. Only checks adult prices, if only have child prices in an order it may not work. I don't have kids, so can not check.
1. It should handle orders made by other people in your party (works in my partner's account for what I booked)
1. May not handle all orders correcty.
1. Prices of internet and beverage are per day, this code divides by the length of your cruise. If you buy a partial package, this logic may not work correctly.
1. If other prices are per day, it will not work. Let me know if other daily purchases are not calculating correctly.
1. Please double check that the price is lower before you rebook! I am not responsible if you book at a higher price!
