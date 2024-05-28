# Test Document: `tests/test_app.py`

This document contains test cases for the `test_app.py` module.

## Test Setup

```python
# Import necessary modules and libraries
import json
import pytest
from flask_jwt_extended import JWTManager, jwt_required, get_jwt_identity, get_jwt, create_access_token, unset_jwt_cookies
from werkzeug.datastructures import FileStorage
from io import BytesIO

# Fixture to set up the testing client
@pytest.fixture
def client():
    from run import app
    app.config["TESTING"] = True
    app.config["JWT_SECRET_KEY"] = "542e10c55fd5e5f427e2dc8c6e4e6839187fbc9114cd8b64a6f398169aa9341e"
    app.config["JWT_ACCESS_TOKEN_EXPIRES"] = False  # Disable token expiration for testing
    app.config["UPLOAD_FOLDER"] = "./tests/test_data"  # Use a separate upload folder for testing
    client = app.test_client()
    yield client
```
## Test Cases
### 1. Test Login
``` python
Copy code
def test_login(client):
    print("test1-----------------")
    print(client)
    response = client.post(
        "/login",
        json={"username": "saachi", "password": "admin"},
        content_type="application/json",
    )
    data = json.loads(response.data)
    assert response.status_code == 200
    assert "access_token" in data
```

### 2. Test Search (Authorized)
``` python
def test_search_authorized(client):
    with client.application.app_context():
        access_token = create_access_token(identity={"username": "Pankaj", "role": "admin"})
        headers = {"Authorization": f"Bearer {access_token}"}
        response = client.get("/search", headers=headers)
        assert response.status_code == 400
        data = json.loads(response.data)
        assert "error" in data and "No query found." in data["error"]
```
### 3. Test Search (Unauthorized)
``` python
def test_search_unauthorized(client):
    response = client.get("/search")
    assert response.status_code == 401
```
### 4. Test Upload Files (Success)
``` python
def test_upload_files_success(client):
    # Create a sample access token for an admin user
    with client.application.app_context():
        access_token = create_access_token(identity={"username": "admin", "role": "admin"})

        # Create a file with the desired content and properties
        file_content = b"Sample file content"
        file = FileStorage(
            stream=BytesIO(file_content),
            filename="test_file.doc",  # Change the extension to .doc
            content_type="application/msword"
        )

        # Make the API call to upload the file
        response = client.post("/upload", data={"file": (file, "test_file.doc")},
                            headers={"Authorization": f"Bearer {access_token}"})

        # Check that the response is as expected
        assert response.status_code == 200
        data = json.loads(response.data)
        assert "uploaded_files" in data
        assert data["uploaded_files"] == ["test_file.doc"]

```
### 5. Test Upload Files (Unauthorized)
``` python
def test_upload_files_unauthorized(client):
    with client.application.app_context():
        # Create a sample access token for a non-admin user
        access_token = create_access_token(identity={"username": "user", "role": "user"})

        # Create a file with the desired content and properties
        file_content = b"Unauthorized file content"
        file = FileStorage(
            stream=BytesIO(file_content),
            filename="unauthorized_file.txt",
            content_type="text/plain"
        )

        # Make the API call to upload the file
        response = client.post("/upload", data={"file": (file, "unauthorized_file.txt")},
                                headers={"Authorization": f"Bearer {access_token}"})

        # Check that the response is as expected
        assert response.status_code == 403
        data = json.loads(response.data)
        assert "error" in data
        assert "Unauthorized" in data["error"]


```
### 6. Test Upload Files (Invalid Extension)
``` python
def test_upload_files_invalid_extension(client):
    # Create a sample access token for an admin user
    with client.application.app_context():
        access_token = create_access_token(identity={"username": "admin", "role": "admin"})

        # Create a file with an invalid extension
        file_content = b"Invalid file content"
        file = FileStorage(
            stream=BytesIO(file_content),
            filename="invalid_file.exe",  # Invalid extension
            content_type="application/octet-stream"
        )

        # Make the API call to upload the file
        response = client.post("/upload", data={"file": (file, "invalid_file.exe")},
                            headers={"Authorization": f"Bearer {access_token}"})

        # Check that the response is as expected
        assert response.status_code == 400
        data = json.loads(response.data)
        assert "error" in data
        assert "Invalid file type" in data["error"]

```
### 7. Test Upload Files (Large File)
``` python
def test_upload_files_large_file(client):
    # Create a sample access token for an admin user
    with client.application.app_context():
        # Define the maximum file size in megabytes
        MAX_FILE_SIZE_MB = 2
        access_token = create_access_token(identity={"username": "admin", "role": "admin"})

        # Create a large file that exceeds the size limit
        file_content = b"Large file content" * (MAX_FILE_SIZE_MB * 1024 * 1024 + 1)
        file = FileStorage(
            stream=BytesIO(file_content),
            filename="large_file.pptx",
            content_type="text/plain"
        )

        # Make the API call to upload the file
        response = client.post("/upload", data={"file": (file, "large_file.txt")},
                            headers={"Authorization": f"Bearer {access_token}"})

```

