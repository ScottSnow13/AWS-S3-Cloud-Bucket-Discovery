# AWS S3 Cloud Bucket Discovery

This walkthrough guides you through identifying and assessing the threat of misconfigured cloud storage buckets using a simulated cloud environment. You'll interact with AWS S3 buckets using the AWS CLI and bucket_finder tool, generating custom bucket name lists using different methods.

## Prerequisites:

  - **`Linux VM`**: Ensure your Linux VM is running before starting this walkthrough.
  - **`AWS CLI`**: Preconfigured on your VM.
  - **`bucket_finder`**: A tool by Robin Wood for identifying S3 buckets.

## Steps:

  1. Start the Simulated Cloud Service

     Open a terminal on your Linux VM and run gos3 to launch the simulated cloud environment.

     ```
     user@LinuxVM:~/home$ gos3
     ```

  2. Examine AWS Credentials

     Display the contents of the AWS credentials file.

     ```
     user@LinuxVM:~$ cat ~/.aws/credentials
     ```
     
  3. Create and Interact with an S3 Bucket

     - Create a Bucket: Use the AWS CLI to create a new bucket.
    
       ```
       user@LinuxVM:~$ aws s3 mb s3://mybucket2
       ```
       
     - Upload a File: Create a file and upload it to the bucket.

       ```
       user@LinuxVM:~$ ps -ef > pslist.txt
       user@LinuxVM:~$ aws s3 cp pslist.txt s3://mybucket2/
       ```

      - List the Bucket: Verify the uploaded file.
    
        ```
        user@LinuxVM:~$ aws s3 ls s3://mybucket2
        ```
  4. Reconnaissance Analysis

     Use Firefox to visit the Falsimentis website and identify the S3 bucket service.

     - Open Firefox: Navigate to the website.
    
       ```
       user@LinuxVM:~$ firefox http://www.faketestsite.com
       ```

     - Identify the Bucket: Note the URL structure of linked resources, e.g., '**`www.faketestsite.com.s3.amazonaws.com`**'.
    
  5. Access the Discovered Bucket

     List the contents of the discovered bucket using the AWS CLI.

     ```
     user@LinuxVM:~$ aws s3 ls s3://www.faketestsite.com
     ```

   6. Bypass Directory Authentication

      Access the protected directory and download its contents.

      - List the Protected Directory:
     
        ```
        user@LinuxVM:~$ aws s3 ls s3://www.faketestdite.com/protected/
        ```
        
      - Download the Contents:
     
        ```
        user@LinuxVM:~$ aws s3 sync s3://www.faketestsite.com/protected/ protected/
        ```

  7. Bucket Discovery with Short List

     Use bucket_finder with a short list of bucket names.

     ```
     user@LinuxVM:~$ bucket_finder.rb ~/home/s3/shortlist.txt
     ```
     
  8. Bucket Discovery with Longer List

     - Run bucket_finder:
    
       ```
       user@LinuxVM:~$ bucket_finder.rb ~/home/s3/bucketlist.txt | tee bucketlist1-output.txt
       ```

     - Filter Results:
    
       ```
       user@LinuxVM:~$ grep -v "does not exist" bucketlist1-output.txt
       ```
  9. Generate a Custom Bucket List

     - Create Custom List Using Permutations:
    
       ```
       user@LinuxVM:~$ awk '{print "faketestsite-" $1}' ~/home/s3/permutations.txt > bucketlist2.txt
       ```

     - Verify the List:
    
       ```
       user@LinuxVM:~$ head bucketlist2.txt
       ```

     - Run bucket_finder with Custom List:
    
       ```
       user@LinuxVM:~$ bucket_finder.rb bucketlist2.txt | tee bucketlist2-output.txt
       ```

  10. Generate and Use a CeWL List

      - Run CeWL:

        ```
        user@LinuxVM:~$ /opt/cewl/cewl.rb -m 2 -w cewl-output.txt http://www.faketestsite.com
        ```

      - Convert to Lowercase:
     
        ```
        user@LinuxVM:~$ cat cewl-output.txt | tr [:upper:] [:lower:] > cewl-wordlist.txt
        ```

      - Run bucket_finder with CeWL List:
     
        ```
        user@LinuxVM:~$ bucket_finder.rb cewl-wordlist.txt | tee cewl-output.txt
        ```
        
## Conclusion:

In this walkthrough, you explored methods for identifying insecure cloud storage buckets. Using the AWS CLI and bucket_finder tool, you successfully created, accessed, and enumerated S3 buckets, demonstrating how attackers might exploit misconfigured cloud storage services.

## References:

  - AWS CLI: https://aws.amazon.com/cli/
  - bucket_finder Tool: https://digi.ninja/projects/bucket_finder.php
  - CeWL Tool: https://digi.ninja/projects/cewl.php


       

       
       
     





       



     
