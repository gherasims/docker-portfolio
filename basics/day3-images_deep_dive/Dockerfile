# Base image: official Ubuntu 22.04
FROM ubuntu:22.04

# Update package index and install a tiny dependency (not strictly needed, but realistic)
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*

# Set working directory inside the container
WORKDIR /app

# Create a simple script inside the image
RUN echo '#!/bin/bash' > /app/run.sh \
    && echo 'echo "Day 3 image works! ✅"' >> /app/run.sh \
    && chmod +x /app/run.sh

# Set the default command the container runs
CMD ["./run.sh"]
