# Cloud Computing Project
**DRAFT**

<!--## Group members-->

## Objective
We are writing a lightweight cloud storage deduplication program. Technical issues like scalability, deduplication granularity, the usefulness of deduplication, and security will be addressed.

DynamoDB is used to store the key-value pairs of `<block id; binary content>`, `<file id; user id, file path, file sequence, metadata>`, `<user id; hash, salt, first name, last name>`

The front end is planned to be written in Flutter, so that it can be used on mobile devices.

The master EC2 node will hold the Flask REST endpoints.

Amazon EC2 instances are created on demand using the Boto3 library, depending on the input data size.

## Test cases

Some large files (~ 10 GB) will be prepared.

## 0. Ideas
Deduplication is a trade-off between user's storage size and file reassembly time.
Points to consider:
* What the optimal block size is (a.k.a. granularity)
* How much latency is needed to reassemble the files
* How to store the blocks in the database without security implications
### a. On-demand deduplication
Elasticity in Cloud Computing. Deduplicate when the user's storage is nearly full, exchange for longer reassembly time. In another scenario: small local cloud storage providers that do not have large data centres. They can save the cost to buy new hardware.
### b. Distributed Data Deduplication

## 1. Algorithms
* a. Rabin fingerprinting

The basic idea is that the filesystem computes the cryptographic hash of each block in a file. To save on transfers between the client and server, they compare their checksums and only transfer blocks whose checksums differ. But one problem with this scheme is that a single insertion at the beginning of the file will cause every checksum to change if fixed-sized (e.g. 4 KB) blocks are used. So the idea is to select blocks not based on a specific offset but rather by some property of the block contents. LBFS does this by sliding a 48 byte window over the file and computing the Rabin fingerprint of each window. When the low 13 bits of the fingerprint are zero LBFS calls those 48 bytes a breakpoint and ends the current block and begins a new one. Since the output of Rabin fingerprints are pseudo-random the probability of any given 48 bytes being a breakpoint is {\displaystyle 2^{-13}}2^{{-13}} (1 in 8192). This has the effect of shift-resistant variable size blocks. Any hash function could be used to divide a long file into blocks (as long as a cryptographic hash function is then used to find the checksum of each block): but the Rabin fingerprint is an efficient rolling hash, since the computation of the Rabin fingerprint of region B can reuse some of the computation of the Rabin fingerprint of region A when regions A and B overlap.

Note that this is a problem similar to that faced by rsync.
* b. AES

## 2. Case Studies
* Dropbox Security Breach due to loophole in Deduplication algorithm:

  - [https://github.com/driverdan/dropship](https://github.com/driverdan/dropship)
  - [https://blog.fosketts.net/2011/07/11/dropbox-data-format-deduplication/](https://blog.fosketts.net/2011/07/11/dropbox-data-format-deduplication/)
  - [https://news.ycombinator.com/item?id=2478567](https://news.ycombinator.com/item?id=2478567)
The algorithm must be designed with special care to avoid birthday attacks:
https://en.wikipedia.org/wiki/Birthday_attack

* StorReduce on Amazon

## References

<!-- Grid computing: needs to stop other instances once the solution is found. -->

![project_pipeline.png](project_pipeline.png)
