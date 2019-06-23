FROM node:11 AS build
LABEL CLI-VERSION="6.0.0"
RUN useradd --user-group --create-home --shell /bin/false app
ENV HOME=/home/app
WORKDIR $HOME
RUN npm install -g @angular/cli@6.0.0 \
    && npm install @angular/compiler-cli@6.0.0 --save \
    && npm install yarn
COPY awesome-sauce .
RUN yarn install
RUN ng build

FROM nginx
ADD default.conf /etc/nginx/conf.d/default.conf
COPY --from=build /home/app/dist/awesome-sauce /usr/share/nginx/html
EXPOSE 80