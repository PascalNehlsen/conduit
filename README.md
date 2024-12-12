# Conduit Deploy with Docker Compose

## Table of Contents

1. [Description](#description)
2. [Quickstart](#quickstart)
3. [Usage](#usage)
   - [Configuration](#configuration)
4. [Contact](#contact)

## Description

This repository sets up a multi-container environment using Docker Compose to run both the Conduit Frontend and Conduit Backend applications.
The Conduit Frontend is forked from <br /> [https://github.com/Developer-Akademie-GmbH/conduit-frontend](https://github.com/Developer-Akademie-GmbH/conduit-frontend), <br /> and the Conduit Backend is forked from <br /> [https://github.com/Developer-Akademie-GmbH/conduit-backend](https://github.com/Developer-Akademie-GmbH/conduit-backend) and integrated as submodules.

Both the frontend and backend are containerized, and Docker Compose is used to orchestrate their operation, ensuring they work seamlessly together. This setup provides a consistent and scalable development environment, simplifying the process of running both applications in parallel.

## Quickstart

### Prerequisites

Ensure you have the following tools installed:

- [Docker](https://www.docker.com/products/docker-desktop)

  - Docker Compose was included with Docker versions 20.10 and later, but recently it is no longer included by default. For the latest Docker versions, you will need to install Docker Compose manually as a plugin. Follow the official instructions for detailed steps.

- For setting up the **Frontend**, refer to the [Conduit Frontend README](https://github.com/Developer-Akademie-GmbH/conduit-frontend/blob/master/README.md)
- For setting up the **Backend**, refer to the [Conduit Backend README](https://github.com/Developer-Akademie-GmbH/conduit-backend/blob/master/README.md)

1. Clone the repository:

   ```bash
   git clone https://github.com/PascalNehlsen/conduit.git
   cd conduit
   ```

2. Copy the `example.env` file to `.env`:

   ```bash
   cp example.env .env
   ```

3. Build and start the application using Docker Compose:

   ```bash
   docker-compose up --build
   ```

4. Access the application:

- The **Frontend** should be accessible at `http://localhost:8282` in your web browser.
- The **Backend** should be accessible at `http://localhost:8000` in your web browser.

5. To stop the application, use:
   ```bash
   docker-compose down
   ```

## Usage

TBA

### Configuration

TBA

## Contact

- Pascal Nehlsen - [LinkedIn](https://www.linkedin.com/in/pascal-nehlsen) - [mail@pascal-nehlsen.de](mailto:mail@pascal-nehlsen.de)
- Project Link: [https://github.com/PascalNehlsen/conduit](https://github.com/PascalNehlsen/conduit)
