# Write your multi-stage Dockerfile for frontend here.
# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY src/ ./src/

# Stage 2: Production with Nginx
FROM nginx:1.25-alpine AS production
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder /app/src/ /usr/share/nginx/html/
COPY --chown=appuser:appgroup --from=builder /app/src/ /usr/share/nginx/html/
RUN chown -R appuser:appgroup /var/cache/nginx /var/run /var/log/nginx /usr/share/nginx/html && \
    sed -i 's/listen       80;/listen       80;\n    listen  [::]:80;/' /etc/nginx/conf.d/default.conf
USER appuser
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:80/ || exit 1
CMD ["nginx", "-g", "daemon off;"]
