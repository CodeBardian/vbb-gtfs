# VBB GTFS Data 

## Prepare Data
download the gtfs data, takes a good minute


```python
# https://www.vbb.de/media/download/2029
from io import BytesIO
from zipfile import ZipFile
from urllib.request import urlopen

resp = urlopen("https://www.vbb.de/media/download/2029")
%time zipfile = ZipFile(BytesIO(resp.read()))
zipfile.namelist()
```

    CPU times: user 89.7 ms, sys: 101 ms, total: 190 ms
    Wall time: 2.25 s





    ['agency.txt',
     'calendar.txt',
     'calendar_dates.txt',
     'frequencies.txt',
     'pathways.txt',
     'routes.txt',
     'shapes.txt',
     'stop_times.txt',
     'stops.txt',
     'transfers.txt',
     'trips.txt']




```python
#import sys
#!{sys.executable} -m pip install datashader
import pandas as pd
import numpy as np
import mplleaflet
import folium
import holoviews as hv
import holoviews.operation.datashader as hd
hd.shade.cmap=["lightblue", "darkblue"]
hv.extension("bokeh", "matplotlib") 
import datashader as ds
import datashader.transfer_functions as tf
```







<div class="logo-block">
<img src='data:image/png;base64,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
roTBs2RqAAAAAElFTkSuQmCC'
     style='height:25px; border-radius:12px; display: inline-block; float: left; vertical-align: middle'></img>


  <img src='data:image/png;base64,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'
       style='height:15px; border-radius:12px; display: inline-block; float: left'></img>



  <img src='data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAABHNCSVQICAgIfAhkiAAAAAlwSFlz
AAAFMAAABTABZarKtgAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAArNSURB
VFiFnVd5VFNXGv/ee0kgGyQhbFoXIKCFYEXEDVErTucMoKUOWA/VLsNSLPQgFTOdyrHPiIp1lFIQ
OlaPShEG3EpPcQmISCuV1bQ1CLKIULeQhJA9JO+9+UMT0x5aPfOdc895373f/e7v/t537/ddBF5Q
JBIJl81mJwCACEVRQBCEQhAEAQCgnghCURRCkmS7Wq2+WlJSYn0Rv8jzDHAcD0EQJIVGo5mFQuGF
jIyMu39kq1KpkOrq6gU6nS6aIAiGzWY7VVBQ0P9/AcjNzWXy+fxcOp2uiY+Przm0d6+n8dblv/Fo
kzM4SzYfPlRePvFnjnt6ehh1dXVv2mw2nlar/byoqMj8wgBwHBchCJIZEhJSeu1yHVi7vtu02t8+
NykQ7BMWoOUMhXQsXLv5IQAwSJJEEASxcDicoeTk5DtCoZBy9XX69Gnv3t7ebJIky3EcH3guAKlU
GoGiaOKWLVsOvhs7/9XXPMde3/IyIFbMnaPDuD5AUdQuOf2XlD0npTExMWYAgNbWVpZcLg8xGAzB
JEnSvby82tPT052LaTQatLy8fBtJkt/s3Lnz5h8CwHFcRKPRNu/YsePAjh072KTs0IGCxRg8RgUB
TGpSx6cmHgMAfNqN6Xa1GvJ/D35gYAAViURkcXHxUrPZHDRv3rxv4uLiDI7xPXv2bLdYLBUFBQWD
jj7M8ZGbm8tkMpmSrKysQiaTScXGxtpqL7dManT6tcu5mgEWWJyOhicozpk+c3NsbKzNFcBbWWEf
1Td9/upA30i3ZJv0h8bGxiSFQmFcuHDhOACAWCy+0d3dvX3lypUtzc3N9t8AiIuLk4SEhByLiooy
AgAcO3ZsNlPgH3Cttb35JZo+bCYXIQAA9MDiUW7sWS1KN687w6Mera2twa2trfMvXboUOS28Pyb1
U08McRtf/sXBSmt5cc35pqamVQqFwhoZGallMpnU/fv3e7RaberVq1d/AABAn1IfQqfTNRs3blQB
AFy+fJk7Nja2XCKRnD3dNSorusPq6NfTPR+gPiEEoLRFXO1tS2+zavv27ReftjNttyr0S1/j0rUP
PEJQwNwQYGgAACQSyXmNRhMtk8lYAAApKSlKDMP0+fn5QU4ACIKkxMfH1zjYuHnz5uspKSlOfdX7
u68fvOePcCzKQR4YVCgATGfa/F3pnzaHWOAXSDyaMCqH2+r8VXErP3D+snXr1tV2dXW94dATExOr
6XT6JgAAVCKRcDEMM4WHh9sAAHJyUqNu//wDymKx7AAAVVVVPiaTKXxByrYMvBsxEMSTwPXhuL+8
e/fu9fv371+flvbemogYNz+TnsBOFEwMFO8/KzEYDKFVVVX+AAChoaGT7u7ud48ePRro0DEMs+bl
5bFRNpud4O3tfdGBzq5uy/5wTUPM/q2zC9atmbVqeHg4Pi0t7WxGRoZFH5rw76I7LI8HqHfwPL7d
rfVagzw1NfW81t4ePUfsP/OrnWZ6fPSuUqFQSEkkkrOjo6OvuQR5q0ajiXLoPj4+lzgcTjwKACLH
9SqXy2kzhBO8haGo+UA2wZW+p880DxeveGt9aHx9fT09ctlq3sC0NT9e6xsbjuZblSxl7wKtVotM
m6PnXvlmZJBtX91CEMQsxyJsNlteXl4udugIghAajQYFAEhPTx9AEGQOimGY8y4oLt63KlJkdB4t
P282Z/c/dPrDH04ktJ9P2tfWXP3+2o1vHzunEp6Xq0lsGt08KzUrcSGTQ3n3XeefLCs5UqnT6Rap
VCoEACA7O/snvV4f5gJooLa2NsihoygKKEVRzquTND2OCpttGXdG1tOxwOlgzdvE9v30rV+m3W5I
2jfJNQmLH85QUUzPNTwvkAx0+vVGhq2/VV9fT+dyuZ01NTXOXQOA3fGxevXq2waDYY5r8KIoij5b
jzB5Cz2oKdOo0erOm+1tVuVtBMZXElNMRJR1fvvjx9iPLQ/RjpuB0Xu/Vp7YmH1864YNG3oNBkPw
VD7mzp1rJUnSzZUBmqsBggAgGFC/n6jVA+3WoN3tu1Gg39cg2tEx1Cg3CIJHsclxnl2HRorMN8Z0
fRW+vr7GJ36Q56Z5h9BIknzGAMJWtvdQYs0EZe3/FSwqk5tpXEMb1JoYD+n8xRdQJl/fMPEgzKhS
L40KCD7lGzg92qIyovpb3y/msT2un2psvFpWVvYyl8vtc1nDSXFXV5c7iqLOtEyS5LNBAADfWeKm
Ly4uuvR1++sfv51/P5sfnHm2/Iy+mBmwsaHJbpt+Q0jHSS7TZ/PSNVkNJ/973OxtemD1s91CPb12
h9MfvZsk5meo1eqo5ORkxTNWn7HR1tY2l8PhOAsUiqIolCRJcETtv/61qzNySYK5trZ2TCgUUiwW
S1FSUhLR+bA/kAzwXcAbHa/cFhrTXrJ/v+7IkSPu3Je4Xm5eboJv2wba5QbO5fQwxhsP679Y+nFO
jgAAoKSkJILFYjnBGI1G0YYNGwYBnqRoiqIQlKKojurq6gUAAAKBgKQoiuGYkJWVpTCZTOKmI1Xd
HwnDcm+cOnOMw+H0FxYWbqpvqv/r9EV+bky+O+/QoUPiqJRt9JphTLFHbKBCR87tWL9EPN9oNIZn
ZWUpXHaMCQQCEgCgsrIyEgBuoGq1+qpOp4t2GPH5/BvFxcVLHXpgYGDD8ePH/56Xl2cCAMjMzOxP
S0s7pWfow4RCbz/fAF9RT0+P9yeffHJySSqev+9nxLD1FaAlTR8vlJ8vxxzsFhUVLRMIBB0OvwaD
YRlFUdfQkpISK0EQ9J6eHgYAQEZGxl2z2Rw0MjJCBwBITk5+xOVyfzpw4ECSw5lQKKQIbxtJm4EN
8eZ7jPz0oNv+dK5FG/jq54eH+IFr/S1KabBy0UerAvI+++wzD4vFEpCWljYEACCTyVh2ux3FcXwS
BQCw2WxVdXV1bzrQRURE1FVVVTn1zMzM/pkzZ35/9OjRd0pLS19RqVQIy4/tCwDgOcPTQvFQEQBA
aWnpK0ERK2LbyVllN341GUJ4YDu8zD5bKyur7O+85tx9Z2fnO1ar9QjA04KkpaVFs2LFir8olcq7
YWFhJpFINNnX16drbGyMjY6Ovg0AIBaLjcuXL5d3d3d7XbhwIW704b3F479MeD1qVfJ5Og/bvb4R
LwaDMZabm9uwflNa/z/3HOIv5NsDEK7XS7FeevXPvYNLvm5S/GglCK5KpZorlUobXE8g5ObmMqVS
6UG1Wu1BURSHoijOiRMnwgoLC7coFAqBo+9Fm0KhEKStmvvto3TeucFN7pVJYbytarXaQyqVHsRx
3N15TF1BuBaljr4rV66wOzo63mAymXdzcnKuwwtIUVHRMqvVGkgQxMV7NXvyJijGvcNXB/7z5Zdf
bicI4gSO40NTAgD4bVnuODIAT2pElUq1FEEQO4fD6QsPD++fqixHEATj8/ntjoCrqKhwS0hIsJWV
leURBHEOx3G563pT3tn5+flBDAbjg6CgoMMpKSlK17GhoSFMJpMFPk04DJIkEQzDzCwW6+5UD5Oa
mhrfO3fufECS5GHXnf8pAAAAHMfdURTdimGYPjExsTo0NHTyj2ynEplMxurs7HyHIAiKJMlSHMct
U9k9N2vl5+cH0en0TRiGWX18fC65vnh+LxqNBq2oqFhgMpmi7XY7arVaj+zdu/fxn/l/4bSZl5fH
5nK5CQAQMtXznCRJePpEbwOAZhzHX4ix/wHzzC/tu64gcwAAAABJRU5ErkJggg=='
       style='height:15px; border-radius:12px; display: inline-block; float: left'></img>



