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

`docker run -it --entrypoint=bash --rm test:pandas`

Когда использую эту команду, то по заверншению работы с контейнером, все файлы удалятся. Видно, что находимся в каталоге /app который указали выше в докер файле.
И когда выполняю `ls` то видим файл pipline.py который сохранили. Но файла parquet ещё нет, потому что не запускали скрипт.

Что здесь происходит:

`docker run` — создаёт новый контейнер

`-it` — интерактивный режим (можно вводить команды)

`--entrypoint=bash` — переопределяет `ENTRYPOINT` из `Dockerfile`
(вместо `python pipeline.py` запускается bash)

`--rm` — контейнер удалится автоматически, файл исчезнет, когда ты выйдешь

`test:pandas` — образ

Тоесть мы оказались внутри контейнера:`root@<container_id>:/app#`

<img width="376" height="51" alt="image" src="https://github.com/user-attachments/assets/0e61d0e3-8e9a-4da1-8f3a-f57157130fe8" />

Чтобы увидеть parquet файл, внутри контейнера надо выполнить: `python pipeline.py 12`. Скрипт отработал и создал файл внутри контейнера.

<img width="458" height="154" alt="image" src="https://github.com/user-attachments/assets/38f925f7-9299-4cb9-a7e6-43bb566121e7" />

Теперь можно выйти из контейнера `exit`

<img width="220" height="51" alt="image" src="https://github.com/user-attachments/assets/b91ba281-2e34-42f4-977b-de8e7320b449" />

Посмотерть список всех запущенных контейнеров: `docker ps` - покажет контейнеры, которые сейчас работают.

Список всех контейнеров (включая остановленные): `docker ps -a`

Увидим контейнеры которые сосздали ранее выполняя команду `docker run` два раза.

<img width="923" height="126" alt="image" src="https://github.com/user-attachments/assets/e08d2a18-6d02-4e78-8201-50d213f07c01" />

Удалим контенеры: `docker rm <container_id или имя>`

<img width="924" height="189" alt="image" src="https://github.com/user-attachments/assets/01dfaf6f-a215-41d0-b103-bf562dc1cf90" />

Если запустить сборку еще раз то увидим `CACHED`, это значит что команды не будут выполняться плвторно. Докер воспользуется тем что уже было выполнено. Это экономит время.

<img width="910" height="305" alt="image" src="https://github.com/user-attachments/assets/59d0cc06-557a-490a-a9fb-c5d7f8202ca5" />

49:00
