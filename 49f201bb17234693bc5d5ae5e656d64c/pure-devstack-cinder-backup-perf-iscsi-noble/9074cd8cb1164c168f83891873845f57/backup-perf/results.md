# cinder-backup chunked driver measurement

cinder 8313581b64 on Intel(R) Xeon(R) Gold 6326 CPU @ 2.90GHz x8, kernel 6.8.0-139-generic, python 3.12.3, zstd module 1.5.7.2, boto3 1.43.97.
S3 chunk 52428800 (default) bytes, sha block 32768 (default) bytes; S3 target is the lab FlashBlade over the network, Swift (if run) is on the same node.
volume 10 GiB; image 4 GiB = 1236 zero / 1643 text / 1217 random MiB blocks; the rest of the volume is zeros.

| target | mode | algorithm | wall s | MiB/s | chunks | ratio | read+sha+refresh med s | compress med s | upload+md5 med s | loop share r/c/u % | cpu us/sy/id % |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|---|
| s3 | threading | zlib | 167.7 | 61.1 | 205 | 5.77 | 0.098 | 0.223 | 0.083 | 12.7/68.0/19.2 | 13.5/2.0/84.3 |
| s3 | threading | zstd | 71.6 | 143.0 | 205 | 6.46 | 0.096 | 0.026 | 0.082 | 32.1/21.1/46.8 | 10.9/4.6/84.1 |
| s3 | threading | none | 129.4 | 79.1 | 205 | None | 0.105 | 0.0 | 0.47 | 18.3/0.0/81.7 | 8.8/6.2/84.7 |

Stage medians are per chunk.  "loop share" is the fraction of the chunk loop spent reading+hashing, compressing and uploading; the driver does these strictly one after another, so their sum is the loop.
