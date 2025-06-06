# LLMxMapReduce: Simplified Long-Sequence Processing using Large Language Models

<a href='https://surveygo.modelbest.cn/'><img src='https://img.shields.io/badge/Demo-Page-pink'></a> <a href='https://arxiv.org/abs/2410.09342'><img src='https://img.shields.io/badge/V1-Paper-Green'></a> <a href='https://arxiv.org/abs/2504.05732'><img src='https://img.shields.io/badge/V2-Paper-blue'></a> <a href='https://huggingface.co/datasets/R0k1e/SurveyEval'><img src='https://img.shields.io/badge/SurveyEval-Benchmark-yellow'></a> <a href='README_zh.md'><img src='https://img.shields.io/badge/Chinese-Readme-red'></a>

# 🎉 News
- [x] **`2025.04.22`** Release [SurveyGO](https://surveygo.modelbest.cn/), an online writting system driven by LLMxMapReduce-V2.
- [x] **`2025.04.09`** Release the paper of LLMxMapReduce-V2 in [arXiv](https://arxiv.org/abs/2504.05732).
- [x] **`2025.02.21`** Add support for both OpenAI API and OpenAI-compatible APIs (e.g., vLLM).
- [x] **`2024.10.12`** Release the paper of LLMxMapReduce-V1 in [arXiv](https://arxiv.org/abs/2410.09342).
- [x] **`2024.09.12`** Release the code for LLMxMapReduce-V1.

# 📚 Overview
**LLMxMapReduce** is a divide-and-conquer framework designed to enhance modern large language models (LLMs) in understanding and generating long sequences. Developed collaboratively by **AI9STARS**, **OpenBMB**, and **THUNLP**, this framework draws inspiration from the classic MapReduce algorithm introduced in the field of big data. Our goal is to build an LLM-driven distributed computing system capable of efficiently processing long sequences. Here are the main versions of LLMxMapReduce:

* [**LLMxMapReduce-V1**](https://github.com/thunlp/LLMxMapReduce/blob/main/LLMxMapReduce_V1): Utilizes a structured information protocol and in-context confidence calibration to enhance long-sequence understanding, enabling [MiniCPM3-4B](https://github.com/OpenBMB/MiniCPM) to outperform 70B-scale models in long-context evaluations.
* [**LLMxMapReduce-V2**](https://github.com/thunlp/LLMxMapReduce/tree/main/LLMxMapReduce_V2): Introduces an entropy-driven convolutional test-time scaling mechanism to improve the integration of extremely large volumes of information, powering the online [SurveyGO](https://surveygo.modelbest.cn/) system.

# 📖 Introduction


Long-form generation is crucial for a wide range of practical applications, typically categorized into short-to-long and long-to-long generation. While short-to-long generations have received considerable attention, generating long texts from extremely long resources remains relatively underexplored. The primary challenge in long-to-long generation lies in effectively integrating and analyzing relevant information from extensive inputs, which remains difficult for current large language models (LLMs). In this paper, we propose LLMxMapReduce-V2, a novel test-time scaling strategy designed to enhance the ability of LLMs to process extremely long inputs. Drawing inspiration from convolutional neural networks, which iteratively integrate local features into higher-level global representations, LLMxMapReduce-V2 utilizes stacked convolutional scaling layers to progressively expand the understanding of input materials. Both quantitative and qualitative experimental results demonstrate that our approach substantially enhances the ability of LLMs to process long inputs and generate coherent, informative long-form articles, outperforming several representative baselines.

<div align="center">
  <img src="assets/main_pic.jpg" alt="$\text{LLM}\times \text{MapReduce}$-V2 framework">
</div>

# ⚡️ Getting Started
The following steps are about LLMxMapReduce-V2. If you want to use LLMxMapReduce-V1, you need to refer to [here](LLMxMapReduce_V1/README.md).

To get started, ensure all dependencies listed in requirements.txt are installed. You can do this by running:
```bash
cd LLMxMapReduce_V2
conda create -n llm_mr_v2 python=3.11
conda activate llm_mr_v2
pip install -r requirements.txt
python -m playwright install --with-deps chromium
```

Before evaluation, you need to download punkt_tab firstly.
```python
import nltk
nltk.download('punkt_tab')
```
## Env config
Please set your OPENAI_API_KEY and OPENAI_API_BASE in your environment variables before start the pipeline. If you use miniconda, replace `anaconda3` in `LD_LIBRARY_PATH` with `miniconda3`
```bash
export LD_LIBRARY_PATH=${HOME}/anaconda3/envs/llm_mr_v2/lib/python3.11/site-packages/nvidia/nvjitlink/lib:${LD_LIBRARY_PATH}
export PYTHONPATH=$(pwd):${PYTHONPATH}
export OPENAI_API_KEY=Your OpenAI Key. You need to set it when you choose the infer type as OpenAI.
export OPENAI_API_BASE=Your OpenAI base url
export GOOGLE_API_KEY=Your Google Cloud key. you need to set it when you choose the infer type as Google.
export SERP_API_KEY= Get SERP API key from https://serpapi.com
```

We provide both English and Chinese version of prompt. Default version is English. If you wish to use Chinese version, please set this env:
``` bash
export PROMPT_LANGUAGE="zh"
```

## Model Set
⚠️ We strongly recommend using the Gemini Flash models. There may be unknown errors when using any other models. This project has high requirements for API consumption and concurrent volume, so it's not recommended to use locally deployed models.

The models used in the generation process are configured in the `./LLMxMapReduce_V2/config/model_config.json` file. Currently, we support both the OpenAI API and the Google API. You can specify the API to be used in the `infer_type` key. Additionally, you need to specify the model name in the `model` key.

Moreover, the crawling process also requires large language model (LLM) inference. You can make changes in a similar manner in the `./LLMxMapReduce_V2/src/start_pipeline.py` file. 

## Start LLMxMapReduce_V2 pipeline
Follow the instructions and generate a report. The generated Markdown file is at ./output/md. 
```bash
cd LLMxMapReduce_V2
bash scripts/pipeline_start.sh TOPIC output_file_path.jsonl
```

If you wish to use your own data, you need to set the `--input_file` and don't set `--topic` in scripts.

The input data should have following components at least:
```json
{
  "title": "The article title you wish to write",
  "papers": [
    {
      "title": "The material title",
      "abstract": "The abstract material. Optional, if not, part of the full text will be excerpted",
      "txt": "The reference material full content"
    }
  ]
}
```

You could use to use [this script](LLMxMapReduce_V2/scripts/output_to_md.py) to convert data from `.jsonl` to multiple `.md` files.

# 📃 Evaluation
The following steps are about LLMxMapReduce-V2. If you want to use LLMxMapReduce-V1, you need to refer to [here](LLMxMapReduce_V1/README.md).

Follow the steps below to set up the evaluation:
## 1. Download the Dataset
Before running the evaluation, you need to download the `test` split of [SurveyEval dataset](https://huggingface.co/datasets/R0k1e/SurveyEval). After downloading, store it in a `.jsonl` file.

## 2. Run the Evaluation
Execute the [scripts](LLMxMapReduce_V2/scripts/eval_all.sh) to evaluate the generated result. 
```bash
cd LLMxMapReduce_V2
bash scripts/eval_all.sh output_data_file_path.jsonl
```
Aware that the evaluation process is token-consuming, you need to make sure you have enough balance.

# 📊 Experiment Results
Our experiments demonstrate the improved performance of LLM using the LLMxMapReduce-V2 framework on SurveyEval. Detailed results are provided below.

| **Methods**           | **Struct.** | **Fait.** | **Rele.** | **Lang.** | **Crit.** | **Num.** | **Dens.** | **Prec.** | **Recall** |
|-----------------------|-------------|-----------|-----------|-----------|-----------|----------|-----------|-----------|------------|
| Vanilla               | 94.44       | 96.43     | **100.00**| **96.50** | 37.11     | 78.75    | **74.64** | 25.48     | 26.46      |
| + Skeleton            | **98.95**   | **97.03** | **100.00**| 95.95     | **41.01** | **135.15**| 72.96     | **62.60** | **65.11**  |
| AutoSurvey            | 86.00       | 93.10     | **100.00**| 92.90     | 68.39     | 423.35   | 31.97     | 50.12     | 51.73      |
| LLMxMapReduce_V2       | **95.00**   | **97.22** | **100.00**| **94.34** | **71.99** | **474.90**| **52.23** | **95.50** | **95.80**  |

# 📝 Citation
If you have used the content of this repository, please cite the paper and leave your star :).

```
@misc{wang2025llmtimesmapreducev2entropydrivenconvolutionaltesttime,
      title={LLM$\times$MapReduce-V2: Entropy-Driven Convolutional Test-Time Scaling for Generating Long-Form Articles from Extremely Long Resources}, 
      author={Haoyu Wang and Yujia Fu and Zhu Zhang and Shuo Wang and Zirui Ren and Xiaorong Wang and Zhili Li and Chaoqun He and Bo An and Zhiyuan Liu and Maosong Sun},
      year={2025},
      eprint={2504.05732},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2504.05732}, 
}

@misc{zhou2024llmtimesmapreducesimplifiedlongsequenceprocessing,
      title={LLM$\times$MapReduce: Simplified Long-Sequence Processing using Large Language Models}, 
      author={Zihan Zhou and Chong Li and Xinyi Chen and Shuo Wang and Yu Chao and Zhili Li and Haoyu Wang and Rongqiao An and Qi Shi and Zhixing Tan and Xu Han and Xiaodong Shi and Zhiyuan Liu and Maosong Sun},
      year={2024},
      eprint={2410.09342},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2410.09342}, 
}
```



