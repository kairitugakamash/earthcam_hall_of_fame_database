from datetime import datetime
import json 
import numpy as np
import os
from os import listdir
from PIL import Image
import random
import re
import requests
import shutil
import sys
import yaml 
from time import sleep

cams=['Algonquin', 'Amsterdam', 'Bermuda', 'Capetown', 'Corsica', 'Edmonton', 'Gibraltar','Mauritius', 'Mountkilimanjaro' ,'Saipan', 'Thailand']
city = input(f"Select one Earthcam from the list given :\n {cams}\n\n")

# Enter Earhcam name -- case insensitive

if city.lower() not in [x.lower() for x in cams]:
    print("Enter a valid city")
else:
    city_name = city.lower()
    #print(city_name)
    
    #date_accessed = now
    main_path = f"C:/Users/jane.waweru/python/Web Scraping/earthcam/{city}/"
    #main_path = f"C:/Users/jane.waweru/python/Web Scraping/earthcam/{city}-{now}/"
    photos_path = f"C:/Users/jane.waweru/python/Web Scraping/earthcam/{city}/{city}_photos/"
    metadata_path = f"C:/Users/jane.waweru/python/Web Scraping/earthcam/{city}/{city}_metadata/"
    bad_images = f"C:/Users/jane.waweru/python/Web Scraping/earthcam/{city}/incomplete_image/"

    # Check whether the specified path exists or not
    isExist = os.path.exists(main_path)

    #printing if the path exists or not
    #print(isExist)

    if not isExist:
        # Create a new directory because it does not exist
        os.makedirs(main_path)
        os.makedirs(photos_path)
        os.makedirs(metadata_path)
        os.makedirs(bad_images)
        print("The new directory is created!")

counter = 0 #if scraping gets cut off in the middle, restart by changing counter to #imgs already scraped
url = "https://www.earthcam.com/cams/common/gethofitems.php?hofsource=com&tm=ecn&camera=" + city_name + "&start=" + str(counter) + "&length=21&ec_favorite=0&cdn=0&callback=onjsonpload"
result = re.search('\[(.*?)\]',"[some_tmp_start]" )
while(result.group() != ""):
    r = requests.get(url)
    toParse = str(r.content)
    #print(toParse)
    result = re.search('\[(.*?)\]', toParse)
    toIter = result.group(1)[:-1]
    #pattern = r'doesn\'t'
    clean_string = toIter.replace('\\\'', '')
    toIter = clean_string.split('},')
    for img in toIter: 
        img += '}'
        img = img.replace('\\\"', '')
        img = img.replace("\\", "")
        #print(eval(img))
        #print(img[219:])
        img_dict = yaml.load(img)
        image_url = img_dict["image_source"]
        image_url = image_url.replace('\\','')
        r = requests.get(image_url)
        with open(photos_path +image_url[-20:],'wb+') as outfile:
            outfile.write(r.content)
        image_date = img_dict["date_added_plain"]
        with open(metadata_path+ image_url[-20:-3]+"txt" , 'w+') as metaoutfile: 
            metaoutfile.write(image_date)
        #print(image_url)
        print(image_date)
    counter += 21
    print(counter, " images have been saved")
    url = "https://www.earthcam.com/cams/common/gethofitems.php?hofsource=com&tm=ecn&camera=" + city_name + "&start=" + str(counter) + "&length=21&ec_favorite=0&cdn=0&callback=onjsonpload"

# cleaning from earthcam files

for filename in listdir(photos_path):
    with open(os.path.join(photos_path, filename), 'rb') as f:
        check_chars = f.read()[-2:]
        img = np.array(Image.open(photos_path + filename))
        if len(img.shape) !=3 or check_chars != b'\xff\xd9':
            print('Not complete image')
            shutil.move(photos_path + filename, bad_images)
            metadata = filename[:-3] + 'txt'
            shutil.move(metadata_path + metadata, bad_images)
        
print('No more incomplete images present')
