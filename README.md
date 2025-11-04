# Home Assistant test container

When testing a Home Assistant integration it can be useful to
have a containerised version with a user and long-lived token already configured.

This image is **not intended for production use**.

## Usage

```shell
docker run \
  --publish 8123:8123 \
  ghcr.io/ciaranmcnulty/homeassistant-test-container:latest
```

This container is used the same way as [the Home Assistant container](https://www.home-assistant.io/installation/linux/#platform-installation)
except that you can configure a test user:

| Variable      | Default         | Description               |
|---------------|-----------------|---------------------------|
| `HA_USER`     | `test_user`     | A login user              |
| `HA_PASSWORD` | `test_password` | A login password          |
| `HA_NAME`     | `Test User`     | The display name          |
| `HA_TOKEN`    | (see below)     | A long-lived access token |

The default access token's value is:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhOWRkNzY0NDkwMTA0NTg2ODVmMmZjNjFhZTRkNWY2NiIsImlhdCI6MTc2MjE3MTYzNSwiZXhwIjoyMDc3NTMxNjM1fQ.ud4A0VlTCDwE-Ir94AC4R9QOui3rqnGd4ib24AvokLY
```

If for some reason you want to replace it, you will need to generate a JWT using a script similar to the following:

```python
import jwt
import time
import secrets

secret_token = secrets.token_hex(64)

payload = {
    "iss": secret_token,        # the issuer is your secret token
    "iat": int(time.time()),    # issued at (current time)
    "exp": int(time.time()) + 3600*24*365*10  # expiration (10 years later)
}

jwt_token = jwt.encode(payload, secret_token, algorithm="HS256")
print(jwt_token)
```

