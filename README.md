# Monitoring Design Document for LoyaltyOne
This design document outlines the way, the monitoring configuration file is being designed. It explains the steps required to understand the existing configuration & add up any new configuration for an existing component to be displayed on the AWS Cloudwatch monitoring dashboard.

`MonitoringConfiguration.json` is a JSON file that stores information related to all the metrics to be displayed on the cloudwatch dashboard. At present, the AWS services included as metrics in the configuration file are - **S3, Lambda, DynamoDB, Transfer & ECS.**
>There are some other custom metrics like the activity/file pending count of each service type on state machine which are handled via the Lambda function.

## S3
The configuration file has the name of the AWS service (S3) as a key at the top level. For each AWS S3 service, we can have entries of multiple buckets. For each bucket, we can have entries of multiple metrics along with their refresh interval time in seconds.

The following keys inside S3 i.e. **bucketName, region, metricName, awsMetric & refreshIntervalInSec** are pre-defined and fixed. Also, the name of the metrics for S3 service i.e. **NumberOfObjects, BucketSizeBytes, PutRequests & BytesUploaded** have been taken from AWS & are pre-defined.

   | Metric Name       | Description                                                     |
   | ----------------  |:--------------------------------------------------------------- |
   | NumberOfObjects   | The total number of objects stored in a bucket                  |
   | BucketSizeBytes   | The amount of data in bytes stored in a bucket                  |
   | PutRequests       | The number of HTTP PUT requests made for objects in a bucket    |
   | BytesUploaded     | The number of bytes uploaded to an Amazon S3 bucket             |

However, the user has a choice whether to include all of the pre-defined metrics for an individual bucket or not.

Let's take a look at the snippet of the configuration of S3 service -

```
"S3": [{
		"bucketName": "<name-of-the-bucket>",
		"region": "<name-of-the-aws-region>",
		"metricName": [{
				"awsMetric": "NumberOfObjects",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "BucketSizeBytes",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "PutRequests",
				"refreshIntervalInSec": <time-in-seconds>
			}
		]
	}
]
```

## Usecases
### To add a new metric for an existing bucket -
If a user wants to add a new metric i.e. BytesUploaded for an existing bucket entry, add below snippet in the bucket configuration -

```
{
	"awsMetric": "BytesUploaded",
	"refreshIntervalInSec": <time-in-seconds>
}
```

After adding the above snippet, it should look like this -

```
"S3": [{
		"bucketName": "<name-of-the-bucket>",
		"region": "<name-of-the-aws-region>",
		"metricName": [{
				"awsMetric": "NumberOfObjects",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "BucketSizeBytes",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "PutRequests",
				"refreshIntervalInSec": <time-in-seconds>
			},
            {
	            "awsMetric": "BytesUploaded",
	            "refreshIntervalInSec": <time-in-seconds>
            }
		]
	}
]
```

### To add a new bucket along with a metric -
If a user wants to add a new bucket and monitor just a single metric over it i.e. NumberOfObjects, add below snippet in the S3 configuration -

```
{
	"bucketName": "<name-of-the-bucket>",
	"region": "<name-of-the-aws-region>",
	"metricName": [{
			"awsMetric": "NumberOfObjects",
			"refreshIntervalInSec": <time-in-seconds>
		}
	]
}
```
After adding the above snippet, it should look like this -

```
"S3": [{
		"bucketName": "<name-of-the-bucket>",
		"region": "<name-of-the-aws-region>",
		"metricName": [{
				"awsMetric": "NumberOfObjects",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "BucketSizeBytes",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "PutRequests",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "BytesUploaded",
				"refreshIntervalInSec": <time-in-seconds>
			}
		]
	},
	{
		"bucketName": "<name-of-the-bucket>",
		"region": "<name-of-the-aws-region>",
		"metricName": [{
			"awsMetric": "NumberOfObjects",
			"refreshIntervalInSec": <time-in-seconds>
        }]
	}
]
```

### To remove a metric from an existing bucket -
If a user wants to remove a metric i.e. BytesUploaded from an existing bucket entry, remove below snippet from the bucket configuration -

```
{
	"awsMetric": "BytesUploaded",
	"refreshIntervalInSec": <time-in-seconds>
}
```

After removing the above snippet, it should look like this -

