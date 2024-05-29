
# 0x00. Personal data

## Back-end | Authentication

![authentication](images/5c48d4f6d4dd8081eb48.png)

### Resources

Read or watch:

- [What Is PII, non-PII, and Personal Data?](https://en.wikipedia.org/wiki/Personal_data)
- [Logging documentation](https://docs.python.org/3/library/logging.html)
- [bcrypt package](https://pypi.org/project/bcrypt/)
- [Logging to Files, Setting Levels, and Formatting](https://docs.python.org/3/howto/logging.html#logging-to-files-setting-levels-and-formatting)

### Learning Objectives

At the end of this project, you are expected to be able to explain to anyone, without the help of Google:

- Examples of Personally Identifiable Information (PII)
- How to implement a log filter that will obfuscate PII fields
- How to encrypt a password and check the validity of an input password
- How to authenticate to a database using environment variables

### Requirements

- All your files will be interpreted/compiled on Ubuntu 18.04 LTS using python3 (version 3.7)
- All your files should end with a new line
- The first line of all your files should be exactly `#!/usr/bin/env python3`
- A `README.md` file, at the root of the folder of the project, is mandatory
- Your code should use the `pycodestyle` style (version 2.5)
- All your files must be executable
- The length of your files will be tested using `wc`
- All your modules should have documentation (`python3 -c 'print(__import__("my_module").__doc__)'`)
- All your classes should have documentation (`python3 -c 'print(__import__("my_module").MyClass.__doc__)'`)
- All your functions (inside and outside a class) should have documentation (`python3 -c 'print(__import__("my_module").my_function.__doc__)'` and `python3 -c 'print(__import__("my_module").MyClass.my_function.__doc__)'`)
- A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class, or method (the length of it will be verified)
- All your functions should be type annotated

## Tasks

### 1. Regex-ing
   - Write a function called `filter_datum` that returns the log message obfuscated:

   ```python
   def filter_datum(fields, redaction, message, separator):
       pass
   ```

   - Arguments:
     - `fields`: a list of strings representing all fields to obfuscate
     - `redaction`: a string representing by what the field will be obfuscated
     - `message`: a string representing the log line
     - `separator`: a string representing by which character is separating all fields in the log line (message)

   - The function should use a regex to replace occurrences of certain field values.
   - `filter_datum` should be less than 5 lines long and use `re.sub` to perform the substitution with a single regex.

### 2. Log formatter
   - Copy the provided code into `filtered_logger.py`.
   - Update the class to accept a list of strings `fields` constructor argument.
   - Implement the `format` method to filter values in incoming log records using `filter_datum`.
   - Values for fields in `fields` should be filtered.

### 3. Create logger
   - Implement a `get_logger` function that takes no arguments and returns a `logging.Logger` object.
   - The logger should be named "user_data" and only log up to `logging.INFO` level. It should not propagate messages to other loggers. It should have a `StreamHandler` with `RedactingFormatter` as formatter.
   - Create a tuple `PII_FIELDS` constant at the root of the module containing the fields from `user_data.csv` that are considered PII.

### 4. Connect to secure database
   - Implement a `get_db` function that returns a connector to the database (`mysql.connector.connection.MySQLConnection` object).
   - Use the `os` module to obtain credentials from the environment.
   - Use the module `mysql-connector-python` to connect to the MySQL database.

### 5. Read and filter data
   - Implement a `main` function that takes no arguments and returns nothing.
   - The function will obtain a database connection using `get_db` and retrieve all rows in the `users` table and display each row under a filtered format.

### 6. Encrypting passwords
   - Implement a `hash_password` function that expects one string argument `password` and returns a salted, hashed password, which is a byte string.
   - Use the `bcrypt` package to perform the hashing (with `hashpw`).

### 7. Check valid password
   - Implement an `is_valid` function that expects 2 arguments and returns a boolean.
   - Arguments:
     - `hashed_password`: bytes type
     - `password`: string type
   - Use `bcrypt` to validate that the provided password matches the hashed password.