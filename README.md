## Level 1
**_Description:_** This level is *buckets* of fun. See if you can find the first sub-domain.

The emphasis on *buckets* tells me this may be related to S3 buckets. To confirm, the ```host``` command was used to obtain the site's IP address:<br>
```sh
$ host flaws.cloud 
flaws.cloud has address 52.92.241.115
flaws.cloud has address 52.92.233.11
flaws.cloud has address 52.218.234.82
flaws.cloud has address 52.92.230.107
flaws.cloud has address 52.218.133.131
flaws.cloud has address 52.92.226.227
flaws.cloud has address 52.92.209.155
flaws.cloud has address 52.218.153.106
```
A reverse DNS lookup was then performed on one of the IP addresses:
```sh
$ host 52.92.241.115
115.241.92.52.in-addr.arpa domain name pointer s3-website-us-west-2.amazonaws.com.                                                           
```
This shows that the IP is resolved to ```s3-website-us-west-2.amazonaws.com```, which is a [website endpoint](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteEndpoints.html#website-endpoint-examples) used by AWS S3 for hosting static websites.

[AWS](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/getting-started-s3.html#getting-started-create-s3-website-bucket) requires the bucket and domain name to be the same, therefore we can conclude flaws.cloud as the bucket name.
Using the AWS CLI, I attempted to list the bucket's content and found the following files:
```sh
$ aws s3 ls s3://flaws.cloud/ 
2017-03-13 23:00:38       2575 hint1.html
2017-03-02 23:05:17       1707 hint2.html
2017-03-02 23:05:11       1101 hint3.html
2024-02-21 21:32:41       2861 index.html
2018-07-10 12:47:16      15979 logo.png
2017-02-26 20:59:28         46 robots.txt
2017-02-26 20:59:30       1051 secret-dd02c7c.html
```
Upon visiting the secret file, I obtained a link to progress to level 2.
```
http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
```

## Level 2
**_Description:_** The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the free tier.

Level 2 was pretty straight forward and the link to level 3 was obtained by using the same command as level 1, but with a different bucket name:

```sh
$ aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/
2017-02-26 21:02:15      80751 everyone.png
2017-03-02 22:47:17       1433 hint1.html
2017-02-26 21:04:39       1035 hint2.html
2017-02-26 21:02:14       2786 index.html
2017-02-26 21:02:14         26 robots.txt
2017-02-26 21:02:15       1051 secret-e4443fc.html
```
Link to level 3:
```
http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
```

One thing I learned from this level was that the bucket in the level 1 was publicly accessible. Since I had already configured an account in my AWS CLI, the command I used worked fine. However, if no
account was configured (person with no AWS credentials), the ```--no-sign-request``` parameter would have been needed to list the bucket's content. 

## Level 3 
**_Description:_** The next level is fairly similar, with a slight twist. Time to find your first AWS key! I bet you'll find something that will let you list what other buckets are.

Just like the previous levels, I start off by listing the contents of the current bucket:
```sh
 aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/
                           PRE .git/
2017-02-26 19:14:33     123637 authenticated_users.png
2017-02-26 19:14:34       1552 hint1.html
2017-02-26 19:14:34       1426 hint2.html
2017-02-26 19:14:35       1247 hint3.html
2017-02-26 19:14:33       1035 hint4.html
2020-05-22 14:21:10       1861 index.html
2017-02-26 19:14:33         26 robots.txt
```


