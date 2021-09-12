# -*- coding: utf-8 -*-
"""
Created on Fri Nov 24 23:28:14 2017

Module that reads and extract rainfall and wind gusts data from html webpages

@author: TYF
"""

import ConfigParser
import urllib
from bs4 import BeautifulSoup
import datetime as dt

def read_configuration():
    '''Read the configured values by GUI user'''
    Config = ConfigParser.ConfigParser()
    Config.read('heavy_rain_sitrep.ini')
    global past_3_hours
    past_3_hours = Config.get('Past_3_Hours', 'past_3_hours')
    global url_daily_rainfall
    url_daily_rainfall = Config.get('Url_Path', 'url_daily_rainfall')
    global url_60min_rainfall
    global url_30min_rainfall
    if past_3_hours != '1':
        url_60min_rainfall = Config.get('Url_Path', 'url_60min_rainfall') 
        url_30min_rainfall = Config.get('Url_Path', 'url_30min_rainfall')
    else:
        url_60min_rainfall = Config.get('Url_Path', 'url_60min_rainfall_past_3hrs')
        url_30min_rainfall = Config.get('Url_Path', 'url_30min_rainfall_past_3hrs')
    global url_wind_gusts
    url_wind_gusts = Config.get('Url_Path', 'url_wind_gusts')
    global time_filter
    time_filter = Config.get('Time_Filter', 'time_filter')
    if time_filter == '1':
        global filter_start
        global filter_end
        filter_start = dt.datetime.strptime(Config.get('Time_Filter', 'filter_start'), '%d%m%y %H%M')
        filter_end = dt.datetime.strptime(Config.get('Time_Filter', 'filter_end'), '%d%m%y %H%M')
    global criteria_60min
    criteria_60min = Config.get('SITREP_Criteria', 'criteria_60min')
    global criteria_30min
    criteria_30min = Config.get('SITREP_Criteria', 'criteria_30min')
    global criteria_wind_gusts
    criteria_wind_gusts = Config.get('SITREP_Criteria', 'criteria_wind_gusts')
    global excluded_stations
    excluded_stations = Config.get('Excluded_Stations', 'excluded_stations').split('; ')

def get_daily_rainfall():
    '''
    Read the table of total rainfalls and obtain 
    a dictionary of station, rainfall, date and time 
    with maximum daily rainfall, 
    if the station is not in the excluded_station list
    '''
    try:
        page = urllib.urlopen(url_daily_rainfall).read()
    except Exception as e:
        print 'ERROR: Unable to connect to webpages, please check machine connection'
        print e
    else:
        splitted_page = page.split('</html>')[-2] # due to an extra </html> tag on the html webpage
        soup = BeautifulSoup(splitted_page, 'lxml')
        rows = soup.find_all('tr')[1:]
        for row in rows:
            row = str(row).split('</td><td>')
            try:
                datetime = dt.datetime.strptime(row[3][:11], '%y%m%d %H%M')
                if row[1] not in excluded_stations:
                    print 'INFO: Daily rainfall data extracted'
                    return {'station': row[1], 'rainfall': row[2], 'datetime': datetime}
            except:
                pass
        

def get_60min_rainfall():
    '''
    Read the table of 60min rainfalls and 
    obtain a list of dictionaries of station, rainfall, date and time 
    with 60min rainfall exceeding the criteria,
    and three extra dictionaries with 60min rainfall below criteria, 
    if the stations are not in the excluded_station list
    '''
    try:
        page = urllib.urlopen(url_60min_rainfall).read()
    except Exception as e:
        print 'ERROR: Unable to connect to webpages, please check machine connection'
        print e
    else:
        splitted_page = page.split('</html>')[-2] # due to an extra </html> tag on the html webpage
        soup = BeautifulSoup(splitted_page, 'lxml')    
        rows = soup.find_all('tr')[1:]
        criteria_exceeded = False
        row_count_below_criteria = 0
        extracted_rows = []
        for row in rows:
            row = str(row).split('</td><td>')
            try:
                datetime =  dt.datetime.strptime(row[3][:11], '%y%m%d %H%M')
                if time_filter == '1' and datetime >= filter_start and datetime <= filter_end or time_filter == '0':
                    if row[1] not in excluded_stations and row_count_below_criteria < 3:
                        if float(row[2]) >= float(criteria_60min):
                            exceeded = True
                            criteria_exceeded = True
                        else:
                            exceeded = False
                            row_count_below_criteria += 1
                        extracted_rows.append({'station': row[1], 'rainfall': row[2], 'datetime': datetime, 'exceeded': exceeded})
            except:
                pass
        print 'INFO: 60min-rainfall data extracted'
        return (criteria_exceeded, extracted_rows)

