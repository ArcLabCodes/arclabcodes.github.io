# Use an official Ruby runtime as a parent image.
FROM ruby:3.1

# Install Node.js (if you plan to use Tailwind later) and other dependencies.
RUN apt-get update && \
    apt-get install -y nodejs npm && \
    rm -rf /var/lib/apt/lists/*

# Set the working directory in the container
WORKDIR /workspace

# Install bundler
RUN gem install jekyll bundler

# Expose Jekyll’s default port (used later for forwarding)
EXPOSE 4000
