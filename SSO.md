> Author: [SuperCode](https://github.com/iamsupercode)

# Oacey SSO Integration

## Purpose
This guide provides detailed steps for integrating authentication into your project using the central OAuth Provider **OaceySSO**.

## Please Note
- **Production Only:** This guide should be used for projects that are being deployed to production. Perform all test runs locally before proceeding.
- **No Customization:** Do not customize any provided code unless specifically instructed by an admin. Future packages will address customization needs.
- **Client Key and Secret Security:** Never include the **Client Key or Secret** in your GitHub history. These keys should only be stored on your **Local Machine** and the **Production server**.
- **Unique Client Credentials:** Do not use the same client credentials for different projects.

## Requirements
1. Request a Client Key and Secret Key for your project via the **Office YeDjango** Channel.
2. Have a basic understanding of [OAuth2.0](https://auth0.com/intro-to-iam/what-is-oauth-2#:~:text=Principles%20of%20OAuth2.0,OAuth%202.0%20uses%20Access%20Tokens.).
3. Ensure your project has no unresolved issues.
4. Install Python and Django if you haven't already. Refer to the [Django installation guide](https://docs.djangoproject.com/en/stable/topics/install/) for help.

## Step 1: Install Dependencies
Install the required Django package:

    pip install social-auth-app-django

## Step 2: Include Required Configurations for Django
1. **Download the necessary backend file:**
    - Download the Python file for your project and place it in **accounts/backends.py**. [Download Here](https://gist.github.com/iamsupercode/790c40d045e1774a3c47cf6b80616dec).

2. **Update your Django settings:**
    In your **{project}.settings.py**, add the following configurations:

    ```python
    INSTALLED_APPS = [
        # Your other apps
        'social_django',
    ]

    SOCIAL_AUTH_REDIRECT_IS_HTTPS = True

    AUTHENTICATION_BACKENDS = [
        # Your other backends here
        'accounts.backends.OaceyOAuth2',
    ]

    # Add Social Django Context Processors
    TEMPLATES = [
        {
            "OPTIONS": {
                "context_processors": [
                    # Your other options
                    'social_django.context_processors.backends',
                    'social_django.context_processors.login_redirect',
                ],
            },
        },
    ]

    # Load these values from .env file and do not hardcode them
    SOCIAL_AUTH_OACEY_KEY = '<CLIENT_KEY_YOU_RECEIVED>'
    SOCIAL_AUTH_OACEY_SECRET = '<CLIENT_SECRET_YOU_RECEIVED>'
    ```

    - **Tip:** Use a package like `python-decouple` to manage environment variables. This will help keep your secrets out of the codebase.
    
    Example:
    ```python
    from decouple import config

    SOCIAL_AUTH_OACEY_KEY = config('SOCIAL_AUTH_OACEY_KEY')
    SOCIAL_AUTH_OACEY_SECRET = config('SOCIAL_AUTH_OACEY_SECRET')
    ```

3. **Configure your environment variables:**
    - Create a `.env` file in your project's root directory with the following content:
    ```env
    SOCIAL_AUTH_OACEY_KEY=your_client_key
    SOCIAL_AUTH_OACEY_SECRET=your_client_secret
    ```

## Step 3: Include Social Django URLs
In your root URLs file, include the **social_django** URLs:

    ```python
    from django.urls import path, include

    urlpatterns = [
        # Your other paths
        path('auth/', include('social_django.urls', namespace='social')),
    ]
    ```

## Step 4: Migrate Database
Apply the necessary migrations to include the `social_django` models:

    python manage.py migrate

## Step 5: Update Templates
Update your templates to include login/logout links using the context processors:

    ```html
    {% if user.is_authenticated %}
        <a href="{% url 'social:logout' %}">Logout</a>
    {% else %}
        <a href="{% url 'social:begin' args='oacey' %}">Login with Oacey</a>
    {% endif %}
    ```

## Step 6: Final Steps
**You're done!** Push your production-ready version to a new branch named **production**.

## Troubleshooting Tips
- **Common Issues:**
  - Ensure your client key and secret are correctly loaded from the environment variables.
  - Check if the `social_django` app is correctly added to your `INSTALLED_APPS`.
  - Verify that the context processors are included in the `TEMPLATES` configuration.

- **Logs and Debugging:**
  - Use Django's debugging tools to trace issues.
  - Check the server logs for authentication-related messages.

## Resources
- [Django Documentation](https://docs.djangoproject.com/)
- [social-auth-app-django Documentation](https://python-social-auth.readthedocs.io/en/latest/backends/django.html)
- [OAuth2.0 Overview](https://auth0.com/intro-to-iam/what-is-oauth-2)

---

# Thank You! HAPPY CODING
