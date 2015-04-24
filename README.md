# aws_certified_developer_associate_NOTES
##AWS regions
- Total 11 regions, 9 below + gov cloud + beijing
|Region name|Location|
|-----------|--------|
|ap-northeast-1|Asia Pacific (Tokyo)|
|ap-southeast-1|Asia Pacific (Singapore)|
|ap-southeast-2|Asia Pacific (Sydney)|
|eu-central-1|EU (Frankfurt)|
|eu-west-1|EU (Ireland)|
|sa-east-1|South America (Sao Paulo)|
|us-east-1|US East (N. Virginia)|
|us-west-1|US West (N. California)|
|us-west-2|US West (Oregon)|

##Cloud Formation
- Allows us to deploy resources through a json template
- the template can build a complete stack
- stack is set of resources that are created and managed as a single unit when cloud formation instantiated a template
- the AWS resourced which are a part of the stack can be individually managed
- the template json has 5 types of elements
  * parameters optional
  * outputs optional
  * list of data tables optional
  * list of resources
  * template file format version number

- Sections: Resources, Parameters, Outputs, AWSTemplateFormatVersion, Description, Mappings
- Resources section in the template is the only thing required for it to work
- Fn::GetAtt returns a value for the specified attribute
- If error happens, then a rollback takes place. However charges may apply for the time the resources were created
- cfn-list-stacks : lists all current stacks in your cloud formation
- template description cannot be added if AWSTemplateFormatVersion is not declared at the top 
- deletion policy can be specified, and snapshots can be taken before deletion
- stack can be modified after creation
- stacks can be created in VPC

- Limits
o there are no limits on number of templates
o Template, parameters, output and resource description are limited to 4096 characters
o you can have 60 parameters and 60 outputs in a template
o Each cloudformation account is limited to 20 stacks. Can be increased by contacting AWS via form

- cloud formation access point url https://coundformation.<region>.amazonaws.com

##DynamoDB
- NoSQL fully managed database service
- Dynamo db stores 3 geographically distinct replicas of each table 
- tables can have any amount of data, you need to manage throughput 
- remains available when scaling up or scaling down
- no need for user to partition, as it is fully managed service
- can scale read/write capacity up/down without any downtime
- performance is controlled through provisioned throughput
- all data is stored on SSD
- Synchronous replication across availability zones
- strong consistency and atomic counters available
- holds structured data with low latency read writes to items ranging from 1 byte to 400kb; whereas s3 stores unstructured data upto 5tb
- dynamodb can be used by applications running on any operating system
- Dynamodb local is an offline version of dynamo db
- supports key-value and document data structure
- key-value store is normal key-value store that I know about
- document store you can store query update documents formats like json, xml, html
- dynamodb does not have json data type, but you can pass json. Dynamob datatypes are superset of json

- -DynamoDB data model
o table : collection of data items, can have unlimited data items
o item : is composed of a primary key, composite key and flexible number of attributes. can have unlimited number of attributes, but total item size should be from 1 byte to 400kb
o attribute: has attribute name and value or set of values. Individual attributes have no size restriction, but total item size should be less than 400kb
- Dynamo DB Limits
o no limit on how much data and throughput 
o 256 tables per region, can increase by calling aws
o range primary key can have 1024 bytes
o hash primary key can have 2048 bytes
o item size - one row in table 400kb including attribute name
o 5 local and 5 global secondary indexes per table

- Keys
o primary key is the only required attribute
o you specify primary key when you create a table
o primary key can be hash index ; or hash and a range key composite
o primary key should be unique for good performance; eg userid for user table
- Secondary index
- upto 5 Local Secondary Index: same Id as primary hash, but different range
- It is local because the scope stays to the same hash key partition as the table
- upto 5 global secondary index: hash and rage key are different to that of table, hence queries can span all data in the table across all partitions
- Local secondary index
o Local Secondary index can be added only at the time of table creation. They cannot be added or changed later
o You can specify the indexed range key and projection ie the subset of attributes to be returned
o LSI supports both strong and eventually consistent read
o the primary index of the table must use a hash-range composite key
o there can be upto 20 projected non-key attribues

