FROM node:16 AS builder
#RUN apk update

WORKDIR /app
RUN yarn global add turbo
COPY . .
RUN turbo prune --scope=docs --docker

# Add lockfile and package.json's of isolated subworkspace
FROM node:16 AS installer
#RUN apk update
WORKDIR /app

#COPY .gitignore .gitignore
COPY --from=builder /app/out/json/ .
COPY --from=builder /app/out/yarn.lock ./yarn.lock
RUN yarn install

# Build the project and its dependencies
COPY --from=builder /app/out/full/ .
COPY turbo.json turbo.json
RUN yarn turbo run build --filter=docs...

FROM node:16 AS runner
WORKDIR /app

#RUN addgroup --system --gid 1001 nodejs
#RUN adduser --system --uid 1001 nodejs
#USER nodejs
#COPY --chown=nodejs:nodejs . ./app
COPY --from=installer /app .

WORKDIR /app/apps/docs
EXPOSE 3001
CMD ["yarn", "run", "dev"]
