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
import os
def gethsgt(start,end,path='I:\\'):
    if os.path.exists(path)==False:
        os.mkdir(path)
    path1='沪深通'+str(start)+'---'+str(end)
    sj1=[]
    for x in range(start,end+1):
        if int(str(x)[-2:])>31:
            continue
        else:
            try:
                header={'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36'}
                url='http://data.eastmoney.com/hsgt/top10/'+str(x)[0:4]+'-'+str(x)[4:6]+'-'+str(x)[6:]+'.html'
                res=requests.get(url,headers=header)
                res.status_code
                res.encoding=res.apparent_encoding
                soup=BeautifulSoup(res.text,'html.parser')
                l=soup('tbody')
                sj=[]
                i=0
                while i<=90:
                        xs=[]
                        for k in range(1,3):
                            xs.append(str(x)[0:4]+'-'+str(x)[4:6]+'-'+str(x)[6:])
                            n=l[k]('td')
                            xs.append((n[i+1].text))
                            xs.append(n[i+2].text)
                            if n[i+6].text[-1]=='万':
                                xs.append(int(float(n[i+6].text[0:-1])))
                            if n[i+6].text[-1]=='亿':
                                xs.append(int(float(n[i+6].text[0:-1])*10000))
                        sj.append(xs)
                        i=i+10
                sj1=sj1+sj
                print('已保存'+str(x)+'数据')
            except:
                print(str(x)+'当天未开盘无数据')
                continue
    df=pd.DataFrame(sj1,columns=['日期','代码','名称','成交额/万','日期','代码','名称','成交额/万'])
    df.to_excel(path+path1+'.xlsx')
