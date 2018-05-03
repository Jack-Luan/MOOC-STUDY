# MOOC-STUDY
# 爬虫学习
# -*- coding: utf-8 -*-
# 抓取沪深股通成交前十活跃股票
"""
Created on Wed Apr 25 13:32:28 2018

@author: Jack Luan
"""
import requests
import pandas as pd
from bs4 import BeautifulSoup
def hsgt(m,n):
    for x in range(m,n):
        try:
            header={'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36'}
            url='http://data.eastmoney.com/hsgt/top10/'+str(x)[0:4]+'-'+str(x)[4:6]+'-'+str(x)[6:]+'.html'
            res=requests.get(url,headers=header)
            soup=BeautifulSoup(res.text,'html.parser')
            l=soup.select('tbody')
            sj=[]
            i=0
            while i<=90:
                xs=[]
                xs.append(str(x)[0:4]+'-'+str(x)[4:6]+'-'+str(x)[6:])
                for k in range(1,3):
                    n=l[k].select('td')
                    xs.append((n[i+1].text))
                    xs.append(n[i+2].text)
                    if n[i+6].text[-1:]=='万':
                        xs.append(int(float(n[i+6].text[0:-1])))
                    if n[i+6].text[-1:]=='亿':
                        xs.append(int(float(n[i+6].text[0:-1])*10000))
                sj.append(xs)
                i=i+10
            df=pd.DataFrame(sj,columns=['日期','代码','名称','成交额/万','代码','名称','成交额/万'])
            path='I:\gpdata\沪深通数据'+'\\'+str(x)[0:4]+'-'+str(x)[4:6]+'-'+str(x)[6:]+'沪深通'+'.xlsx'
            df.to_excel(path)
        except:
            print('当天未开盘无数据')
            continue
