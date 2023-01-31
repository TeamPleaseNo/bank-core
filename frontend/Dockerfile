FROM ubuntu as copy
RUN apt-get update
RUN apt-get install -y git
WORKDIR /
ADD "https://www.random.org/cgi-bin/randbyte?nbytes=10&format=h" skipcache
RUN git clone https://github.com/TeamPleaseNo/frontend.git

FROM node:lts-alpine as build-stage

# устанавливаем простой HTTP-сервер для статики
RUN npm install -g http-server

# делаем каталог 'app' текущим рабочим каталогом
WORKDIR /app

# копируем оба 'package.json' и 'package-lock.json' (если есть)
COPY --from=copy /frontend/package*.json ./

# устанавливаем зависимости проекта
RUN npm install

# копируем файлы и каталоги проекта в текущий рабочий каталог (т.е. в каталог 'app')
COPY --from=copy /frontend .

# собираем приложение для production с минификацией
RUN npm run build

FROM nginx as production-stage
# # Add the Nginx configuration file
# ADD ./nginx/nginx.conf /etc/nginx/nginx.conf
# Copy over static assets from the client application
COPY --from=build-stage /app/build /usr/share/nginx/html