- Item Collection
o A group of items having the same hash key is an item collection. Similar to shards or partitions in normal sql
o Item collection max size is 10GB
o Item collections are automatically created and maintained for tables having local secondary index

- Global Secondary Index
o Global secondary index can be specified at any time, they can be changed even after table creation
o global secondary index need not be on unique attribute, unlike primary key
o Global secondary index support eventual consistency only
o you can provision throughput for table and each associated global secondary index at the time of table creation


- Provisioned throughput
o each table can have read write throughput provisioned. based on provisioning, in the background aws assigns resources
o Unit of read capacity: 1 strongly consistent read per second of 4kb item
• 2 eventually consistent read per second of 4kb item
o Unit of write capacity: 1 write per second of 1kb item
o if you want to have read/write throughput of more than 10000 per table, then contact AWS
o if you want to have more than 20000 read/write per aws account, then contact AWS
o minimum throughput is 1 for read/write
o you can increased throughput any number of time. increasing throughput takes few minutes to few hours
o you can decrease throughput only 4 times in a day. takes few seconds to few minutes
o if more read/write than provisioned then they will be throttled and you will get 400 error code. You can monitor throughput in cloud watch


- Reserved capacity
o you can reserve capacity by upfront payment and a commitment of minimum monthly usage
o You cannot cancel reserved capacity
o smallest reserved capacity is 100 reads/writes
o cannot move reserved capacity to another region

- Query 
o supports get/put operations using user defined primary key
o Supports conditional operations
o supports increment and decrement operations
o search only primary and secondary keys
o more efficient
o returns all attributed of an item, but projection expression can be created to return fewer attributes
o by default it is eventually consistent, can change to strongly consistent
- Scan
o reads every item in the table, and applies filter to refine results
o will scan all items/ attributes in the table
o inefficient and slower performance
o Only eventually consistent reads are available
o Scan has limit of 1MB per operation. LastEvaulatedKey is returned to pickup next set of data

- Conditional Writes and atomic counters
o multiple users updating item at the same time. Conditional write only updates the item if a condition is met
o atomic counter allows you to increment or decrement a value of an existing attribute without interfering with other write requests
o atomic: all writes are applied in the order received
o Use UpdateItem for atomic coutner inc/dec
o atomic update is fast in place update

- Dynamo DB strongly vs eventually consistent
o when reading data, user can specify if they want eventual or strongly consistent read
o eventually consistent is default consistency is usually reached within 1 second
o strongly used extra read throughput capacity to ensure that you receive most upto date version of an item


- Common API commands
o BatchGetItem to get many items at time through API. Not BatchGetItems
o BatchWriteItem, PutItem
o CreateTable, DeleteTable, UpdateTable, DescribeTable, ListTables
o UpdateItem
o ProjectionExpressions can be defined to determine which attributes are returned form a table


##SNS Simple Notification Service
- sends notifications from the cloud
- fast and fully managed push messaging system.
- sends out time critical notifications
- SNS sends push messages, whereas SQS pulls messages
- Topic can be created for notifications to be sent out
- You can create multiple subscriptions for a single topic. Subscription is an endpoint. 
- topic owners can change permissions to allow more than 1 user to publish a topic
- Endpoints can be
o http, https
o email, email-json
o sms
o amazon sqs
o application
- Examples of services using SNS
o Cloudwatch alarm
o s3 rrs
o rds notification of change
- can send different messages based on endpoint
- Message data has the following
- Message
- MessageId
- Signature - Base64 encoded SHA1withRSA signature of message, messageid, subject, type, timestamp and topicARN
- SignatureVersion
- SigningCertURL
- Subject
- Timestamp
- TopicArn
- Type
- UnsubscribeURL

- Confirmation of subscription message stays valid for 3 days
- A notification will not have more than one message
- SNS messages are not guaranteed to be delivered in order
- Message once sent cannot be recalled
- direct addressing is possible, allows delivery to a single endpoint. direct addressing only supported for mobile push, not for sms or email

- SMS notifications
- Topics with SMS subscription need to have DisplayName; for others it is optional
- Only first 10 characters of display name will be included in SMS
- SMS subscriber can stop messages by sending STOP to 30304
- SMS publications are right now available only in the US east region
- MMS is not supported
- SMS restricted to 140 characters including display name
- Currently only available to US phone numbers

