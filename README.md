# ETL
ETL: Extract → Transform → Load


## What is ETL?

Extract → Transform → Load

Any ETL pipeline needs three functions implemented — for extracting, transforming, and loading the data. Here’s what these functions will do in our case:

- `extract(url: str) -> dict` — makes a GET request to the `url`parameter. Tests to see if some data was returned — in that case, it is returned as a dictionary. Otherwise, an exception is raised.
- `transform(data: dict) -> pd.DataFrame` — transforms the data so only specific attributes are kept: ID, name, username, email, address, phone number, and company. Returns the transformed data as a Pandas DataFrame.
- `load(data: pd.DataFrame, path: str) -> None` — saves the previously transformed `data` to a CSV file at `path`. We’ll also append a timestamp to the file name, so the files don’t get overwritten.


```
import json
import requests
import pandas as pd
from datetime import datetime


def extract(url: str) -> dict:
    res = requests.get(url)
    if not res:
        raise Exception('No data fetched!')
    return json.loads(res.content)


def transform(data: dict) -> pd.DataFrame:
    transformed = []
    for user in data:
        transformed.append({
            'ID': user['id'],
            'Name': user['name'],
            'Username': user['username'],
            'Email': user['email'],
            'Address': f"{user['address']['street']}, {user['address']['suite']}, {user['address']['city']}",
            'PhoneNumber': user['phone'],
            'Company': user['company']['name']
        })
    return pd.DataFrame(transformed)


def load(data: pd.DataFrame, path: str) -> None:
    data.to_csv(path_or_buf=path, index=False)


if __name__ == '__main__':
    users = extract(url='https://jsonplaceholder.typicode.com/users')
    df_users = transform(users)
    load(data=df_users, path=f'data/users_{int(datetime.now().timestamp())}.csv')
```


---

Prefect is a Python-based workflow management system

Conclusion:

Perfect - task, flow ==> get detailed information about when every task started and finished.

Perfect - parameter ==> provide flexibility

Perfect - schedule => to do the ETL task every interval specified

handle failures
