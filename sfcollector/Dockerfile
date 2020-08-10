# Docker container for hcicollector
# 
FROM python:3.8.5-slim-buster
LABEL maintainer="@scaleoutSean"
RUN pip install --no-cache-dir solidfire-sdk-python==1.5.0.87.post1 requests graphyte==1.6.0
RUN apt-get install sed -y
RUN sed -i 's/raise ValueError(self._member_name+/#raise ValueError(self._member_name+/g' /usr/local/lib/python3.8/site-packages/solidfire/common/model.py
RUN sed -i 's/#out\[self._member_name\] = None/out\[self._member_name\] = None/' /usr/local/lib/python3.8/site-packages/solidfire/common/model.py
COPY wrapper.sh /wrapper.sh
COPY solidfire_graphite_collector.py /solidfire_graphite_collector.py
CMD [ "/bin/sh", "/wrapper.sh" ]
