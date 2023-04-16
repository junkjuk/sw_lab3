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
 
  ## [Завдання 3](https://github.com/junkjuk/sw_lab3/commit/f3be3ddb1682380ce1946aa4eec84e25ab34887f)
  
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

## [Завдання 4](https://github.com/junkjuk/sw_lab3/commit/1c4ee168ca4d701513598d73d422567910cfeb5d)
Ось як я змінив докерфайл
```dockerfile
FROM python:3.10-slim
```

При спробі встановити python:3.10-alpine не могли встановитись якісь пакети, тому я вибрав інший базовий образ

Виміри: Розмір образу - 309mb, час збірки - 25s

## [Завдання 5](https://github.com/junkjuk/sw_lab3/commit/a9cfb3b49902ec5152821c278a887ad14e48c1ab)

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

# Go

## [Завдання 1](https://github.com/junkjuk/sw_lab3/commit/a4332c8c8ef9e856a31a1d02ae187e4ce2c777ca)

Спочатку я стоврив ткий докерфайл

 ```dockerfile
FROM golang:1.20.3-bullseye

WORKDIR /usr/src/fizzbuzz-app 

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -v -o /usr/src/fizzbuzz-app/

CMD ["./fizzbuzz", "serve"]
```

Виміри: Розмір образу - 880mb, час збірки - 30s

Ось перелік файлів які потрапили в наш образ

![image](https://user-images.githubusercontent.com/38862851/232289977-d79ca884-c68c-45cf-815a-790f312c95c7.png)

Для запуску програми нам потрібен лише скомпільований файл, тому ми можемо перенести в образ лише бінарний файл, і це не вплине на роботу програми

## [Завдання 2](https://github.com/junkjuk/sw_lab3/commit/6278bb80875f945e71ee48fb297226bdea5f375f)

Я переписав докерфайл наступним чином

 ```dockerfile
FROM golang:1.20.3-bullseye as build-stage

WORKDIR /usr/src/fizzbuzz-app 

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -v -o /usr/src/fizzbuzz-app/bin/

FROM scratch
WORKDIR / 
COPY --from=build-stage /usr/src/fizzbuzz-app/bin/ ./
COPY --from=build-stage /usr/src/fizzbuzz-app/templates ./templates
CMD ["./fizzbuzz", "serve"]
```

Тепер з наших вайлів до контейнера потрапило тільки бінарний файл та папка templates. Ось скріншот

![image](https://user-images.githubusercontent.com/38862851/232300311-f7a69127-add0-40dc-9d4a-b161e2dcd460.png)

Я пробував використати команду docker exec (як я зрозумів цього не можна зробити у scratch образі) і docker export (в мене не відкривався нормально файл :), тому я використав docker desktop)

Виміри: Розмір образу - 9.8mb, час збірки - 12s

Чи зручно користуватись таким образом - якщо потрібно взаємодіяти з самим контейнером то ні, але якщо ми хочемо "запустити і забути" то різниці я не побачив

## [Завдання 3](https://github.com/junkjuk/sw_lab3/commit/c8541ce289589d572639142afbf914545222204f)

Я відредагував докерфайл наступним чином

 ```dockerfile
FROM golang:1.20.3-bullseye as build-stage

WORKDIR /usr/src/fizzbuzz-app 

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -v -o /usr/src/fizzbuzz-app/bin/

FROM gcr.io/distroless/base-debian11 AS build-release-stage
WORKDIR / 
COPY --from=build-stage /usr/src/fizzbuzz-app/bin/ ./
COPY --from=build-stage /usr/src/fizzbuzz-app/templates ./templates
CMD ["./fizzbuzz", "serve"]
```

Ось фали в цій системі. Видно що потрапили до образу також лише потрібні файли, але інших директорій сталобільше

![image](https://user-images.githubusercontent.com/38862851/232302269-4a165fb3-0b67-484a-8eaf-f3ddd9babc07.png)

Виміри: Розмір образу - 30.2mb, час збірки - 10s
