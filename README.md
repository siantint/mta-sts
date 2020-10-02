# Activate MTA-STS and host policy file with AWS

Table of Contents
* [DNS](#dns)
* [Host policy file over HTTPS](#policy)
   * [Create SSL certificate](#policy-ssl)
   * [Upload policy file](#policy-upload)



# <a name='dns' />DNS

Create DNS TXT records listed in [dns.txt](./dns.txt)



# <a name='policy' />Host policy file over HTTPS

## <a name='policy-ssl' />Create SSL certificate

```
> timestamp=$(date +%s%3N)
> certarn=$(aws acm --region=us-east-1 request-certificate \
    --domain-name=mta-sts.siant.com.sg \
    --validation-method=DNS \
    --subject-alternative-names=mta-sts.benkei.com.sg \
    --subject-alternative-names=mta-sts.silicon.com.sg \
    --idempotency-token=$timestamp)

{ CertificateArn: arn:aws:acm:ap-southeast-1:693815924520:certificate/f664cd33-8725-4c4a-a8ac-9be845437c6f }

> certarn=$(set -- junk $certarn && shift && echo $3 | tr -d '"')
```


## <a name='policy-upload' />Upload policy file

1. Create bucket and folder structure, and upload text file

```
> s3bucket=mta-sts.siant.com.sg
> aws s3 mb s3://$s3bucket/.well-known
> aws s3 cp ./mta-sts.txt s3://$s3bucket/.well-known/
```

1. Restrict all public access from S3, so that CloudFront distribution is only
access point

```
> aws s3api put-public-access-block \
    --bucket $s3bucket
    --public-access-block-configuration \
    "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```
