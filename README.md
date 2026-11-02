# ClearSky — Grade Statistics Platform

ClearSky is a microservice-based SaaS platform for publishing and analysing course grades. Instructors upload grade spreadsheets, students see their grades and course-wide statistics and can request a review, and each institution manages its users and buys the credits that grade uploads consume.

Built by **Team 27** for the *Software as a Service* course (SaaS 2025), School of Electrical and Computer Engineering, NTUA.

## Architecture

![Component diagram](docs/diagrams/Component%20Diagram.png)

The system is split into small services, each with its own MongoDB database, behind three orchestrators that implement the user-facing workflows. Services communicate over REST and asynchronously through RabbitMQ.

| Layer | Component | Responsibility |
|---|---|---|
| Frontend | `frontend_new` | Role-based web pages (HTML/JavaScript, Chart.js), served by nginx |
| Orchestrators | `userflow-orchestrator` | Sign-up, login (incl. Google OAuth), institutions and users |
| | `grade-orchestrator` | Initial/final grade upload, credit checks, statistics requests |
| | `review-orchestrator` | Review requests and instructor replies |
| Services | `user-auth-service` | Authentication and JWT tokens |
| | `user-management-service` | Users, roles and course enrolments |
| | `institution-service` | Institutions and their courses |
| | `credit-service` | Institution credit balance and usage |
| | `grade-service` | Parses Excel uploads and stores per-question and final grades |
| | `statistics-service` | Grade distributions and summary statistics per course |
| | `review-service` | Review requests and their status |
| | `notification-service` | E-mail notifications, consumed from RabbitMQ |
| Messaging | `rabbit-mq` | Message broker between services |

**Stack:** Node.js, Express, MongoDB (Mongoose), RabbitMQ, JWT, Google OAuth 2.0, nginx, Docker Compose.

More design diagrams (class, deployment, and sequence diagrams for every main use case) are in [`docs/diagrams`](docs/diagrams), and the full Visual Paradigm model is in [`architecture/`](architecture).

## Roles and features

- **Student** — view personal grades per course, see course statistics, request a grade review.
- **Instructor** — upload initial and final grades from Excel, view statistics, answer review requests.
- **Institution representative** — register and update the institution, manage users, purchase credits, view statistics.

## Running locally

Requires Docker and Docker Compose.

```bash
git clone https://github.com/harrissfiris/clearsky.git
cd clearsky
docker compose up --build
```

Then open [http://localhost](http://localhost). Seeder containers create the initial users on start-up.

Sample grade files to upload as an instructor are included: `grades.xlsx`, `grades1.xlsx`, `grades2.xlsx`, `grades3.xlsx`.

## Repository layout

```
frontend_new/          web pages and nginx config
*-orchestrator/        workflow orchestrators
*-service/             microservices (OpenAPI specs in openapi.yaml)
rabbit-mq/             message broker setup
architecture/          Visual Paradigm model
docs/diagrams/         exported UML diagrams
ai-log/                AI-assistance logs required by the course
docker-compose.yaml    full system definition
```

## License

See [LICENSE](LICENSE).
