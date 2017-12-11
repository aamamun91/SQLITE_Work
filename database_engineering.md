

```python
import pandas as pd
import csv
import pymysql
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.ext.automap import automap_base
pymysql.install_as_MySQLdb()
Base = declarative_base()
import sqlalchemy
from sqlalchemy import create_engine

# Import modules to declare columns and column data types
from sqlalchemy import Column, Integer, String, Float, inspect
```


```python
measurement = pd.read_csv("clean_measurement.csv")
measurement.rename(columns={"id":"measurement_id"}, inplace=True)
```


```python
station = pd.read_csv("clean_station.csv")
station.rename(columns={"id":"station_id"}, inplace=True)
```


```python
station.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>station_id</th>
      <th>station</th>
      <th>name</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>elevation</th>
      <th>location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>USC00519397</td>
      <td>WAIKIKI 717.2, HI US</td>
      <td>21.2716</td>
      <td>-157.8168</td>
      <td>3.0</td>
      <td>POINT(21.2716 -157.8168)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>USC00513117</td>
      <td>KANEOHE 838.1, HI US</td>
      <td>21.4234</td>
      <td>-157.8015</td>
      <td>14.6</td>
      <td>POINT(21.4234 -157.8015)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>USC00514830</td>
      <td>KUALOA RANCH HEADQUARTERS 886.9, HI US</td>
      <td>21.5213</td>
      <td>-157.8374</td>
      <td>7.0</td>
      <td>POINT(21.5213 -157.8374)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>USC00517948</td>
      <td>PEARL CITY, HI US</td>
      <td>21.3934</td>
      <td>-157.9751</td>
      <td>11.9</td>
      <td>POINT(21.3934 -157.9751)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>USC00518838</td>
      <td>UPPER WAHIAWA 874.3, HI US</td>
      <td>21.4992</td>
      <td>-158.0111</td>
      <td>306.6</td>
      <td>POINT(21.4992 -158.0111)</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create the connection engine
from sqlalchemy.orm import Session
engine = create_engine("sqlite:///hawaii.sqlite")
session = Session(bind=engine)
conn = engine.connect()
```


```python
# Create Measurement and Station Classes
# ----------------------------------
class Measurement(Base):
    __tablename__ = "measurement"
    measurement_id = Column(Integer)
    station = Column(String(255),primary_key=True)
    date = Column(String(255))
    prcp = Column(Float)
    tobs = Column(Float)
```


```python
class Station(Base):
    __tablename__ = "station"
    station_id = Column(Integer)
    station = Column(String(255), primary_key = True)
    name = Column(String(255))
    latitude = Column(Float)
    longitude = Column(Float)
    elevation = Column(Float)
    location = Column(String(255))
```


```python
from sqlalchemy import MetaData
```


```python
# Create a "Metadata" Layer That Abstracts our SQL Database
# ----------------------------------
Base.metadata.create_all(engine)
```


```python
metadata = MetaData(engine)
```


```python
ins = inspect(engine)
for _t in ins.get_table_names(): print(_t)
```

    measurement
    station
    


```python
session.commit()
```


```python
measurement.to_sql(Measurement.__tablename__,conn,if_exists='replace', index=False)
```


```python
station.to_sql(Station.__tablename__,conn,if_exists='replace', index=False)
```


```python
measures_sql = pd.read_sql("SELECT * FROM Measurement",conn)
```


```python
station_sql = pd.read_sql("SELECT * FROM Station",conn)
```


```python
print("Shape:" + str(measures_sql.shape))
```

    Shape:(18103, 5)
    


```python
print("Shape:" + str(station_sql.shape))
```

    Shape:(9, 7)
    


```python
measurement.shape
```




    (18103, 5)




```python
measures_sql.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measurement_id</th>
      <th>station</th>
      <th>date</th>
      <th>prcp</th>
      <th>tobs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>USC00519397</td>
      <td>2010-01-01</td>
      <td>0.08</td>
      <td>65.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>USC00519397</td>
      <td>2010-01-02</td>
      <td>0.00</td>
      <td>63.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>USC00519397</td>
      <td>2010-01-03</td>
      <td>0.00</td>
      <td>74.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>USC00519397</td>
      <td>2010-01-04</td>
      <td>0.00</td>
      <td>76.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6</td>
      <td>USC00519397</td>
      <td>2010-01-07</td>
      <td>0.06</td>
      <td>70.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
ins = inspect(engine)
for _t in ins.get_table_names(): print(_t)
```

    measurement
    station
    