def get_30min_rainfall():
    '''
    Read the table of 30min rainfalls and 
    obtain a list of dictionaries of station, rainfall, date and time 
    with 30min rainfall exceeding the criteria,
    and three extra dictionaries with 30min rainfall below criteria, 
    if the stations are not in the excluded_station list
    '''
    try:
        page = urllib.urlopen(url_30min_rainfall).read()    
    except Exception as e:
        print 'ERROR: Unable to connect to webpages, please check machine connection'
        print e
    else:
        splitted_page = page.split('</html>')[-2] # due to an extra </html> tag on the html webpage
        soup = BeautifulSoup(splitted_page, 'lxml')    
        rows = soup.find_all('tr')[1:]
        criteria_exceeded = False
        row_count_below_criteria = 0
        extracted_rows = []
        for row in rows:
            row = str(row).split('</td><td>')
            try:
                datetime =  dt.datetime.strptime(row[3][:11], '%y%m%d %H%M')
                
                if time_filter == '1' and datetime >= filter_start and datetime <= filter_end or time_filter == '0':
                    if row[1] not in excluded_stations and row_count_below_criteria < 3:
                        if float(row[2]) >= float(criteria_30min):
                            exceeded = True
                            criteria_exceeded = True
                        else:
                            exceeded = False
                            row_count_below_criteria += 1
                        extracted_rows.append({'station': row[1], 'rainfall': row[2], 'datetime': datetime, 'exceeded': exceeded})
            except:
                pass
        print 'INFO: 30min-rainfall data extracted'
        return (criteria_exceeded, extracted_rows)
            
def get_wind_gusts():
    '''
    Read the table of wind gusts and 
    obtain a list of dictionaries of station, rainfall, date and time 
    with wind gusts exceeding the criteria,
    and three extra dictionaries with wind gusts below criteria, 
    if the stations are not in the excluded_station list
    '''
    try:
        page = urllib.urlopen(url_wind_gusts).read()    
    except Exception as e:
        print 'ERROR: Unable to connect to webpages, please check machine connection'
        print e
    else:
        splitted_page = page.split('</html>')[-2] # due to an extra </html> tag on the html webpage
        soup = BeautifulSoup(splitted_page, 'lxml')    
        rows = soup.find_all('tr')[1:]
        criteria_exceeded = False
        row_count_below_criteria = 0
        extracted_rows = []
        for row in rows:
            row = str(row).split('</td><td>')
            try:
                datetime =  dt.datetime.strptime(row[4][:11], '%y%m%d %H%M')
                
                if time_filter == '1' and datetime >= filter_start and datetime <= filter_end or time_filter == '0':
                    if row[1] not in excluded_stations and row_count_below_criteria < 2:
                        if float(row[3]) >= float(criteria_wind_gusts):
                            exceeded = True
                            criteria_exceeded = True
                        else:
                            exceeded = False
                            row_count_below_criteria += 1
                        extracted_rows.append({'station': row[1], 'wind_gusts': row[3], 'datetime': datetime, 'exceeded': exceeded})
            except:
                pass
        print 'INFO: Wind gusts data extracted'
        return (criteria_exceeded, extracted_rows)



    