</div>



## Stops


```python
%time stops_df = pd.read_csv(zipfile.open('stops.txt'))
stops_df.tail()
stops_df.info()
```

    CPU times: user 89.5 ms, sys: 20.1 ms, total: 110 ms
    Wall time: 110 ms
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 41733 entries, 0 to 41732
    Data columns (total 11 columns):
     #   Column               Non-Null Count  Dtype  
    ---  ------               --------------  -----  
     0   stop_id              41733 non-null  object 
     1   stop_code            0 non-null      float64
     2   stop_name            41733 non-null  object 
     3   stop_desc            0 non-null      float64
     4   stop_lat             41733 non-null  float64
     5   stop_lon             41733 non-null  float64
     6   location_type        41733 non-null  int64  
     7   parent_station       28607 non-null  float64
     8   wheelchair_boarding  2944 non-null   float64
     9   platform_code        4419 non-null   object 
     10  zone_id              15336 non-null  object 
    dtypes: float64(6), int64(1), object(4)
    memory usage: 3.5+ MB



```python
stops_df.fillna('', inplace=True)
stops_df = stops_df.drop(['stop_code', 'stop_desc'], axis=1)
stops_df.loc[stops_df["wheelchair_boarding"] == '','wheelchair_boarding'] = 0
stops_df_multiple_stops = stops_df.copy()
stops_df.drop_duplicates(subset=['stop_name', 'location_type', 'wheelchair_boarding', 'platform_code'],keep='first', inplace = True)
stops_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>stop_id</th>
      <th>stop_name</th>
      <th>stop_lat</th>
      <th>stop_lon</th>
      <th>location_type</th>
      <th>parent_station</th>
      <th>wheelchair_boarding</th>
      <th>platform_code</th>
      <th>zone_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>000008012713</td>
      <td>Rangsdorf, Bahnhof</td>
      <td>52.294125</td>
      <td>13.431112</td>
      <td>0</td>
      <td>900000245025.0</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>000008010205</td>
      <td>Leipzig, Hauptbahnhof</td>
      <td>51.344817</td>
      <td>12.381321</td>
      <td>0</td>
      <td>900000550090.0</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>000008010327</td>
      <td>Senftenberg, Bahnhof</td>
      <td>51.526790</td>
      <td>14.003977</td>
      <td>0</td>
      <td>900000435000.0</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>000008010324</td>
      <td>Schwerin, Hauptbahnhof</td>
      <td>53.635261</td>
      <td>11.407520</td>
      <td>0</td>
      <td>900000550112.0</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>000008012393</td>
      <td>Mühlanger, Bahnhof</td>
      <td>51.855704</td>
      <td>12.748198</td>
      <td>0</td>
      <td>900000550319.0</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
stops_df.apply(lambda x: x.unique().size, axis=0)
```




    stop_id                28907
    stop_name              13119
    stop_lat               13069
    stop_lon               13082
    location_type              2
    parent_station         13088
    wheelchair_boarding        2
    platform_code             59
    zone_id                14586
    dtype: int64




