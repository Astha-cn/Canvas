# ---------- Build stage ----------
FROM node:18-alpine AS builder
# 👆 Node 18 LTS is most stable with Vite

WORKDIR /app

# Copy only dependency files first (cache optimization)
COPY package.json package-lock.json ./

# Install ALL deps (including devDependencies)
RUN npm ci

# Copy rest of the app
COPY . .

# Build Vite app
RUN npm run build


# ---------- Runtime stage ----------
FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy built static files
COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

