# api_postgres
updating, inserting, deleting, displaying values stored in postgres using python api's

create a table in postgres initailly


code

from fastapi import FastAPI, status, HTTPException
from pydantic import Json
from database import execute_query
from fastapi.encoders import jsonable_encoder
from fastapi.responses import ORJSONResponse

app = FastAPI()


@app.get('/display')
def get_all_items():
    items = execute_query("select * from public.items", {}, True)
    return items


@app.post("/insert/{name}/{price}")
def insert_value(name: str, price: int):
    insert_query = "INSERT INTO public.items VALUES (:item_name,:item_price);"
    values = {"item_name": name, "item_price": price}
    items = execute_query(insert_query, values, with_result=False)
    return items


@app.put("/update/{name}/{price}")
def update_value(name: str, price: int):
    insert_query = 'UPDATE public.items SET item_name=:item_name where item_price=:item_price'
    values = {"item_name": str(name), "item_price": price}
    items = execute_query(insert_query, values, with_result=False)
    return items


@app.delete("/delete/{price}")
def delete_values(price: int):
      insert_query = "DELETE FROM public.items where item_price=:item_price;"
      values={"item_price": price}
      items = execute_query(insert_query, values, with_result=False)
      return items
      
#######################################################################################################################################################
database.py

from sqlalchemy.orm import declarative_base
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
import itertools

Base = declarative_base()
Session = sessionmaker()

engine = create_engine("postgresql://postgres:{password}@{localhost}/{item_db}", echo=True)
Base.metadata.create_all(bind=engine)
Session.configure(bind=engine)

def execute_query(query,values=None,with_result=False):
    session = Session()
    print(query)
    res = session.execute(query, values)
    session.commit()
    print('res',res)
    result = []
    if with_result:
        result = res.mappings().all()
    return result




