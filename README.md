# Universal Forensic Notes

This repo has a collection of quick notes and scripts to be used
on most Linux systems to collect and process forensic data

## Adhoc Commands

```
read -rp "Enter Input Name: " INPUT
read -rp "Enter Output Name: " OUTPUT

cat ${OUTPUT} | \
  tee \
  >(md5sum > ${OUTPUT}.md5sum) \
  >(sha256sum > ${OUTPUT}.sha256sum) \
  | sha1sum > ${OUTPUT}.sha1sum

# make squashfs w/ image
mksquashfs dump ${OUTPUT}.squashfs -all-root -noappend

# make squashfs w/ dd
mksquashfs dump ${OUTPUT}.squashfs \
  -all-root \
  -noappend \
  -p "${OUTPUT}.raw f 400 root root dd bs=4096 conv=noerror,sync if=${INPUT}"

# convert gzip image to squashfs
mksquashfs dump ${OUTPUT}.squashfs \
  -all-root \
  -noappend \
  -p "${OUTPUT}.raw f 400 root root gunzip -c ${INPUT}"


mount ${OUTPUT}.squashfs /mnt/tmp
```

## Links

- https://www.zeitgeist.se/2017/09/01/linux-way-to-disable-the-virtual-cd-on-wd-disks/