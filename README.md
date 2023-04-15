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
При першому запуску на збірку пішло 19.3 секунди і роз мір був 1.09 gb (зміна розміру мене зливувала, тому я спробував повторити збірку з старим докерфайлом, що не змінило його розмір)

При доданні змін у spaceship/app.py час і розмір збірки залишився таким самим 

## [Завдання 4]
Ось як я змінив докерфайл
```dockerfile
FROM python:3.10-slim
```

При спробі встановити python:3.10-alpine не могли встановитись якісь пакети, тому я вибрав інший базовий образ

Виміри: Розмір образу - 309mb, час збірки - 25s

## [Завдання 5]

Я додав наступний код у файл api.py

```python
@router.get('/matrixProduct')
def matrixProduct() -> dict:
    matrix_a = numpy.random.rand(10,10)
    matrix_b = numpy.random.rand(10,10)
    res = numpy.matmul(matrix_a, matrix_b)
    return {
        'matrix_a': matrix_a.tolist(),
        'matrix_b': matrix_b.tolist(),
        'product': res.tolist()
    }
```

Виміри:

python:3.10-slim Розмір образу - 309mb, час збірки - 21s

python:3.10-bullseye Розмір образу - 1.09gb, час збірки - 22s

## Висновки

1 - потрібно з розумом підходити до порядку написання докерфайлку

2 - не вказувати latest(чи щось типу того) в залежностях, бо може статись сюрприз)

3 - коли розмір образу є критичним моментом, то можна використати більш базову версію, що зменшить розмір вашого образу
