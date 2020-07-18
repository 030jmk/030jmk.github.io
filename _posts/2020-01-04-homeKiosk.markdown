---
layout: post
title:  "Public Transport Departures"
date:   2020-01-04 12:00:00 +0200
categories: [bots and scripts]
---

Annoyed by very irregular departure times of a specific bus line run by the local public transportation company I wrote a python script that displays delays on a html page when run. The script can be run on demand or with a time-based job scheduler such as cron and can be combined with a web server.

Information is called through an API provided by the HaCon (Siemens) Fahrplan-Auskunfts-System (HAFAS) and requires an API key. Bootstrap is the CSS framework chosen for the generation of the html file in order to be easily viewable on mobile and desktop devices.

```
import requests
from json import loads
import datetime

access_ID="KOP-super-secret-access-ID-123"

#testing
goslarer_ufer='900001155'
zoo='900023201'
griebnitzsee='900230003'
turm_str_b ='900003174'
zehlendorf_s='900049201'


STOP=goslarer_ufer

demo_link= "http://demo.hafas.de/openapi/vbb-proxy/departureBoard?extId={}&format=json&accessId={}".format(STOP,access_ID)

js_data = loads(requests.get(demo_link).text)

def stop_name():
    try:
        stop_name = str(js_data['Departure'][0]['stop'])
    except:
        stop_name = str('')
    return stop_name

def clean_name(i):
    '''
    clean up the spaces in the name of the lines given the index of the connection.
    '''
    if js_data['Departure'][i]['name'][:6] == '      ':
        return str(js_data['Departure'][i]['name'].split('      ')[-1])
    else:
        return str(js_data['Departure'][i]['name'])

def dt(i):
    '''
    returns nominal date and time of the provided index connection
    '''
    year,month,day=(js_data['Departure'][i]['date'].split('-')[:3])
    hour,minute = js_data['Departure'][i]['time'].split(':')[:2]
    return datetime.datetime(int(year),int(month),int(day),int(hour),int(minute),second=0)

def rt_dt(i):
    '''
    returns actual or predicted date and time of the provided index connection
    '''
    try:
        year,month,day=(js_data['Departure'][i]['rtDate'].split('-')[:3])
        hour,minute = js_data['Departure'][i]['rtTime'].split(':')[:2]
        return datetime.datetime(int(year),int(month),int(day),int(hour),int(minute),second=0)
    except:
        return dt(i)

def delta_time(i):
    '''
    returns the time delta between the nominal and predicted departure times given the index conection
    '''
    if dt(i)>rt_dt(i):
        return ("{}{}".format('-',dt(i)-rt_dt(i)))
    if dt(i)<rt_dt(i):
        return "{}{}".format('+',rt_dt(i)-dt(i))
    if dt(i)==rt_dt(i):
        return str("0:00:00")

def footer_out(i):
    if str(delta_time(i)) =="0:00:00":
        return str('On time')
    else:
        if str(delta_time(i))[0]=='-':
            return str('Sooner: {} min'.format(int(str(delta_time(i).split(':')[1]))))
        if str(delta_time(i))[0]=='+':
            return str("Delay: {} min".format(int(str(delta_time(i)).split(':')[1])))

    
'''    
for i in range(len(js_data['Departure'])):
    name = clean_name(i)
    direction = js_data['Departure'][i]['direction']
    planned = str(dt(i))
    actual = str(rt_dt(i))
    delta = footer_out(i)

    
    print('Linie:\t\t{}\nRichtung:\t{}\nSoll-Abfahrt:\t{}\nIst-Abfahrt:\t{}'.format(name,direction,planned,actual))
    print('Delta:\t\t{}'.format(delta_time(i)))
    print('info:\t\t{}'.format(delta))

    print('=========')
'''



html = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <meta http-equiv="refresh" content="60" />
    <title>VBB</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">

</head>
<body style="margin-top: 30px;">
    <div class="container" id="container">
    <div class="row">
    {}
    </div>
    </div>


    <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
</body>
</html>
"""

cards=[]

card='''
<div class="col-4">
<div class="card" style="margin-top: 10px;">
            <div class="card-header">
                <h5 class="card-title text-muted" id="name">{}</h5>
                <h6 class="card-subtitle mb-2 text-muted small" id="direction">{}</h6>
            </div>
            <div class="card-body">
                <h6 class="card-subtitle mb-2 text-muted small text-center">Plan</h6>
                <p class="card-title text-muted text-center" id="planned">{}</p>
              
              <h3 class="card-title text-center" id="actual">{}</h3>
              <h6 class="card-subtitle mb-2 text-muted small text-center">Actual</h6>
              
            </div>
            <div class="card-footer">
                <div class="small text-muted" id="footer">{}</div>
    
              </div>
          </div>
          </div>'''

for i in range(len(js_data['Departure'])):
    cards.append(str(card.format(clean_name(i),js_data['Departure'][i]['direction'],str(dt(i)).split(' ')[-1],str(rt_dt(i)).split(' ')[-1],str(footer_out(i)))))

html_out = (html.format(' '.join(cards)))

with open("output.html", "w", encoding='utf-8') as file:
    file.write(str(html_out))
```

or
[direct link to the python script](https://gist.githubusercontent.com/030jmk/b9706ab802a5b64a04c804f2c4b1393f/raw/529370b051b2ee7687c6602d2354d21ac402e3b5/VBB.py)

