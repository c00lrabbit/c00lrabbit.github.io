---
layout: post
title: Automation using Pacu
date: 2025-05-21
categories: [Cloud, Aws]
tags: [Enum, Pacu, cybr]
---

### Use Pacu to Automate Enumeration

### Configuring AWS Creds 

```bash
aws configure --profile cybr 

# You can name anything instead of cybr name doesn't matter
```

## Creating a new session 

If you are stating pacu for the first time it will ask you to enter a session name
![session](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/session.png)

## Importing Keys into Pacu
```
import_keys <profile_name>
```

![import-key](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/import-key.png)

### To search for a module
```
search <Name_of_The_Module>
search iam
```
![search-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/search-module.png)

### Found a Modules used for Enumerating users, roles, policies and groups
![Found-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/Found-module.png)

### To run a Module
```
run <Name_of_the_Module>
run iam__enum_users_roles_policies_groups
```

![Run-module](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/run-module.png)

## Pacu Database to view the iam output
```
data IAM
```
![database](https://c00lrabbit.github.io/assets/Images/Aws-enum-chal1/Database.png)