```python
# visualization with folium: takes way longer + more memory consumption than bokeh

#f = folium.Figure(width=800, height=600)
#m = folium.Map(location=[45.5236, -122.6750], prefer_canvas=True).add_to(f)
#for lat, lon in zip(stops_df['stop_lat'], stops_df['stop_lon']):
#    folium.CircleMarker(
#        location=[lat, lon],
#        radius=1,
#        popup="Laurelhurst Park",
#        color="#3186cc",
#        fill=True,
#        fill_color="#3186cc",
#    ).add_to(m)
#m
```


```python
from bokeh.plotting import figure, output_notebook, show, reset_output
from bokeh.tile_providers import OSM, get_provider

output_notebook()
```



<div class="bk-root">
    <a href="https://bokeh.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
    <span id="1002">Loading BokehJS ...</span>
</div>





```python
def merc_from_arrays(lats, lons):
    r_major = 6378137.000
    x = r_major * np.radians(lons)
    scale = x/lons
    y = 180.0/np.pi * np.log(np.tan(np.pi/4.0 + lats * (np.pi/180.0)/2.0)) * scale
    return (x, y)
```


```python
p = figure(plot_width=800, plot_height=700,title="Public Transport Stops of VBB",tools="pan,wheel_zoom",
           x_range=(1215654.4978, 1721973.3732), y_range=(6533225.6816, 7296372.9720),
           x_axis_type="mercator", y_axis_type="mercator",
           tooltips=[("Name", "@stop_name"), ("platform", "@platform_code"), ("(Lat, Lon)", "(@stop_lat, @stop_lon)")])
p.add_tile(get_provider(OSM))
stops_df['merc_x'], stops_df['merc_y'] = merc_from_arrays(stops_df['stop_lat'], stops_df['stop_lon'])
p.circle(x='merc_x', y='merc_y', source=stops_df)
show(p)
```








<div class="bk-root" id="c6740ba8-6cfc-4c51-8de9-6821a9cb4eca" data-root-id="1003"></div>






```python
hv.output(backend="bokeh")
tiles = hv.element.tiles.OSM().opts(width=600, height=550, alpha=0.5)
stops = hv.Points(stops_df, ['merc_x', 'merc_y'], label='Public Transport Stops')
stops_wa = hv.Points(stops_df.loc[stops_df['wheelchair_boarding'] == 1], ['merc_x', 'merc_y'], label='Wheelchair accessible Stops')
tiles * hd.datashade(stops).opts(height=550, width=600) + tiles * hd.datashade(stops_wa).opts(height=550, width=600)
```






<div id='1100'>





  <div class="bk-root" id="b6db77c0-042f-4c89-b87a-5f10bb04a9af" data-root-id="1100"></div>
</div>
<script type="application/javascript">(function(root) {
  function embed_document(root) {
    var render_items = [{"docid":"8b94d20e-2077-4000-b390-d2f87e5c2d84","root_ids":["1100"],"roots":{"1100":"b6db77c0-042f-4c89-b87a-5f10bb04a9af"}}];
    root.Bokeh.embed.embed_items_notebook(docs_json, render_items);
  }
  if (root.Bokeh !== undefined && root.Bokeh.Panel !== undefined) {
    embed_document(root);
  } else {
    var attempts = 0;
    var timer = setInterval(function(root) {
      if (root.Bokeh !== undefined && root.Bokeh.Panel !== undefined) {
        clearInterval(timer);
        embed_document(root);
      } else if (document.readyState == "complete") {
        attempts++;
        if (attempts > 100) {
          clearInterval(timer);
          console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
        }
      }
    }, 10, root)
  }
})(window);</script>



