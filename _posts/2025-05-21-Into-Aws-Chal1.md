---
layout: post
title: Enumeration using Pacu
date: 2025-05-21
categories: [Cloud, Aws]
tags: [Enum, Pacu, cybr]
---
# Introduction

This is a Writup for challange lab from wonderful AWS Pentesting course by Tyler Ramsbay. I Highly Recommand you to take this course.

Course Link - [Click Here]{https://academy.simplycyber.io/l/pdp/introduction-to-aws-pentesting}

## What is Pacu ?
- Pacu is an open-source AWS exploitation framework used for cloud penetration testing.
- It helps find weaknesses, like misconfigured permissions or exposed data. 
- Think of it as a toolkit for checking how secure a cloud setup is.

Github repo link to download Pacu - [Click Here](https://github.com/RhinoSecurityLabs/pacu)

## Configuring AWS Creds 

```bash
aws configure --profile cybr

AWS Access Key ID [****************U2PR]: <Enter-The-Access-Key>
AWS Secret Access Key [****************It/y]: <Enter-The-Secret-Key>
Default region name [None]: <Enter-The-Region>
Default output format [json]: <Enter-The-Output-Format>

# You can name anything instead of cybr name doesn't matter
```

## Creating a new session 

- If you are stating pacu for the first time it will ask you to enter a session name.
![session](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/session.png)

## Importing Keys into Pacu

- We have configured the keys using the `aws configure` command. We have to configure pacu to run command on the profile we have created here it is 'cybr'.
- By importing the keys we can run commands on the 'cybr' profile.

```
Pacu (Into-to-AWS:) import_keys <profile_name>
Pacu (Into-to-AWS:) import_keys cybr
```

![import-key](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/import-key.png)

## To search for a module

- If you have used metasploit it is similar to search option.
- We can search through different modules.
```
Pacu (Into-to-AWS:imported-cybr) search <Name_of_The_Module>
Pacu (Into-to-AWS:imported-cybr) search iam
```
![search-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/search-module.png)

## Found a Modules used for Enumerating users, roles, policies and groups

- This module is used to Enumerate users, roles, policies and groups.
![Found-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/Found-module.png)

## To run a Module
```
Pacu (Into-to-AWS:imported-cybr) run <Name_of_the_Module>
Pacu (Into-to-AWS:imported-cybr) run iam__enum_users_roles_policies_groups
```

![Run-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/run-module.png)

## Pacu Database
- Pacu Database is where all the output of the command will be stored.
- We can filter a specific catagory like IAM To display only that

```
Pacu (Into-to-AWS:imported-cybr) data IAM
```
![database](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/Database.png)

# Conclusion
- Simple walkthrough about Enumerating using Pacu. 
- Pacu make the enumeration process so easy and Fast if we have to enumerate these thing manually it would probally take 10 minutes but using pacu it took me just a minute.