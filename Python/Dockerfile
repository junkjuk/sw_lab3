FROM python:3.10-bullseye

WORKDIR /usr/src/spaceship-app 

COPY ./requirements/requirements.in ./requirements.in 
RUN pip install --no-cache-dir -r requirements.in

COPY . . 

CMD [ "uvicorn", "spaceship.main:app", "--host=0.0.0.0", "--port=8080" ] 