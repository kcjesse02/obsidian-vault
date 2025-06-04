#### Challenge Description
The US Coast Guard (USCG) recorded an unregistered signal over 30 nautical miles away from the continental US (OCONUS). NSA is contacted to see if we have a record of a similar signal in our databases. The Coast guard provides a copy of the signal data. Your job is to provide the USCG any colluding records from NSA databases that could hint at the object’s location. Per instructions from the USCG, to raise the likelihood of discovering the source of the signal, they need multiple corresponding entries from the NSA database whose geographic coordinates are within 1/100th of a degree. Additionally, record timestamps should be no greater than 10 minutes apart.

- find coordinates matching within 01/100 precision.
- No greater than 10 minutes apart
	- no individual blips

##### Intro
- we are given a `database.db`file for the db of signals
	- a database file common in embedded devices and phones
	- can be examined with SQLite
- and a `USCG.log` file for the thing we are supposed to search for
	- in a JSON format (I think)

### My Solution(??)
- Use sqlite to query the table! (databases memoment)
	- there is a python api! We can just write a python script
	- https://docs.python.org/3/library/sqlite3.html#
- Use the python Json library to convert the log file into a python dictionary
- extract the longitude and latitude and create upper and lower bounds

##### A Roadblock
- I tried to filter for correct long and lat values in the database. This proved to be difficult to debug. I then just got the values from the 
- Another thing: I think floats need to be the same precision to be equal in python. Nothing came up when I rounded the upper and lower bounds to two decimal places

### Code
`db_search.txt`
```python
import sqlite3
import json

conn = sqlite3.connect("database.db")
cur = conn.cursor()

fp = open("USCG.log")
uscg = json.load(fp)

# setting up the searching parameters
lat = float(uscg["coordinates"][0]["latitude"])

lat_upper = lat+0.1
lat_lower = lat-0.1
print(f"{lat_upper} {lat_lower}")

long = float(uscg["coordinates"][0]["longitude"])

long_upper = long+0.1
long_lower = long-0.1
print(f"{long_upper} {long_lower}")


res = cur.execute(f'''SELECT L.id, recDate, recTime, longitude, latitude
                        FROM location L 
                        JOIN timestamp T ON L.id = T.id 
                  ''')
records = res.fetchall()

for x in records:
    xLong = float(x[3])
    #it turns out that just searching for longitude was enough to get the correct records
    if (xLong >=long_lower and xLong<=long_upper):
        print(x)
```