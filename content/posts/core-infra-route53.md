---
title: "Managing Route 53 Records with Terraform and GitHub Actions"
summary: "Why learn to use Squarespace when I can write custom IaC solutions instead?"
date: 2023-06-21
draft: false
toc: false
tags:
  - AWS
  - Terraform
  - GitHub Actions
---
My domain (mleone.dev) is now using AWS's name servers, so future development efforts shouldn't need to touch Squarespace.  I took the opportunity to spin up some basic IaC in a new repo, [core-infra](https://github.com/mleone10/core-infra).  This repo will host any common infrastructure going forward, such as domains and reusable security groups.

In setting up that repo's automation (so that pushes to the main branch automatically deploy to AWS), I experimented with using GitHub as an IAM identity provider.  GitHub's own [documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) is pretty thorough in that regard, though I had some trouble with the role's trust policy.  Now, though, I don't need to use an IAM user for GitHub Action authentication.

I also switched my homelab over to use Route 53 for Dynamic DNS.  Alistair Mackay's [instructions](https://github.com/fireflycons/pfSense-aws-DynamicDNS) for doing so were helpful, though I just configured a new IAM policy manually instead of using his CFT.
