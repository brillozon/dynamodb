
# dynamodb - container with a local instance of dynamodb

This project can be used to build a docker image that contains
an instance of the local DynamoDB source code.  This image can
then be used to serve as a DynamoDB provider without needing
access to the cloud or internet.  This can be useful for development
or for execution in restricted environments.

A built image is available from docker hub and accessed by:

> ```docker pull brillozon/dynamodb```

The local DynamoDB instance can be explored interactively using
the shell at location: http://localhost:8000/shell.

### Build

Build an image:

> ```docker-compose build```

THe image is based on the JRE 8 base image.  An attempt was made
to use the Alpine JRE 8 base image, but the DynamoDB code does not
run correctly.  This will require additional investigation.  It is
likely that additional packages need to be installed to the Alpine
image.

The default command is set to use the default port and place
the code and data into named volumes of the image.

### Run

Start an instance:

> ```docker-compose up -d ```

The compose file will mount a named volume for the code and
another for the data used by the running instance.  This allows
the instance to be stopped and restarted while retaining the
data from previous executions.

The default port (8000) will be forwarded to the default port
on the host to provide access to the instance.

Stop the instance:

> ```docker-compose down```

### Clients

Clients in several languages can be used to access this local
dynamodb instance.  Some of them are described here:

#### Bash

Clients using `curl` should use the hostname and port where the
instance is executing.

> ```curl htpp://localhost:8000/shell```

#### Python

From the [AWS Documentation](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/GettingStarted.Python.Summary.html),
a service resource reference can be created as:
 
```python
> dynamodb = boto3.resource('dynamodb',endpoint_url="http://localhost:8000")
```

From [stack overflow](http://stackoverflow.com/a/32260680/3882815),
mechanisms for creating a client as well as service resource
reference in Python to access the local DynamoDB instance include:

```python
> import boto3
> 
> # For a Boto3 client.
> ddb = boto3.client('dynamodb', endpoint_url='http://localhost:8000')
> 
> # For a Boto3 service resource
> ddb = boto3.resource('dynamodb', endpoint_url='http://localhost:8000')
```

An example taken from the AWS documentation is included in
the file [MoviesCreateTable.py](src/MoviesCreateTable.py)

```bash
> cd src
> pip install -r requirements.txt
> python MoviesCreateTable.py
```

You can find additional source and data files in the AWS
[DynamoDB documentation](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/GettingStarted.Python.html).

#### Javascript

From the [AWS documentation](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/GettingStarted.Js.01.html),
the Javascript client can be configured to access
the local instance by updating the configuration
before creating the DynamoDB object:

```javascript
> AWS.config.update({
>   region: "us-west-2",
>   endpoint: 'http://localhost:8000',
>   // accessKeyId default can be used while using the downloadable version of DynamoDB. 
>   // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
>   accessKeyId: "fakeMyKeyId",
>   // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
>   // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
>   secretAccessKey: "fakeSecretAccessKey"
> });
> 
> var dynamodb = new AWS.DynamoDB();
```

