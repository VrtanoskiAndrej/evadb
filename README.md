<div >
  <a href="https://georgia-tech-db.github.io/eva/index.html">
    <img src="https://raw.githubusercontent.com/georgia-tech-db/eva/master/docs/eva-banner.png" alt="EVA" width="1000px" margin-left="-5px">
  </a>
  <div>
        <h3>Try It Out!</h3>
        <a href="https://colab.research.google.com/github/georgia-tech-db/eva/blob/master/tutorials/02-object-detection.ipynb">
            <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open EVA on Colab"/>
        </a>
        <a href="https://github.com/georgia-tech-db/eva/discussions">
            <img alt="Discuss on Github!" src="https://img.shields.io/badge/-Discuss%20on%20Github!-blueviolet">
        </a>
        <img alt="PyPI" src="https://img.shields.io/pypi/v/evadb.svg"/>
        <img alt="License" src="https://img.shields.io/badge/license-Apache%202-brightgreen.svg?logo=apache"/>
        <img alt="Python Versions" src="https://img.shields.io/badge/Python--versions-3.7%20|%203.8%20|%203.9%20|%203.10-brightgreen"/>
    </div>
</div>

# EVA Multimedia Database System

EVA is a new database system tailored for video analytics -- think PostgreSQL for videos. It supports a SQL-like language for querying videos (e.g., finding frames in a movie with your favorite actor or finding touchdowns in a football game). It comes with a wide range of commonly used computer vision models.

## Why EVA? ##

<details>
  <summary><b>Easily combine SQL and Deep Learning to build next-generation database applications</b></summary>
  Easily query videos in user-facing applications with a SQL-like interface for commonly used computer vision models.
</details>

<details>
  <summary><b>Speed up queries and save money spent on model inference</b></summary>
  EVA comes with a collection of built-in sampling, caching, and filtering optimizations inspired by time-tested relational database systems.
</details>

<details>
  <summary><b>Extensible by design to support custom deep learning models </b></summary>
  EVA has first-class support for user-defined functions that wrap around your deep learning models in PyTorch.
</details>

## Links
* [Documentation](https://evadb.readthedocs.io/en/latest/)
* [Tutorials](https://github.com/georgia-tech-db/eva/tree/master/tutorials)
* [Slack](https://join.slack.com/t/eva-db/shared_invite/zt-1i10zyddy-PlJ4iawLdurDv~aIAq90Dg)
* [Quick Demo](https://ada-00.cc.gatech.edu/eva/playground)

## Quick Start

1. EVA supports Python versions 3.7 through 3.10. To install EVA, we recommend using the pip package manager.

```shell
pip install evadb
```

1. EVA works on Jupyter notebooks -- illustrative notebooks are available in the [Tutorials](https://github.com/georgia-tech-db/eva/tree/master/tutorials) folder. EVA adopts a client-server architecture and comes with a terminal-based client. To start the EVA server and a terminal-based client, use the following commands:
```shell
eva_server &   # launch server
eva_client     # launch client
```

2. Load a video onto the server using the client (we use [ua_detrac.mp4](data/ua_detrac/ua_detrac.mp4) video as an example):

```mysql
LOAD FILE "data/ua_detrac/ua_detrac.mp4" INTO MyVideo;
```

3. That's it! You can now start running queries over the loaded video:

```mysql
SELECT id, data FROM MyVideo WHERE id < 5;
```

4. Search for frames in the video that contain a car

```mysql
SELECT id, data FROM MyVideo WHERE ['car'] <@ FastRCNNObjectDetector(data).labels;
```
| Source Video  | Query Result |
|---------------|--------------|
|<img alt="Source Video" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/traffic.webp" width="400"> |<img alt="Query Result" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/outtraffic.webp" width="400"> |

5. Search for frames in the video that contain a pedestrian and a car

```mysql
SELECT id, data FROM MyVideo WHERE ['pedestrian', 'car'] <@ FastRCNNObjectDetector(data).labels;
```

6. Search for frames in the video with more than 3 cars

```mysql
SELECT id, data FROM MyVideo WHERE Array_Count(FastRCNNObjectDetector(data).labels, 'car') > 3;
```

7. You can create a new user-defined function (UDF) that wraps around your custom vision model or an off-the-shelf model like FastRCNN:
```mysql
CREATE UDF IF NOT EXISTS MyUDF
INPUT  (frame NDARRAY UINT8(3, ANYDIM, ANYDIM))
OUTPUT (labels NDARRAY STR(ANYDIM), bboxes NDARRAY FLOAT32(ANYDIM, 4),
        scores NDARRAY FLOAT32(ANYDIM))
TYPE  Classification
IMPL  'eva/udfs/fastrcnn_object_detector.py';
```
## Demos 

### MNIST Digit Recognition
| Source Video  | Query Result |
|---------------|--------------|
|<img alt="Source Video" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/mnistvideo.webp" width="100"> |<img alt="Query Result" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/mnistoutput.webp" width="100"> |

### Emotion Detection

| Source Video  | Query Result |
|---------------|--------------|
|<img alt="Source Video" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/gangubai.webp" width="400"> |<img alt="Query Result" src="https://raw.githubusercontent.com/Aryan-Rajoria/eva/readme-gif/data/assets/gangubaioutput.webp" width="400"> |

## Contributing to EVA

[![PyPI Version](https://img.shields.io/pypi/v/evadb.svg)](https://pypi.org/project/evadb)
[![CI Status](https://circleci.com/gh/georgia-tech-db/eva.svg?style=svg)](https://circleci.com/gh/georgia-tech-db/eva)
[![Coverage Status](https://coveralls.io/repos/github/georgia-tech-db/eva/badge.svg?branch=master)](https://coveralls.io/github/georgia-tech-db/eva?branch=master)
[![Documentation Status](https://readthedocs.org/projects/evadb/badge/?version=latest)](https://evadb.readthedocs.io/en/latest/index.html)

To file a bug or request a feature, please use GitHub issues. Pull requests are welcome.
For more information on installing from source and contributing to EVA, see our
[contributing guidelines](https://evadb.readthedocs.io/en/latest/source/contribute/index.html).

## License
Copyright (c) 2018-2022 [Georgia Tech Database Group](http://db.cc.gatech.edu/)
Licensed under [Apache License](LICENSE).
