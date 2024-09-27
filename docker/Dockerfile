# Stage 1: Build stage
FROM node:20-alpine AS build
RUN apk add --update libc6-compat python3 make g++
# needed for pdfjs-dist
RUN apk add --no-cache build-base cairo-dev pango-dev

# Install Chromium
RUN apk add --no-cache chromium

#install PNPM globaly
RUN npm install -g pnpm

ENV PUPPETEER_SKIP_DOWNLOAD=true
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

ENV NODE_OPTIONS=--max-old-space-size=8192

WORKDIR /usr/src

# Copy app source
COPY . .

RUN pnpm install

RUN pnpm build


# Stage 2: Runtime stage
FROM node:20-alpine

# Install runtime dependencies
RUN apk add --no-cache chromium git python3 py3-pip make g++ build-base cairo-dev pango-dev

# Set the environment variable for Puppeteer to find Chromium
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

# Copy Flowise from the build stage
#COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
#COPY --from=build /usr/local/bin /usr/local/bin
COPY --from=build /usr/src/ /usr/local/lib/ 
#COPY --from=build /usr/src/node_modules/flowise/bin /usr/local/bin/flowise-server

ENTRYPOINT ["/usr/local/lib/node_modules/flowise/bin/run", "start"]
