<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>
<!--
*** Thanks for checking out the Best-README-Template. If you have a suggestion
*** that would make this better, please fork the repo and create a pull request
*** or simply open an issue with the tag "enhancement".
*** Don't forget to give the project a star!
*** Thanks again! Now go create something AMAZING! :D
-->

# [API: Attention Prompting on Image for Large Vision-Language Models](https://arxiv.org/abs/2409.17143) (ECCV 2024)

TL;DR (1) - Add an **adaptive mask** onto the image to enhance LVLM performance.

TL;DR (2) - Mask is generated by an **auxiliary LVLM** based on the relevance between the image regions and the query.

<p align="left">
[<a href="https://arxiv.org/abs/2409.17143">Paper</a>] 
[<a href="https://huggingface.co/spaces/rp-yu/apiprompting">Playground</a>]
[<a href="https://yu-rp.github.io/api-prompting/">Project Page</a>]
[<a href="https://pypi.org/project/apiprompting">Python Package</a>]
[<a href="https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/API_DEMO.mp4">Demo Video</a>]
</p>

## Graphical Abstract
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/HeadImage_1.png)
🔧 The process of using Attention Prompting on Image (API) in VQA involves two steps. First, employ an auxiliary LVLM to generate a mask. Second, overlay the mask on the original image before performing inference. For instance, an auxiliary CLIP can be used to calculate the similarity between each image patch and the query. Patches with low similarity are assigned a heavier mask, while patches with high similarity remain unmasked. Such mask serves as a visual cue to guide the VLM during inference, directing attention to regions of the image relevant to the question.

![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/HeadImage_2.png)
👍 Here is an example comparing our API method with the naive VQA method without prompting. The question in the example is particularly challenging, testing the VLM's abilities in visual grounding and spatial attribute reasoning. The API-generated mask reduced the difficulty of the visual grounding task, highlighting the red bird mentioned in the query.


<!-- TABLE OF CONTENTS -->
## Table of Contents
<!-- <summary style="font-size: 20px;"></summary> -->
<ol>
  <li><a href="#briefing">Briefing</a></li>
  <li><a href="#play-with-api">Play with API</a></li>
  <li><a href="#api-package">API Package</a></li>
  <li><a href="#environment-setup">Environment Setup</a></li>
  <li><a href="#use-api">Use API</a></li>
  <li><a href="#tutorial">Tutorial</a></li>
  <li><a href="#more-examples">More Examples</a></li>
</ol>

## Briefing

In this repo, we provide the code for generate annotated images using API method. Both the CLIP-based and LLaVA-based generation are included. After obtaining the annotated images, you may use them to evalute the corresponding VQA performance of any LVLM you want. 

Now, there are two ways to try API. We have a python package, with which you can use minimal lines of code to try API or integrate API into your code. The other way is to use the code in this repo, which is more flexible and editable.

  * Instructions for apiprompting package: <a href="#apiprompting-package">API Package</a>.
  * Instructions for directly using the code in this repo: <a href="#environment-setup">Environment Setup</a> and <a href="#use-api">Use API</a>.


<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## Play with API

