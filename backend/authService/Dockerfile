FROM node:18-alpine AS base

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .
ENV HOST=3001
ENV NODE_ENV=production
EXPOSE 3001

CMD ["npm", "run", "start"]
