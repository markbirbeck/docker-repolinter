FROM node:9-alpine

LABEL maintainer="mark.birbeck@gmail.com"
LABEL version="0.1.0"

# Set the working directory:
#
WORKDIR /repolinter/repo

# Install repolinter:
#
# Note that modules are installed globally so that they don't
# interfere with the Node modules directory allowing a volume
# to be mapped in:
#
ENV NODE_PATH /usr/local/lib/node_modules

# Installation details:
#
#  https://github.com/todogroup/repolinter#command-line-dependencies
#
RUN apk add --no-cache \
  git

RUN npm install -g \
  log-symbols \
  todogroup/repolinter

# Copy in a basic configuration:
#
COPY repolint.json /repolinter/

# Basic behaviour is to run repolinter:
#
ENTRYPOINT ["repolinter"]