* Try our demo online.

  [![Static Badge](https://img.shields.io/badge/Huggingface%20Space-%F0%9F%94%A5apiprompting-blue?style=plastic)](https://huggingface.co/spaces/rp-yu/apiprompting)
* Run it locally using the following code.

  [![Static Badge](https://img.shields.io/badge/gradio-demo-green?style=plastic)](https://github.com/yu-rp/apiprompting/tree/master/demo)

* Check out the [video demo](https://github.com/yu-rp/asserts/blob/main/API_Prompting/API_DEMO.mp4).

https://github.com/user-attachments/assets/e60ba0b6-c0e2-45ad-9676-98c5e2288fb4

<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## API Package
To install the package, run the following command:
```bash
pip install apiprompting
```

You can view the full package description on its <a href="https://pypi.org/project/apiprompting">PyPI Page</a>. Below is a simplified case for illustration.
```python
from apiprompting import clip_api, llava_api

images, queries = ["path/to/image"], ["query"]

# CLIP_Based API
masked_images = clip_api(images, queries, model_name="ViT-L-14-336")
# LLaVA_Based API
masked_images = llava_api(images, queries, model_name="llava-v1.5-13b")
```


## Environment Setup

For directly using the code in this repo, one environment are required for each of the CLIP-based code and the LLaVA-based code.

### Environment for CLIP-based API
The code for CLIP-based API is based on the [this](https://github.com/yossigandelsman/clip_text_span?tab=readme-ov-file) repo. To create the environment, you may follow the instruction in the `api/CLIP/README.md` file or simply create a environment using the following commands.

```bash
conda create -n clip_api python=3.11
conda activate clip_api
pip install torch torchvision timm einops ftfy scipy imageio h5py scikit-image scikit-learn opencv-python regex
```

### Environment for LLaVA-based API
The code for LLaVA-based API is based on the official [LLaVA](https://github.com/haotian-liu/LLaVA) repo. To create the environment, you may follow the instruction in the `api/LLaVA/LLaVA/README.md` file.

<details>
  <summary>Error and Solution</summary>
  
  * ValueError: Unable to create tensor, you should probably activate padding with 'padding=True' to have batched tensors with the same length.  
    * Upgrade Numpy package. For now, using numpy==1.24.0 fixs this error.
  
</details>



### Environment for Data Loading (can be customized)
We use an extra DataManager script to control the dataset pre-processing and loading. You may include any dataset and modify the function in `API/DatasetManager/dataloader.py` to customize it. After setup the environment for CLIP-Based API and LLaVA-Based API, you can use the following command to install the extra DataManager module.

```bash
# For CLIP-Based API
conda activate clip_api
# For LLaVA-Based API
# conda activate llava_api

cd API/DatasetManager
pip install -e .
```

<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## Use API

### CLIP-Based API

The following command can generate masked images from a given dataset using the CLIP-Based API.

```bash
cd API/API_CLIP

python main.py \
  --dataset mmvet \  
  --range 0 100 \ 
  --model_name ViT-L-14-336 \ 
  --layer_index 22 \  
  --batch_size 8 \ 
  --output_folder "../../experiments" \
  --interpolate_method_name LANCZOS \ 
  --enhance_coe 5 \
  --kernel_size 3 \
  --grayscale 0

  # Dataset Parameters
  # --dataset: Dataset, e.g., mmvet, LLaVA_Wild.
  # --range: Range of images to be processed, for example, args.range = [0,100] indicates that only the first 100 images will be processed.

  # Auxiliary Model Parameters
  # --model_name: Name of the auxiliary CLIP model.
  # --layer_index: Layer index of the feature used to calculate the similarity score. Based on our observations, the second-to-last and third-to-last layers perform better.

  # Processing Parameter
  # --batch_size: CLIP-Based API supports batch processing. Increasing batch_size to speed up.
  # --output_folder: Output Folder.

  # Masking Parameter
  # --interpolate_method_name: The interpolation method used during mask resizing, such as LANCZOS or BICUBIC.
  # --enhance_coe: Contrast control parameter. The larger this parameter, the greater the contrast between the bright and dark areas of the mask, such as 1,5,10.
  # --kernel_size: Smoothness control parameter. The larger this parameter, the smoother the generated mask appears, reducing the rectangular shapes in the mask.
  # --grayscale: Grayscale control parameter, determining the grayscale level of the mask.
```

### LLaVA-Based API

The following command can generate masked images from a given dataset using the LLaVA-Based API.

```bash
cd API/API_LLaVA

python main.py \
  --dataset mmvet \  
  --range 0 100 \ 
  --model_name 13b \ 
  --layer_index 22 \  
  --output_folder "../../experiments" \
  --interpolate_method_name LANCZOS \ 
  --enhance_coe 5 \
  --kernel_size 3 \
  --grayscale 0

  # Dataset Parameters
  # --dataset: Dataset, e.g., mmvet, LLaVA_Wild.
  # --range: Range of images to be processed, for example, args.range = [0,100] indicates that only the first 100 images will be processed.

  # Auxiliary Model Parameters
  # --model_name: Name of the auxiliary LLaVA model.
  # --layer_index: Layer index of the feature used to calculate the similarity score. Based on our observations, the second-to-last and third-to-last layers perform better.

  # Processing Parameter
  # --output_folder: Output Folder.

  # Masking Parameter
  # --interpolate_method_name: The interpolation method used during mask resizing, such as LANCZOS or BICUBIC.
  # --enhance_coe: Contrast control parameter. The larger this parameter, the greater the contrast between the bright and dark areas of the mask, such as 1,5,10.
  # --kernel_size: Smoothness control parameter. The larger this parameter, the smoother the generated mask appears, reducing the rectangular shapes in the mask.
  # --grayscale: Grayscale control parameter, determining the grayscale level of the mask.
```

<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## Tutorial
[sglang_inference/tutorial.ipynb](sglang_inference/tutorial.ipynb) is a tutorial that uses the MM-Vet and LLaVA-Wild datasets as examples to demonstrate how to generate masked images using the CLIP-Based API and perform inference with LLaVA 1.5. The experimental results mentioned in the tutorial are included in the results folder. 

<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## More Examples
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case01.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case02.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case03.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case04.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case05.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case06.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case07.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case08.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case09.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case10.png)
![.](https://raw.githubusercontent.com/yu-rp/asserts/main/API_Prompting/case11.png)

<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>

## Citation
If you find our work useful, please cite using this BibTeX:
```bibtex
@misc{yu2024api,
      title={API: Attention Prompting on Image for Large Vision-Language Models}, 
      author={Runpeng Yu and Weihao Yu and Xinchao Wang},
      year={2024},
      booktitle={European Conference on Computer Vision},
}
```
<p align="right"><a href="#readme-top"><img src=https://img.shields.io/badge/back%20to%20top-red?style=flat
></a></p>
