FROM node:18-alpine as base

## INSTALL DEPS ##
FROM base AS deps
WORKDIR /app
RUN apk add --no-cache curl libc6-compat

COPY package.json .
COPY yarn.lock .

RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi

#### DEVELOPMENT ####
FROM deps as development

COPY . .

EXPOSE 3000

## BUILD APPLICATION ##
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

RUN yarn build

## PRODUCTION ##
FROM base AS production
WORKDIR /app

RUN apk add curl

ENV NODE_ENV production
ENV HOSTNAME "0.0.0.0"

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

RUN mkdir .next
RUN chown nextjs:nodejs .next

COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 8080

CMD ["node", "server.js"]
