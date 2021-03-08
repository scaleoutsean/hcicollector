# Docker container for sfcollector
# 
FROM python:3.9.2-slim-buster
LABEL maintainer="@scaleoutSean"
RUN python -m pip install --upgrade pip
RUN pip install --no-cache-dir solidfire-sdk-python==1.7.0.152 requests graphyte==1.6.0
COPY wrapper.sh /wrapper.sh
COPY solidfire_graphite_collector.py /solidfire_graphite_collector.py
CMD [ "/bin/sh", "/wrapper.sh" ]
