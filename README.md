# Universal Forensic Notes

This repo has a collection of quick notes and scripts to be used
on most Linux systems to collect and process forensic data

## Adhoc Commands

```
# create scratch dir
mkdir scratch
cd scratch

# define input / output
read -rp "Enter Input Name: " INPUT
read -rp "Enter Output Name: " OUTPUT

# create verify hashes
FILENAME=$(basename ${INPUT})
dd bs=4096 conv=noerror,sync if=${INPUT} | \
  tee \
  >(md5sum > ${FILENAME}.md5sum) \
  >(sha256sum > ${FILENAME}.sha256sum) \
  >(sha1sum > ${FILENAME}.sha1sum) \
  | pv > ${FILENAME}

# create verify hashes
cat ${OUTPUT} | \
  tee \
  >(md5sum > ${OUTPUT}.md5sum) \
  >(sha256sum > ${OUTPUT}.sha256sum) \
  | sha1sum > ${OUTPUT}.sha1sum

# make squashfs directly from dd
mksquashfs scratch ${OUTPUT}.squashfs \
  -all-root \
  -noappend \
  -p "${OUTPUT}.raw f 400 root root dd bs=4096 conv=noerror,sync if=${INPUT}"

# make squashfs from scratch folder
mksquashfs scratch ${OUTPUT}.squashfs -all-root -noappend

# convert gzip image to squashfs
mksquashfs scratch ${OUTPUT}.squashfs \
  -all-root \
  -noappend \
  -p "${OUTPUT}.raw f 400 root root gunzip -c ${INPUT}"

# mount squashfs
mount ${OUTPUT}.squashfs /mnt/tmp
```

Example

```
mkdir scratch

OUTPUT=backup

for PART in /dev/sda?
do
  mksquashfs scratch ${OUTPUT}.squashfs \
    -comp zstd \
    -no-xattrs \
    -all-root \
    -p "$(basename ${PART}).raw f 400 root root sudo dd bs=4096 conv=noerror,sync if=${PART}"
done

for PART in /dev/sda?
do
  sudo dd bs=4096 conv=noerror,sync if=${PART} | \
  tee \
  >(md5sum > scratch/$(basename ${PART}).md5sum) \
  >(sha256sum > scratch/$(basename ${PART}).sha256sum) \
  | sha1sum > scratch/$(basename ${PART}).sha1sum
done

sudo smartctl -x /dev/sda > scratch/hdinfo.txt
sudo sfdisk -d /dev/sda > scratch/sfdisk.txt

mksquashfs scratch ${OUTPUT}.squashfs \
  -no-xattrs \
  -all-root

```

## Links

- https://linuxreviews.org/Comparison_of_Compression_Algorithms