- SNS limits
- 10 million subscriptions per topic
- 3000 topics per account
- SNS message can have upto 256kb of data
- Billing unit is 64kb

- Mobile push notifications
- Apple, google, amazon, windows, microsoft and baidu
- You can specify TTL Time to live for each message, message is deleted after ttl expires. Default TTL is 4 weeks for all mobile platforms
- TTL = 0 is special case for message to deliver immediately or expire
- Delivery status feature is available only for mobile push


- Common API
- AddPermission, RemovePermission

- SNS serive acceepoint URL http://sns.<region>.amazonaws.com


##SQS Simple Queue Service
- Loosely decouples the resources
- it is a scalable messaging system
- Queues are used to store messages that travel between different components of architecture
- each queue can have unlimited messages, and you can create unlimited queues
- components are called workers, thy keep polling the queue
- allows individual resource to scale up based on load
- SQS guarantees delivery atleast once. So there can me more than 1 duplicate messages
- messages can be upto 256kb in size. can also attach instructions to access larger messages in dynamodb or s3
- 64kb is 1 billable message unit
- Lifecycle: 1. Component 1 sends message to Q and the message is redundantly distributed across SQS server. 2. When component2 is ready, it retrieves and messages becomes invisible from queue for a period of visibility timeout. 3. Component2 deletes the message after successfully processing the message
- Short polling (default) : polls only a subset of SQS servers. Continuous short polling to get all messages. Each short poll is charged, there might be empty messages and be charged
- Long polling: reduces cost as reduces number of empty messages                            
- Long polling does not return a response till a message is available or till the long poll times out
- Long polling has maximum timeout of 20 seconds
- delay queue(0sec to 12 hours): amount of time to delay the first message
- message retention period (1 minute to 14 days, default is 4 days): amount of time message will remain in queue if not deleted
- default visibility timeout 30 seconds ; can be upto 12 hours. ChangeMessageVisibility
- receive message wait time: If set > 0, then long polling is enabled. 
- Upto 120k inflight messages
- SQS does not guarantee the delivery order of the messages
- if order is important, then sequencing info can be embedded in the message
- each message has a globally unique id which sqs returns when message is delivered to the queue
- Dead letter queues are offered where one SQS can get messages from another SQS
- SQS fanning out can be done using SNS topics where messages can be fanned out to other SQS queues
- SQS Does not guarantee first in first out
- can send upto 10 attributes as metadata on each message. As name-type-value triplets
- Queue names are limited to 80 characters, should be unique within an aws account
- AWS might delete a queue if there is no activity for 30 days
- Messages cannot be shared between queues in different regions

- Common API
- ChangeMessageVisibility : change visibility by worker 
- ChangeMessageVisibility : for upto 10 messages
- SetQueueAttribues/GetQueueAttributes
- VisibilityTimeout attribute to change default visibility timeout
- ReceiveMessageWaitTimeSeconds (>0 for long polling) max is 20 seconds
- DelaySeconds
- DeleteMessageBatch: deletes multiple message
- SentTimestamp
- GetQueueURL : returns URL for an existing queue
- CreateQueue, ListQueue, SendMessage, SendMessageBatch...
- PurgeQueue to delete all messages in the queue

- SQS service access point URL : http://sqs.<region>.amazonaws.com


##Simple Work Flow Services
- Each step is a task, similar to SQS
- Task coordination and state management service
- A workflow can consist of human events, unlike SQS
- A workflow can last upto 1 year
- It guarantees the order in which activities/tasks occurs
- Domains
- domain helps to determine the scope of work
- multiple workflows can live inside a domain
- workflows cannot interact with workflows in other domains
- workers and deciders
- Activity workers - process that performs an activity in the workflow
- Activity workers poll SWF for new tasks that they need to perform
- Activity workers will perform the task and report back to SWF.
- Workers can be servers on EC2 or on premise, or humans
- workers can be run behind firewall

- Activity task is a task assigned to a worker
- Decision task tells the decider that the state has changed. It allows decider to decide next activity. 
- Decision task occurs whenever the state of workflow changes ex when task completes

