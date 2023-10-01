FROM karkaratz/spark-3.5:spark-base

COPY start-worker.sh /

ENV SPARK_WORKER_WEBUI_PORT 8081
ENV SPARK_WORKER_LOG /spark/logs
ENV SPARK_MASTER "spark://spark-master:7077"

EXPOSE 8081 4040

#RUN cd /usr/bin && ln -s python3.5 python
