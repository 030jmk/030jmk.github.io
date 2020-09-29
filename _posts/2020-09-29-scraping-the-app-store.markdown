---
layout: post
title:  "Scraping the Apple App Store"
date:   2020-09-29 12:00:00 +0200
categories: [bots and scripts]
---

WIP

```python
import requests,json
import pandas as pd

list_of_json=[]

def get_app_meta_data(app_id):
    headers = {'Host': 'amp-api.apps.apple.com',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:81.0) Gecko/20100101 Firefox/81.0',
        'Accept': 'application/json',
        'Accept-Language': 'de,en-US;q=0.7,en;q=0.3',
        'Accept-Encoding': 'gzip, deflate, br',
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
        'Authorization': 'Bearer eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IkNSRjVITkJHUFEifQ.eyJpc3MiOiI4Q1UyNk1LTFM0IiwiaWF0IjoxNjAwNzI2NjI2LCJleHAiOjE2MDM3NTA2MjZ9.8U3zH4t-MjPFBgOM-jITZzRhPAiITD1K5aBXCAVn0EqiTIWNU7vQsS44yS1aYAUIYA8jND8t2oUwZwGiZZ6onA',
        'Origin': 'https://apps.apple.com',
        'DNT': '1',
        'Connection': 'keep-alive',
        'Referer': 'https://apps.apple.com/de/app/id'+str(app_id),
        'Pragma': 'no-cache',
        'Cache-Control': 'no-cache',
        'TE': 'Trailers'}
    url='https://amp-api.apps.apple.com/v1/catalog/de/apps?ids={}&platform=web&additionalPlatforms=appletv%2Cipad%2Ciphone%2Cmac'.format(app_id)
    
    try:
        response = requests.get(url,headers=headers,allow_redirects=True).text
        #print(response)
        json_response = json.loads(response)['data'][0]
        list_of_json.append(json_response)
        if json_response == []:
            print(requests.get(url,headers=headers,allow_redirects=True))
    except:
        print('error.')

```


```python
app_id='624517227'
get_app_meta_data(app_id)

# sample app IDs:
#    991789414
#    1053408163
#    1126338982
#    624517227
```


```python
# print app ID, app name, and app developer

for i, app in enumerate(list_of_json):
    try:
        print(app['id'],app['attributes']['name'],app['attributes']['artistName'],sep='---')
    except:
        pass
```

    624517227---Polizei Brandenburg---Polizei Brandenburg
    


