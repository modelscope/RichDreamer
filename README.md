<p align="center">
    <br>
    <img src="https://modelscope.oss-cn-beijing.aliyuncs.com/modelscope.gif" width="400"/>
    <br>
    <h1>RichDreamer</h1>
<p>

RichDreamer: A Generalizable Normal-Depth Diffusion Model for Detail Richness in Text-to-3D.

[Lingteng Qiu\*](https://lingtengqiu.github.io/),
[Guanying Chen\*](https://guanyingc.github.io/),
[Xiaodong Gu\*](https://scholar.google.com.hk/citations?user=aJPO514AAAAJ&hl=zh-CN&oi=ao),
Qi Zuo,
[Mutian Xu](https://mutianxu.github.io/),
Yushuang Wu,
[Weihao Yuan](https://weihao-yuan.com/),
[Zilong Dong](https://scholar.google.com/citations?user=GHOQKCwAAAAJ&hl=zh-CN&oi=ao),
[Liefeng Bo](https://research.cs.washington.edu/istc/lfb/),
[Xiaoguang Han](https://gaplab.cuhk.edu.cn/)

如果您熟悉中文，可以阅读[中文版本的README](./README_ZH.md)。

Our method is based on Normal-Depth diffusion Model, for more details please refer to [normal-depth-diffusion](https://github.com/modelscope/normal-depth-diffusion).

## [Project page](https://lingtengqiu.github.io/RichDreamer/) | [Paper](https://arxiv.org/abs/2311.16918) | [YouTube](https://youtu.be/6gQ1VWiKoc0) | [ND-Diffusion Model](https://github.com/modelscope/normal-depth-diffusion)


<img src=".\figs\richdreamer.gif" alt="richdreamer" style="zoom:200%;" />

## TODO  :triangular_flag_on_post:
- [x]  Text to ND Diffusion Model
- [x]  Multiview-ND and Multiview-Albedo Diffusion Models
- [x]  Release code
- [ ]  Docker image
- [ ]  Provide the generation trial on [ModelScope's 3D Object Generation](https://modelscope.cn/studios/Damo_XR_Lab/3D_AIGC/summary)



## News

- Release RichDreamer :fire::fire::fire: (12.11, 2023 UTC)

## Architecture

![architecture](figs/architecture.png)



# Install

- System requirement: Ubuntu20.04
- Tested GPUs: RTX4090, A100

Install requirements using following scripts.

```bash
git clone https://github.com/modelscope/RichDreamer.git --recursive
conda create -n rd
conda activate rd
# install dependence of threestudio
pip install -r requirements_3d.txt
```

we also provide a dockerfile to build docker image or use our built [docker image](https://code.alibaba-inc.com/dadong.gxd/dream3d/blob/release/1209).

```bash
sudo docker build -t mv3dengine_22.04:cu118 -f docker/Dockerfile .
```

Download pretrained weights.

1. MultiView Normal-Depth Diffusion Model (ND-MV)
2. MultiView Depth-conditioned Albedo Diffusion Model (Alebdo-MV)



**Or** your can download weights using the following scripts.

```bash
python tools/download_nd_models.py
# copy 256_tets file for dmtet.
cp ./pretrained_models/Damo_XR_Lab/Normal-Depth-Diffusion-Model/256_tets.npz ./load/tets/
# link your huggingface models to ./pretrained_models/huggingface
cd pretrained_models && ln -s ~/.cache/huggingface ./
```

if you cannot visit huggingface to download SD 1.5 and SD 2.1, you can download SD models from [aliyun](https://virutalbuy-public.oss-cn-hangzhou.aliyuncs.com/share/RichDreamer/models_sd.tar.gz) and then put `$download_sd` file to `pretrained_models/huggingface/hub/`.

```bash
mkdir -p pretrained_models/huggingface/hub/
cd pretrained_models/huggingface/hub/
mv /path/to/${download_sd} ./
tar -xvf ${download_sd} ./
```

## Generation

### Our (NeRF)

```bash
# Quick Start, Single A-100 80G
python3 ./run_nerf.py -t $prompt -o $output

# Run from prompt list
# e.g. bash ./scripts/nerf/run_batch_fast.sh 0 1 ./prompts.txt
bash ./scripts/nerf/run_batch_fast.sh $start_id $end_id ${prompt.txt}

# If you don't have an A-100 device, we offer a save memory version to generate results.
# For single GTX-3090/4090, 24GB GPU memory.
python3 ./run_nerf.py -t $prompt -o $output -s 1
```

### Our (DMTet)

#### Tips for Training DMTet

**1. Rendering High-resolution:**

We found that optimizing a high-resolution DMTet sphere directly is more challenging compared to the NeRF method.  For example, both Fantasia3D and SweetDreamer require 4 or 8 GPUs for  optimization, which is difficult to obtain for most individuals. During the experimental process, we observed that optimization becomes  significantly more stable when we increase the rendering resolution of  DMTet, such as to **1024**. This trick allows us to optimize from DMTet using only a single GPU, which was previously not feasible.

**2. PBR Modeling:**

Fantasia3D offers three strategies for conducting PBR modeling.  If you **Do Not** require generating models that support relighting and only aim for enhanced realism, we recommend using the sampling strategy  *fantasia3d_2*. **Otherwise** we suggest you to use *fantasia3d strategy_0* with our *depth condition albedo-sds*.



```bash
# Quick Start, Single A-100 80G
python3 ./run_dmtet.py -t $prompt -o $output

# Run from prompt list
# e.g. bash ./scripts/nerf/run_batch.sh 0 1 ./prompts.txt
bash ./scripts/dmtet/run_batch.sh $start_id $end_id ${prompt.txt} 

# If you don't have an A-100 device, we offer a save memory version to generate results.
# For single GTX-3090/4090, 24GB GPU memory.
# bash ./scripts/dmtet/run_batch_fast.sh 0 1 ./prompts.txt
bash ./scripts/dmtet/run_batch_fast.sh $start_id $end_id ${prompt.txt} 
```


## Acknowledgement

This work is built on many amazing research works and open-source projects:

- [Stable-Dreamfusion](https://github.com/ashawkey/stable-dreamfusion)
- [threestudio](https://github.com/threestudio-project/threestudio)
- [Fantasia3D](https://github.com/Gorilla-Lab-SCUT/Fantasia3D)
- [MVDream](https://github.com/bytedance/MVDream-threestudio)

Thanks for their excellent work and great contribution to 3D generation area.

We would like to express our special gratitude to [Rui Chen](https://aruichen.github.io/) for the valuable discussion in training Fantasia3D and PBR modeling. 

Additionally, we extend our heartfelt thanks to Chao Xu for his assistance in conducting relighting experiments.

## Citation	

```
@article{qiu2023richdreamer,
    title={RichDreamer: A Generalizable Normal-Depth Diffusion Model for Detail Richness in Text-to-3D}, 
    author={Lingteng Qiu and Guanying Chen and Xiaodong Gu and Qi zuo and Mutian Xu and Yushuang Wu and Weihao Yuan and Zilong Dong and Liefeng Bo and Xiaoguang Han},
    year={2023},
    journal = {arXiv preprint arXiv:2311.16918}
}
```