- SQS vs SWF
- SQS has best effort message order and potentially has duplicates
- SWF guarantees execution order and uses deciders for next instructions
- SQS messages live upto 14 days, SWF workflow can last upto 1 year
- SWF allows synchronous or asynchronous distributed processing
- SWF is  task oriented, whereas SQS is message oriented

- Limits
- 10000 workflow and activity types
- 100 SWF domains
- 100000 open executions in a domain
- SWF workflow can last maximum for 1 year



##IAM
- Roles
- is a iam entity which defines set of permissions for making aws service requests
- Roles allow someone or a set of code or instance to assume certain permissions- More secure than using api codes
- users, ec2 or even web identity can assume roles
- Identity federated login like facebook etc . Federated login can assume a role- policy  can be attached to user to give it privileges. the api credentials can then be used to login from anywhere and get the privileges
- no limit on number of roles you can assume
- you can create 250 IAM roles under your aws account. more can be added by contacting AWS
- you cannot change the role of a running EC2 instance
- Only one role can be attached to an EC2 instance
- If a role is deleted then the ec2 instance tied to the role will lose permissions immediately

- Groups
- Collection of IAM users, users can be added or removed from any group
- User can belong to multiple groups
- Group cannot belong to another group
- Groups can be granted access control policies
- groups do not have security credentials, so groups cannot login. they are only for managing users
- role cannot be added to a group

- users can be structured in hierarchical way like ldap
- users are global entity and are not bound to regions
- multiple mfa devices can be procured and then assigned to users 
- user access keys and x.509 certificates can be rotated
- users cannot have individual ssh keys for ec2. there is one ssh key per ec2 instance
- iam users need not have email address
- password policy can be set for passwords
- No quotas can be set on a per user basis
- The AWS sign-in endpoint for SAML is https://signin.aws.amazon.com/saml
- The default region for an SDK is "US-EAST-1"
- temporary security credentials : min 1 hour, default 12 and max 36 hours validity

- Common API
- AssumeRole, AssumeRoleWithWebIdentity, and AssumeRoleWithSAML
- GetFederationToken



##EC2
- AMI can be copied, and they can also be shared across multiple AWS accounts
- AMI can also be copied from one availability region to another
- EBS backed or instance storage. EBS can be attached or detached to an instance- Instance storage is ephemeral, data is lost when instance is terminated
- spot instances Let you bid on unused instances. Amazon can take the spot instances away anytime
- on demand instance
- EC2- classic limits: 20 instances per account; 5 Elastic IP addresses. These can be increased by contacting AWS
- EC2 uses ECC memories
- Differences in instances: memory, compute power, block storage, network performance and EBS optimization
- If instance is off state, then you only pay for data storage

- When EC2-Classic is created, an IP address is assigned. This IP address can change when you start or stop the instance
- When EC2-Classic is created, an CNAME is assigned. This CNAME can change when you start or stop the instance
- You can setup an elastic IP address to maintain your IP address. This can be attached/detached to EC2
- EC2-Clasic is deprecated
- Termination protection can be used to avoid accidental termination

- Default SDK region is US-EAST-1
- Different storages:
- s3, instance storage volume and EBS (/dev/sda1) : EBS are network attached storage 
- instance storage is instance attached storage

- EBS can be attached only to 1 instance
- volumes do not need to be un-mouted to take snapshots
- atleast 1GB, and max of 1TB of EBS
- EBS RAID 0 for redundancy
- prewarming EBS: when EBS is reattached to EC2, EBS is erased on first mount causing loss of 5 to 50% OPS firs time. Prewarming can be used to avoid this.
- EBS snapshots can be taken, they can be incremental

- Availability zones are physically separate and do not share generators, coolers etc .

- reserved instances: pay upfront to get guranteed compute 
- reserved instances cannot be moved to another region
- reserved instances can be modified: move to another availability zone, convert the type of instance to larger or smaller size within same family ex convert 8 m1.small to 4 m1.medium etc ..


