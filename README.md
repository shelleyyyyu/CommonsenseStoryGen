# CommonsenseStoryGen
This repository contains the source code for the paper [A Knowledge-Enhanced Pretraining Model for Commonsense Story Generation]() by Jian Guan, Fei Huang, Zhihao Zhao, Xiaoyan Zhu, [Minlie Huang](http://coai.cs.tsinghua.edu.cn/hml/), presented at [TACL](https://transacl.org/ojs/index.php/tacl). 

Contact info: j-guan19@mails.tsinghua.edu.cn

## Introduction

Story generation, namely generating a reasonable story from a leading context, is an important but challenging task. In spite of the success in modeling fluency and local coherence, existing neural language generation models (e.g., GPT-2) still suffer from repetition, logic conflicts, and lack of long-range coherence in generated stories. We conjecture that this is because of the difficulty of associating relevant commonsense knowledge, and managing the causal relationships and temporal orders of entities and events. In this paper, we devise a knowledge-enhanced pretraining model for commonsense story generation. We propose to utilize commonsense knowledge from external knowledge bases to generate reasonable stories. To further capture the causal and temporal dependencies between the sentences in a reasonable story, we employ multi-task learning which combines a discriminative objective to distinguish true and fake stories during ﬁne-tuning. Automatic and manual evaluation shows that our model can generate more reasonable stories than the state-of-the-art baselines, particularly in terms of logic and global coherence.

## Prerequisites

The code is written in [TensorFlow](https://www.tensorflow.org/) library. To use the program the following prerequisites need to be installed.

- Python 3.5.2
- tensorflow-gpu==1.12.0
- numpy==1.15.4
- regex

## Quick Start

- Dataset

  Our dataset contains five-sentence stories from [ROCStories corpus](http://cs.rochester.edu/nlp/rocstories/) (Note that we just split the training data of the original corpus into the new training/validation/testing set). As for the knowledge bases, we used [ConceptNet](https://ttic.uchicago.edu/~kgimpel/commonsense.html) and  [ATOMIC](https://homes.cs.washington.edu/~msap/atomic/). We made delexilization for all the resources as indicated in Section 4.1 of our paper. The processed dataset is available [here](https://cloud.tsinghua.edu.cn/d/670f7787b6554f308226/). The fake stories used for multi-task learning can be generated by executing  `python3 gen_fake_st.py` under the directory of  `data` .

- Pretrained model

  The approach to downloading the pretrained model can be found [here](https://github.com/openai/gpt-2). We adopted the small-version with 124M parameter.

- Parameter setting

  ```
  --is_train		Set to 1/0 to train/inference.
  --cond			Set to 1/0 to generate stories unconditionally/conditionally on the beginning.
  --model_dir		Model directory.
  --gpu			Specify which gpu to use. (default 0)
  --batch_size		Number of batches (only affects speed/memory, default 10)
  --data_name		Set `roc` to train the model on ROCStories corpus or \
  				`kg` to train the model on the knowledge bases or \
  				`multi_roc` to train the model on ROCStories with multi-task learning.
  --n_class		Number of classes for the auxiliary classification task. (default 4)
  --learning_rate		Learning rate. (default 1e-4)
  --data_dir		Data directory.
  --length		Number of tokens in generated text. (default 200)
  --temperature		Float value controlling randomness in boltzmann distribution. Lower temperature results in less random completions. As the temperature approaches zero, the model will become deterministic and repetitive. Higher temperature results in more random completions. (default 0.7)
  --top_k			Integer value controlling diversity. (default 40)
  ```
- Training baseline 

  The pretrained model can be post-trained on the roc by executing the following command:

  ​	`python ./src/main.py --is_train 1 --data_name roc --model_dir ./models_baseline/gpt2-124M`

  
- Training 

  The pretrained model can be post-trained on the knowledge bases by executing the following command:

  ​	`python ./src/main.py --is_train 1 --data_name kg --data_dir ./data/commonsense_data --model_dir ./models/gpt2-124M`

  And then the model is trained on the ROCStories corpus with multi-task learning by executing the following command:

  ​	`python ./src/main.py --is_train 1 --data_name multi_roc --model_dir ./models/gpt2-124M`

- Inference

  You can generating stories conditionally on the beginning using the following command:

  ​	`python ./src/main.py --is_train 0 --cond 1 --data_name roc --model_dir ./models/gpt2-124M `

  You can generating stories unconditionally using the following command:

  ​	`python ./src/main.py --is_train 0 --cond 0 --data_name roc --model_dir ./models/gpt2-124M `

  1000 conditionally generated stories (the beginnings are sampled from the testing set) can be found in `sample_generation_story.txt`.

