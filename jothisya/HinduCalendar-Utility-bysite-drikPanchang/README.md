Python class and a CLI for various calendar utilities according to Hindu Calendar. It uses drikPanchang.com for querying data.

**Some Features:**

* Finding the Tithi of a particular date
* Querying daily Panchang
* Finding an occurrence of a particular Tithi in the current (or specified) year
* Ability to specify date in both regional or Gregorian format
* Display in regional language
* ICS Calendar Creator (Plotting Janma-Tithi on the Gregorian calendar for the specified year)

Files:
---
* **hindu_calendar.py**

  Core HinduCalendar class.

* **hcal_cli.py**

  Provides a command-line interface for various querying options using HinduCalendar class.

* **ics_creator.py**

  Accepts a `.csv` file containing events and creates a `.ics` file plotting the Tithi of
  birthdays/anniversaries on the Gregorian calendar file thus created is importable in Google Calendar.


Requirements:
---
requests, beautifulsoup4, geocoder, ics

CLI Installation:
---
Download the files into a directory of your choice. Place all the `.py` files beside eachother.
Navigate to the directory.
```
$ pip install requests beautifulsoup4 geocoder ics
$ chmod +x hcal_cli.py ics_creator.py
```

Link it to any location in the path.
```
$ ln -s hcal_cli.py ~/bin/hcal
$ hcal -h
```
```
usage: hcal [-h] [-d date] [-f] [-r] [-m M] [-c] [-y year] [-l] [-s]

Hindu Calendar Utilities

optional arguments:
  -h, --help  show this help message and exit
  -d date     Date in dd/mm/yyyy
  -f          Fresh configuration
  -r          Input regional date
  -m M        Regional method to use
  -c          Find re-occurrence of Tithi of the 'date' in the current year
  -y year     Use with '-c' to specify year
  -l          Display in regional language
  -s          Show config and exit
```

Alternatively, one may run the file using `python3 hcal_cli.py`.

Class Usage:
---
Use `help(HinduCalendar)` in the python console to see detailed help.

```
>>> import datetime
>>> from hindu_calendar import HinduCalendar
>>> cal =HinduCalendar(method='marathi')
>>> cal.find_city('Mumbai')
>>> cal.set_city('1275339', 'Mumbai')
>>> today = datetime.datetime.today().strftime("%d/%m/%Y")
>>> cal.find_date(today)
>>> cal.find_occurrence('15/08/1947', year='2020')
```

CLI Usage:
---
`$ hcal`

The first run will interactively setup initial config. Subsequent runs will show the date object for 'today'

`$ hcal -d dd/mm/yyyy`

Returns the date object for the given date
Other acceptable formats: (dd-mm-yyyy, dd:mm:yyyy, dd.mm.yyyy, dd, dd-mm)

`$ hcal -d dd-mm-yyyy -m hindi`

Specify which regional method to use to query and show the date object.

*Available methods*: marathi, hindi, bengali, tamil, telugu, kannada, gujarati, malayalam

`$ hcal -d dd-mm-yyyy -r`

`-r` flag indicates that the specified date is in regional format.
Although the most natural way of specifying the regional dates is in terms of moon phase, the CLI expects the date to be specified in `dd-mm-yyyy` format.

`dd` - Date index. For a 'Ashtami' from the second half of the month, the date would thus not be `8`, but `23` (15+8).
`mm` - Month index according to the regional index. (e.g. `Chaitra` has index `01` in the North Indian (`hindi`) calendar, while the same month would have index `12` in the Bangali (`bengali`) calendar.
`yyyy` - Year in the regional calendar.

`$ hcal -d dd-mm-yyyy -c`
Finds the *Tithi* on the date `dd-mm-yyyy`, and then returns the date object corresponding to the occurrence of that Tithi in the current year. (Year can be specified with the `-y` flag, to go with the `-c` flag).

ICS Creation:
---

Following is a sample `events.csv` file.

```
date,label,name,method,is_regional,include
15-08-1947,Independence Day,India,marathi,0,1
```

The flag `is_regional` is `1` if the `date` column contains the date in the regional format. This can be useful for plotting festivals.
The flag `include` is used to determine if that row will be processed or not. Value `1` indicates the row will be processed and an event will be created. Value `0` indicates the row will be ignored.

`$ python3 ics_creator.py -i events.csv -o events.ics`

This would go through each entry of `events.csv`, find out what was the *Tithi* on the `date` and then locate the *Tithi* in the current year (or the year specified with `-y` flag).

Ultimately, all these events will be written to a `.ics` file which can be imported in various calendars such as Google Calendar.

About Date Object:
---
Date object is a JSON object (dictionary) containing following entities,

- method: method used to query this object
- language: language being used to query this object
- city: city used to query this object
- ce_date: date in theg regorian calendar (common era)
- ce_datestring: date string
- event: special event according to Hindu calendar (if any)
- regional_date: Hindu date (Tithi) (dd/mm/yyyy)
- regional_datestring: string description of the Tithi
- panchang: panchang entries
- url: URL used to fetch this data
