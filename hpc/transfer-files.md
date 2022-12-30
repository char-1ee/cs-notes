# Transfer files

### Download files from the Internet with `wget` and `curl`&#x20;

```bash
$ curl -O https://char-1ee.github.io/files/intro.tar.gz
$ wget https://char-1ee.github.io/files/intro.tar.gz
```

### Transfer single file and folders with `scp`

```bash
# Upload to another computer (Note : is relative to home dir)
$ scp path/to/local/file.txt xli072@nscc.ntu.sg:/path/on/Graham

# Download from another computer
$ scp xli072@nscc.ntu.sg:/path/on/Graham/file.txt path/to/local/

# To copy a whole directory, use -r, for recursive
4 scp -r path/to/local/file.txt xli072@nscc.ntu.sg:/path/on/Graham
```

Due to HPC cluster firewall, use `curl` or `wget` to download files to local machine, and then upload the files to HPC cluster with secure copy `scp`.

### Alternatively, transfer with `rsync`

The [rsync](https://rsync.samba.org/) utility provides advanced features for file transfer and is typically faster compared to both `scp` and `sftp` (see below). It is especially useful for transferring large and/or many files and creating synced backup folders.

```bash
# Upload files to remote
$ rsync -azvP path/to/local/file.txt xli072@nscc.ntu.sg:/path/on/Graham

# Download files from remote
$ rsync -avzP xli072@nscc.ntu.sg:path/on/Graham/file.txt path/to/local/

# Set port 
$ rsync --port=768 test.txt xli072@nscc.ntu.sg:
```

The options are:

* `a` (archive) to preserve file timestamps and permissions among other things (recursive)
* `v` (verbose) to get verbose output to help monitor the transfer
* `z` (compression) to compress the file during transit to reduce size and transfer time
* `P` (partial/progress) to preserve partially transferred files in case of an interruption and also displays the progress of the transfer.

The good practice to transfer folder with large amount of files:

```bash
$ tar -cvzf data.tar.gz data
$ rsync -raz data.tar.gz yourUsername@graham.computecanada.ca:~/
```

The `tar` combine all files into a single file, and use `-z` to compress them. The `rsync` recursively transfer files with compression `-z` and preservation `-a`.

### Archiving files

```bash
# Show tar contents
$ tar -tf hpc-data.tar.gz

# Extract files
$ tar -xvzf hpc-data.tar.gz

# Compress files
$ tar -cvzf hpc-data.tar.gz hpc-data
```

The options are:

* `-x` to e**x**tract the archive
* `-c` to compress the to-archive
* `-v` for **v**erbose output
* `-z` for g**z**ip compression
* `-f` for the file to be unpacked

Note `.tar.gz` and `.tar.xz` stands for different compression algorithms used for `tar`.&#x20;

Note convert Windows files to Unix encoding with `dos2unix` and reversely with `unix2dos`.
