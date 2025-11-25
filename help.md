# Django REST Framework Backend

This module creates a bare minimum Django REST Framework API application. Django is a popular Python web framework, and Django REST Framework (DRF) extends it for building APIs.

## What This Creates

A Django REST Framework application with:
- REST API endpoints
- Django admin panel
- CORS support for frontend
- Testing framework (pytest)
- Gunicorn for production server

---

## Configuration Fields

### Python Version `pythonVersion`
**What it is**: The version of the Python programming language.

**Options**:
- `3.12` - Latest stable version

**Note**: Python 3.12 is the current standard. Newer versions will be added as they become stable.

**Why Python 3.12**:
- Latest language features
- Best performance
- Active security support
- Supported until 2028

---

### Django Version `djangoVersion`
**What it is**: The version of the Django web framework.

**Options**:
- `5.2` - Latest LTS (Long Term Support) version

**Why Django 5.2**:
- Latest stable release
- Long-term support (security updates until 2027)
- Best practices and security
- Modern async support

---

### Django REST Framework Version `drfVersion`
**What it is**: The version of Django REST Framework for building APIs.

**Options**:
- `3.16` - Latest stable version

**Why DRF 3.16**:
- Latest features and bug fixes
- Full Django 5.x support
- Best documentation
- Active maintenance

---

## What Gets Created

### Project Structure
```
backend/
├── prj_{projectname}/          # Django project settings
│   ├── settings/
│   │   ├── base.py            # Base settings
│   │   ├── development.py     # Dev settings
│   │   └── production.py      # Production settings
│   ├── urls.py                # URL routing
│   └── wsgi.py                # WSGI application
├── app_core/                   # Main application
│   ├── models.py              # Database models
│   ├── serializers.py         # API serializers
│   ├── views.py               # API views
│   └── urls.py                # App-specific URLs
├── manage.py                   # Django management script
├── requirements.txt            # Python dependencies
└── pytest.ini                  # Test configuration
```

### Endpoints Created
- `GET /admin/` - Django admin panel (development)
- `GET /api/` - API root (lists available endpoints)
- `GET /api/health/` - Health check endpoint

---

## Common Commands

### Run Development Server
```bash
python manage.py runserver
```
Opens http://localhost:8000 by default.

### Create Database Migrations
```bash
python manage.py makemigrations
```
Creates migration files for database schema changes.

### Apply Migrations
```bash
python manage.py migrate
```
Applies migrations to update database schema.

### Create Superuser (Admin)
```bash
python manage.py createsuperuser
```
Creates an admin user for Django admin panel.

### Run Tests
```bash
pytest
```

### Run with Gunicorn (Production)
```bash
gunicorn prj_{projectname}.wsgi:application
```

### Django Shell
```bash
python manage.py shell
```
Opens Python shell with Django environment loaded.

---

## Environment Configuration

Django uses settings files for configuration. The project structure includes:

- `settings/base.py` - Common settings for all environments
- `settings/development.py` - Development-specific settings
- `settings/production.py` - Production-specific settings

Set environment:
```bash
export DJANGO_SETTINGS_MODULE=prj_myapp.settings.development  # Dev
export DJANGO_SETTINGS_MODULE=prj_myapp.settings.production   # Prod
```

---

## Common Issues

### "Port 8000 Already in Use"
**Problem**: Another application is using port 8000.

**Solutions**:
- Stop the other application
- Change port: `python manage.py runserver 8001`
- Find and kill process: `lsof -i :8000` then `kill -9 <PID>`

### "No Such Table" Error
**Problem**: Database tables don't exist.

**Solutions**:
- Run migrations: `python manage.py migrate`
- Check if migrations were created: `python manage.py makemigrations`
- Verify database connection in settings

### "CORS Error" from Frontend
**Problem**: Browser blocks API requests due to CORS policy.

**Solutions**:
- Add frontend URL to `CORS_ALLOWED_ORIGINS` in settings
- Verify `django-cors-headers` is installed
- Check CORS middleware is enabled in settings
- For development, can use `CORS_ALLOW_ALL_ORIGINS = True` (not for production!)

### "ModuleNotFoundError"
**Problem**: Missing Python package.

**Solutions**:
- Install requirements: `pip install -r requirements.txt`
- Activate virtual environment first
- Check package name spelling in requirements.txt

### "DisallowedHost" Error
**Problem**: Django blocks request due to host configuration.

**Solutions**:
- Add domain to `ALLOWED_HOSTS` in settings
- For development: `ALLOWED_HOSTS = ['localhost', '127.0.0.1']`
- For production: `ALLOWED_HOSTS = ['myapp.com', 'api.myapp.com']`

---

## Virtual Environment Setup

**Always use a virtual environment for Python projects:**

### Create Virtual Environment
```bash
python -m venv venv
```

### Activate Virtual Environment

Mac/Linux:
```bash
source venv/bin/activate
```

Windows:
```bash
venv\Scripts\activate
```

### Deactivate
```bash
deactivate
```

### Install Dependencies
```bash
pip install -r requirements.txt
```

---

## Database Configuration

Django supports multiple databases. Configure in `settings/base.py`:

### PostgreSQL (recommended for production)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME', 'myapp_db'),
        'USER': os.getenv('DB_USER', 'postgres'),
        'PASSWORD': os.getenv('DB_PASSWORD', 'password'),
        'HOST': os.getenv('DB_HOST', 'localhost'),
        'PORT': os.getenv('DB_PORT', '5432'),
    }
}
```

### SQLite (default for development)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

---

## Django Admin Panel

Django includes a powerful admin interface for managing data.

### Enable Admin for Your Models
```python
# app_core/admin.py
from django.contrib import admin
from .models import MyModel

@admin.register(MyModel)
class MyModelAdmin(admin.ModelAdmin):
    list_display = ['id', 'name', 'created_at']
    search_fields = ['name']
```

### Access Admin Panel
1. Create superuser: `python manage.py createsuperuser`
2. Start dev server: `python manage.py runserver`
3. Go to http://localhost:8000/admin/
4. Log in with superuser credentials

---

## API Development Tips

### Creating an API Endpoint

**1. Define Model** (`models.py`):
```python
class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField()
```

**2. Create Serializer** (`serializers.py`):
```python
from rest_framework import serializers

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'published_date']
```

**3. Create ViewSet** (`views.py`):
```python
from rest_framework import viewsets

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

**4. Register URL** (`urls.py`):
```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = router.urls
```

Now you have a full CRUD API at `/api/books/`!

---

## Best Practices

1. **Always use virtual environment** - Isolates project dependencies
2. **Use PostgreSQL in production** - SQLite is only for development
3. **Run migrations** after model changes
4. **Use Django admin** for quick data management
5. **Use DRF ViewSets** for standard CRUD operations
6. **Write tests** - Django has excellent testing support
7. **Use environment variables** for secrets (never commit to code)

---

## Additional Resources

- **Django Documentation**: https://docs.djangoproject.com/en/5.2/
- **Django REST Framework**: https://www.django-rest-framework.org/
- **Django Tutorial**: https://docs.djangoproject.com/en/5.2/intro/tutorial01/
- **DRF Tutorial**: https://www.django-rest-framework.org/tutorial/quickstart/
- **Real Python Django**: https://realpython.com/tutorials/django/
