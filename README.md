# Node.js Docker Application

This is a simple Node.js application that is containerized with Docker and can be deployed using Jenkins.

### How to Run

1. Install dependencies:
npm install


2. Start the app:
npm start

3. To build and run using Docker:
docker build -t nodejs-docker-app .

Run the Docker container:
docker run -p 8080:8080 nodejs-docker-app

The application will be available at `http://localhost:8080`.
