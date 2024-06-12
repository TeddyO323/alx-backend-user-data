# 0x03. User Authentication Service

![auth](images/4cb3c8c607afc1d1582d.jpg)

This content outlines the tasks and requirements for implementing a user authentication service. It covers creating the user model, adding and finding users, hashing passwords, registering users, setting up a basic Flask app, and creating an endpoint to register users via POST requests.

## Back-end | Authentication

In the industry, you should not implement your own authentication system and use a module or framework that does it for you (like in Python-Flask: Flask-User). Here, for learning purposes, we will walk through each step of this mechanism to understand it by doing.

## Resources

Read or watch:
- [Flask documentation](https://flask.palletsprojects.com/)
- [Requests module](https://docs.python-requests.org/)
- [HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

## Learning Objectives

At the end of this project, you are expected to be able to explain to anyone, without the help of Google:
- How to declare API routes in a Flask app
- How to get and set cookies
- How to retrieve request form data
- How to return various HTTP status codes

## Requirements

- Allowed editors: `vi`, `vim`, `emacs`
- All your files will be interpreted/compiled on Ubuntu 18.04 LTS using `python3` (version 3.7)
- All your files should end with a new line
- The first line of all your files should be exactly `#!/usr/bin/env python3`
- A `README.md` file, at the root of the folder of the project, is mandatory
- Your code should use the `pycodestyle` style (version 2.5)
- You should use `SQLAlchemy 1.3.x`
- All your files must be executable
- The length of your files will be tested using `wc`
- All your modules should have documentation (`python3 -c 'print(__import__("my_module").__doc__)'`)
- All your classes should have documentation (`python3 -c 'print(__import__("my_module").MyClass.__doc__)'`)
- All your functions (inside and outside a class) should have documentation (`python3 -c 'print(__import__("my_module").my_function.__doc__)'` and `python3 -c 'print(__import__("my_module").MyClass.my_function.__doc__)'`)
- A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class, or method (the length of it will be verified)
- All your functions should be type annotated
- The Flask app should only interact with `Auth` and never with `DB` directly
- Only public methods of `Auth` and `DB` should be used outside these classes

## Setup

You will need to install `bcrypt`:

```sh
pip3 install bcrypt
```

## Tasks

### 0. User model

In this task, you will create a SQLAlchemy model named `User` for a database table named `users` (by using the mapping declaration of SQLAlchemy).

The model will have the following attributes:
- `id`, the integer primary key
- `email`, a non-nullable string
- `hashed_password`, a non-nullable string
- `session_id`, a nullable string
- `reset_token`, a nullable string

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from user import User

print(User.__tablename__)

for column in User.__table__.columns:
    print("{}: {}".format(column, column.type))

bob@dylan:~$ python3 main.py
users
users.id: INTEGER
users.email: VARCHAR(250)
users.hashed_password: VARCHAR(250)
users.session_id: VARCHAR(250)
users.reset_token: VARCHAR(250)
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `user.py`

### 1. Create user

In this task, you will complete the `DB` class provided below to implement the `add_user` method.

```python
"""DB module
"""
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy.orm.session import Session

from user import Base

class DB:
    """DB class
    """
    def __init__(self) -> None:
        """Initialize a new DB instance
        """
        self._engine = create_engine("sqlite:///a.db", echo=True)
        Base.metadata.drop_all(self._engine)
        Base.metadata.create_all(self._engine)
        self.__session = None

    @property
    def _session(self) -> Session:
        """Memoized session object
        """
        if self.__session is None:
            DBSession = sessionmaker(bind=self._engine)
            self.__session = DBSession()
        return self.__session
```

Note that `DB._session` is a private property and hence should NEVER be used from outside the `DB` class.

Implement the `add_user` method, which has two required string arguments: `email` and `hashed_password`, and returns a `User` object. The method should save the user to the database. No validations are required at this stage.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from db import DB
from user import User

my_db = DB()

user_1 = my_db.add_user("test@test.com", "SuperHashedPwd")
print(user_1.id)

user_2 = my_db.add_user("test1@test.com", "SuperHashedPwd1")
print(user_2.id)

bob@dylan:~$ python3 main.py
1
2
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `db.py`

### 2. Find user

In this task, you will implement the `DB.find_user_by` method. This method takes in arbitrary keyword arguments and returns the first row found in the users table as filtered by the method’s input arguments. No validation of input arguments required at this point.

Make sure that SQLAlchemy’s `NoResultFound` and `InvalidRequestError` are raised when no results are found, or when wrong query arguments are passed, respectively.

Warning:
`NoResultFound` has been moved from `sqlalchemy.orm.exc` to `sqlalchemy.exc` between the version 1.3.x and 1.4.x of SQLAlchemy - please make sure you are importing it from `sqlalchemy.orm.exc`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from db import DB
from user import User

from sqlalchemy.exc import InvalidRequestError
from sqlalchemy.orm.exc import NoResultFound

my_db = DB()

user = my_db.add_user("test@test.com", "PwdHashed")
print(user.id)

find_user = my_db.find_user_by(email="test@test.com")
print(find_user.id)

try:
    find_user = my_db.find_user_by(email="test2@test.com")
    print(find_user.id)
except NoResultFound:
    print("Not found")

try:
    find_user = my_db.find_user_by(no_email="test@test.com")
    print(find_user.id)
except InvalidRequestError:
    print("Invalid")        

bob@dylan:~$ python3 main.py
1
1
Not found
Invalid
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `db.py`

### 3. Update user

In this task, you will implement the `DB.update_user` method that takes as argument a required `user_id` integer and arbitrary keyword arguments, and returns `None`.

The method will use `find_user_by` to locate the user to update, then will update the user’s attributes as passed in the method’s arguments then commit changes to the database.

If an argument that does not correspond to a user attribute is passed, raise a `ValueError`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from db import DB
from user import User

from sqlalchemy.exc import InvalidRequestError
from sqlalchemy.orm.exc import NoResultFound

my_db = DB()

email = 'test@test.com'
hashed_password = "hashedPwd"

user = my_db.add_user(email, hashed_password)
print(user.id)

try:
    my_db.update_user(user.id, hashed_password='NewPwd')
    print("Password updated")
except ValueError:
    print("Error")

bob@dylan:~$ python3 main.py
1
Password updated
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `db.py`

### 4. Hash password

In this task, you will define a `_hash_password` method that takes in a password string argument and returns bytes.

The returned bytes is a salted hash of the input password, hashed with `bcrypt.hashpw`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import _hash_password

print(_hash_password("Hello Holberton"))

bob@dylan:~$ python3 main.py
b'$2b$12$eUDdeuBtrD41c8dXvzh95ehsWYCCAi4VH1JbESzgbgZT.eMMzi.G2'
bob@dylan:~$


```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 5. Register user

In this task, you will implement the `Auth.register_user` in the `Auth` class provided below:

```python
"""Auth module
"""
from db import DB
from user import User

class Auth:
    """Auth class to interact with the authentication database.
    """

    def __init__(self):
        self._db = DB()
```

Note that `Auth._db` is a private property and should NEVER be used from outside the `Auth` class.

Implement the `register_user` method that takes mandatory `email` and `password` string arguments and return a `User` object.

The method should hash the password with `_hash_password`, save the user to the database with `DB.add_user`, and return the `User` object.

If a user already exists with the passed email, raise a `ValueError` with the message `User <email> already exists`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'me@me.com'
password = 'mySecuredPwd'

auth = Auth()

try:
    user = auth.register_user(email, password)
    print("User registered")
except ValueError:
    print("User already exists")

try:
    user = auth.register_user(email, password)
    print("User registered")
except ValueError:
    print("User already exists")

bob@dylan:~$ python3 main.py
User registered
User already exists
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 6. Basic Flask app

In this task, you will set up a basic Flask app.

Create a Flask app that has a single route `/` and returns the string `{"message": "Bienvenue"}` in a JSON payload.

Make sure that Flask’s JSON representation is enabled by using the `jsonify` method and setting the content type to `application/json`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

bob@dylan:~$ python3 main.py
 * Serving Flask app "main" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `main.py`

### 7. Register user (POST)

In this task, you will implement the end-point to register a user.

Define a `POST /users` route that saves the new user to the database using `Auth.register_user`.

The request is expected to contain form data with `email` and `password`.

Return the newly created user as a JSON payload with the keys `email` and `message`, and set the status code to 201.

If the user already exists, catch the exception and return a JSON payload with the key `message` set to `email already registered`, and set the status code to 400.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify, request
from auth import Auth

app = Flask(__name__)
AUTH = Auth()

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

bob@dylan:~$ curl -X POST http://0.0.0.0:5000/users -d "email=bob@dylan.com&password=bob_pwd"
{"email":"bob@dylan.com","message":"user created"}
bob@dylan:~$ curl -X POST http://0.0.0.0:5000/users -d "email=bob@dylan.com&password=bob_pwd"
{"message":"email already registered"}
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `main.py`

### 8. Credentials validation

In this task, you will implement the `Auth.valid_login` method that takes `email` and `password` string arguments and returns a boolean.

This method should use `DB.find_user_by` to locate the user, then check the user’s `hashed_password` against the `password` argument using `bcrypt.checkpw`. 

If the password is valid, return `True`. In any other case, return `False`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)

print(auth.valid_login(email, password))

print(auth.valid_login(email, "WrongPwd"))

bob@dylan:~$ python3 main.py
True
False
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 9. Generate UUIDs

In this task, you will implement the `Auth.create_session` method that takes `email` string argument and returns the session ID as a string.

The method should find the corresponding user using `DB.find_user_by`, generate a new UUID, store it in the database as the user’s `session_id`, and return the session ID.

Use the `uuid` module.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)

session_id = auth.create_session(email)
print(session_id)

bob@dylan:~$ python3 main.py
b930d5e1-8acb-4f19-ae9e-6382d53bc2d3
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 10. Session authentication

In this task, you will implement the `Auth.get_user_from_session_id` method that takes a single `session_id` string argument and returns the corresponding `User` or `None`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)

session_id = auth.create_session(email)
print(auth.get_user_from_session_id(session_id))

bob@dylan:~$ python3 main.py
<User #1 bob@bob.com>
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 11. Log out

In this task, you will implement the `Auth.destroy_session` method that takes a single `user_id` integer argument and returns `None`.

The method should update the corresponding user’s session ID to `None` in the database.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)

session_id = auth.create_session(email)
print(auth.get_user_from_session_id(session_id))

auth.destroy_session(auth.get_user_from_session_id(session_id).id)
print(auth.get_user_from_session_id(session_id))

bob@dylan:~$ python3 main.py
<User #1 bob@bob.com>
None
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 12. Find user by session ID

In this task, you will implement the `Auth.get_user_from_session_id` method that takes a single `session_id` string argument and returns the corresponding `User` or `None`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)

session_id = auth.create_session(email)
print(auth.get_user_from_session_id(session_id))

bob@dylan:~$ python3 main.py
<User #1 bob@bob.com>
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

### 13. Request validation

In this task, you will implement a new Flask view that handles requests to the `/profile` route.

The request to this endpoint will contain the session_id as a cookie with key `session_id`.

The function should use `Auth.get_user_from_session_id` to return the corresponding `User` object.

If the session ID is `None` or the user is not found, the route should return a 403 HTTP status code.

Otherwise, the route should return a JSON payload with the key `email` and the user’s email, and set the status code to 200.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify, request, abort
from auth import Auth

app = Flask(__name__)
AUTH = Auth()

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400

@app.route('/profile', methods=['GET'], strict_slashes=False)
def profile() -> str:
    """GET /profile
    Return:
      - the user's profile
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    return jsonify({"email": user.email}), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

bob@dylan:~$ curl -X POST http://0.0.0.0:5000/users -d "email=bob@dylan.com&password=bob_pwd"
{"email":"bob@dylan.com","message":"user created"}
bob@dylan:~$ SESSION_ID=$(curl -s -X POST http://0.0.0.0:5000/sessions -d "email=bob@dylan.com&password=bob_pwd" | jq -r '.session_id')
bob@dylan:~$ curl -s http://0.0.0.0:5000/profile -b "session_id=$SESSION_ID"
{"email":"bob@dylan.com"}
bob@dylan:~$ curl -s http://0.0.0.0:5000/profile -b "session_id=WrongSessionId"
bob@dylan:~$ curl -s http://0.0.0.0:5000/profile
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `main.py`

### 14. Log out

In this task, you will implement a new Flask view that handles `DELETE` requests to the `/sessions` route.

The request to this endpoint will contain the session_id as a cookie with key `session_id`.

The function should use `Auth.get_user_from_session_id` to find the corresponding `User` object and destroy the session.

After destroying the session, the function should return an empty JSON payload with the status code set to 200.

If the session ID is `None` or the user is not found, the route should return a 403 HTTP status code.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify, request, abort
from auth import Auth

app = Flask(__name__)
AUTH = Auth()

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email

, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400

@app.route('/profile', methods=['GET'], strict_slashes=False)
def profile() -> str:
    """GET /profile
    Return:
      - the user's profile
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    return jsonify({"email": user.email}), 200

@app.route('/sessions', methods=['DELETE'], strict_slashes=False)
def logout() -> str:
    """DELETE /sessions
    Return:
      - empty JSON payload
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    AUTH.destroy_session(user.id)
    return jsonify({}), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

bob@dylan:~$ curl -X POST http://0.0.0.0:5000/users -d "email=bob@dylan.com&password=bob_pwd"
{"email":"bob@dylan.com","message":"user created"}
bob@dylan:~$ SESSION_ID=$(curl -s -X POST http://0.0.0.0:5000/sessions -d "email=bob@dylan.com&password=bob_pwd" | jq -r '.session_id')
bob@dylan:~$ curl -s http://0.0.0.0:5000/profile -b "session_id=$SESSION_ID"
{"email":"bob@dylan.com"}
bob@dylan:~$ curl -s -X DELETE http://0.0.0.0:5000/sessions -b "session_id=$SESSION_ID"
{}
bob@dylan:~$ curl -s http://0.0.0.0:5000/profile -b "session_id=$SESSION_ID"
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `main.py`

### 15. Delete user

In this task, you will implement the `Auth.delete_user` method that takes a single `email` string argument and returns `None`.

The method should find the corresponding user using `DB.find_user_by`, delete the user using `DB.delete_user`, and return `None`.

```sh
bob@dylan:~$ cat main.py
#!/usr/bin/env python3
"""
Main file
"""
from auth import Auth

email = 'bob@bob.com'
password = 'MyPwdOfBob'

auth = Auth()
auth.register_user(email, password)
auth.delete_user(email)

print(auth.valid_login(email, password))

bob@dylan:~$ python3 main.py
False
bob@dylan:~$
```

Repo:
- GitHub repository: `alx-backend-user-data`
- Directory: `0x03-user_authentication_service`
- File: `auth.py`

---

**Complete Example:**

Below is an example of how the `auth.py` and `main.py` files should be implemented based on the tasks above:

**auth.py:**

```python
"""Auth module
"""
import bcrypt
from db import DB
from user import User
from uuid import uuid4


class Auth:
    """Auth class to interact with the authentication database.
    """

    def __init__(self):
        self._db = DB()

    def _hash_password(self, password: str) -> str:
        """Hashes a password"""
        return bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())

    def register_user(self, email: str, password: str) -> User:
        """Registers a new user"""
        try:
            self._db.find_user_by(email=email)
        except NoResultFound:
            hashed_password = self._hash_password(password)
            return self._db.add_user(email, hashed_password)
        else:
            raise ValueError(f"User {email} already exists")

    def valid_login(self, email: str, password: str) -> bool:
        """Validates login credentials"""
        try:
            user = self._db.find_user_by(email=email)
            return bcrypt.checkpw(password.encode('utf-8'), user.hashed_password)
        except NoResultFound:
            return False

    def create_session(self, email: str) -> str:
        """Creates a session ID for the user"""
        user = self._db.find_user_by(email=email)
        session_id = str(uuid4())
        self._db.update_user(user.id, session_id=session_id)
        return session_id

    def get_user_from_session_id(self, session_id: str) -> User:
        """Gets a user from a session ID"""
        try:
            user = self._db.find_user_by(session_id=session_id)
            return user
        except NoResultFound:
            return None

    def destroy_session(self, user_id: int) -> None:
        """Destroys a user's session"""
        self._db.update_user(user_id, session_id=None)

    def delete_user(self, email: str) -> None:
        """Deletes a user"""
        user = self._db.find_user_by(email=email)
        self._db.delete_user(user.id)
```

**main.py:**

```python
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify, request, abort
from auth import Auth

app = Flask(__name__)
AUTH = Auth()

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400

@app.route('/profile', methods=['GET'], strict_slashes=False)
def profile() -> str:
    """GET /profile
    Return:
      - the user's profile
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    return jsonify({"email": user.email}), 200

@app.route('/sessions', methods=['DELETE'], strict_slashes=False)
def logout() -> str:
    """DELETE /sessions
    Return:
      - empty JSON payload
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    AUTH.destroy_session(user.id)
    return jsonify({}), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

```
### 16. User registration

In this task, you will add a new view function in `main.py` to handle user registration via a `POST` request to the `/users` route.

When a user registers, their `email` and `password` are provided in the form data. You should use the `Auth.register_user` method to create a new user.

If the user is successfully created, return a JSON payload with the user's email and a message saying "user created" with a status code of 201.

If the email is already registered, return a JSON payload with a message saying "email already registered" with a status code of 400.

Here's the code snippet to add:

```python
@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400
```

### 17. Log in

In this task, you will implement a new Flask view that handles `POST` requests to the `/sessions` route for user login.

The request to this endpoint will contain `email` and `password` form data. Use the `Auth.valid_login` method to validate the credentials.

If the credentials are valid, create a session ID using `Auth.create_session`, set it in the cookies, and return a JSON payload with the session ID.

If the credentials are invalid, return a JSON payload with a message saying "invalid credentials" with a status code of 401.

Here's the code snippet to add:

```python
@app.route('/sessions', methods=['POST'], strict_slashes=False)
def login() -> str:
    """POST /sessions
    Return:
      - the session id
    """
    email = request.form.get('email')
    password = request.form.get('password')
    if AUTH.valid_login(email, password):
        session_id = AUTH.create_session(email)
        response = jsonify({"session_id": session_id})
        response.set_cookie("session_id", session_id)
        return response
    else:
        return jsonify({"message": "invalid credentials"}), 401
```

### 18. User profile

In this task, you will implement a new Flask view that handles `GET` requests to the `/profile` route to retrieve the user's profile.

The request to this endpoint should contain the session_id as a cookie with the key `session_id`.

Use `Auth.get_user_from_session_id` to retrieve the corresponding user. If the session ID is invalid, return a 403 status code.

If the session ID is valid, return a JSON payload with the user's email and a status code of 200.

The code for this task was already provided in task 13. Here it is again for completeness:

```python
@app.route('/profile', methods=['GET'], strict_slashes=False)
def profile() -> str:
    """GET /profile
    Return:
      - the user's profile
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    return jsonify({"email": user.email}), 200
```

### 19. Log out

In this task, you will implement a new Flask view that handles `DELETE` requests to the `/sessions` route to log out a user.

The request to this endpoint should contain the session_id as a cookie with the key `session_id`.

Use `Auth.get_user_from_session_id` to retrieve the corresponding user. If the session ID is invalid, return a 403 status code.

If the session ID is valid, destroy the session using `Auth.destroy_session` and return an empty JSON payload with a status code of 200.

The code for this task was already provided in task 14. Here it is again for completeness:

```python
@app.route('/sessions', methods=['DELETE'], strict_slashes=False)
def logout() -> str:
    """DELETE /sessions
    Return:
      - empty JSON payload
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    AUTH.destroy_session(user.id)
    return jsonify({}), 200
```

### Complete `main.py` File

Here's the complete `main.py` file with all the routes included:

```python
#!/usr/bin/env python3
"""
Route module for the API
"""
from flask import Flask, jsonify, request, abort
from auth import Auth

app = Flask(__name__)
AUTH = Auth()

@app.route('/', methods=['GET'], strict_slashes=False)
def index() -> str:
    """GET /
    Return:
      - a welcome message
    """
    return jsonify({"message": "Bienvenue"})

@app.route('/users', methods=['POST'], strict_slashes=False)
def users() -> str:
    """POST /users
    Return:
      - the user
    """
    email = request.form.get('email')
    password = request.form.get('password')
    try:
        user = AUTH.register_user(email, password)
        return jsonify({"email": email, "message": "user created"}), 201
    except ValueError:
        return jsonify({"message": "email already registered"}), 400

@app.route('/sessions', methods=['POST'], strict_slashes=False)
def login() -> str:
    """POST /sessions
    Return:
      - the session id
    """
    email = request.form.get('email')
    password = request.form.get('password')
    if AUTH.valid_login(email, password):
        session_id = AUTH.create_session(email)
        response = jsonify({"session_id": session_id})
        response.set_cookie("session_id", session_id)
        return response
    else:
        return jsonify({"message": "invalid credentials"}), 401

@app.route('/profile', methods=['GET'], strict_slashes=False)
def profile() -> str:
    """GET /profile
    Return:
      - the user's profile
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    return jsonify({"email": user.email}), 200

@app.route('/sessions', methods=['DELETE'], strict_slashes=False)
def logout() -> str:
    """DELETE /sessions
    Return:
      - empty JSON payload
    """
    session_id = request.cookies.get('session_id')
    if session_id is None:
        abort(403)
    user = AUTH.get_user_from_session_id(session_id)
    if user is None:
        abort(403)
    AUTH.destroy_session(user.id)
    return jsonify({}), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
