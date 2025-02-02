# Cyber

**Cyber** represents a model implementation that seamlessly integrates state-of-the-art (SOTA) world models with the proposed **CyberOrigin Dataset**, pushing the boundaries of artificial intelligence and machine learning.

Follow this document to train the models using our readily-available data or adapt your data for training.

## CyberOrigin Dataset
Our data includes information from home services, the logistics industry, and laboratory scenarios.
For more details, please refer to our [Offical Data Website](https://cyberorigin2077.github.io/).

* **Format & Description**</br>
Currently, the dataset contains image tokens generated by Magvit2. For more information, please refer to the dataset card on [Huggingface](https://huggingface.co/datasets/cyberorigin/CyberDataset).

* **Download the Dataset**</br>
The dataset is currently available on Huggingface.

    - 🤗 [Pipette](https://huggingface.co/datasets/cyberorigin/cyber_pipette)
    - 🤗 [Take Item](https://huggingface.co/datasets/cyberorigin/cyber_take_the_item)
    - 🤗 [Twist Tube](https://huggingface.co/datasets/cyberorigin/cyber_twist_the_tube)
    - 🤗 [Fold Towels](https://huggingface.co/datasets/cyberorigin/cyber_fold_towels)

You can download the dataset using the following command:
```bash
bash ../scripts/download_dataset.sh
```

* **Visualize the Dataset**</br>
You can visualize the dataset using this [notebook](notebooks/visualize_data.ipynb).
Make sure to install the jupyter before running the notebook. `pip install jupyter notebook`


## Quick Start for CyberOrigin Dataset
### Download the Dataset
```bash
bash ../scripts/download_dataset.sh
```
### Model Training & Evaluation
```
python models/world/train_world.py --train-config-path configs/models/world/world_model.yaml --model-config-path configs/models/world/MagVIT_Genie.yaml
```
*Note: The model will train on the default configuration provided.*

## Model configuration and hyperparameters
### GENIE
The code is adapted from [1x's implementation](https://github.com/1x-technologies/1xgpt) of [GENIE](https://arxiv.org/abs/2402.15391). The model is based on an ST-transformer architecture that predicts the next frame given the previous frames.

**Model parameters tuning**</br>
The detailed configuration file is provided in the `configs/models/world` folder. 
```
{
    "num_layers": 32, // number of ST-transformer blocks
    "num_heads": 8, // number of heads in multi-head attention
    "d_model": 256, // dimension of the model latent
    "T": 16, // number of frames in the input sequence
    "S": 256, // number of tokens in the input sequence S=16x16
    "image_vocab_size": 262144, // codebook size for the image tokens
    "use_mup": false, // whether to use MUP
    "num_factored_vocabs": 2, // number of factored vocabularies
    "qkv_bias": false, // whether to use bias in qkv projection
    "proj_bias": true, // whether to use bias in projection
    "attn_drop": 0, // dropout rate in attention
    "qk_norm": false, // whether to normalize qk
    "mlp_ratio": 4, // ratio of hidden size to model latent size in MLP
    "mlp_drop": 0, // dropout rate in MLP
    "mlp_bias": true // whether to use bias in MLP
}
```
It is recommended to only modify the first three parameters to adjust model size.

**Training parameters tuning**</br>
Please refer to the help message for hyperparameter descriptions
```bash
python models/world/train.py -h
```

### Magvit2
Code is modified from [1XGPT](https://github.com/1x-technologies/1xgpt) and [Open-MAGVIT2](https://github.com/TencentARC/Open-MAGVIT2) but removed unnecessary files and code.

**Pretrained checkpoint**</br>
Download the checkpoint [HERE](https://huggingface.co/datasets/1x-technologies/worldmodel/blob/main/magvit2.ckpt) Or run the command:

```
huggingface-cli download 1x-technologies/worldmodel magvit2.ckpt --repo-type dataset --local-dir ./experiments/
```

**Try with our provided samples**</br>
We provide the notebook you can try to compress and decompress your video. Please try [autoencoder_demo.ipynb](notebooks/autoencoder_demo.ipynb) and follow the instructions.

**Compress your video data**</br>
```
root-folder
    └── subfolder1/
        ├── color(videos inside)
            ├── 1b17c56e-02acc10001.mp4
            ├── 0ce6190f-02acc11002.mp4
            ├── ...
        ├── ...
    └── subfolder2/
        ├── ...
```

Make sure your datasets are the same as the structure above.
```bash
python experiments/compress_video.py --root_path /path/to/root/folder --ckpt_path experiments/magvit2.ckpt --output_path /path/to/output/folder
```

```videos.bin``` ```metadata.json``` ```segment_ids.bin``` will be generated in ```output_path/date_folder/compressed```, you can decompress it and check the reconstructed video.