# CLI tool for S3 operations
**A simple CLI tool written for S3 using boto3, the Python SDK for AWS.**

This CLI tool allows you to perform the following operations:
1. List objects by bucket name
2. Put object by bucket name & object path
3. Delete object by bucket name & object path

# Getting Started
#### Requirements
* Python 3.9.5
* IAM user with full s3 premissions
* Access key ID, Secret access key

#### Installing required package
In order to make it work, install the boto3 module with **pip**:<br>
```python
pip install boto3
```

#### Configuring
Please make sure you put your Access key ID and Secret access key in the "config" file that is presented in the same folder as this tool(S3Tool.py).<br>
**First line** should be the Access key ID and the **second line** is the Secret access key.<br>
For example, your config file should look like this:<br>
```
ABCDEFQWERTPIVOP5ZXC
Qu12w34Z5xNztUIhx+f/uZb6krlqK7lm8Ftn9Fiq
```

#### Running
Simply run the py file 
```python
py S3Tool.py
```

# User Guide
#### Options menu
Once you'll start the app, you'll be presented with the current existing buckets in your storage and three options from which you can choose. Each option lets you perform a different operation.<br>
In order to choose an operation, type its number and press Enter. If you want to quit, type *exit* and press Enter.
<img src="https://user-images.githubusercontent.com/50183122/154837879-40e5827b-02dc-48cb-a05f-d91c35903aa9.png" width="40%" height="40%">

#### Performing operation
After choosing an operation, you'll be promoted to enter a bucket name and/or files path.<br> Simply type them as input and press Enter.<br>
<img src="https://user-images.githubusercontent.com/50183122/154838111-6a29a763-19af-4fd6-b3e8-cf9898891d2d.png" width="40%" height="40%"><br>
After the operation is complete, the operations menu will reappear.

### Help menu --help
In order to view the help menu within the app, run:
Simply run the py file 
```python
py S3Tool.py --help
```
<img src="https://user-images.githubusercontent.com/50183122/154839371-396f34f5-dfd0-4916-95d1-d8c45e9b974e.png" width="40%" height="40%"><br>


# Code explanation
This app was made using AWS SDK for Python (Boto3) which allows to create, configure, and manage AWS services, such as Amazon Simple Storage Service (Amazon S3). The SDK provides an object-oriented API as well as low-level access to AWS services.
<br>Specifically for this app I used the low-level client.
```python
# initialize the client with aws service name, region, key and secret
s3_client = boto3.client('s3', region_name='us-east-1',
                         aws_access_key_id=AWS_KEY_ID,
                         aws_secret_access_key=AWS_SECRET)
```
On app startup, the code retrieves current existing buckets, and print them to the user:
```python
response = s3_client.list_buckets()
print('Existing buckets:')
for bucket in response['Buckets']:
    print("     "+bucket["Name"])
```
Within the main function of this code, a menu is printed to the user, which lets the user to choose an operation.<br>
According to the chosen operation, a specific function will be called which performs user’s request.<br>
The three functions are:
1. list_objects()
2. put_object()
3. delete_object()

### list_objects
Gets bucket name from the user and retrieves its objects using the boto3 utilities.
```python
def list_objects():
    # Let the user to choose a bucket by name
    bucketName = input("Please enter a bucket name:")
    try:
        # Retrieve the objects from a bucket
        objects = s3_client.list_objects_v2(Bucket=bucketName)

        # Output the objects
        for obj in objects['Contents']:
            print(obj['Key'])

    except Exception as e:
        logging.error(e)
```
### put_object
Gets bucket name and file path from the user as input, check if the file path is correct. If the file path is correct, it passes the backets name and file path to the upload_file() function.
```python
def put_object():
    bucket_name = input("Please enter buckets name: ")
    file_path = input("Please enter files full path: ")

    # check if the selected file path is correct
    if (os.path.isfile(file_path)):
        upload_file(file_path, bucket_name)
    else:
        print("File path incorrect. Please try again.")
```

The upload_file() function uses the boto3 utilities to actually upload the file.
```python
# A function that uploads selected file to the selected bucket
def upload_file(file_name, bucket, object_name=None):
    """Upload a file to an S3 bucket

    :param file_name: File to upload
    :param bucket: Bucket to upload to
    :param object_name: S3 object name. If not specified then file_name is used
    :return: True if file was uploaded, else False
    """

    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = os.path.basename(file_name)

    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
        print("Object was uploaded successfully!")
    except Exception as e:
        logging.error(e)
```
### delete_object
Gets bucket name and file path from the user as input, using boto3 utilities to delete the specified object.
```python
def delete_object():
    bucket_name = input("Please enter buckets name: ")
    file_path = input("Please enter files path: ")
    try:
        response = s3_client.delete_object(Bucket=bucket_name, Key=file_path)
        print("Object was deleted successfully!")
    except Exception as e:
        logging.error(e)
```
*Note: each function contains a try/except block in order to catch such errors as wrong bucket name, permission denied, etc..*

# Resource that was used in making of this task
https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html
