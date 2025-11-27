 Stage 1: Build the Angular application
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
COPY --from=build /app/dist/*/ /usr/share/nginx/html

# Expose port 80 (Internal to container)
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
