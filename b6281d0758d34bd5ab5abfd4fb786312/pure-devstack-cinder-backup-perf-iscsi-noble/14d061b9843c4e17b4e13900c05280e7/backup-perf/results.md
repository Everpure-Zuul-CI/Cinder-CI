# cinder-backup chunked driver measurement

cinder 8313581b64 on Intel(R) Xeon(R) Gold 6326 CPU @ 2.90GHz x8, kernel 6.8.0-139-generic, python 3.12.3, zstd module 1.5.7.2, boto3 1.43.97.
S3 chunk 52428800 (default) bytes, sha block 32768 (default) bytes; S3 target is the lab FlashBlade over the network, Swift (if run) is on the same node.
volume 10 GiB; image 4 GiB = 1236 zero / 1643 text / 1217 random MiB blocks; the rest of the volume is zeros.

| target | mode | algorithm | wall s | MiB/s | chunks | ratio | read+sha+refresh med s | compress med s | upload+md5 med s | loop share r/c/u % | cpu us/sy/id % |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|---|
| s3 | threading | zlib |  |  |  |  |  |  |  | // | // | FAILED: backup create kept failing right after submission
| s3 | threading | zstd |  |  |  |  |  |  |  | // | // | FAILED: backup create kept failing right after submission
| s3 | threading | none |  |  |  |  |  |  |  | // | // | FAILED: backup create kept failing right after submission

Stage medians are per chunk.  "loop share" is the fraction of the chunk loop spent reading+hashing, compressing and uploading; the driver does these strictly one after another, so their sum is the loop.
