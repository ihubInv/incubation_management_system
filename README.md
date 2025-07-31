# incubation_management_system

# Incubation Hub API

A comprehensive REST API for managing startup incubation data, analytics, and user management.

## Overview

Incubation Hub API provides a robust backend system for incubator management platforms, enabling organizations to track startups, funding, metrics, and generate analytics. The API is built using Django REST Framework with MongoDB integration.

## Repository

The project code is available at: [https://github.com/LearnCodeWithRam/incubation_management_system.git](https://github.com/LearnCodeWithRam/incubation_management_system.git)

## Features

- **Authentication**: JWT-based authentication system with token refresh
- **Startups Management**: Create, read, update, and delete startup information
- **User Management**: Role-based user system with profiles
- **Funding Tracking**: Monitor investment rounds, valuations, and equity
- **Performance Metrics**: Track startup growth, revenue, customer base, etc.
- **Document Management**: Store and retrieve startup-related documents
- **Analytics**: Comprehensive analytics and reporting features
- **Export**: Export data to Excel and PDF formats

## Installation & Setup

### Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/LearnCodeWithRam/incubation_management_system.git
   cd incubation_management_system
   ```

2. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Set up environment variables (create a `.env` file in the project root):
   ```
   SECRET_KEY=your_secret_key
   DEBUG=True
   ALLOWED_HOSTS=localhost,127.0.0.1
   MONGODB_URI=mongodb://localhost:27017/incubation_hub
   ```

5. Run migrations:
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

6. Create a superuser:
   ```bash
   python manage.py createsuperuser
   ```

7. Run the development server:
   ```bash
   python manage.py runserver
   ```

8. Access the local API at:
   ```
   http://localhost:8000/api/
   ```

9. Access the API documentation at:
   ```
   http://localhost:8000/swagger/
   http://localhost:8000/redoc/
   ```

### Production Deployment

1. Clone the repository on your production server:
   ```bash
   git clone https://github.com/LearnCodeWithRam/incubation_management_system.git
   cd incubation_management_system
   ```

2. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Set up environment variables for production:
   ```
   SECRET_KEY=your_secure_production_key
   DEBUG=False
   ALLOWED_HOSTS=your-domain.com,www.your-domain.com
   MONGODB_URI=mongodb://your-production-mongodb-uri
   ```

5. Run migrations:
   ```bash
   python manage.py migrate
   ```

6. Collect static files:
   ```bash
   python manage.py collectstatic
   ```

7. Configure Gunicorn:
   Create a `gunicorn_start.sh` script:
   ```bash
   #!/bin/bash
   NAME="incubation_hub"
   DIR=/path/to/your/project
   USER=your_user
   GROUP=your_group
   WORKERS=3
   BIND=unix:/path/to/your/project/run/gunicorn.sock
   DJANGO_SETTINGS_MODULE=incubation_hub.settings
   DJANGO_WSGI_MODULE=incubation_hub.wsgi
   LOG_LEVEL=error

   cd $DIR
   source venv/bin/activate

   exec gunicorn ${DJANGO_WSGI_MODULE}:application \
     --name $NAME \
     --workers $WORKERS \
     --user=$USER \
     --group=$GROUP \
     --bind=$BIND \
     --log-level=$LOG_LEVEL \
     --log-file=-
   ```

8. Configure Nginx:
   ```nginx
   server {
       listen 80;
       server_name your-domain.com www.your-domain.com;

       location = /favicon.ico { access_log off; log_not_found off; }
       location /static/ {
           root /path/to/your/project;
       }

       location /media/ {
           root /path/to/your/project;
       }

       location / {
           include proxy_params;
           proxy_pass http://unix:/path/to/your/project/run/gunicorn.sock;
       }
   }
   ```

9. Set up SSL with Let's Encrypt (recommended):
   ```bash
   sudo apt-get install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com -d www.your-domain.com
   ```

10. Set up a supervisor to manage the Gunicorn process:
    ```ini
    [program:incubation_hub]
    command=/path/to/your/project/gunicorn_start.sh
    user=your_user
    autostart=true
    autorestart=true
    redirect_stderr=true
    stdout_logfile=/path/to/your/project/logs/gunicorn-error.log
    ```

## API Documentation

### Base URL
- Local Development: `http://localhost:8000/api/`
- Production: `https://your-domain.com/api/`

### Authentication

The API uses JWT authentication. To access protected endpoints, include the token in the Authorization header:

```
Authorization: Bearer <access_token>
```

#### Getting a Token

```http
POST /auth/login/
```

Request body:
```json
{
  "email": "user@example.com",
  "password": "your_password"
}
```

Response:
```json
{
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "user": {
    "id": 1,
    "email": "user@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "role": "Admin",
    "is_active": true,
    "last_login": "2023-05-15T10:30:00Z",
    "profile": {
      "phone": "+91 9876543210",
      "profile_picture": null,
      "bio": null
    }
  }
}
```

#### Refreshing a Token

```http
POST /auth/refresh/
```

Request body:
```json
{
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

Response:
```json
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

### Key Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/startups/` | GET | List all startups (paginated) |
| `/startups/` | POST | Create a new startup |
| `/startups/{id}/` | GET | Get startup details |
| `/startups/{id}/` | PUT | Update startup details |
| `/startups/{id}/` | DELETE | Delete a startup |
| `/startups/categories/` | GET | List all startup categories |
| `/users/` | GET | List all users (paginated) |
| `/users/` | POST | Create a new user |
| `/users/{id}/` | GET | Get user details |
| `/users/{id}/` | PUT | Update user details |
| `/users/{id}/` | DELETE | Delete a user |
| `/analytics/overview/` | GET | Get analytics overview |
| `/analytics/startups-by-year/` | GET | Get startups count by year |
| `/analytics/funding-by-year/` | GET | Get funding amounts by year |
| `/analytics/category-distribution/` | GET | Get startup category distribution |
| `/export/startups/` | GET | Export startups data (Excel/PDF) |
| `/export/analytics/` | GET | Export analytics data (Excel/PDF) |

For detailed information about request parameters, response formats, and error codes, please refer to the [API Documentation](docs/api_documentation.md).

## Query Parameters

Many endpoints support filtering, sorting, and pagination:

| Parameter | Description | Example |
|-----------|-------------|---------|
| `page` | Page number | `?page=2` |
| `page_size` | Items per page | `?page_size=25` |
| `search` | Search term | `?search=Tech` |
| `ordering` | Field to order by | `?ordering=name` or `?ordering=-created_at` |
| `status` | Filter by status | `?status=Active` |
| `category` | Filter by category | `?category=FinTech` |

## Project Structure

```
incubation_hub_api/
├── manage.py
├── requirements.txt
├── incubation_hub/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── api/
│   ├── __init__.py
│   ├── models/
│   ├── serializers/
│   ├── views/
│   ├── urls.py
│   ├── permissions.py
│   ├── pagination.py
│   └── utils/
└── docs/
    └── api_documentation.md
```

## Technology Stack

- Django (3.2.20)
- Django REST Framework
- Djongo (MongoDB integration)
- JWT Authentication
- pandas, openpyxl, reportlab (for exports)
- drf-yasg (for Swagger documentation)

## Error Handling

The API uses standard HTTP status codes to indicate success or failure:

- `200 OK`: Request successful
- `201 Created`: Resource successfully created
- `204 No Content`: Request successful with no content to return
- `400 Bad Request`: Invalid request
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Request understood but refused
- `404 Not Found`: Resource not found
- `500 Internal Server Error`: Server error

Error responses follow this format:
```json
{
  "detail": "Error message",
  "code": "error_code",
  "errors": {
    "field_name": ["Error message for field"]
  }
}
```

## Database Backup & Restoration

### MongoDB Backup
```bash
mongodump --uri="mongodb://localhost:27017/incubation_hub" --out=/path/to/backup/directory
```

### MongoDB Restore
```bash
mongorestore --uri="mongodb://localhost:27017/incubation_hub" /path/to/backup/directory
```

## Monitoring & Logging

In production, set up monitoring using:
- Sentry for error tracking
- Prometheus and Grafana for system monitoring
- ELK Stack (Elasticsearch, Logstash, Kibana) for log management

Configure logging in `settings.py`:
```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': '/path/to/logs/django.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': True,
        },
    },
}
```

## License

[MIT License](LICENSE)

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Contact

For support or queries, please contact: support@incubationhub.com
