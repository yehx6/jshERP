# 阶段1: 构建前端
FROM node:20-alpine AS builder

WORKDIR /build

# 配置npm淘宝镜像
RUN npm config set registry https://registry.npmmirror.com

COPY package.json ./
RUN npm install

COPY . .
RUN npm run build

# 阶段2: Nginx部署
FROM nginx:1.24-alpine

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 复制构建产物
COPY --from=builder /build/dist /usr/share/nginx/html

# 复制nginx配置
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
