FROM node:14

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .

EXPOSE 3000

### execute the app.mjs file with the node command
CMD ["node", "app.mjs"]

### sudo docker run -d --name first_demo_run -p 3000:3000 first_demo
### ở đây dùng -d để chạy ở chế độ nền (detached mode) không chiếm terminal hiện tại
### --name là đặt tên cho container chạy từ image first_demo