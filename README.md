# Universal Forensic Notes

This repo has a collection of quick notes and scripts to be used
on most Linux systems to collect and process forensic data

## Adhoc Commands

```
read -rp "Enter Output Name: " OUTPUT
read -rp "Enter Input Name: " INPUT

cat ${OUTPUT} | \
  tee \
  >(md5sum > ${OUTPUT}.md5sum) \
  >(sha256sum > ${OUTPUT}.sha256sum) \
  | sha1sum > ${OUTPUT}.sha1sum

# make squashfs w/ image
mksquashfs dump ${OUTPUT}.squashfs -all-root -noappend

# make squashfs w/ dd
mksquashfs dump ${OUTPUT}.squashfs -all-root -noappend -p "${OUTPUT} f 400 root root dd if=${DEVICE} bs=1M"

mount ${OUTPUT}.squashfs /mnt/tmp
```
