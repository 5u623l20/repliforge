# repliforge
A script to check reproducibility issues with FreeBSD `src` tree

This script is capable of using a local compressed/uncompressed image. Or a
remote compressed/uncompressed image.

At the end the script generates 3 files in the `$HOME/repliforge-results`. A
sample example is like following:
```
❯ ll repliforge-results
total 4926
-rw-------  1 root wheel uarch  3.8M Aug 19 05:24 2024-08-19-amd64-amd64-10e31f0946d8-image.sha256
-rw-------  1 root wheel uarch  3.8M Aug 19 05:24 2024-08-19-amd64-amd64-10e31f0946d8-obj.sha256
-rw-r--r--  1 root wheel uarch  1.3M Aug 19 05:24 2024-08-19-amd64-amd64-10e31f0946d8.diff
```
The `diff` file is the file which mentions which files were unable to reproduce
with the same hash.


## Running the script
```
# git clone https://github.com/5u623l20/repliforge.git
# cd repliforge
# chmod +x repliforge
# ./repliforge -t local -i <LOCAL IMAGE NAME> -a <OPTIONAL ARCHITECTURE> -p
<OPTIONAL PLATFORM> -b <OPTIONAL GIT BRANCH> -h <OPTIONAL GIT HASH>
# ./repliforge -t remote -i <REMOTE URL of IMAGE> -a <OPTIONAL ARCHITECTURE> -p
<OPTIONAL PLATFORM> -b <OPTIONAL GIT BRANCH> -h <OPTIONAL GIT HASH>
# ./repliforge -t remote -i
https://download.freebsd.org/releases/VM-IMAGES/14.1-RELEASE/amd64/Latest/FreeBSD-14.1-RELEASE-amd64-ufs.raw.xz
```
## Limitations
There are couple of limitations of the script:
- It can work only with RAW type images
- It tries to find the UNAME string from the kernel and based on that determine
    the GIT BRANCH and GIT HASH, however it's not always the case that the
    kernel file will have these strings and in those case the script will fail
    if GIT BRANCH and GIT HASH is not provided as arguments.
- At the moment it does not create a fresh jail to build the build however
    without this in most cases the results will be ambiguous. As re@ creates a
    fresh jail of the GIT HASH and uses that jail to build the final builds so
    there might be environment pollution or even compiler version discrepancy.
- There are some relevant codes related to downloading AWS images and also test
    them. However AWS has recently removed the feature `get-image` which was in
    previous used to download AMI. However there are lots of complicacy in
    downloading the image in a straightforward way. Attempts has been made to
    create an EC2 instance first. From their creating an AMI and exporting that
    image to S3. However at the end the export process fails with the
    following:
    ```
    ClientError: Unsupported: Incomplete root disk. No /etc directory found
    ```
    which I have no clue about.