#### Setup Plotting



```python
import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
import seaborn as sns
sns.set_style(
    style='darkgrid', 
    rc={'axes.facecolor': '.9', 'grid.color': '.8'}
)
sns.set_palette(palette='deep')
sns_c = sns.color_palette(palette='deep')
%matplotlib inline
from pandas.plotting import register_matplotlib_converters
register_matplotlib_converters()

plt.rcParams['figure.figsize'] = [10, 6]
plt.rcParams['figure.dpi'] = 100
```

#### Stations with most stops


```python
stops_df_multiple_stops['stop_name'].value_counts().head(10)
```




    S Potsdam Hauptbahnhof                  23
    Cottbus, Hauptbahnhof                   19
    S Wannsee Bhf (Berlin)                  19
    Potsdam, Medienstadt Babelsberg Bhf     19
    Potsdam, Johannes-Kepler-Platz          17
    S+U Zoologischer Garten Bhf (Berlin)    17
    Fürstenwalde, Bahnhof                   17
    S+U Berlin Hauptbahnhof                 16
    S Schöneweide/Sterndamm (Berlin)        16
    Frankfurt (Oder), Bahnhof               16
    Name: stop_name, dtype: int64




```python
num_stops = stops_df_multiple_stops.groupby(['stop_name']).agg(num_stops=('stop_id', 'count')).query('num_stops > 1').sort_values('num_stops', ascending=False)
num_stops.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>num_stops</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>13087.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>3.186445</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.292919</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>23.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
num_stops_mean = num_stops['num_stops'].mean()
num_stops_median = num_stops['num_stops'].median()

fig, ax = plt.subplots()
sns.histplot(x='num_stops', data=num_stops, color=sns_c[3], ax=ax, discrete=True)
ax.axvline(x=num_stops_mean, color=sns_c[1], linestyle='--', label=f'mean = {num_stops_mean: ,.2f}')
ax.axvline(x=num_stops_median, color=sns_c[4], linestyle='--',label=f'median = {num_stops_median}')
ax.legend(loc='upper right')
ax.set(title='Number of Stops per Location', xlabel='number of stops', xlim=(0, None))
```




    [Text(0.5, 1.0, 'Number of Stops per Location'),
     Text(0.5, 0, 'number of stops'),
     (0.0, 24.6)]




    
![png](index_files/index_18_1.png)
    


## Shapes


```python
%time shapes = pd.read_csv(zipfile.open('shapes.txt'))
shapes.tail()
shapes.apply(lambda x: x.unique().size, axis=0)
```

    CPU times: user 2.08 s, sys: 156 ms, total: 2.24 s
    Wall time: 2.24 s





    shape_id              14518
    shape_pt_lat         283697
    shape_pt_lon         298415
    shape_pt_sequence      3578
    dtype: int64




```python
shapes.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>shape_id</th>
      <th>shape_pt_lat</th>
      <th>shape_pt_lon</th>
      <th>shape_pt_sequence</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>210</td>
      <td>52.390812</td>
      <td>13.066689</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>210</td>
      <td>52.390761</td>
      <td>13.067064</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>210</td>
      <td>52.390835</td>
      <td>13.066852</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>210</td>
      <td>52.390894</td>
      <td>13.065428</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>210</td>
      <td>52.390932</td>
      <td>13.064851</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




```python
p = figure(plot_width=800, plot_height=700,title="Public Transport Lines of VBB",tools="pan,wheel_zoom",
           x_range=(1215654.4978, 1721973.3732), y_range=(6533225.6816, 7296372.9720),
           x_axis_type="mercator", y_axis_type="mercator",
           output_backend="webgl")
