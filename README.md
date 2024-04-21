# Django Quickstart
This repository serves as a quickstart guide for new Django project. It is targeted for developers that are already familiar with Django as the indivdual steps are not explained in detail.

## Django Project Setup

1. Copy over and Adjust the following file in the top level of your project:
```Dockerfile```
```docker-compose.yml```
```requirements.dev.txt```
```requirements.txt```
```.gitignore```
```.dockerignore```
```.env.default```

2. Create a .env file in the top level folder and copy the content from the .env.default file over

3. Build the container with the following command:
```
docker compose build
```

4. Initialize Django app in src folder with the following command:
```
docker compose run --rm app sh -c "django-admin startproject app ."
```

5. Copy over the ```.flake8``` file into the /src folder.

6. Open the ```settings.py``` and import os like so:
```
import os
```

7. In the ```settings.py``` change the ```DATABASES``` section to:
```
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": os.environ.get("DB_HOST"),
        "NAME": os.environ.get("DB_NAME"),
        "USER": os.environ.get("DB_USER"),
        "PASSWORD": os.environ.get("DB_PASS"),
    }
}
```

8. In the same ```settings.py```, add the following apps to ```INSTALLED_APPS```:
```
    "rest_framework",
    "drf_spectacular",
```

9. Create a utils app with the following command:
```
docker compose run --rm app sh -c "python manage.py startapp utils"
```

10. Open the ```settings.py``` again and add ```'utils'``` to ```INSTALLED_APPS``` as well.

11. Delete the following files and folders from your utils app:
```views.py```
```models.py```
```migrations```

12. In the utils app, create a ```management/commands/``` folder.

13. Place the ```wait_for_db.py``` file from the ```utils``` folder of this project into the just created ```management/commands/``` folder.

14. Back in the utils app, replace the ```tests.py``` with the one from the ```utils``` folder of this project.

15. In the ```urls.py``` of the ```app``` folder, add the following import:
```
from drf_spectacular.views import (
    SpectacularAPIView,
    SpectacularSwaggerView,
)
```

16. In the same ```urls.py```, add the following paths to the ```urlpatterns```:
```
    path("api/schema/", SpectacularAPIView.as_view(), name="api-schema"),
    path(
        "api/docs/",
        SpectacularSwaggerView.as_view(url_name="api-schema"),
        name="api-docs",
    ),
```

## Opentional: User management

The following steps are only necessary for services that are supposed to include user management. If your service does not require its own user management (for example when the user management is handled in a different service), you can skip these steps and continue at step 25.

17. Create a user app with the following command:
```
docker compose run --rm app sh -c "python manage.py startapp user"
```

18. Open the ```settings.py``` and add ```'user'``` to ```INSTALLED_APPS```.

19. Import ```from datetime import timedelta``` in the  ```settings.py```.

20. Add the following code to the end of the ```settings.py```:
```
AUTH_USER_MODEL = "user.User"

REST_FRAMEWORK = {
    "DEFAULT_SCHEMA_CLASS": "drf_spectacular.openapi.AutoSchema",
    "DEFAULT_AUTHENTICATION_CLASSES": (
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ),
}

SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=5),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
    "SIGNING_KEY": "secret_key_here",
}
```

21. Add the following files from the ```user_management``` folder to the user app (if the files already exist, replace them):```models.py``` ```serializers.py``` ```views.py``` ```urls.py``` ```admin.py```

22. In the ```urls.py``` of the app folder, add/replace the following import:
```
from django.urls import path, include
```

23. In the same ```urls.py```, add the following path to the ```urlpatterns```:
```
    path("api/user/", include("user.urls")),
```

24. Run the following command to create an initial migration (necessary for the new User model):
```
docker compose run --rm app sh -c "python manage.py makemigrations"
```

## Start up the application

25. Run migration with the following command:
```
docker compose run --rm app sh -c "python manage.py migrate"
```

26. Create a super user for the django admin panel with the following command:
```
docker compose run --rm app sh -c "python manage.py createsuperuser"
```

27. Start the application with the following command:
```
docker compose up
```


