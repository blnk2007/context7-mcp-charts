FROM node:18-alpine

WORKDIR /app

# Set the Context7 MCP version (should match values.yaml and Chart.yaml)
ARG CTX7_MCP_VERSION=1.0.13

# Install the specified version globally
RUN npm install -g @upstash/context7-mcp@${CTX7_MCP_VERSION}

# Expose default port 8080
EXPOSE 8080

# Default command to run the server
CMD ["sh","-c","set -e; context7-mcp serve --port 8080 < /dev/null & child=$!; trap \"kill $child\" TERM INT; while kill -0 $child 2>/dev/null; do sleep 5; done"]
