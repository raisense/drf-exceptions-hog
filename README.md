# DRF Exceptions Hog

Standardized and **easy-to-parse** API error responses for [Django REST Framework][drf].

After reusing similar code in multiple projects, we realized this might actually help others. The problem we're trying to solve is that DRF exceptions tend to vary in format and therefore require complex parsing logic on the frontend, which generally needs to be implemented in more than one language or framework. This simple package standardizes the exception responses to simplify the parsing logic on the frontend and enable developers to provide clear errors to their users (instead of the cryptic or even shady-looking parsing errors). This package is inspired on the way [Stripe API](https://stripe.com/docs/api/errors) handles errors. See an example below.

You will get predictable responses like these:

```json
// Example 1
{
  "type": "validation_error",
  "code": "required",
  "detail": "This field is required.",
  "attr": "name"
}

// Example 2
{
    "type": "authentication_error",
    "code": "permission_denied",
    "detail": "You do not have permission to perform this operation.",
    "attr": null
}

```

instead of these:

```json
// Example 1
{
  "name": ["This field is required."]
}

// Example 2
{
    "detail": "You do not have permission to perform this operation."
}
```

**Note:** Currently we only support JSON responses. If you'd like us to support a different response format, please open an issue or a PR (see [Contributing](#-contributing))

## 🔌 Usage

To start using DRF Exceptions Hog please follow these instructions:

Install the package with `pip`

```bash
pip install drf-exceptions-hog
```

Update your DRF configuration on your `settings.py`.

```python
REST_FRAMEWORK={
    "EXCEPTION_HANDLER": "exceptions_hog.exception_handler",
}
```

## 📑 Documentation

We're working on more comprehensive documentation. Feel free to open a PR to contribute to this. In the meantime, you will find the most relevant information for this package here.

### Response structure

All responses handled by DRF Exceptions Hog have the following format:

```json
{
  "type": "server_error",
  "code": "server_error",
  "detail": "Something went wrong.",
  "attr": null
}
```

where:

- `type` entails the high-level grouping of the type error returned (See [Error Types](#error-types)).
- `code` is a machine-friendly error code specific for this type of error (e.g. `permission_denied`, `method_not_allowed`, `required`)
- `detail` will contain human-friendly information on the error (e.g. "This field is required.", "Authentication credentials were not provided.").
  - For security reasons (mainly to avoid leaking sensitive information) this attribute will return a generic error message for unhandled server exceptions, like an `ImportError`.
  - If you use Django localization, all our exception detail messages support using multiple languages.
- `attr` will contain the name of the attribute to which the exception is related. Relevant mostly for `validation_error`s.

### Error types

Our package introduces the following general error types (but feel free to add custom ones):

- `authentication_error` indicates there is an authentication-related problem with the request (e.g. no authentication credentials provided, invalid or expired credentials provided, credentials have insufficient privileges, etc.)
- `invalid_request` indicates a general issue with the request that must be fixed by the client, excluding validation errors (e.g. request has an invalid media type format, request is malformed, etc.)
- `server_error` indicates a generic internal server error that needs to be addressed on the server.
- `throttled_error` indicates the request is throttled or rate limited and must be retried by the client at a later time.
- `validation_error` indicates the request has not passed validation and must be fixed by the client (e.g. a required attribute was not provided, an incorrect data type was passed for an attribute, etc.)

## 🤝 Contributing

Want to help move this project forward? Read our [CONTRIBUTING.md](CONTRIBUTING.md).

## 👩‍💻 Development

To run a local copy of the package for development, please follow these instructions:

1. Clone the repository.
1. **[Optional]**. Install and activate a virtual enviroment. Example:

   ```bash
   python3 -m venv env && source env/bin/activate
   ```

1. Install the project dependencies and the test dependencies.

   ```bash
   python setup.py develop
   pip install -r requirements-test.txt
   ```

1. Run the tests to make sure everything is working as expected.

   ```bash
   python runtests.py
   ```

1. Start coding!

## 👨‍⚖️ License

We ♥ Open Source! This repository is MIT licensed by PostHog. Full license [here](LICENSE).

[drf]: https://github.com/encode/django-rest-framework
