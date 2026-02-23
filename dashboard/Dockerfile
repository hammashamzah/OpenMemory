# ===== BUILD STAGE =====
FROM node:20-bookworm-slim AS builder

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci

# Copy source code
COPY . .

# Build the Next.js application (extra memory for large builds)
RUN NODE_OPTIONS="--max-old-space-size=4096" npm run build

# ===== PRODUCTION STAGE =====
FROM node:20-bookworm-slim AS production

WORKDIR /app

# Install only production dependencies
COPY package*.json ./
RUN npm ci --omit=dev

# Copy built assets from builder
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/next.config.ts ./next.config.ts

# Create a dedicated non-root user for security
RUN groupadd -g 1001 nodejs \
 && useradd -u 1001 -g nodejs -s /bin/sh nextjs \
 && chown -R nextjs:nodejs /app

USER nextjs

# Expose the application port
EXPOSE 3000

# Set environment to production
ENV NODE_ENV=production

# Start the Next.js application
CMD ["npm", "start"]