p.add_tile(get_provider(OSM))
shapes['merc_x'], shapes['merc_y'] = merc_from_arrays(shapes['shape_pt_lat'], shapes['shape_pt_lon'])
merc_x = shapes.groupby(['shape_id'])['merc_x'].apply(list)
merc_y = shapes.groupby(['shape_id'])['merc_y'].apply(list)
p.multi_line(xs=merc_x.values.tolist(), ys=merc_y.values.tolist(), color='red', line_width=1)
show(p)
```








<div class="bk-root" id="08e03a07-e690-453c-9fc1-fade545cb101" data-root-id="1680"></div>





## Routes


```python
%time routes = pd.read_csv(zipfile.open('routes.txt'))
routes.tail()
routes.apply(lambda x: x.unique().size, axis=0)
```

    CPU times: user 4.73 ms, sys: 0 ns, total: 4.73 ms
    Wall time: 4.58 ms





    route_id            1279
    agency_id             34
    route_short_name     905
    route_long_name        2
    route_type             8
    route_color            6
    route_text_color       2
    route_desc             2
    dtype: int64




```python
routes.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>route_id</th>
      <th>agency_id</th>
      <th>route_short_name</th>
      <th>route_long_name</th>
      <th>route_type</th>
      <th>route_color</th>
      <th>route_text_color</th>
      <th>route_desc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18804_3</td>
      <td>92</td>
      <td>648</td>
      <td>NaN</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>18804_700</td>
      <td>92</td>
      <td>648</td>
      <td>NaN</td>
      <td>700</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>17694_3</td>
      <td>92</td>
      <td>338</td>
      <td>NaN</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17694_700</td>
      <td>92</td>
      <td>338</td>
      <td>NaN</td>
      <td>700</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>16735_700</td>
      <td>92</td>
      <td>683</td>
      <td>NaN</td>
      <td>700</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, ax = plt.subplots()
rename = {2: "Intercity Rail Service", 100: "Railway Service", 109: "Suburban Railway", 400: "Urban Railway Service", 700: "Bus Service", 900: "Tram Service", 1000: "Water Transport Service"}
routes['route_type'].replace(rename, inplace=True)
routes \
    .groupby(['route_type']) \
    .agg(n=('route_id', 'count')) \
    .reset_index() \
    .sort_values('n', ascending=False) \
    .assign(share= lambda x: x['n'] / x['n'].sum()) \
    .pipe((sns.barplot, 'data'), 
        x='share', 
        y='route_type',
        color=sns_c[2],
        edgecolor=sns_c[2],
        ax=ax
    )
fmt = lambda y, _: f'{y :0.0%}'
ax.xaxis.set_major_formatter(mtick.FuncFormatter(fmt))
ax.set(
    title='Share of Routes per Route Type', 
    xlabel='share of routes', 
    ylabel='route type'
);
```




    [Text(0.5, 1.0, 'Share of Routes per Route Type'),
     Text(0.5, 0, 'share of routes'),
     Text(0, 0.5, 'route type')]




    
![png](index_files/index_26_1.png)
    


## Trips


```python
%time trips = pd.read_csv(zipfile.open('trips.txt'))
trips.tail()
trips.apply(lambda x: x.unique().size, axis=0)
```

    CPU times: user 219 ms, sys: 0 ns, total: 219 ms
    Wall time: 218 ms





    route_id                   1279
    service_id                 2101
    trip_id                  224268
    trip_headsign              2714
    trip_short_name             371
    direction_id                  2
    block_id                  10529
    shape_id                  14518
    wheelchair_accessible         2
    bikes_allowed                 2
    dtype: int64




```python
trips = trips.join(routes[['route_id','route_type', 'route_short_name']].set_index('route_id'), on='route_id')
trips.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>route_id</th>
      <th>service_id</th>
      <th>trip_id</th>
      <th>trip_headsign</th>
      <th>trip_short_name</th>
      <th>direction_id</th>
      <th>block_id</th>
      <th>shape_id</th>
      <th>wheelchair_accessible</th>
      <th>bikes_allowed</th>
      <th>route_type</th>
      <th>route_short_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18804_3</td>
      <td>1</td>
      <td>148928020</td>
      <td>Seegefeld, Bahnhof</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>594</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>648</td>
    </tr>
    <tr>
      <th>1</th>
      <td>18804_3</td>
      <td>1</td>
      <td>148928022</td>
      <td>Seegefeld, Bahnhof</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>594</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>648</td>
    </tr>
    <tr>
      <th>2</th>
      <td>18804_3</td>
      <td>2</td>
      <td>148928008</td>
      <td>Seegefeld, Bahnhof</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>594</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>648</td>
    </tr>
    <tr>
      <th>3</th>
      <td>18804_3</td>
      <td>1</td>
      <td>148928025</td>
      <td>Seegefeld, Bahnhof</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>594</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>648</td>
    </tr>
    <tr>
      <th>4</th>
      <td>18804_3</td>
      <td>1</td>
      <td>148928026</td>
      <td>Seegefeld, Bahnhof</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>594</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>648</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, ax = plt.subplots()
