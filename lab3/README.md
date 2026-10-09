# Lab 3

Instructions for this section will be provided in class and on Blackboard when we reach it.

Put your work for Lab 3 in this folder.

Terraform v1.16.4 (installed a newer version from last week)

error has been fixed (the bracket was not closed)

cloud accounts use OIDC to establish a trust relationship without having to embed any long
living credentials such as API keys. This removes the issue of security risks, 
and rotating through said API keys

The reason we use session-scoped secrets in this course is for because it is easily repeatable
for students, and it has tight permissions and auto expires
