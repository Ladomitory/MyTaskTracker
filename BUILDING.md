# Сборка проекта
В этом документе будет полное описание всех вариантов сборки, предусмотренных разработчиком. \
Версии всего что используется в проекте перечисленны в документе [VERSION.md](VERSIONS.md).

## Сборка Java проекта
В проекте используется сборщик Maven. \
Вся информация о конфигурации сборки находиться в [pom.xml](tasktracker/pom.xml). \
Команда для сборки (выполняется в **корневой** диретории):
```cmd
mvn -f tasktracker/pom.xml package
```
Или (выполняется в директории `tasktracker`):
```cmd 
mvn package
```
Собраный `.jar`-файл сохраняется в директории `./target/`.

## Сборка Java проекта в Docker-образ
Проект настроен для использования в Docker. \
Для этого нужно положить наш `.jar`-файл в новый образ. \
За основу берёться официальный (выпущеный Docker) образ `openjdk`. 
Его можно скачать через `DockerHub`. \
Информация о сборке образа храниться в [Dockerfile](tasktracker/Dockerfile) \
Команда для сборки (выполняется в **корневой** директории):
```cmd 
docker build -t ladomitorydev/tasktracker:0.0.1 --platform linux/amd64 --no-cache ./tasktracker
```
Или (выполняется в директории **tasktracker**).
```cmd
docker build -t ladomitorydev/tasktracker:0.0.1 --platform linux/amd64 --no-cache ./
```

## Перенос Docker-образа на сервер
Далее нам нужно перенести на сервер следующие образы: \
1. ladomitorydev/tasktracker:0.0.1
2. postgres:18.0
3. dpage/pgadmin4:9.9.0

Сохранение образа в `.tag`-файл.
```cmd
docker save -o <path_to_file> <image_name_or_id> 
```
Пример:
```cmd
docker save -o c:/docker/image.tag my_image
или
docker save -o ./docker-images/tasktracker-0_0_1.tag ladomitorydev/tasktracker:0.0.1
```
После этого перенести файл любым доступным способом.
Распаковка образа.
```cmd
docker load -i <path_to_file>
```
Пример:
```cmd
docker load -i ./docker-images/tasktracker-0_0_1.tag 
```

## Развёртывание контейнеров
После переноса всех трёх образов переносим в рабочую директорию файл [docker-compose.yml](docker-compose.yml). \
В рабочей папке выполняем команду:
```cmd
docker-compose -f 'docker-compose.yml' up -d --build
```
Она проведёт сборку и запустит контейнеры.


Web-интервейс будет доступне на порту 8080. \
База данных будет доступна на порту 5432. \
А gui pqAdmin будет доступне на порту 80.

Для изменения портов в файле [docker-compose.yml](docker-compose.yml) измените порты сервисов. \
**!!!ВАЖНО!!!** \
Порты идут парами `<port1>:<port2>`. 
Менять можно только первое значение.