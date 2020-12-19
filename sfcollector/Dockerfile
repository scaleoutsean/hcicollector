# Docker container for hcicollector
# 
FROM python:3.8.6-slim-buster
LABEL maintainer="@scaleoutSean"
RUN pip install --no-cache-dir solidfire-sdk-python==1.7.0.152 requests graphyte==1.6.0
RUN apt-get install sed -y
COPY wrapper.sh /wrapper.sh
COPY solidfire_graphite_collector.py /solidfire_graphite_collector.py
CMD [ "/bin/sh", "/wrapper.sh" ]
