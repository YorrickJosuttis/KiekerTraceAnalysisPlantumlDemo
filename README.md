# Kieker Trace Analysis PlantUML Pipeline Demo

## Table of Contents

1. Introduction
1. Prerequisites
   * Linux system
   * Software
1. Instructions
1. Run the Kieker Trace Analysis

## Introduction

[Kieker observability framework](https://kieker-monitoring.net/) features monitoring and analysis capabilities.
OpenTelemetry, in comparison, provides means to monitor the program but analysis. Our new [Otkt DSL](https://github.com/kieker-monitoring/OtktDSL) can define a mapping from an OpenTelemetry Span to a Kieker record.
Using Otkt, we show how we can use OpenTelemetry to collect monitoring data from a Python program and send it to a Kieker Analysis endpoint. Further, with the use of the Kieker Trace Analysis PlantUML pipeline implementation, we are capable of generating diagrams out of the monitored data. With the help of our library kieker-for-jupyter, we can display the diagrams directly within the Jupyter Notebook. In addition, both the java implementation of Trace Analysis and the python package implementation of this library support Py4J.

## Prerequisites

### Jupyter Notebook
* We encourage partiicpants to use the provided Jupyter Notebook docker, which runs on Ubuntu 24.04.

```bash
docker run -p 8888:8888 kieker/sustainkieker-hackathon
```

### Linux system
   
#### macOS
* [Multipass](https://github.com/canonical/multipass) ([latest download](https://canonical.com/multipass/download/macos))
  - [x] M1, M2, or Intel based
  - [x] macOS 10.15 Catalina or later

#### Windows 10/11 (two options)
* Multipass ([latest download](https://canonical.com/multipass/download/windows))
  - [x] Windows 10 Pro or Enterprise version 1803 (“April 2018 Update”) or later
* WSL Install Instructions
  * [Windows 10 2004 or higher](https://learn.microsoft.com/en-us/windows/wsl/install)
  * Old versions: [Windows 10 1903 or higher](https://learn.microsoft.com/en-us/windows/wsl/install-manual)

### Software
1. Python3

1. Java
   ```bash
   # Fedora 40, 41, 42
   sudo dnf install java-21-openjdk-devel
   # Ubuntu 20.04, 22.04, 24.04, 24.10
   sudo apt install openjdk-21-jdk
   ```
1. Maven
   ```bash
   # Fedora 40, 41, 42
   sudo dnf install maven
   # Ubuntu 20.04, 22.04, 24.04, 24.10
   sudo apt install maven
   ```
   or [install manually](https://maven.apache.org/install.html).

1. [GNU plotutils](http://www.gnu.org/software/plotutils/)
   ```bash
   # Fedora 40, 41, 42
   sudo dnf install plotutils
   # Ubuntu 20.04, 22.04, 24.04, 24.10
   sudo apt install plotutils
   ```
1. The [graphviz](http://www.graphviz.org/) graph visualization tools
   ```bash
   # Fedora 40, 41, 42
   sudo dnf install graphviz
   # Ubuntu 20.04, 22.04, 24.04, 24.10
   sudo apt install graphviz
   ```

1. The [Ghostscript](https://ghostscript.com/) interpreter for PostScript language & PDF
   ```bash
   # Fedora 40, 41, 42
   sudo dnf install ghostscript
   # Ubuntu 20.04, 22.04, 24.04, 24.10
   sudo apt install ghostscript
   ```

## Instructions

1. Build the Otkt collector.
   ```bash
   # Run inside the demo repository:
   cd collector
   mvn clean package
   ```

1. Export the `PYTHONPATH` variable with the parent path of the `otkt`
   directory.

   ```bash
   export PYTHONPATH=/path/to/demo-repo/python:$PYTHONPATH
   ```

1. Instrument the entry point with the following python import.

   ```python
   from otkt.otelinit import tracer
   ```

1. Instrument all python files of the target software using [`instrument-py.sh`](tools/instrument-py.sh)

   ```bash
   # Run inside the target software repository:
   /path/to/demo-repo/tools/instrument-py.sh
   ```

   * It is important to adhere to the indentation of each method definitions.

1. Install all required python module dependencies. A python project usually
   comes with `requirements.txt`. Append all dependencies in
   [res/requirements.txt](res/requirements.txt) to the existing (or new)
   `requirements.txt`.

   ```bash
   # Run inside the target software repository:
   pip install -r requirements.txt 
   ```

1. Run the Otkt collector on a separate terminal.

   ```bash
   java -jar /path/to/Collector-0.0.1-SNAPSHOT-jar-with-dependencies.jar -c /path/to/config.txt
   ```

1. On a new terminal, run the target program (e.g., `python3 main.py`).

    ```bash
    # Run inside the target software repository:
    python3 main.py
    ```

## Run the Kieker Trace Analysis

   * Prepare Trace Analysis
     ```bash
     # Run inside the demo repository
     unzip tools/trace-analysis-2.0.3-py4j.zip
     ```

   * Aggregated Deployment Call Tree
     ```bash
     # Run inside the demo repository
     mkdir output-adctree
     trace-analysis-2.0.3-SNAPSHOT/bin/trace-analysis \
       --inputdirs /path/to/kieker-logs               \
       --outputdir output-adctree                     \
       --plot-Aggregated-Deployment-Call-Tree         \
       --graphics-engine PLANTUML                     \
       --pdf
     ```

   * Deployment Component Dependency Graph
     ```bash
     # Run inside the demo repository
     mkdir output-dcdgraph
     trace-analysis-2.0.3-SNAPSHOT/bin/trace-analysis    \
       --inputdirs /path/to/kieker-logs                  \
       --outputdir output-dcdgraph                       \
       --plot-Deployment-Component-Dependency-Graph none \
       --graphics-engine PLANTUML                        \
       --pdf
     ```

   * Deployment Sequence Diagrams
     ```bash
     # Run inside the demo repository
     mkdir output-dsdiagrams
     trace-analysis-2.0.3-SNAPSHOT/bin/trace-analysis \
       --inputdirs /path/to/kieker-logs               \
       --outputdir output-dsdiagrams                  \
       --plot-Deployment-Sequence-Diagrams            \
       --graphics-engine PLANTUML                     \
       --pdf
     ```