# backend/Dockerfile
FROM node:18-alpine
# Create app directory
WORKDIR /usr/src/app
# Install dependencies
COPY package*.json ./
RUN npm install --production
# Copy source code
COPY . .
# The app should use process.env.MONGO_URI to connect to MongoDB
EXPOSE 8080
CMD ["npm", "start"]
