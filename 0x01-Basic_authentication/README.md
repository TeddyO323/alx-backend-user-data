# 0x01. Basic Authentication

## Back-end | Authentication

![Authentication](images/6ccb363443a8f301bc2bc38d7a08e9650117de7c.png)

### Background Context
In this project, you will learn what the authentication process means and implement Basic Authentication on a simple API.

In the industry, you should not implement your own Basic authentication system and use a module or framework that does it for you (like in Python-Flask: Flask-HTTPAuth). Here, for the learning purpose, we will walk through each step of this mechanism to understand it by doing.

### Resources
Read or watch:
- [REST API Authentication Mechanisms](https://www.youtube.com/watch?v=501dpx2IjGY)
- [Base64 in Python](https://docs.python.org/3.7/library/base64.html)
- [HTTP header Authorization](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)
- [Flask](https://palletsprojects.com/p/flask/)
- [Base64 - concept](https://en.wikipedia.org/wiki/Base64)

### Learning Objectives
At the end of this project, you are expected to be able to explain to anyone, without the help of Google:

#### General
- What authentication means
- What Base64 is
- How to encode a string in Base64
- What Basic authentication means
- How to send the Authorization header

### Requirements

#### Python Scripts
- All your files will be interpreted/compiled on Ubuntu 18.04 LTS using python3 (version 3.7)
- All your files should end with a new line
- The first line of all your files should be exactly `#!/usr/bin/env python3`
- A `README.md` file, at the root of the folder of the project, is mandatory
- Your code should use the `pycodestyle` style (version 2.5)
- All your files must be executable
- The length of your files will be tested using `wc`
- All your modules should have a documentation (`python3 -c 'print(__import__("my_module").__doc__)'`)
- All your classes should have a documentation (`python3 -c 'print(__import__("my_module").MyClass.__doc__)'`)
- All your functions (inside and outside a class) should have a documentation (`python3 -c 'print(__import__("my_module").my_function.__doc__)' and `python3 -c 'print(__import__("my_module").MyClass.my_function.__doc__)'`)
- A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class, or method (the length of it will be verified)

### Tasks

#### 0. Simple-basic-API
**Mandatory**

Download and start your project from this [archive.zip](https://example.com).

In this archive, you will find a simple API with one model: User. Storage of these users is done via serialization/deserialization in files.

**Setup and start server**
```sh
bob@dylan:~$ pip3 install -r requirements.txt
...
bob@dylan:~$
bob@dylan:~$ API_HOST=0.0.0.0 API_PORT=5000 python3 -m api.v1.app
 * Serving Flask app "app" (lazy loading)
...
bob@dylan:~$
```

**Use the API (in another tab or in your browser)**
```sh
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/status" -vvv
*   Trying 0.0.0.0...
* TCP_NODELAY set
* Connected to 0.0.0.0 (127.0.0.1) port 5000 (#0)
> GET /api/v1/status HTTP/1.1
> Host: 0.0.0.0:5000
> User-Agent: curl/7.54.0
> Accept: */*
>
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Content-Type: application/json
< Content-Length: 16
< Access-Control-Allow-Origin: *
< Server: Werkzeug/1.0.1 Python/3.7.5
< Date: Mon, 18 May 2020 20:29:21 GMT
<
{"status":"OK"}
* Closing connection 0
bob@dylan:~$
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`

#### 1. Error handler: Unauthorized
**Mandatory**

What is the HTTP status code for a request unauthorized? 401, of course!

**Edit `api/v1/app.py`:**
- Add a new error handler for this status code, the response must be:
  - A JSON: `{"error": "Unauthorized"}`
  - Status code 401
  - You must use `jsonify` from Flask

For testing this new error handler, add a new endpoint in `api/v1/views/index.py`:

- Route: GET `/api/v1/unauthorized`
- This endpoint must raise a 401 error by using `abort` - Custom Error Pages
- By calling `abort(401)`, the error handler for 401 will be executed.

**In the first terminal:**
```sh
bob@dylan:~$ API_HOST=0.0.0.0 API_PORT=5000 python3 -m api.v1.app
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
....
```

**In a second terminal:**
```sh
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/unauthorized"
{
  "error": "Unauthorized"
}
bob@dylan:~$
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/unauthorized" -vvv
*   Trying 0.0.0.0...
* TCP_NODELAY set
* Connected to 0.0.0.0 (127.0.0.1) port 5000 (#0)
> GET /api/v1/unauthorized HTTP/1.1
> Host: 0.0.0.0:5000
> User-Agent: curl/7.54.0
> Accept: */*
>
* HTTP 1.0, assume close after body
< HTTP/1.0 401 UNAUTHORIZED
< Content-Type: application/json
< Content-Length: 30
< Server: Werkzeug/0.12.1 Python/3.4.3
< Date: Sun, 24 Sep 2017 22:50:40 GMT
<
{
  "error": "Unauthorized"
}
* Closing connection 0
bob@dylan:~$
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/app.py`, `api/v1/views/index.py`

#### 2. Error handler: Forbidden
**Mandatory**

What is the HTTP status code for a request where the user is authenticated but not allowed to access a resource? 403, of course!

**Edit `api/v1/app.py`:**
- Add a new error handler for this status code, the response must be:
  - A JSON: `{"error": "Forbidden"}`
  - Status code 403
  - You must use `jsonify` from Flask

For testing this new error handler, add a new endpoint in `api/v1/views/index.py`:

- Route: GET `/api/v1/forbidden`
- This endpoint must raise a 403 error by using `abort` - Custom Error Pages
- By calling `abort(403)`, the error handler for 403 will be executed.

**In the first terminal:**
```sh
bob@dylan:~$ API_HOST=0.0.0.0 API_PORT=5000 python3 -m api.v1.app
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
....
```

**In a second terminal:**
```sh
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/forbidden"
{
  "error": "Forbidden"
}
bob@dylan:~$
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/forbidden" -vvv
*   Trying 0.0.0.0...
* TCP_NODELAY set
* Connected to 0.0.0.0 (127.0.0.1) port 5000 (#0)
> GET /api/v1/forbidden HTTP/1.1
> Host: 0.0.0.0:5000
> User-Agent: curl/7.54.0
> Accept: */*
>
* HTTP 1.0, assume close after body
< HTTP/1.0 403 FORBIDDEN
< Content-Type: application/json
< Content-Length: 27
< Server: Werkzeug/0.12.1 Python/3.4.3
< Date: Sun, 24 Sep 2017 22:54:22 GMT
<
{
  "error": "Forbidden"
}
* Closing connection 0
bob@dylan:~$
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/app.py`, `api/v1/views/index.py`

#### 3. Auth

 class
**Mandatory**

Now, you will create a class to manage the API authentication.

**Create a folder `api/v1/auth`**

**Create an empty file `api/v1/auth/__init__.py`**

**Create a new class `Auth` in the file `api/v1/auth/auth.py`**

- Create a method `def require_auth(self, path: str, excluded_paths: List[str]) -> bool:` in `Auth`:
  - Return False - path is in the list of excluded paths
  - Return True otherwise
- Create a method `def authorization_header(self, request=None) -> str:` in `Auth`:
  - Return None - request does not contain the header key Authorization
  - Return the value of the header request Authorization

```python
#!/usr/bin/env python3
""" Auth class
"""

from typing import List

class Auth:
    """ Auth class
    """

    def require_auth(self, path: str, excluded_paths: List[str]) -> bool:
        """ Check if the path requires authentication
        """
        if path is None or excluded_paths is None or len(excluded_paths) == 0:
            return True

        path = path.rstrip('/')

        for excluded_path in excluded_paths:
            if excluded_path.rstrip('/') == path:
                return False

        return True

    def authorization_header(self, request=None) -> str:
        """ Get the value of the header request Authorization
        """
        if request is None:
            return None

        return request.headers.get('Authorization', None)
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/auth.py`, `api/v1/auth/__init__.py`

#### 4. Define which routes don't need authentication
**Mandatory**

Update the `Auth` class to filter the routes which don’t need authentication.

- Update the method `require_auth` in `Auth`:
  - If `path` is None, return True
  - If `excluded_paths` is None or not a list of strings, return True
  - If `path` is in `excluded_paths`, return False
  - If one string in `excluded_paths` ends with a wildcard (*), return False if `path` starts with that string (without the star)

Example:

```python
from api.v1.auth.auth import Auth

a = Auth()

print(a.require_auth("/api/v1/status", ["/api/v1/status"]))  # False
print(a.require_auth("/api/v1/status", ["/api/v1/status/"]))  # False
print(a.require_auth("/api/v1/status", ["/api/v1/stats"]))  # True
print(a.require_auth("/api/v1/users", ["/api/v1/*"]))  # False
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/auth.py`

#### 5. Request validation!
**Mandatory**

Update the `Auth` class to validate all requests to secure the API.

- Update the `Auth` class in `api/v1/auth/auth.py`:
  - Create a method `def current_user(self, request=None) -> TypeVar('User'):` that returns None - request is not authenticated.

Update `api/v1/app.py`:

- Create a variable `auth` initialized to `None` after the CORS definition
- Before each request (`app.before_request`), if `auth` is different from None:
  - Validate that all requests to secure the API:
    - If the request path is not in the list of excluded paths, return an error 401
    - If `request.authorization_header` returns None, return an error 401
    - If `auth.current_user(request)` returns None, return an error 403

**In the first terminal:**
```sh
bob@dylan:~$ API_HOST=0.0.0.0 API_PORT=5000 python3 -m api.v1.app
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
....
```

**In a second terminal:**
```sh
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/users" -vvv
{
  "error": "Unauthorized"
}
bob@dylan:~$
bob@dylan:~$ curl "http://0.0.0.0:5000/api/v1/users" -H "Authorization: Basic dGVzdDp0ZXN0" -vvv
{
  "error": "Forbidden"
}
bob@dylan:~$
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/auth.py`, `api/v1/app.py`

#### 6. Basic - Base64 part
**Mandatory**

Now you will decode the Base64 part of the Authorization header.

- Create a new class `BasicAuth` that inherits from `Auth` in `api/v1/auth/basic_auth.py`
- Create a method `def extract_base64_authorization_header(self, authorization_header: str) -> str:` in `BasicAuth`:
  - Return None if `authorization_header` is None
  - Return None if `authorization_header` is not a string
  - Return None if `authorization_header` doesn’t start by `Basic` (with a space at the end)
  - Otherwise, return the value after `Basic` (after the space)

```python
#!/usr/bin/env python3
""" Basic Auth
"""

from api.v1.auth.auth import Auth

class BasicAuth(Auth):
    """ Basic Auth
    """

    def extract_base64_authorization_header(self, authorization_header: str) -> str:
        """ Extract Base64 Authorization header
        """
        if authorization_header is None:
            return None

        if not isinstance(authorization_header, str):
            return None

        if not authorization_header.startswith("Basic "):
            return None

        return authorization_header.split(" ")[1]
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/basic_auth.py`

#### 7. Basic - Base64 decode
**Mandatory**

Now you will decode the Base64 part of the Authorization header to plain text.

- Create a method `def decode_base64_authorization_header(self, base64_authorization_header: str) -> str:` in `BasicAuth`:
  - Return None if `base64_authorization_header` is None
  - Return None if `base64_authorization_header` is not a string
  - Return the decoded value in UTF8 if `base64_authorization_header` is a valid Base64

You can use [base64.b64decode](https://docs.python.org/3/library/base64.html#base64.b64decode).

```python
#!/usr/bin/env python3
""" Basic Auth
"""

import base64
from api.v1.auth.auth import Auth

class BasicAuth(Auth):
    """ Basic Auth
    """

    def extract_base64_authorization_header(self, authorization_header: str) -> str:
        """ Extract Base64 Authorization header
        """
        if authorization_header is None:
            return None

        if not isinstance(authorization_header, str):
            return None

        if not authorization_header.startswith("Basic "):
            return None

        return authorization_header.split(" ")[1]

    def decode_base64_authorization_header(self, base64_authorization_header: str) -> str:
        """ Decode Base64 Authorization header
        """
        if base64_authorization_header is None:
            return None

        if not isinstance(base64_authorization_header, str):
            return None

        try:
            decoded = base64.b64decode(base64_authorization_header).decode('utf-8')
        except Exception:
            return None

        return decoded
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/basic_auth.py`

#### 8. Basic - User credentials
**Mandatory**

Now you will extract the user credentials from the Base64 decoded value.

- Create a method `def extract_user_credentials(self, decoded_base64_authorization_header: str) -> (str, str):` in `BasicAuth`:
  - Return None, None if `decoded_base64_authorization_header` is None
  - Return None, None if `decoded_base64_authorization_header` is not a string
  - Return None, None if `decoded_base64_authorization_header` doesn’t contain `:`
  - Otherwise, return the user email and password as a tuple (user_email, user_pwd) - user email and password are separated by a `:`

```python
#!/usr/bin/env python3
""" Basic Auth
"""

import base64
from api.v1.auth.auth import Auth

class BasicAuth(Auth):
    """ Basic Auth
    """

    def extract_base64_authorization_header(self, authorization_header: str) -> str:
        """ Extract Base64 Authorization header
        """
        if authorization_header is None:
            return None

        if not isinstance(authorization_header, str):
            return None

        if not authorization_header.startswith("Basic "):
            return None

        return authorization_header.split(" ")[1]

    def decode_base64_authorization_header(self, base64_authorization_header: str) -> str:
        """ Decode Base64 Authorization header
        """
        if base64_authorization_header is None:
            return None

        if not isinstance(base64_authorization_header, str):
            return None

        try:
            decoded = base64.b64decode(base64_authorization_header).decode('utf-8')
        except Exception:
            return None

        return decoded

    def extract_user_credentials(self, decoded_base64_authorization_header: str) -> (str, str):
        """ Extract user credentials
        """
        if decoded_base64_authorization_header is None:
            return None, None

        if not isinstance(decoded_base64_authorization_header, str):
            return None, None

        if ':' not in decoded_base64_authorization_header:
            return None, None

        user_email, user_pwd = decoded_base64_authorization_header.split(':', 1)

        return user_email, user_pwd
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/basic_auth.py`

#### 9. Basic - User object
**Mandatory**

Now, you will retrieve the `User` instance based on his/her credentials.

- Create a method `def user_object_from_credentials(self, user_email: str, user_pwd: str) -> TypeVar('User'):` in `BasicAuth`:
  - Return None if `user_email` is None or not a string
  - Return None if `user_pwd` is None or not a string
  - Return None if your storage engine (file) doesn’t find the `User` instance
  - Return None if the `User` password doesn’t match with the `

user_pwd`
  - Otherwise, return the `User` instance

To retrieve the `User` instance, you should use the `User.search` class method. Test the returned list, it should contain only one `User`.

```python
#!/usr/bin/env python3
""" Basic Auth
"""

import base64
from api.v1.auth.auth import Auth
from models.user import User

class BasicAuth(Auth):
    """ Basic Auth
    """

    def extract_base64_authorization_header(self, authorization_header: str) -> str:
        """ Extract Base64 Authorization header
        """
        if authorization_header is None:
            return None

        if not isinstance(authorization_header, str):
            return None

        if not authorization_header.startswith("Basic "):
            return None

        return authorization_header.split(" ")[1]

    def decode_base64_authorization_header(self, base64_authorization_header: str) -> str:
        """ Decode Base64 Authorization header
        """
        if base64_authorization_header is None:
            return None

        if not isinstance(base64_authorization_header, str):
            return None

        try:
            decoded = base64.b64decode(base64_authorization_header).decode('utf-8')
        except Exception:
            return None

        return decoded

    def extract_user_credentials(self, decoded_base64_authorization_header: str) -> (str, str):
        """ Extract user credentials
        """
        if decoded_base64_authorization_header is None:
            return None, None

        if not isinstance(decoded_base64_authorization_header, str):
            return None, None

        if ':' not in decoded_base64_authorization_header:
            return None, None

        user_email, user_pwd = decoded_base64_authorization_header.split(':', 1)

        return user_email, user_pwd

    def user_object_from_credentials(self, user_email: str, user_pwd: str) -> TypeVar('User'):
        """ Retrieve User instance based on credentials
        """
        if user_email is None or not isinstance(user_email, str):
            return None

        if user_pwd is None or not isinstance(user_pwd, str):
            return None

        try:
            user_list = User.search({'email': user_email})
        except Exception:
            return None

        if len(user_list) == 0:
            return None

        user = user_list[0]

        if not user.is_valid_password(user_pwd):
            return None

        return user
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/basic_auth.py`

#### 10. Basic - Overload current_user
**Mandatory**

Finally, you will update the `current_user` method to retrieve the `User` instance for a request.

- Update the method `def current_user(self, request=None) -> TypeVar('User'):` in `BasicAuth`:
  - You must first extract the Authorization header from the request
  - Decode the Base64 value from the Authorization header
  - Extract the user email and password from the Base64 value
  - Retrieve the `User` instance based on these credentials

Update `api/v1/app.py` to import `BasicAuth` instead of `Auth` and use an instance of `BasicAuth`.

```python
#!/usr/bin/env python3
""" Basic Auth
"""

import base64
from api.v1.auth.auth import Auth
from models.user import User

class BasicAuth(Auth):
    """ Basic Auth
    """

    def extract_base64_authorization_header(self, authorization_header: str) -> str:
        """ Extract Base64 Authorization header
        """
        if authorization_header is None:
            return None

        if not isinstance(authorization_header, str):
            return None

        if not authorization_header.startswith("Basic "):
            return None

        return authorization_header.split(" ")[1]

    def decode_base64_authorization_header(self, base64_authorization_header: str) -> str:
        """ Decode Base64 Authorization header
        """
        if base64_authorization_header is None:
            return None

        if not isinstance(base64_authorization_header, str):
            return None

        try:
            decoded = base64.b64decode(base64_authorization_header).decode('utf-8')
        except Exception:
            return None

        return decoded

    def extract_user_credentials(self, decoded_base64_authorization_header: str) -> (str, str):
        """ Extract user credentials
        """
        if decoded_base64_authorization_header is None:
            return None, None

        if not isinstance(decoded_base64_authorization_header, str):
            return None, None

        if ':' not in decoded_base64_authorization_header:
            return None, None

        user_email, user_pwd = decoded_base64_authorization_header.split(':', 1)

        return user_email, user_pwd

    def user_object_from_credentials(self, user_email: str, user_pwd: str) -> TypeVar('User'):
        """ Retrieve User instance based on credentials
        """
        if user_email is None or not isinstance(user_email, str):
            return None

        if user_pwd is None or not isinstance(user_pwd, str):
            return None

        try:
            user_list = User.search({'email': user_email})
        except Exception:
            return None

        if len(user_list) == 0:
            return None

        user = user_list[0]

        if not user.is_valid_password(user_pwd):
            return None

        return user

    def current_user(self, request=None) -> TypeVar('User'):
        """ Retrieve User instance for request
        """
        auth_header = self.authorization_header(request)

        if auth_header is None:
            return None

        base64_auth_header = self.extract_base64_authorization_header(auth_header)

        if base64_auth_header is None:
            return None

        decoded_base64_auth_header = self.decode_base64_authorization_header(base64_auth_header)

        if decoded_base64_auth_header is None:
            return None

        user_email, user_pwd = self.extract_user_credentials(decoded_base64_auth_header)

        if user_email is None or user_pwd is None:
            return None

        user = self.user_object_from_credentials(user_email, user_pwd)

        return user
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/auth/basic_auth.py`, `api/v1/app.py`

#### 11. First User!
**Mandatory**

Now, create your first `User`. Define an environment variable `HBNB_TYPE_STORAGE` with the value `db` to use MySQL instead of the default file storage.

```sh
bob@dylan:~$ echo 'HBNB_TYPE_STORAGE=db' >> ~/.bashrc
bob@dylan:~$ source ~/.bashrc
```

If you have no SQL database, use the default file storage. Open the Python console and create your first `User`:

```sh
bob@dylan:~$ HBNB_MYSQL_USER=hbnb_dev HBNB_MYSQL_PWD=hbnb_dev_pwd HBNB_MYSQL_HOST=localhost HBNB_MYSQL_DB=hbnb_dev_db HBNB_TYPE_STORAGE=db python3
>>> from models.user import User
>>> from models import storage
>>> user = User()
>>> user.email = "bob@hbtn.io"
>>> user.first_name = "Bob"
>>> user.last_name = "Dylan"
>>> user.password = user.hash_password("bobpwd")
>>> user.save()
>>> storage.close()
>>> exit()
bob@dylan:~$
```

If you have the file `models/.db_data` file (default storage), you should see your new user. If you have a SQL database, check the `users` table.

Now, restart your API:

```sh
bob@dylan:~$ API_HOST=0.0.0.0 API_PORT=5000 python3 -m api.v1.app
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
....
```

Finally, open a new terminal and request the `GET /api/v1/users/me` endpoint:

```sh
bob@dylan:~$ curl -u "bob@hbtn.io:bobpwd" "http://0.0.0.0:5000/api/v1/users/me"
{
  "created_at": "2017-04-14T00:00:00",
  "email": "bob@hbtn.io",
  "first_name": "Bob",
  "id": "af972c14-3f2d-4ff0-b0cf-57d1cd5910d7",
  "last_name": "Dylan",
  "updated_at": "2017-04-14T00:00:00"
}
bob@dylan:~$
```

**Repo:**

- GitHub repository: `alx-backend-user-data`
- Directory: `0x01-Basic_authentication`
- File: `api/v1/app.py`, `models/user.py`

### 12. Use Session ID for Identifying a User

#### `SessionAuth` Class Implementation

First, let's create the `SessionAuth` class in `api/v1/auth/session_auth.py`:

```python
#!/usr/bin/env python3
""" SessionAuth module
"""

from api.v1.auth.auth import Auth
import uuid
from models.user import User

class SessionAuth(Auth):
    """ SessionAuth class
    """
    user_id_by_session_id = {}

    def create_session(self, user_id: str = None) -> str:
        """ Create a Session ID for a user_id """
        if user_id is None or not isinstance(user_id, str):
            return None

        session_id = str(uuid.uuid4())
        self.user_id_by_session_id[session_id] = user_id

        return session_id

    def user_id_for_session_id(self, session_id: str = None) -> str:
        """ Returns a User ID based on a Session ID """
        if session_id is None or not isinstance(session_id, str):
            return None

        return self.user_id_by_session_id.get(session_id)

    def current_user(self, request=None) -> TypeVar('User'):
        """ Retrieve a User instance based on a cookie value """
        session_id = self.session_cookie(request)

        if session_id is None:
            return None

        user_id = self.user_id_for_session_id(session_id)

        if user_id is None:
            return None

        return User.get(user_id)

    def destroy_session(self, request=None):
        """ Deletes the user session / logout """
        if request is None:
            return False

        session_id = self.session_cookie(request)

        if session_id is None:
            return False

        if not self.user_id_for_session_id(session_id):
            return False

        del self.user_id_by_session_id[session_id]
        return True
```

#### Update `api/v1/app.py` to Use `SessionAuth`

Modify the `api/v1/app.py` to use `SessionAuth` instead of `Auth`:

```python
#!/usr/bin/env python3
"""API entry point
"""
from os import getenv
from flask import Flask, jsonify, request, abort
from models import storage
from api.v1.views import app_views
from api.v1.auth.auth import Auth
from api.v1.auth.session_auth import SessionAuth

app = Flask(__name__)
app.register_blueprint(app_views)

auth = None
if getenv('AUTH_TYPE') == 'session_auth':
    auth = SessionAuth()

@app.before_request
def before_request():
    """ Method to handle before request """
    if auth is None:
        return

    excluded_paths = ['/api/v1/status/', '/api/v1/unauthorized/', '/api/v1/forbidden/', '/api/v1/auth_session/login/']
    if not auth.require_auth(request.path, excluded_paths):
        return

    if auth.authorization_header(request) is None and auth.session_cookie(request) is None:
        abort(401)

    current_user = auth.current_user(request)
    if current_user is None:
        abort(403)

    request.current_user = current_user

@app.errorhandler(404)
def not_found(error):
    """ Not found handler """
    return jsonify({"error": "Not found"}), 404

@app.errorhandler(401)
def unauthorized(error):
    """ Unauthorized handler """
    return jsonify({"error": "Unauthorized"}), 401

@app.errorhandler(403)
def forbidden(error):
    """ Forbidden handler """
    return jsonify({"error": "Forbidden"}), 403

if __name__ == "__main__":
    host = getenv("API_HOST", "0.0.0.0")
    port = getenv("API_PORT", "5000")
    app.run(host=host, port=port, threaded=True)
```

#### Create the Login Route

Now, let's add the login route in `api/v1/views/index.py`:

```python
#!/usr/bin/env python3
""" Index module
"""
from flask import jsonify, request, abort
from api.v1.views import app_views
from models.user import User
from os import getenv
from api.v1.auth.session_auth import SessionAuth

session_auth = None
if getenv('AUTH_TYPE') == 'session_auth':
    session_auth = SessionAuth()

@app_views.route('/auth_session/login/', methods=['POST'], strict_slashes=False)
def login():
    """ Login method """
    email = request.form.get('email')
    password = request.form.get('password')

    if email is None or email == '':
        return jsonify({"error": "email missing"}), 400

    if password is None or password == '':
        return jsonify({"error": "password missing"}), 400

    try:
        user_list = User.search({'email': email})
    except Exception:
        return jsonify({"error": "no user found for this email"}), 404

    if len(user_list) == 0:
        return jsonify({"error": "no user found for this email"}), 404

    user = user_list[0]
    if not user.is_valid_password(password):
        return jsonify({"error": "wrong password"}), 401

    session_id = session_auth.create_session(user.id)

    response = jsonify(user.to_dict())
    response.set_cookie("session_id", session_id)
    return response
```

### Running and Testing

1. **Set Environment Variables:**

   ```sh
   export API_HOST=0.0.0.0
   export API_PORT=5000
   export AUTH_TYPE=session_auth
   ```

2. **Run the API:**

   ```sh
   python3 -m api.v1.app
   ```

3. **Create a User:**

   ```sh
   from models.user import User
   from models import storage
   user = User()
   user.email = "bob@hbtn.io"
   user.first_name = "Bob"
   user.last_name = "Dylan"
   user.password = user.hash_password("bobpwd")
   user.save()
   storage.close()
   ```

4. **Login and Get Session ID:**

   ```sh
   curl -X POST http://0.0.0.0:5000/api/v1/auth_session/login/ -d "email=bob@hbtn.io" -d "password=bobpwd"
   ```

5. **Access User Information Using Session ID:**

   ```sh
   curl -X GET http://0.0.0.0:5000/api/v1/users/me -b "session_id=your_session_id_from_login"