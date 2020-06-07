# Docker container for hcicollector
# 
FROM alpine:3.12.0
LABEL maintainer="@scaleoutSean"
RUN apk update && apk add py-pip git bash 
RUN pip install solidfire-sdk-python==1.5.0.87.post1
RUN pip install requests graphyte
ADD solidfire_graphite_collector.py /solidfire_graphite_collector.py
ADD wrapper.sh /wrapper.sh
ENTRYPOINT /wrapper.sh
