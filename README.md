# Dockerizing the Pipeline

Теперь поместим скрипт в контейнер. Создадим следующий файл Dockerfile:

```
# base Docker image that we will build on
FROM python:3.13.11-slim

# set up our image by installing prerequisites; pandas in this case
RUN pip install pandas pyarrow

# set up the working directory inside the container
WORKDIR /app
# copy the script to the container. 1st name is source file, 2nd is destination
COPY pipeline.py pipeline.py

# define what to do first when the container runs
# in this example, we will just run the script
ENTRYPOINT ["python", "pipeline.py"]
```

**Объяснение:**

- `FROM`: Базовый образ, на чем основывается образ (Python 3.13)
- `RUN`: Выполнение команд во время сборки, установка pandas и pyarrow
- `WORKDIR`: Устанавливаю рабочую директорию
- `COPY`: Копирую файлы в образ, тоесть скопировал pipline.py в рабочий каталог /app в докере
- `ENTRYPOINT`: Команда для запуска по умолчанию

**Создадим образ:**

`docker build -t test:pandas .`

`-t` - это тег. `test` — имя образа. `pandas` — тег (версия). Если тег не указан, по умолчанию будет использоваться значение latest. `COPY <source> <destination>` - копирую файл локально: pipeline.py в контейнер: /app/pipeline.py

<img width="902" height="510" alt="image" src="https://github.com/user-attachments/assets/0c1d7e11-7813-4640-be79-21823e84b8f7" />

Теперь можно запустить контейнер и передать ему аргумент, чтобы наш конвейер получил его:

`docker run -it test:pandas some_number` где `some_number` какое то число

<img width="240" height="105" alt="image" src="https://github.com/user-attachments/assets/4a87ba4d-d304-4c53-aa4d-42dc072011a7" />

Должны получить тот же результат, что и при самостоятельном запуске конвейерного скрипта.

*Примечание: в этих инструкциях предполагается, что `pipeline.py` и `Dockerfile` находятся в одном каталоге. Команды `Docker` также должны запускаться из того же каталога, что и эти файлы.*

Каждый `docker run` создаёт новый контейнер с чистой файловой системой. Файл существует только внутри того конкретного контейнера. То есть запустили два раза, создалось два контейнера

<img width="883" height="71" alt="image" src="https://github.com/user-attachments/assets/8aa2ef27-108c-4ead-af16-74b841860a41" />

`docker run -it --entrypoint=bash --rm test:pandas` Когда использую эту команду, то по заверншению работы с контейнером, все файлы удалятся. Видно, что находимся в каталоге /app который указали выше в докер файле.
И когда выполняю `ls` то видим файл pipline.py который сохранили.

<img width="376" height="51" alt="image" src="https://github.com/user-attachments/assets/0e61d0e3-8e9a-4da1-8f3a-f57157130fe8" />

Запустили `python pipeline.py 12`

<img width="402" height="120" alt="image" src="https://github.com/user-attachments/assets/ab7d3f9f-9cd9-4685-b7e6-3c74a5c6b21e" />


<img width="402" height="120" alt="image" src="https://github.com/user-attachments/assets/9c4a8242-c8db-4805-81f6-a57f864b29a6" />


