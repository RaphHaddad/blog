---
title: "Azure Function Blob Trigger to Read an Entire Virtual Path's Contents (python)"
date: 2023-02-28T00:00:00.000+11:00
draft: false
tags : [azure, python, function]
---

## Scenario

In this tutorial, I'll demonstrate how to setup an Azure function for the below
scenario:

1. A blob file is uploaded to a storage container into a virtual path;
2. The files under that virtual path are needed to be read;
3. The files under that virtual path are processed in sequence.

Code for this post is host on [this repository on GitHub](https://github.com/RaphHaddad/target-local-container).

## Step 1: Create the Azure Function with Bindings

Create the relevant bindings and the entry point code for the Azure
Function:

```python
@app.function_name(name="BlobTrigger")
@app.blob_trigger(arg_name="theblob", path="a-foo-bar-container/drop-here/{name}",
                  connection="AzureWebJobsStorage")

def run_this(theblob: func.InputStream):
    ...
    ...
    ...
```

The code above can be interpreted as such: every time a blob file is dropped into
the path `a-foo-bar-container/drop-here/` Run the function with the signature
`run_this(theblob: func.InputStream)`. The argument `theblob` is the blob that
has been uploaded.

## Step 2: Construct the ContainerClient from the Connection String

Add the library `azure-storage-blob` to the requirements.txt, do a pip install,
and import the `ContainerClient` (`from azure.storage.blob import
ContainerClient, BlobClient`).

The function to create the ContainerClient is below:

```python
def create_storage_container_client(blob_folder_path):
    logging.info(f"Creating container client from blob (with full virtual path) '{blob_folder_path}'")
    connection_string = os.environ.get("AzureWebJobsStorage")
    container_name = blob_folder_path.split('/')[0]

    return ContainerClient.from_connection_string(connection_string, container_name=container_name)
```

The container client is created based on the path of the uploaded blob. The line `blob_folder_path.split('/')[0]` extracts the
container name from the full path of the blob (in this case, the parameter `blob_folder_path` will be
akin to: `a-foo-bar-container/drop-here/file-name.txt`. Meaning that the container name
will be `a-foo-bar-container`).

The function then uses this container name to construct the `ContainerClient`
via the function `from_connection_string`, which accepts as a keyword parameter
`container_name`.

## Step 3: Read and Process Blobs

Finally, use the `ContainerClient` to read the blobs and their contents one at a
time.

```python
def read_and_process_blobs(container_client, blob_virtual_path):
    blob_path_without_container = '/'.join(blob_virtual_path.split('/')[1:-1])
    logging.info(F"Reading files in within specified container under the path '{blob_path_without_container}'")
    blobs = container_client.list_blobs()
    for blob in [blob for blob in blobs if blob.name.startswith(blob_path_without_container)]:
        blob_name = blob.name.split('/')[-1]
        as_bytes = container_client.download_blob(blob).content_as_bytes()
        logging.info(F"Reading blob '{blob_name}'")
```

As the `ContainerClient` is scoped to a specific container (and not the virtual
path). Filtering for blobs under a specific virtual path becomes the onus of the
 custom Python code.

The line `blob_path_without_container =
'/'.join(blob_virtual_path.split('/')[1:-1])` constructs the virtual path
without the container and without the blob file name. It does this by taking the
second element of the path without the container (indexed at 1) and takes every
element of the path apart from the last element, being the blob file name
(indexed at -1).

The `blob_path_without_container` is then used as the filter value to only
extract blobs belonging to that specific virtual path, this is done via Python
list comprehension:

`for blob in [blob for blob in blobs if blob.name.startswith(blob_path_without_container)]:`

Inside the for loop above, the processing of the blobs should occur.

## Conclusion

In this brief post, I've demonstrated how to:
1. Setup an Azure Function Blob trigger that listens on a virtual path; and
1. Read all Blobs that exist side-by-side to the blob triggering the Azure Function.

Thank you for reading!