- ELB allows to distribute traffic to available instances. Automatically stops traffic to unhealthy instance. 
- SSL certificates can be setup on ELB.  
- Maintaining session state when using ELB
- session will be saved on individual instance. But if there are multiple instances load balanced, then session can be lost
- Enable ELB cookie stickiness or application generated stickyness
- Database or memcache can be used to solve the session problem as well in case of elasticache
- Elasticache with memcache is the recommended way to sovle the cookie prolem. 
- When using elasticache, ELB works truly like ELB and not route all traffic to few instances based on cookies



##Virtual Private Cloud - VPC
- creates a virtual network. It resembles a private datacenter or a private corporate network
- If you delete default VPC, the only way you can get it back is by contacting AWS
- default vpc is created upon instance creation. Now by default all ec2 is launched under a vpc.dfault vpc is connected to internet by default. It replaces ec2 classic functionality
- Instances in VPC behave as if they were on the same private network
- VPCs can be peered. Peering is in a star config, ie 1 central vpc
- VPC peering can be done with vpc in the same region only
- no additional charges for vpc, underlying services are charged as usual
- size of vpc between /28 (14 Ip addresses) and /16 
- to change vpc size, you have to terminate and startover
- amazon vpc does not support multicast and broadcast
- you can assign multiple ip addresses 

- instances without Elastic IP address can access internet in two ways
- via a NAT instance. they can use EIP of nat instance. Nat allows outbound connections, but does not allow internet to initiate connection to these machines
- for vpc with hardware vpn, via virtual private gateway to existing data center

- components of VPC
- VPC
- subnet
- internet gateway
- NAT instance: an ec2 instance that provides port address translation for non-eip instances to access internet via the gateway
- hardware VPN
- virtual private gateway : amazon side of a vpn connection
- customer gateway : customer side of vpn connection
- router to interconnect subnets and direct traffic between internet gateway , virtual private gateway, nat and subnets 

- Benefits of VPC
- Ability to launch instances into a subnet (range of ip)
- Ability to define custom ip address ranges inside each subnet
- Ability to configure route tables between subnets
- ability to configure internet gateways and attach them to subnets
- create layered network of resources


- VPC limits
- 5 VPC per region, more on request
- 5 internet gateways per account, equal to VPC limit
- 5 virtual private gateways and 5 customer gateways
- 1 internet gateway attached to a subnet at a time
- 1 subnet can only be in 1 availability zone
- 50 customer gateways per region
- 50 VPN connections per region
- 200 route tables per region
- 200 subnets per amazon vpc
- 5 elastic IP addresses
- 100 security groups
- 50 rules per security group
- VPC via public subnet is accessible over internet. VPC via private subnet is not accessible over internet, atleast not without NAT (network address translation)
- CIDR: Classless Inter Domain routing: specifies what your continuous IP address range is
- You cannot create a VPC larger than /16
- Tenancy: Default vs dedicated. Default is shared with others, dedicated means hardware is dediacated for you. the underlying hypervisor is dedicated in dedicated tenancy
- if EC2 choose a dedicated vpc, automatically EC2 will be created as a dedicated. similar for any resouce which choosed a dedicated vpc
- You can create subnets in different availability zones for highly available architecture


- A route table is automatically provisioned when you create a VPC
- You can create a route table, and assign it to a subnet
- Each subnet can be associated with only 1 route table

- Internet Gateway
- a gateway out to internet. 
- You can only have 1 internet gateway per VPC
- Internet gateway needs to be attached to a subnet
- EC2 do not automatically get connection to internet. You need to connect the E2 via Elastic Ip or Elastic load balancer

- Steps to get internet
- Create internet gateway
- Attach internet gateway to vpc
- create a new route table and attach it to a subnet
- Edit route table and attach it to internet gateway
- Create elastic ip or elb and attach it to ec2 created under the vpc


- under aws, you can have 251 ip addresses, opposed to industry standard 254 as they reserve a few 
- EC2 launched under a dedicated subnet will not have a public DNS or a public IP The only way to get a public IP is by creating a gateway and : assigning IP addresses manually or using ELB groups
- Even if you attach a public IP address , you still need a route to the internet to ssh in or for access over internet
- You can ssh into one EC2 and then to the other EC2 without public address from within as they are all members of same subnet. So you can connect to public instance first and then to the internal instances from within the public instance

