Task 3: naprawa Dockerfile

Dockerfile nie działał. Naprawiłem go, zbudowałem obraz, uruchomiłem kontener i sprawdziłem, że aplikacja odpowiada na / i /healthz. app.py nie ruszałem.

Co było źle

Stary Dockerfile:

FROM docker.io/library/python:3.12-alpine
WORKDIR /app
COPY server.py /srv/app.py
EXPOSE 8080
CMD ["python", "server.py"]

Porównałem go z plikami w katalogu (ls) i wyszły trzy błędy:

CMD uruchamiał server.py, a mój plik to app.py.
COPY miał złe źródło: server.py zamiast app.py.
COPY kopiował do /srv, a WORKDIR to /app, więc CMD nie znalazłby pliku.

COPY źródło cel bierze plik z mojego komputera i wkłada go do obrazu. Kropka jako cel to katalog z WORKDIR.

Po poprawce
FROM docker.io/library/python:3.12-alpine
WORKDIR /app
COPY app.py .
EXPOSE 8080
CMD ["python", "app.py"]
Co uruchamiałem
docker build -t interview-docker:candidate .
docker run -d --name interview-docker-candidate -p 127.0.0.1:18080:8080 interview-docker:candidate
docker ps -a
docker logs interview-docker-candidate
curl -fsS http://127.0.0.1:18080/
curl -fsS http://127.0.0.1:18080/healthz

-p 18080:8080 to port na moim komputerze i port aplikacji w kontenerze. Pomyłka po drodze: docker logs wywołałem z nazwą obrazu zamiast kontenera, i wyszło No such container.

Wynik
build przeszedł
kontener ma status Up
logi: Listening on 0.0.0.0:8080
/ zwraca JSON z komunikatem, /healthz zwraca ok