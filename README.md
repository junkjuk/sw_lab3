# Лабораторна робота №3
# Python
 
 ## [Завдання 1](https://github.com/junkjuk/sw_lab3/commit/7c805c705b0100c5971f09e52d7505218e9f5f1b)
 
 Для закріплення версій залежностей я використав команду pip freeze і розмістив залежності у файл /requirements/requirements.in
 
 Далі я створив цей докерфайл
 
```dockerfile
#базовий образ
FROM python:3.10-bullseye

#робоча директорія
WORKDIR /usr/src/spaceship-app 

#встановелння залежностей
COPY ./requirements/requirements.in ./requirements.in 
RUN pip install --no-cache-dir -r requirements.in

#копіювання коду
COPY . . 

#запуск разом з контейнером 
CMD [ "uvicorn", "spaceship.main:app", "--host=0.0.0.0", "--port=8080" ] 
```
 
 Для створення образу я використовував данну команду
 
```powershell
docker build -t <imagename> .
```

Виміри: Розмір образу - 978mb, час збірки - 60s

 ## Завдання 2
 
 Я додав коментар у файл spaceship/app.py, і повторив збірку
 
 Виміри: Розмір образу - 978mb, час збірки - 1.6s
 
  ## [Завдання 3]
  
  Я переписав докерфайл наступним чином
  
 ```dockerfile
 FROM python:3.10-bullseye

WORKDIR /usr/src/spaceship-app 

COPY . . 

COPY ./requirements/requirements.in ./requirements.in 
RUN pip install --no-cache-dir -r requirements.in

CMD [ "uvicorn", "spaceship.main:app", "--host=0.0.0.0", "--port=8080" ] 
 ```