- Each subnet can only be associated with one route table

- Access Control Layer ACL
- ACL allows to create firewall rules that deny or allow traffic at subnet / network level. This is tighter than security group
- ACL are stateless filtering
- Security group sits in front of EC2 instance and allow or deny traffic at EC2 level


- NAT instance can make private machines inside vpc access internet, however the machines are not publically accesible



##Simple Storage Service S3
- An AWS account can have upto 100 S3 buckets
- No limits on the number of objects stored within a bucket
- any kind of data in any format can be stored in s3
- S3 is a simple key based object store
- S3 in US Standard provided eventual consistency. In all other regions, s3 provided read-after-write consistency for PUTS of new objects and eventual consistency for overwtite PUTS and DELETES
- within a region objects are redundantly stored on multiple devices across multiple facilities
- Content MD5 and CRC are used to detct data corruption
- Cross region replication (CRR) is allowed. Enable it to replicate data across different regions. ofcouse more cost
- Event notifications can be setup in s3, messages can be sent out using SNS, SQS or lambda
- Reduced redundancy storage is provide RRS. durability is 99.99% vs 99.999999999% of standard s3
- if data lost in RRS, then 405 error. Can also setup  SNS
- S3 can host static website. Bucket name must match domain name for website hosting. 
- S3 supports own website name or website redirects

- Access control in s3 : 4 ways can be used to secure access
- IAM policies
- bucket policies
- ACLs
- Query string authentication: can create URL to s3 object which is only valid for certain time


- Bucket name restrictions
- upper, lowercase letters, numbers periods and dashes
- must start and end with letter or number, not period or  dash
- minimum 3, max 63 characters
- cannot be an IP address style name (10.2.2.1)
- periods and dashes cannot follow each other
- S3 object size
- minimum of 1 byte
- maximum of 5TB
- Object greater than 5G are required to be multipart upload api
- largest object using PUT operation is 5G
- for greater than 100Mb, you should consider multipart
- can upload a file as it is being created,
- can start or stop uploads

- Must call CompleteMultiPartUpload to reassemble the file
- S3 sorts and stores files in lexicographical order - alphabetical order. 
- So if you are reading or writing many files, then add a random hash in the begining of filename so s3 stores in different partitions. Hence throughput performance increases
- S3 allows hosting static HTML
- Route 53 can direct your domain to your bucket. But the bucket name needs to match domain name
- No subdomains using s3 buckets

- URL name <bucket-name>.s3-website-<region>.amazonaws.com

- Error codes 400-client side error, 500 server side error
- 404 not found : requesting bucket which does not exist
- 403 forbidden : accessing bucket for which you do not have permission
- 400 bad request : invalid bucket state
- 409 conflict: If you try to delete a bucket that is not empty
- 500 server : s3 issue

- CORS Cross Origin Resource Sharing
- in order to load resource from another bucket, you have to enable CORS
- In the resource bucket add CORSconfiguration and add CORSRule and specify the AllowedOrigin and AllowedMethod

- S3 Permissions: bucket permissions
- S3 polices can be created only by the owner of the bucket
- can allow/deny bucket level permissions
- bucket ownership cannot be transferred
- can allow/deny object level permission if the bucket owner is owner of object
- Cross account management through ACLs
- Bucket policies can only be 20kb size

- IAM policies vs bucket policies vs ACLs 
- IAM polices are account level
- Bucket policies are object / resource based - only by bucket owner
- ACLs are cross account object/bucket level permissions. ACLs can be used for grantint other AWS accounts to s3 resources

- AWS gives full permissions to owners of the bucket
- However, IAM policy can deny owner of a bucket from upload/modify or list
- explicit deny always overrides an allow
- Permissions are applied to S3 ARNs
- access can be applied at object level, as each object will have ARN

- S3 Server side encryption
o provides encryption on bucket level and object level
o x-amz-server-side-encryption request header is added in metadata with the upload, then aws will automatically add server side encryption
o AES 256 encryption
o Enable SSE through console or api. Python currently does not support SSE
o AW deals with encryption and decryption automatically
o You can also use your own encryption keys, but then you have to manage encription and decryption

