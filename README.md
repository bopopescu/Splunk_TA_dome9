# Splunk_TA_dome9
Splunk Technology Add-on for Dome9

## App works in conjunction with https://splunkbase.splunk.com/app/3203/

This Technology Add-on (TA) is for people who have an account with dome9 (http://www.dome9.com) and want to gather their alerts into their Splunk instance.  This TA requires both the Splunk App for AWS (https://apps.splunk.com/app/1274/) AND the Splunk Add-on for Amazon Web Services (https://apps.splunk.com/app/1876/). 

### This TA uses the AWS SQS to pull data from Dome9 on a 30 second (default) interval. For a more real-time data feed, please use the Dome9 App for Splunk which leverages Amazon's Lambda functions. 

Here's a list of files that are added to your $SPLUNK_HOME/etc/apps/Splunk_TA_aws folder:
bin/aws_dome9.py
default/data/ui/manager/data_inputs_aws-dome9.xml
default/props.conf
default/inputs.conf
README/inputs.conf.spec

##Caution, this will overwrite the configuration files in your default directory
Any subsequent upgrades to future versions of the Splunk TA for Amazon Web Services will remove the changes made. 

If you need to make any changes to the TA, make sure to just copy the aws_dome9.py and data_inputs_aws-dome9.xml files and make the following config file entry changes:

props.conf
-------------
    ##################################
    ###         AWS dome9      ###
    ##################################
    
    [aws-dome9]
	SHOULD_LINEMERGE = false
	TRUNCATE = 8388608
	TIME_PREFIX = \"TimeStamp\"\s*\:\s*\"
	TIME_FORMAT = %Y-%m-%dT%H:%M:%S%Z
	MAX_TIMESTAMP_LOOKAHEAD = 28
	KV_MODE=json
	EXTRACT-UNQUOTED-KVPS = (?:\\r\\n)?(?:\\n)?(?:\\t)?(?<_KEY_2>[a-zA-Z0-9._]+)=(?<_VAL_2>[a-zA-Z0-9_:;!@#$%^&*()\/[\]{}|+.~'\-]+)
    
    # Legacy Input
    [aws:dome9]
    SHOULD_LINEMERGE = false
    TRUNCATE = 8388608
    TIME_PREFIX = \"eventTime\"\s*\:\s*\"
    TIME_FORMAT = %Y-%m-%dT%H:%M:%S%Z
    MAX_TIMESTAMP_LOOKAHEAD = 28

    
    # Notifications/Diff Payloads
    [source::aws:dome9:notification]
    sourcetype = aws:dome9
    #SHOULD_LINEMERGE = false
    #TRUNCATE = 8388608
    #TIME_PREFIX = \"eventTime\"\s*\:\s*\"
    #TIME_FORMAT = %Y-%m-%dT%H:%M:%S.%3NZ
    #TZ = GMT
    #MAX_TIMESTAMP_LOOKAHEAD = 28
    #KV_MODE = json
    #ANNOTATE_PUNCT = false

inputs.conf
-------------
    [aws_dome9]
    aws_account =
    sourcetype = aws:dome9
    #exclude_describe_events = true
    enable_additional_notifications = false
    queueSize = 128KB
    persistentQueueSize = 24MB
    interval = 30
  

README\inputs.conf.spec
-------------------------
    [aws_dome9://<name>]
    aws_account = AWS account used to connect to AWS
    aws_region = AWS region of log notification SQS queue
    sqs_queue = Starling Notification SQS queue
    enable_additional_notifications = Enable collection of additional helper notifications

