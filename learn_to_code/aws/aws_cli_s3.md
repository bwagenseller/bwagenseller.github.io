# AWS CLI (Command Line Interface)  

# Notes For This Tutorial 

This section will focus on the <font color="purple">AWS Command Line Interface</font> (<font color="purple">AWS CLI</font>), _spectifically_ for the <font color="green">S3 bucket type</font>. The <font color="purple">AWS S3 CLI</font> is helpful if you have an AWS S3 bucket and you wish to issue it commands directly from your local system.  

Also, the default S3 bucket in the examples will be `s3://project-artifacts` unless otherwise stated.  

!> This does NOT cover the EC2 or any other bucket types.  

# Other Good Sources  

Some other good sources for <font color="purple">AWS CLI</font> commands are:  
* [cloudacademy](https://cloudacademy.com/blog/aws-cli-10-useful-commands/)  
* [Amazon](https://aws.amazon.com/cli/)  


# Installing AWS CLI (Ubuntu) 

Here is how you install the <font color="purple">AWS S3 CLI</font> on your Ubuntu instance:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).  

3\. Install the <font color="purple">AWS CLI</font>: `apt-get install awscli`  

> Now you can fully interact with your AWS bucket from your own command line!  

# Config Directory / Setup  

The install will probably prompt you to give configuration information; if not, you can set it up yourself. Create the directory `~/.aws` and create one file named simply <font color="purple">config</font> and store the following:  
```
[default]
region = us-west-2
output = json
```  
* Your region may be different.  

Now, create another file named <font color="purple">credentials</font> and store the following in it:   
```
[default]
aws_access_key_id = YOUR_ACCESS_KEY_ID
aws_secret_access_key = YOUR_SECRET_ACCESS_KEY_ID
```
* Make sure to change `aws_access_key_id` and `aws_secret_access_key` above.  
* <font color="red">Note</font> I like to [change the file permissions](operating_systems/ubuntu/linux_notes?id=changing-file-permissions) on this file to `700`.  

## Multiple Account Config

You can set up multiple accounts. Above, it was shown how you can make your default profile in `~/.aws/credentials`; you can add _additional_ accounts, and you should name them uniquely. For example, lets say we want a secondary account named <font color="green">king</font>; your `~/.aws/credentials` will look like so:  
```
[default]
aws_access_key_id = YOUR_ACCESS_KEY_ID
aws_secret_access_key = YOUR_SECRET_ACCESS_KEY_ID
[king]
aws_access_key_id = KINGS_ACCESS_KEY_ID
aws_secret_access_key = KINGS_SECRET_ACCESS_KEY_ID
```  
Then, in the AWS CLI, you would add a `--profile king` at the end of the command; for example, if you wanted to list all files in the filder `filters` in the S3 bucket `project-artifacts` - but you wanted to connect with the `king` account - the cli is:  
```
aws s3 ls s3://project-artifacts/filters/ --profile king  
```  


# Show Buckets (ls)  

To show all S3 buckets associated with your account, simply type the following at the command line:  
```
aws s3 ls
```  

Example output:  
```
2017-06-04 16:34:56 project-artifacts
2019-02-07 01:33:24 repo.mycompany.com
```  
* There are two S3 buckets available in this example.  
* The datetimes are the creation time of the bucket, using your local timezone.  

## Show Specific Bucket  

To show a _specific_ bucket - _and_ you want to list all files in the bucket recursively _as well as_ the file sizes to be human-readable - type the following at the command line:  
```
aws s3 ls s3://project-artifacts --recursive --human-readable
```  
* This shows the content of the _specific_ S3 bucket `project-artifacts`.  
* If you do not want the recursion, simply omit `--recursive`.  
* If you do not want the human-readable file sizes, simply omit `--human-readable`.  

You can also show the contents of a _specific_ directory on a _specific_ bucket too like so:  
```
aws s3 ls s3://project-artifacts/someFolder --recursive --human-readable
```  
* <font color="red">Note</font> _If_ you do not use the `--recursive` option, you *must* end the directory with a `/` (for example, `s3://project-artifacts/someFolder/`) - otherwise, it will simply return the name of the directory with a `/` at the end of it.  

# Copying Files 

## Copying Files To S3  

If you have a file on your local machine (`someFile.txt`) and you wish to copy it to your S3 bucket, you can do that like so:  
```
aws s3 cp someFile.txt s3://project-artifacts/someFile.txt
```  

If you wish to copy a directory to your S3 bucket recursively, you can do that like so:  
```
aws s3 cp someDirectory s3://project-artifacts/someDirectory --recursive [--region us-west-2]  
```  
* This copies the directory `someDirectory` on your local machine to `someDirectory` on the remote S3 bucket.  
* We also specified the region too with `[--region us-west-2]`; this is only necessary if you wish to specify the region.  

## Copying Files From S3  

If you have a file on your S3 bucket (`someFile.txt`) and you wish to copy it to your local machine, you can do that like so:  
```
aws s3 cp s3://project-artifacts/someFile.txt someFile.txt 
```  

If you wish to copy a directory from your S3 bucket recursively, you can do that like so:  
```
aws s3 cp s3://project-artifacts/someDirectory someNewLocalDirectory --recursive  
```  
* This copies the directory `someDirectory` on your S3 bucket to `someNewLocalDirectory` on your local machine.  
* Note that `someNewLocalDirectory` does _not_ have to exist on your local machine before you start the copy.

# Print Single S3 File To Screen  

Its possible to use something similar to `cat` to print the contents of a file to the screen; the trick relies on using copy and the `-` parameter (which means send to the screen):
```
aws s3 cp s3://project-artifacts/someDirectory/someFile.txt -
```  
* Prints the contents of `someFile.txt` to the screen.  
* Note the `-` at the end - this is important!  

# Move A Bucket

Its possible to _move an entire bucket_ to a different location with one command - that command is:
```
aws s3 sync s3://someOldbucket s3://someNewbucket --source-region us-west-2 --region us-west-1
```  
* `--source-region` is the current region, and `--region` is the new region.  

# Transcribe Commands  

## Adding a Vocabulary Filter

AWS Transcribe has the ability to detect specific words in the returned text; a text file is used to identify these words. Once you have the list together, save them to a S3 bucket (I use the name <font color="green">test-vocabulary-filter.txt</font> below). Once you have the list and have [loaded it onto a S3 bucket](learn_to_code/aws/aws_cli_s3?id=copying-files-to-s3), come up with a unique name for this filter (I will use the basic name of <font color="green">test</font> below) and then run this command to actually load the filter:

```
aws transcribe create-vocabulary-filter  
    --vocabulary-filter-name test
    --language-code en-US  
    --vocabulary-filter-file-uri s3://project-artifacts/filters/test-vocabulary-filter.txt
```  

Note that you will have to actively name the filter in your code, as well as instruct what should be done with the word. For example if we are using Java and we simply want to have the code passively notify us of the word, we would activate these two methods in the `StartStreamTranscriptionRequest` object:

```
...
	.vocabularyFilterMethod(VocabularyFilterMethod.TAG)
	.vocabularyFilterName("test")
...
```  

This will tell the code to set the `.vocabularyFilterMatch()` in the return to `true` if the associated word was in the filter.  

!> Once a vocabulary filter is created, you _cannot_ overwrite it - to do this you will need to delete the filter first, and then re-create it via the command in this section.  

## Removing a Vocabulary Filter  

We learned how to add a vocabulary filter [here](learn_to_code/aws/aws_cli_s3?id=adding-a-vocabulary-filter), but sometimes you need to delete the filter (especially if you intend on updating the filter); to do so, use this command:
```
 aws transcribe delete-vocabulary-filter --vocabulary-filter-name test
```  
* The above assumes the vocabulary filter name is <font color="green">test</font>.  

