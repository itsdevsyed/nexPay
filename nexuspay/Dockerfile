# ---------- Stage 1: Build ----------
    FROM node:20-slim AS builder

    WORKDIR /app

    # Prisma query engine needs openssl
    RUN apt-get update -y \
        && apt-get install -y --no-install-recommends openssl \
        && rm -rf /var/lib/apt/lists/*

    # Copy dependency manifests + prisma schema first (better layer caching)
    COPY package*.json ./
    COPY prisma ./prisma

    # Install deps without running lifecycle scripts
    # (postinstall/prepare scripts often fail here — we run prisma generate explicitly below)
    RUN npm ci --ignore-scripts

    # Placeholder envs so `prisma generate` has something to resolve env("DATABASE_URL")/env("DIRECT_URL")
    ENV DATABASE_URL="postgresql://placeholder:placeholder@localhost:5432/placeholder"
    ENV DIRECT_URL="postgresql://placeholder:placeholder@localhost:5432/placeholder"

    # Generate Prisma client into node_modules
    RUN npx prisma generate

    # Copy the rest of the source and build
    COPY . .
    RUN npm run build

    # Remove dev dependencies from node_modules for the runtime image
    RUN npm prune --production

    # ---------- Stage 2: Runtime ----------
    FROM node:20-slim AS runner

    WORKDIR /app

    ENV NODE_ENV=production

    # openssl required at runtime by the Prisma query engine
    RUN apt-get update -y \
        && apt-get install -y --no-install-recommends openssl \
        && rm -rf /var/lib/apt/lists/*

    # Copy only what's needed from builder
    COPY --from=builder /app/node_modules ./node_modules
    COPY --from=builder /app/dist ./dist
    COPY --from=builder /app/prisma ./prisma
    COPY --from=builder /app/package*.json ./

    USER node

    EXPOSE 3000

    CMD ["node", "dist/main.js"]
