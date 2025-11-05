# ---- Build Stage ----
FROM node:18-alpine AS build
WORKDIR /app

# Copy only package files first
COPY package*.json ./

# Install dependencies in the container
RUN npm install --legacy-peer-deps

# Copy the rest of the frontend source
COPY . .

# Build react with API URL injected at build time
ARG REACT_APP_SERVER_URL
ENV REACT_APP_SERVER_URL=$REACT_APP_SERVER_URL

RUN npm run build

# ---- Run Stage ----
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
