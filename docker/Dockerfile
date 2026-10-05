FROM quay.io/jupyter/minimal-notebook:notebook-7.4.2

USER root

RUN ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone

RUN apt update && \
    apt install -y openjdk-21-jdk maven plotutils graphviz ghostscript vim unzip git-lfs

USER jovyan

RUN git clone https://github.com/YorrickJosuttis/KiekerTraceAnalysisPlantumlDemo.git --branch py4j --depth 1 && \
    git clone https://github.com/toruseo/UXsim.git --branch v1.7.3 --depth 1 && \
    cd KiekerTraceAnalysisPlantumlDemo && \
    git lfs install && \
    git lfs pull && \
    cd collector && \
    mvn clean package -DskipTests=true

RUN sed -i 's/PyQt5>=5.15.7/PyQt6/g' ~/UXsim/pyproject.toml

RUN ln -s /home/jovyan/KiekerTraceAnalysisPlantumlDemo/notebooks/README.ipynb /home/jovyan/README.ipynb

RUN ln -s /home/jovyan/KiekerTraceAnalysisPlantumlDemo /home/jovyan/PlantumlDemo

RUN mkdir ~/.ssh ~/kieker-logs

RUN sed -i 's@/tmp@'"${PWD}"'/kieker-logs@g' ~/KiekerTraceAnalysisPlantumlDemo/res/config.txt

RUN echo 'export PYTHONPATH=/home/jovyan/KiekerTraceAnalysisPlantumlDemo/python:$PYTHONPATH' >> /home/jovyan/.bashrc

RUN cd ~/KiekerTraceAnalysisPlantumlDemo && unzip ./tools/trace-analysis-2.0.3-py4j.zip

RUN cd ~/KiekerTraceAnalysisPlantumlDemo && git pull

RUN pip install -r ~/PlantumlDemo/res/requirements.txt

RUN pip install build

RUN pip install pypdf

RUN pip install graphviz

RUN pip install kieker-for-jupyter

RUN pip install py4j