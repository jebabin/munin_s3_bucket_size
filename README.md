# munin_s3_bucket_size
Munin plugin to check size and number of object of a S3 compatible bucket

## Name

    s3_____multi

This plugin should be linked with a name like this

    s3_<endpoint>_<region>_<bucket>_<folder>_multi

Where:
- endpoint is the s3 endpoint. Ex: s3.eu-west-3.amazonaws.com
- region is the s3 region. Ex: eu-west-3
- bucket is the name of your bucket
- folder is optional.
  If you specify a folder, you will monitor the size of folders inside the specified folder instead of the size of folders at the root of the bucket 
  folder can only be the name of a folder at the root location of the bucket

Example:

    ln -s /path/to/s3_____multi /etc/munin/plugins/s3_s3.eu-west-3.amazonaws.com_eu-west-3_bucket1__multi

## CONFIGURATION

Following config is needed:

    [s3_<endpoint>_<region>_<bucket>_*]
    env.access_key_id ACCESS_KEY
    env.secret_access_key SECRET_ACCESS_KEY

Following config is optional

    user munin
	env.s3hostname 1

running as munin is optional, but if your default user is nobody, you may end up with a write permission erreur when running the plugin with the update_cache parameter  
Setting env.s3hostname to any value, will make the plugin to be advertising itself as running on <endpoint>, creating a dedicated entry in munin host list  
If doing so, you MUST update your munin.conf file on the munin master with the following entry

    [<endpoint>]
        address <hostname of munin-node server running the script>
        use_node_name no

Example:

    [s3.eu-west-3.amazonaws.com]
        address myserver.mydomain.tld
        use_node_name no

Getting the size of a bucket can be (very) long depending of the bucket size.
The script will not perform the actual check every time munin fetch data (every 5m). At fetch time, it gets data from a local cache

You **MUST** run the script by yourself to update this cache. To do so, you may want to use a cron entry.  
You **MUST** run the script with munin-run so that the script run with the right user, and get all the environment variable (including MUNIN_PLUGSTATE, MUNIN_CAP_MULTIGRAPH)

Typical command run by cron would be:

    sudo -u munin /usr/sbin/munin-run -d s3_s3.eu-west-3.amazonaws.com_eu-west-3_bucket1__multi update_cache

**IMPORTANT**: You will not get any graph using you have run the script with the update_cache parameter

## Requirements

Pyhton 3
boto3 module (pip3 install boto3)

## Todo

- Support invocation without bucket name (s3_<endpoint>_<region>___multi) and get a graph with the size/object count of all buckets
