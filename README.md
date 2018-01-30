# export Airtable data to JSON

AirTable has an API, but offers just one full access API key. Obviously this can't be used in a user-facing app. This repo has directions and a script to fetch data from an AirTable API using an AWS Lambda function and save it to a JSON file on S3.

## setup

install dependencies for local testing:

    pip install -e requirements.txt

install dependencies for deployment package:

    pip install requests -t /path/to/project-dir

## AWS setup

- create an S3 bucket (see http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)
- create a new policy that references the bucket (TODO: example)
- make a Lambda role that references the policy (see http://docs.aws.amazon.com/lambda/latest/dg/with-s3-example-create-iam-role.html)
- create an AWS Lambda function (see http://docs.aws.amazon.com/lambda/latest/dg/get-started-create-function.html)
- assign the role to the Lambda function (TODO: example)
- create schedule to update output file (see http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)
- create Lambda deployment package:

    zip -q -r aws/lambda.zip . -x aws/* *.git*

- upload `aws/lambda.zip` to AWS
- set required environment variables for Lambda function:
    - `AIRTABLE_APP` - AirTable app id (from https://airtable.com/api)
    - `AIRTABLE_TABLE` - AirTable table id (from https://airtable.com/api)
    - `AIRTABLE_TOKEN` - API token (from https://airtable.com/api)
    - `S3_BUCKET` - destination bucket
- set optional environment variables for Lambda function if needed (will use defaults if not set):
    - `S3_FILENAME` - destination filename (default airtable.json)
    - `ACL` - access control for S3 file (default public-read)
    - `CACHE_HOURS` - set Expires header to this many hours in future (default 6)
    - `AIRTABLE_VIEW` - AirTable view id (from https://airtable.com/api)
- test the Lambda function; verify it creates `S3_BUCKET/S3_FILENAME`
- use the JSON file in your app
