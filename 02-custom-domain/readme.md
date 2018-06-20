> Work in progress

<p align="center">
  <a href="https://github.com/AurelienLourot/reframe-on-up">
    <img src="https://rawgit.com/AurelienLourot/reframe-on-up/master/readme_assets/reframe-on-up.jpg"
         width="200" height="96">
  </a>
</p>
<p align="center">
  <b>Reframe on Up</b>
</p>
<p align="center">
  How I set up a custom domain for my Reframe web app
</p>
<p align="right">
  <a href="https://github.com/AurelienLourot">AurelienLourot</a> - 17 Jun 2018
</p>

⇐ [Part I: How I deployed for free a Reframe web app on Up in less than an hour](../01-deploy/)

In the first part of this [series](../) we have

* created a [Reframe](https://github.com/reframejs/reframe) web app,
* set up our AWS account,
* set up our AWS CLI client,
* set up our [Up](https://up.docs.apex.sh/) client, and
* deployed our app on [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) via
  Up.

In this part we will now set up a
[custom domain](https://up.docs.apex.sh/#configuration.stages_custom_domains) on top of it.

# Step-by-step guide

## Buying a domain name

1. Buy a domain name on AWS ([ghuser.io](https://ghuser.io) in this example):

```bash
$ export CONTACT="FirstName=My,LastName=User,ContactType=PERSON,OrganizationName=,\
  AddressLine1=42 My Street,AddressLine2=,City=Berlin,State=,CountryCode=DE,ZipCode=12345,\
  PhoneNumber=+49.1234567890,Email=myuser@gmail.com,Fax=,ExtraParams=[]"
$ aws route53domains register-domain --domain-name ghuser.io --duration-in-years 1 \
  --auto-renew --admin-contact "$CONTACT" --registrant-contact "$CONTACT" \
  --tech-contact "$CONTACT" --privacy-protect-admin-contact --privacy-protect-registrant-contact
```

> **NOTE**: We're not using `--privacy-protect-tech-contact`, i.e. we're not making the technical
> contact details secret on [WHOIS](https://en.wikipedia.org/wiki/WHOIS), which will be useful in a
> later step.

## Setting up Up

2. Tell Up to deploy on your custom domain by adding this to your [up.json](up.json):

```json
{
  "...": "...",
  "stages": {
    "staging": {
      "domain": "myapp.ghuser.io"
    }
  }
}
```

> **NOTE**: [It doesn't have to be a sub-domain](https://up.docs.apex.sh/#guides.development_to_production_workflow.mapping_custom_domains_to_stages)
> like `myapp.ghuser.io`. `ghuser.io` is also fine.
