# Lab 4

Instructions for this section will be provided in class and on Blackboard when we reach it.

Put your work for Lab 4 in this folder.
real    0m6.511s
user    0m0.134s
sys     0m0.055s
time was 6.4s


root


.
..
Dockerfile.naive
README.md
app.py
evidence
requirements.txt
tests



TAG                  SIZE
3.12-slim-bookworm   124MB


NEW

real    0m6.511s
user    0m0.134s
sys     0m0.055s
time was 0.5s
# Lab 4: Image comparison

| Image | Base image | Size | Rebuild time (one-line code change) |
|-------|-----------|------|--------------------------------------|
| acs730-lab4:naive | python:latest | 1.15GB | 6.4s |
| acs730-lab4:slim  | python:3.12-slim-bookworm | 130MB | 0.5 |

Base Image is 1GB in difference between this is because naive uses python:latest which downloads
everything including the debian and compilers, but slim skips all of that So the difference is
almost all of it

Docker builds in layrs, so when trying to rebuild it will reuse a layer whos inputs hae not changed
hence the "cached" layer. Slim copies requirements.txt and installs the package, THEN it copies
app.py. Therefore when editing app.py only the final step is returned. hence the time difference.
Naive however copies everything before the pip install. It edits app.py and changes the copy so 
the install has to reinstall everything again, hence the significantly longer time of 6.4s

.dockerignore lists the files Docker should not send to the build. The bullet names the files that 
are to be skipped.
