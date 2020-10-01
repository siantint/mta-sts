# Activate MTA-STS and host policy file with AWS

Table of Contents
* [DNS](#dns)
* [Host policy file over HTTPS](#policy)
   * [Create SSL certificate](#policy-ssl)



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
