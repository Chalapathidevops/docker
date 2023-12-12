# Multi-stage Docker build

A multi-stage Docker build is a feature that allows you to create more efficient and smaller Docker images by using multiple build stages within a single Dockerfile.

In simple terms, it's like having different steps in a recipe to cook a dish. Each step (or stage) of the build process in a multi-stage Docker build focuses on specific tasks, such as installing dependencies, building the application, or copying files, and creates intermediate images for each stage.

Imagine you're making a cake. You gather ingredients, mix them, bake the batter, and finally decorate the cake. Instead of having one big messy kitchen with all your tools and ingredients out at once, you could have separate clean stations for mixing, baking, and decorating. Each station focuses on a specific part of the process and contributes to the final result.

**Similarly, in a multi-stage Docker build:**

* **Each Stage Has a Specific Task:** Each stage in the Dockerfile focuses on a particular aspect of the build process, such as compiling code, installing dependencies, or creating the final executable.
* **Intermediate Images:** Each stage produces an intermediate image. The final image will only contain artifacts or files copied from the final stage, omitting unnecessary tools, dependencies, or build artifacts from previous stages.
* **Reduced Image Size:** By discarding unnecessary tools and files from earlier stages, the final Docker image becomes smaller and more efficient.
* **Clarity and Efficiency:** It improves readability and maintainability of Dockerfiles by separating concerns and keeping each stage focused on a specific task.

**For example, in a multi-stage Docker build for a Node.js application:**

* The first stage might be dedicated to installing dependencies and building the application.
* The second stage could copy only the built application files (without development dependencies) from the first stage into a clean environment, resulting in a smaller and leaner final Docker image that contains only what's needed to run the application.<br>
<br>Overall, multi-stage Docker builds help in creating optimized and smaller Docker images by dividing the build process into distinct stages, resulting in more efficient deployment and execution of applications within containers.


**Sample Dockerfile**

```
# Stage 1: Build stage
FROM node:14 AS build
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json .
RUN npm install

# Copy application source code
COPY . .

# Build the application (example: transpile TypeScript or bundle assets)
RUN npm run build

# Stage 2: Production-ready stage
FROM node:14-alpine
WORKDIR /app

# Copy only necessary files from the previous stage (built application)
COPY --from=build /app/dist ./dist
COPY --from=build /app/package.json .

# Install only production dependencies (omit dev dependencies)
RUN npm install --only=production

# Set the command to start the application
CMD ["node", "dist/app.js"]
```

**Explanation:**

**Stage 1 (Build stage):**

* Uses the node:14 image as the base image for the build process.
* Sets up the working directory and copies package.json to install dependencies.
* Copies the entire application code.
* Runs any build processes (like transpilation or bundling) using npm run build.

**Stage 2 (Production-ready stage):**

* Uses a lightweight node:14-alpine image as the base image.
* Sets up the working directory and copies only the built application (dist directory) and package.json (without dev dependencies).
* Installs only production dependencies using npm install --only=production.
* Defines the command (CMD) to start the application when the Docker container is run.

This structure ensures that the final Docker image only contains the necessary files and dependencies required to run the application, making it smaller and more optimized for production use.
