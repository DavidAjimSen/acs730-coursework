# Lab 5

Instructions for this section will be provided in class and on Blackboard when we reach it.

Put your work for Lab 5 in this folder.
## Why for_each here and count there

Subnets use "for_each" because they are distinct things with stable names. 
The keys in "var.public_subnets" ("a", "b") become part of the resource address, 
e.g. "aws_subnet.public["a"]", and Terraform compares addresses between runs. 
Adding key "c" gives a plan of 1 to add, 0 to change, 0 to destroy. 
Removing "a" gives 1 to destroy, and "b" is left alone, because its address did not change.

The same subnets with "count" would be a trap. Addresses would be 
"public[0]", "public[1]", "public[2]". If I deleted the middle entry, 
everything after it would shift down a slot. 
Terraform would compare "public[1]" in state against a different subnet in the config and plan to 
destroy and recreate two subnets just to remove one. An index is a position, not an identity.

The artifact bucket uses "count" because the input is a yes/no about a single resource. 
There are exactly two possible plans, "0 to add" and "1 to add", and there is no key to keep stable.
 The output uses "one(aws_s3_bucket.artifacts[*].bucket)" so it returns null when the bucket is off,
 instead of failing with an index error like "artifacts[0]" would.

Use "count" when the question is 'how many', and "for_eac"` when the question is 'which ones?'.
