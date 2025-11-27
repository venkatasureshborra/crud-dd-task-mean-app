# backend/Dockerfile
FROM node:18-alpine
# Create app directory
WORKDIR /usr/src/app
# Install dependencies
COPY package*.json ./
RUN npm install --production
# Copy source code
COPY . .
# Environment variables (can be overridden in docker-compose)
ENV PORT=3000
# The app should use process.env.MONGO_URI to connect to MongoDB
EXPOSE 3000
CMD ["npm", "start"]