```python
# output contents of the requested data
list_of_json
```




    [{'id': '624517227',
      'type': 'apps',
      'href': '/v1/catalog/de/apps/624517227',
      'attributes': {'artistName': 'Polizei Brandenburg',
       'familyShareEnabledDate': '0001-04-23T00:00:00Z',
       'url': 'https://apps.apple.com/de/app/polizei-brandenburg/id624517227',
       'isIOSBinaryMacOSCompatible': False,
       'reviewsRestricted': False,
       'supportsOcelot': False,
       'deviceFamilies': ['iphone', 'ipod'],
       'isPreorder': False,
       'platformAttributes': {'ios': {'requires32bit': False,
         'artwork': {'width': 1024,
          'height': 1024,
          'url': 'https://is2-ssl.mzstatic.com/image/thumb/Purple118/v4/d0/c9/43/d0c94316-3b8e-ca45-b81b-1699cd3e192a/AppIcon-1x_U007emarketing-0-0-GLES2_U002c0-512MB-sRGB-0-0-0-85-220-0-0-0-3.png/{w}x{h}{c}.{f}',
          'bgColor': '20274d',
          'textColor1': 'ffffff',
          'textColor2': 'ccd0e0',
          'textColor3': 'd2d3db',
          'textColor4': 'aaaec3'},
         'hasMessagesExtension': False,
         'hasPrivacyPolicyText': False,
         'supportedLocales': [{'name': 'Deutsch', 'tag': 'de-DE'}],
         'supportsGameController': False,
         'releaseDate': '2013-03-27',
         'hasSafariExtension': False,
         'minimumMacOSVersion': '10.16',
         'externalVersionId': 827147239,
         'editorialArtwork': {},
         'requirementsString': 'Erfordert iOS\xa09.3 oder neuer. Kompatibel mit iPhone, iPad und iPod\xa0touch.',
         'isDeliveredInIOSAppForWatchOS': False,
         'bundleId': 'de.brandenburg.PolBB',
         'hasInAppPurchases': False,
         'isAppleWatchSupported': False,
         'isStandaloneForWatchOS': False,
         'supportsPassbook': False,
         'copyright': '© Zentraldienst der Polizei Brandenburg',
         'requiresGameController': False,
         'isHiddenFromSpringboard': False,
         'isGameCenterEnabled': False,
         'minimumOSVersion': '9.3',
         'hasFamilyShareableInAppPurchases': False,
         'runsOnIntel': False,
         'is32bitOnly': False,
         'isStandaloneWithCompanionForWatchOS': False,
         'seller': 'Zentraldienst der Polizei Brandenburg',
         'isSiriSupported': False,
         'requiresRosetta': False,
         'languageList': ['Deutsch'],
         'runsOnAppleSilicon': False,
         'offers': [{'buyParams': 'productType=C&price=0&salableAdamId=624517227&pricingParameters=STDQ&pg=default&appExtVrsId=827147239',
           'type': 'get',
           'priceFormatted': '0,00\xa0€',
           'price': 0.0,
           'currencyCode': 'EUR',
           'assets': [{'flavor': 'iosSoftware', 'size': 24030208}]},
          {'buyParams': 'productType=C&price=0&salableAdamId=624517227&pricingParameters=SWUPD&pg=default&appExtVrsId=827147239',
           'type': 'update',
           'priceFormatted': '0,00\xa0€',
           'price': 0.0,
           'currencyCode': 'EUR',
           'assets': [{'flavor': 'iosSoftware', 'size': 24030208}]}]}},
       'hasEula': False,
       'userRating': {'value': 4.5,
        'ratingCount': 77,
        'ratingCountList': [4, 2, 5, 8, 58],
        'ariaLabelForRatings': '4.5 Sterne'},
       'contentRatingsBySystem': {'appsApple': {'name': '12+',
         'value': 300,
         'rank': 3,
         'advisories': ['Selten/schwach ausgeprägt: Szenen mit erotischen Anspielungen',
          'Selten/schwach ausgeprägt: realistisch dargestellte Gewalt',
          'Selten/schwach ausgeprägt: Gebrauch von Alkohol, Tabak oder Drogen bzw. Verweise hierzu']}},
       'name': 'Polizei Brandenburg',
       'sellerLabel': 'Anbieter',
       'supportsArcade': False,
       'genreDisplayName': 'Nachrichten',
       'isFirstPartyHideableApp': False,
       'usesLocationBackgroundMode': False},
      'relationships': {'developer': {'href': '/v1/catalog/de/apps/624517227/developer',
        'data': [{'id': '624517350',
          'type': 'developers',
          'href': '/v1/catalog/de/developers/624517350'}]},
       'genres': {'href': '/v1/catalog/de/apps/624517227/genres',
        'data': [{'id': '6009',
          'type': 'genres',
          'href': '/v1/catalog/de/genres/6009',
          'attributes': {'name': 'Nachrichten',
           'parentId': '36',
           'url': 'https://itunes.apple.com/de/genre/id6009',
           'parentName': 'Mobile Software Applications'}},
         {'id': '6002',
          'type': 'genres',
          'href': '/v1/catalog/de/genres/6002',
          'attributes': {'name': 'Dienstprogramme',
           'parentId': '36',
           'url': 'https://itunes.apple.com/de/genre/id6002',
           'parentName': 'Mobile Software Applications'}}]}}}]

