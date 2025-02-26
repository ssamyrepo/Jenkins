# Register App

## Overview
This Jenkins App is a web application designed for managing user accounts. It includes a backend server built with Java and a frontend web interface. The application supports Docker containerization and CI/CD integration via Jenkins.

## Project Structure
```
register-app-main/
│-- Dockerfile         # Containerization setup
│-- Jenkinsfile        # CI/CD pipeline configuration
│-- pom.xml            # Maven project configuration
│-- server/            # Backend Java application
│   │-- pom.xml        # Backend dependencies
│   └── src/           # Backend source code
│-- webapp/            # Frontend web application
```

## Prerequisites
- Java 11 or later
- Maven 3+
- Docker (for containerization)
- Jenkins (for CI/CD, optional)

## Setup and Installation

### Running Locally
1. Clone the repository:
   ```sh
   git clone https://github.com/your-repo/Jenkins-app-main.git
   cd register-app-main
   ```
2. Build the backend server:
   ```sh
   cd server
   mvn clean install
   ```
3. Start the application:
   ```sh
   mvn spring-boot:run
   ```

### Running with Docker
1. Build the Docker image:
   ```sh
   docker build -t register-app .
   ```
2. Run the container:
   ```sh
   docker run -p 8080:8080 register-app
   ```

## CI/CD Integration
- The `Jenkinsfile` defines a Jenkins pipeline for automating the build and deployment process.
- To enable Jenkins CI/CD, configure a pipeline job pointing to the repository.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
This project is licensed under the MIT License.

