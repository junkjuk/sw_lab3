FROM python:3.10-slim

WORKDIR /usr/src/spaceship-app 

COPY . . 

COPY ./requirements/requirements.in ./requirements.in 
RUN pip install --no-cache-dir -r requirements.in

CMD [ "uvicorn", "spaceship.main:app", "--host=0.0.0.0", "--port=8080" ] 