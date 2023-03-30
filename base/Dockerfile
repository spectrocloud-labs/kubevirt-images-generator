FROM alpine:3.17.3

LABEL maintainer="Spectro Cloud <help@spectrocloud.com>"

ARG OS_NAME="example_os"
ARG OS_VERSION="0.0"
ARG IMAGE_URL="https://example.com/os_xxx.img"
ARG FILE_NAME="os_xxx.img"
ARG IMAGE_NAME="os_xxx"
ARG IMAGE_EXTENSION="img"

RUN apk update && apk add curl cloud-init cloud-utils

RUN mkdir /disk && curl -kfSL $IMAGE_URL -o /disk/$FILE_NAME

RUN KEY_LOOP="true"; \
while [ $KEY_LOOP == "true" ]; do \
	echo "Process extension: $IMAGE_EXTENSION"; \
	if [ "$IMAGE_EXTENSION" == "qcow2" ] ; then \
		mv /disk/$FILE_NAME /disk/$OS_NAME.qcow2; \
		KEY_LOOP="false"; \
	elif [ "$IMAGE_EXTENSION" == "vmdk" ] ; then \
		qemu-img convert -f vmdk -O raw /disk/$FILE_NAME /disk/$OS_NAME.img; \
		rm -rf /disk/$FILE_NAME; \
		KEY_LOOP="false"; \
	elif [ "$IMAGE_EXTENSION" == "bz2" ] ; then \
		bunzip2 /disk/$FILE_NAME; \
		rm -rf /disk/$FILE_NAME; \
		export FILE_NAME=$(ls -l /disk | grep "^-" | awk '{ print $9 }' | grep -v "$FILE_NAME"); \
		echo "New file name: $FILE_NAME"; \
		export IMAGE_EXTENSION=$(echo $FILE_NAME | cut -d"." -f2); \
	elif [ "$IMAGE_EXTENSION" == "img" ] ; then \
		if [ ! -f /disk/$OS_NAME.img ]; then \
			mv /disk/$FILE_NAME /disk/$OS_NAME.img; \
			ls /disk/; \
		fi ;\
		KEY_LOOP="false"; \
	else \
		KEY_LOOP="false"; \
	fi \
done