trips \
    .groupby(['route_type']) \
    .agg(n=('trip_id', 'count')) \
    .reset_index() \
    .sort_values('n', ascending=False) \
    .assign(share= lambda x: x['n'] / x['n'].sum()) \
    .pipe((sns.barplot, 'data'), 
        x='share', 
        y='route_type',
        color=sns_c[2],
        edgecolor=sns_c[2],
        ax=ax
    )
fmt = lambda y, _: f'{y :0.0%}'
ax.xaxis.set_major_formatter(mtick.FuncFormatter(fmt))
ax.set(
    title='Share of Trips per Route Type', 
    xlabel='share of trips', 
    ylabel='route type'
);
```




    [Text(0.5, 1.0, 'Share of Trips per Route Type'),
     Text(0.5, 0, 'share of trips'),
     Text(0, 0.5, 'route type')]




    
![png](index_files/index_30_1.png)
    


#### Stations with most arriving trips


```python
trips['trip_headsign'].value_counts().head()
```




    S+U Zoologischer Garten    5671
    S+U Rathaus Spandau        4415
    S Schöneweide/Sterndamm    3122
    Moabit, Lüneburger Str.    3059
    S+U Rathaus Steglitz       2712
    Name: trip_headsign, dtype: int64




```python
trips_cleaned = trips.groupby(['trip_headsign', 'route_type']).size().reset_index(name="count")
trips_cleaned['max'] = trips_cleaned.groupby('trip_headsign')['count'].transform('sum')
trips_cleaned = trips_cleaned.sort_values(["max",'trip_headsign',"count"], ascending=False).drop('max', axis=1)
```


```python
fig, ax = plt.subplots(figsize=(15, 6))
trips_cleaned.head(40).pipe(
    (sns.barplot, 'data'), 
    y='count', 
    x='trip_headsign',
    hue='route_type',
    edgecolor='black',
    dodge=True,
    ax=ax
)
ax.tick_params(axis='x', labelrotation=90)
ax.set(title='number of trips per headsign destination', xlabel='destination', ylabel='number of stops');
```




    [Text(0.5, 1.0, 'number of trips per headsign destination'),
     Text(0.5, 0, 'destination'),
     Text(0, 0.5, 'number of stops')]




    
![png](index_files/index_34_1.png)
    


#### Stations with most arriving routes


```python
trips.drop_duplicates(subset=['route_id', 'trip_headsign'])['trip_headsign'].value_counts().head()
```




    Cottbus, Hauptbahnhof     38
    S Potsdam Hauptbahnhof    29
    Pritzwalk, Bahnhof        25
    S+U Rathaus Spandau       22
    S Südkreuz Bhf            22
    Name: trip_headsign, dtype: int64




```python
rtrips_cleaned = trips.drop_duplicates(subset=['route_id', 'trip_headsign'])
rtrips_cleaned = rtrips_cleaned.groupby(['trip_headsign', 'route_type']).size().reset_index(name="count")
rtrips_cleaned['max'] = rtrips_cleaned.groupby('trip_headsign')['count'].transform('sum')
rtrips_cleaned = rtrips_cleaned.sort_values(["max",'trip_headsign',"count"], ascending=False).drop('max', axis=1)
```


```python
fig, ax = plt.subplots(figsize=(15, 6))
rtrips_cleaned.head(40).pipe(
    (sns.barplot, 'data'), 
    y='count', 
    x='trip_headsign',
    hue='route_type',
    edgecolor='black',
    dodge=True,
    ax=ax
)
ax.tick_params(axis='x', labelrotation=90)
ax.set(title='number of routes per headsign destination', xlabel='destination', ylabel='number of routes');
```




    [Text(0.5, 1.0, 'number of routes per headsign destination'),
     Text(0.5, 0, 'destination'),
     Text(0, 0.5, 'number of routes')]




    
![png](index_files/index_38_1.png)
    
