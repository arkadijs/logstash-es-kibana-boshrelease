### Logstash with CloudFoundry

This is a BOSH release you could upload to director and deploy to get a working combo of Logstash that parses CloudFoundry syslog, writes it to ElasticSearch, together with Kibana web UI on port 80.

Example deployment manifests are in `_manifests/`.

#### CloudFoundry

First, configure CloudFoundry via it's deployment manifest to forward all logs:

    properties:
      syslog_aggregator:
        address: LOGSTASH_VM_IP
        port: 5000
        all: false
        transport: tcp

Also, configure user-provided-service to forward application logs:

    cf cups logstash -l syslog://LOGSTASH_VM_IP:5000
    cf bind-service APP_NAME logstash
    cd APP_DIR && cf push

#### Development

For details of BOSH release development refer to:

1. [BOSH documentation](http://docs.cloudfoundry.org/bosh/) hub
2. [Creating a BOSH Release](http://docs.cloudfoundry.org/bosh/create-release.html)
3. Understanding the BOSH Deployment Manifest [properties](http://docs.cloudfoundry.org/bosh/deployment-manifest.html#properties)
4. [Blobs](http://docs.cloudfoundry.org/bosh/reference/blobs.html) storage providers

Note, that S3 blob bucket must be hosted in us-east-1 and it must have a permissive Get policy applied, like the following:

    {
        "Version": "2008-10-17",
        "Id": "Policy1406027947715",
        "Statement": [
            {
                "Sid": "Stmt1406027941762",
                "Effect": "Allow",
                "Principal": "*",
                "Action": [
                    "s3:List*",
                    "s3:Get*"
                ],
                "Resource": "arn:aws:s3:::logstash-es-kibana-boshrelease-us-east-1/*"
            }
        ]
    }
