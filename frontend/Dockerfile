# Stage 1: Build the Angular application
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
# Build the app (Production mode)
RUN npm run build --prod

# Stage 2: Serve with Nginx (Internal container server)
FROM nginx:alpine

# Copy the built artifacts from the build stage
# IMPORTANT: Replace 'mean-app' with the actual name of your project inside dist/ folder
COPY --from=build /app/dist/angular-15-crud/ /usr/share/nginx/html
RUN printf 'server {\n\
    listen 8081;\n\
    server_name localhost;\n\
    root /usr/share/nginx/html;\n\
    index index.html index.htm;\n\
    location / {\n\
        try_files $uri $uri/ /index.html;\n\
    }\n\
}' > /etc/nginx/conf.d/default.conf
EXPOSE 8081

CMD ["nginx", "-g", "daemon off;"]