```
"S3": [{
		"bucketName": "<name-of-the-bucket>",
		"region": "<name-of-the-aws-region>",
		"metricName": [{
				"awsMetric": "NumberOfObjects",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "BucketSizeBytes",
				"refreshIntervalInSec": <time-in-seconds>
			},
			{
				"awsMetric": "PutRequests",
				"refreshIntervalInSec": <time-in-seconds>
			}
		]
	}
]
```

## Lambda
The configuration file has the name of the AWS service (Lambda) as a key at the top level. The rest of the explaination for Lambda service remains the same as that of the S3 service.

The difference here is that we don't have any key for Lambda function name. This is because the metric currently included in the configuration file is not bound to an individual lambda function.

The following keys inside Lambda i.e. **region, metricName, awsMetric & refreshIntervalInSec** are pre-defined and fixed. Also, the name of the metric for Lambda service i.e. **ConcurrentExecutions** has been taken from AWS & is pre-defined.

   | Metric Name            | Description                                                     |
   | ---------------------  |:--------------------------------------------------------------- |
   | ConcurrentExecutions   | Emitted as an aggregate metric for all functions in the account |

## DynamoDB
The configuration file has the name of the AWS service (DynamoDB) as a key at the top level. For each AWS DynamoDB service, we can have entries of multiple tables. For each table, we can have entries of multiple metrics along with their refresh interval time in seconds.

The following keys inside DynamoDB i.e. **tableName, region, metricName, awsMetric & refreshIntervalInSec** are pre-defined and fixed. Also, the name of the metrics for DynamoDB service i.e. **ConsumedReadCapacityUnits & ConsumedWriteCapacityUnits** have been taken from AWS & are pre-defined.

   | Metric Name                | Description                                                     |
   | -------------------------  |:--------------------------------------------------------------- |
   | ConsumedReadCapacityUnits  | The number of read capacity units consumed over the specified time period, so you can track how much of your provisioned throughput is used  |
   | ConsumedWriteCapacityUnits | The number of write capacity units consumed over the specified time period, so you can track how much of your provisioned throughput is used |

However, the user has a choice whether to include all of the pre-defined metrics for an individual table or not.

## Transfer
The configuration file has the name of the AWS service (Transfer) as a key at the top level. For each AWS Transfer service, we can have entries of multiple servers. For each server, we can have entries of multiple metrics along with their refresh interval time in seconds.

The following keys inside Transfer i.e. **serverId, region, metricName, awsMetric & refreshIntervalInSec** are pre-defined and fixed. Also, the name of the metrics for Transfer service i.e. **BytesIn, BytesOut & ServerState** have been taken from AWS & are pre-defined.

   | Metric Name | Description                                  |
   | ------------|:-------------------------------------------- |
   | BytesIn     | The number of incoming bytes via the server  |
   | BytesOut    | The number of outgoing bytes via the server  |
   | ServerState | The state of the server                      |

However, the user has a choice whether to include all of the pre-defined metrics for an individual server or not.

## ECS
The configuration file has the name of the AWS service (ECS) as a key at the top level. For each AWS ECS service, we can have entries of multiple clusters. Each cluster can have the configuration for the services running inside the cluster. For each cluster & service, we can have entries of multiple metrics along with their refresh interval time in seconds.

The following keys inside ECS i.e. **clusterName, region, metricName, awsMetric, refreshIntervalInSec, services & serviceName** are pre-defined and fixed. Also, the name of the metrics for ECS service i.e. **CPUReservation, MemoryReservation, CPUUtilization, MemoryUtilization & RunningTasks** have been taken from AWS & are pre-defined.

   | Metric Name        | Description                                                                   |
   | -------------------|:----------------------------------------------------------------------------- |
   | CPUReservation     | The percentage of CPU units that are reserved by running tasks in the cluster |
   | MemoryReservation  | The percentage of memory that is reserved by running tasks in the cluster     |
   | CPUUtilization     | The percentage of CPU units that are used in the cluster or service           |
   | MemoryUtilization  | The percentage of memory that is used in the cluster or service               |
   | RunningTasks       | The number of tasks in your services that are in the RUNNING state            |

However, the user has a choice whether to include all of the pre-defined metrics for an individual cluster & service or not.
