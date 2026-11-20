# Lab 6

Instructions for this section will be provided in class and on Blackboard when we reach it.

Put your work for Lab 6 in this folder.




Runner-to-instance  mechanism: 

I used AWS Systems Manager Session Manager, because the instances have no port 22 ingress at all, 
the runner connects with the same temporary session credentials it already has, therefore there is no long-lived private key in the repository secrets, 
and no 0.0.0.0/0 SSH rule to let a runner with a changing address in.



What changed=0 on a second run proves

It proves that every task inspected the real state of the host and found it already matched what the role askks for, not that the playbook did nothing, 
which is why the same command is safe on a brand-new instance, on an already converged one, and from a pipeline on every merge


Why registering an AMI is not enough

An AMI is only a pointer, while the EBS snapshot behin it holds the data and is whathkeeps billing, so degregistering leaves the snapshot behind and, once the AMI is gone, 
describe-images can no longer tell you which snapshot belonged to it. I recorded the snapshot id first and deleted it explicitly



##Design notes

- Terraform builds two tagged instances (Lab=lab6, Role=web) with LabInstanceProfile attached and no port 22 rule.

- Ansible finds them with the amazon.aws.aws_ec2 dynamic inventory, filtered on the Lab tag and the running state, so no instance id or IP is written down anywhere in the repository.

- The aws_ssm connection stages file transfers through an S3 bucket. The bucket and ansible_aws_ssm_region must be in the same region (us-east-1 here), otherwise the first task fails with PermanentRedirect.

- The configuration is a role (roles/webserver) with defaults, a handler for restarts, and two Jinja2 templates. State is `present` rather than `latest`, and mode values are quoted, to keep runs idempotent.

- The converge runs in the workflow step immediately after `terraform apply`, with a retry loop that waits for the SSM agent to register.

## Boot-to-ready comparison

| Path | Seconds | What it includes |
|---|---|---|
| Converge on boot (stock AL2023 + Ansible) | 187s | instance launch, boot, SSM agent registration, dnf downloads, Ansible run, service start |
| Golden AMI (Packer-baked) | 74s | instance launch, boot, service start (software already installed) |




Where the time went in the slow path



What a golden AMI moves rather than removes

The installation work still happens, once at build time instead of once per launch, so the AMI wins as soon as you launch more than a couple of instances and loses for a single instance, 
because you paid for a full Packer build to save 113s seconds.



What breaks with golden AMIs

An image is a frozen moment, so updates released after the bake are not in it and you need a rebuild cadence, and you cannot see inside it without reading the build log, 
whereas a playbook is its own documentation.



What breaks with converge-on-boot

Every launch depends on package mirrors being reachable at that moment, so a mirror outage produces instances that boot and never become ready, 
and an autoscaling event at 3 a.m. is exactly when you do not want a network dependency in the boot path.



##Recommendation

I recommend Golden AMI, because we lauch instances in groups and wat them ready without depending on package mirrors, unless we onl ever launch one or two instances, 
in which case converge-on-boot avoids the cost of maintaining an image

