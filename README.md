
---

# Secure File Storage System (SFSS)

The Secure File Storage System (SFSS) is a command-line tool that allows users to securely upload, download, list, and delete files stored in a local file system. It uses GitHub OAuth for user authentication and employs AES encryption to ensure file security.

## Features

- **Command-line operations**: Upload, download, list, and delete files.
- **GitHub OAuth authentication**: Only authenticated users can perform operations.
- **AES encryption**: Files are encrypted before being saved on the local file system.
- **Token management**: OAuth tokens are securely saved and managed.
- **Logging**: Basic logging of authentication events and file operations.

## Architecure

![Alt Text](./arch/SFSS.jpg)

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Initializing the Application](#initializing-the-application)
  - [Authenticating via GitHub OAuth](#authenticating-via-github-oauth)
  - [File Operations](#file-operations)
- [Testing](#testing)
- [Limitations](#limitations)

## Installation

To get started with the SFSS project, you need to clone the repository and install the required dependencies.

### Step 1: Clone the Repository

```bash
git https://github.com/dennis-test-org/sfss.git
cd sfss
```

### Step 2: Set Up a Virtual Environment (Optional but Recommended)

```bash
python -m venv venv
source ./venv/Scripts/activate 
```

### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4: Setting up GITHUB

User have to export GITHUB_CLIENT_ID and GITHUB_CLIENT_SECRET in order for the application to work. Both will be shared via EMAIL (Not a best practice to share SECRET KEYs).

```bash
   export GITHUB_CLIENT_ID="your_github_client_id_here"
   export GITHUB_CLIENT_SECRET="your_github_client_secret_here"
```


## Configuration

The configuration for the project is stored in `config.py`.

- **GITHUB_CLIENT_ID**: Your GitHub OAuth application's Client ID.
- **GITHUB_CLIENT_SECRET**: Your GitHub OAuth application's Client Secret.
- **CALLBACK_URL**: The redirect URI for OAuth (e.g., `http://localhost:8000/`).
- **TOKENS_DIR**: Directory to store OAuth tokens (e.g., `.sfss/tokens`).
- **ACTIVE_USER_FILE**: File to store the active user information (e.g., `.sfss/active_user.txt`).

## Usage

### Initializing the Application

To initialize the Secure File Storage System and prepare the local storage, use the following command:

```bash
python main.py init
```

This command creates the necessary directories for storing files, tokens, and logs.

### Authenticating via GitHub OAuth

Before performing any operations, you must authenticate via GitHub OAuth. Run the following command to open your browser and authenticate with GitHub:

```bash
python main.py auth
```

After successful authentication, your GitHub access token will be saved locally, and you will be able to perform file operations.

**If Step  4 is not done correctly, the cli might freeze. So to fix it just type http://localhost:8000/callback into your browser to exit the code.**

### File Operations

Once authenticated, you can start managing your files using the following commands:

- **Upload a File**:

  ```bash
  python main.py upload /path/to/your/file.txt
  ```

  This will encrypt and upload the file to the local storage directory.

- **Download a File**:

  ```bash
  python main.py download file.txt /path/to/destination
  ```

  This will decrypt and download the file from the local storage.

- **List Files**:

  ```bash
  python main.py list
  ```

  This will list all the files in your storage directory.

- **Delete a File**:

  ```bash
  python main.py delete file.txt
  ```

  This will delete the file from the storage directory.

## Testing

You can run unit tests for the project to ensure everything is working as expected.

### Step 1: Install Testing Dependencies

Make sure you have the necessary testing packages installed:

```bash
cd tests
pip install -r requirement-devs.txt
cd..
```

### Step 2: Run Tests

To run all unit tests, use:

```bash
python -m unittest discover tests
```

The tests cover key functionalities like encryption, file operations, and OAuth authentication.


---

Following security aspects are added to the code.

1. Validates and sanitizes all user inputs to prevent injection attacks.
2. Uses GitHub's OAuth for secure user login.Unauthenticated users wont be able to use the cli.


### **Limitations:**

There are lot of improvments that can be made in future to make this more secure.

1. **Encryption and Key Management**:  Exposing the client secret to public. Becuase usually the secret keep is kept at backend side away from the user. But this implementaion user is able to see SECRET key. Currnetly one encryption key is used to encrypt all the data,in future better to use mutiple keys for different users.

    **Solution:**

          Store encryption keys in secure locations, such as environment variables, encrypted files, or dedicated key management services (e.g., AWS KMS, Azure Key Vault).

          Implement Key Rotation: Regularly rotate encryption keys and handle re-encryption of existing data as needed.
    
2. **Lack of Integrity Checks**: Without verifying the integrity of encrypted files, tampering or corruption may go undetected. Implmenting a Hash-Based Message Authentication Code provides confidentiality and integrity.


3. **Folder Traversal**: User can do folder traversal to location and see data.Currently apps store all the important data in base directory. So if users with high privilege can traverse the folders. To prevent it in future,can make use of directory permission.Ensure that the application runs with the least privileges necessary. For instance, the application should not require administrative or root privileges to operate, minimizing the potential impact of a compromised process. 