# cinder-backup chunked driver measurement

cinder 8313581b64 on Intel(R) Xeon(R) Gold 6326 CPU @ 2.90GHz x8, kernel 6.8.0-139-generic, python 3.12.3, zstd module 1.5.7.2, boto3 1.43.97.
S3 chunk 52428800 (default) bytes, sha block 32768 (default) bytes; S3 target is the lab FlashBlade over the network, Swift (if run) is on the same node.
volume 10 GiB; image 4 GiB = 1236 zero / 1643 text / 1217 random MiB blocks; the rest of the volume is zeros.

| target | mode | algorithm | wall s | MiB/s | chunks | ratio | read+sha+refresh med s | compress med s | upload+md5 med s | loop share r/c/u % | cpu us/sy/id % |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|---|
| s3 | threading | zlib | 167.6 | 61.1 | 205 | 5.77 | 0.096 | 0.223 | 0.083 | 12.4/68.3/19.3 | 13.3/2.1/84.5 |
| s3 | threading | zstd | 71.5 | 143.2 | 205 | 6.46 | 0.102 | 0.025 | 0.082 | 33.1/20.6/46.3 | 10.8/4.5/84.2 |
| s3 | threading | none | 122.4 | 83.7 | 205 | None | 0.097 | 0.0 | 0.461 | 17.4/0.0/82.6 | 8.9/6.3/84.5 |

Stage medians are per chunk.  "loop share" is the fraction of the chunk loop spent reading+hashing, compressing and uploading; the driver does these strictly one after another, so their sum is the loop.
