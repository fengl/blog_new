---
title: 利用Python进行数据分析 第9章例子
date: 2016-11-22 12:16:48
tags: [python, Basemap]
---
最近在看《利用Python进行数据分析》这本书，感慨python的方便，pandas的强大，跟着书中的例子，一路下来，收获颇多。

但是，在第9章最后的例子“根据州统计赞助信息”时，文中的代码确不成功，作为一个giser，我想，我必须把它改改，调通，哈哈。

以下我的修改的代码，保证可用啊，有问题，欢迎邮件讨论哦：fenglgis@gmail.com

```
# -*- coding: utf-8 -*-

import pandas as pd
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap
from matplotlib.collections import LineCollection
import shapefile

fec = pd.read_csv('data/P00000001-ALL.csv')

fec = fec[fec.contb_receipt_amt > 0]
fec_mrbo = fec[fec.cand_nm.isin(['Obama, Barack', 'Romney, Mitt'])]

parties = {'Bachmann, Michelle': 'Republican',
           'Cain, Herman': 'Republican',
           'Gingrich, Newt': 'Republican',
           'Huntsman, Jon': 'Republican',
           'Johnson, Gary Earl': 'Republican',
           'McCotter, Thaddeus G': 'Republican',
           'Obama, Barack': 'Democrat',
           'Paul, Ron': 'Republican',
           'Pawlenty, Timothy': 'Republican',
           'Perry, Rick': 'Republican',
           "Roemer, Charles E. 'Buddy' III": 'Republican',
           'Romney, Mitt': 'Republican',
           'Santorum, Rick': 'Republican'}
fec['party'] = fec.cand_nm.map(parties)


fec_mrbo = fec[fec.cand_nm.isin(['Obama, Barack', 'Romney, Mitt'])]

grouped = fec_mrbo.groupby(['cand_nm', 'contbr_st'])
totals = grouped.contb_receipt_amt.sum().unstack(0).fillna(0)
totals = totals[totals.sum(1) > 100000]
percent = totals.div(totals.sum(1), axis=0)
obama = percent['Obama, Barack']


fig = plt.figure(figsize=(12, 12))
ax = fig.add_axes([0.1, 0.1, 0.8, 0.8])
lllat = 21; urlat = 53; lllon = -118; urlon = -62

m = Basemap(ax=ax, projection='stere',
            lon_0=(urlon + lllon) / 2, lat_0=(urlat + lllat) / 2,
            llcrnrlat=lllat, urcrnrlat=urlat, llcrnrlon=lllon,
            urcrnrlon=urlon, resolution='l')

m.drawcoastlines()
m.drawcounties()

state_to_code = {
    'Alabama': 'AL',
    'Alaska': 'AK',
    'Arizona': 'AZ',
    'Arkansas': 'AR',
    'California': 'CA',
    'Colorado': 'CO',
    'Connecticut': 'CT',
    'Delaware': 'DE',
    'Florida': 'FL',
    'Georgia': 'GA',
    'Hawaii': 'HI',
    'Idaho': 'ID',
    'Illinois': 'IL',
    'Indiana': 'IN',
    'Iowa': 'IA',
    'Kansas': 'KS',
    'Kentucky': 'KY',
    'Louisiana': 'LA',
    'Maine': 'ME',
    'Maryland': 'MD',
    'Massachusetts': 'MA',
    'Michigan': 'MI',
    'Minnesota': 'MN',
    'Mississippi': 'MS',
    'Missouri': 'MO',
    'Montana': 'MT',
    'Nebraska': 'NE',
    'Nevada': 'NV',
    'New Hampshire': 'NH',
    'New Jersey': 'NJ',
    'New Mexico': 'NM',
    'New York': 'NY',
    'North Carolina': 'NC',
    'North Dakota': 'ND',
    'Ohio': 'OH',
    'Oklahoma': 'OK',
    'Oregon': 'OR',
    'Pennsylvania': 'PA',
    'Rhode Island': 'RI',
    'South Carolina': 'SC',
    'South Dakota': 'SD',
    'Tennessee': 'TN',
    'Texas': 'TX',
    'Utah': 'UT',
    'Vermont': 'VT',
    'Virginia': 'VA',
    'Washington': 'WA',
    'West Virginia': 'WV',
    'Wisconsin': 'WI',
    'Wyoming': 'WY',
}

shp_reader = shapefile.Reader('data/states/statesp020')
for i in range(shp_reader.numRecords):
    shpsegs = []
    shp = shp_reader.shape(i)
    record = shp_reader.record(i)
    coords = shp.points
    lons, lats = zip(*coords)
    x, y = m(lons, lats)
    shpsegs.append(zip(x, y))
    name = record[3]
    lines = LineCollection(shpsegs, antialiaseds=(1,))
    try:
        state_code = state_to_code[name]
        per = obama[state_code]
        print per
    except KeyError:
        continue
    lines.set_facecolors('k')
    lines.set_alpha(0.75 * per)
    lines.set_edgecolors('k')
    lines.set_linewidth(0.3)
    ax.add_collection(lines)

plt.show()

```


以下是效果图：

![](/images/figure_finance.png)