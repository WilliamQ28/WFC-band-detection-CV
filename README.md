This repository contains all scripts, configuration, and results for the **WFC-YOLO** project: a custom dataset and YOLOv8 fine-tuning pipeline that detects structural “bands”  (Header, Nav, Main, Banner, Supplement, Footer) in website screenshots.

The dataset and model are part of the IAT 360 Computer-Vision project at Simon Fraser University.

Dataset link:
https://1sfu-my.sharepoint.com/:u:/g/personal/qza85_sfu_ca/ESxtxICGc8pEh7TSIqeTxOABR8NR3_pPX1IeG1LsNHOcHA?e=OAEzpw

CheckSum - SHA256: f2aa74c5df525846e0cd525b023660fea2be4e90713bf801e3b07b778c91c6aa
To check: Windows PowerShell: certutil -hashfile datasets.zip SHA256
          Linux: sha256sum datasets.zip

Verify the dataset's structure: 
datasets/
 ├── images/train/
 ├── images/val/
 ├── labels/train/
 └── labels/val/

Environment Setup(at project root):
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python verify_cuda.py

(**Do not do this for grading, data wrangling step, for demonstration only**)
(**Project root assumed to be C:\wfc**)
Screenshot capture:
python screenshot_from_links.py --in C:\wfc\source\links.txt --out C:\wfc\screens --width 1440 --timeout 20000 --concurrency 6 --retries 1 --throttle 100

80/20 train val split:
# Expects C:\wfc\datasets\images and C:\wfc\datasets\labels to contain flat files already.
python split_dataset.py

Color augment pass:
# Per original image, make k variants in TRAIN split
python color_augment.py --dataset C:\wfc\datasets --split train --per_image 2 --exts ".jpg,.jpeg,.png"

Grayscale pass:
python color_decolorize.py --dataset C:\wfc\datasets --split train --ab_amp 20 --p_shuffle 0.0 --b_gain 0.95 1.05 --c_gain 0.95 1.05
(**End of data augmentation and wrangling**)

To train (assumed project root: C:\wfc):
CheckSum - SHA256: 98f944b5af7adbc93453fbb7663aa43ac15b74a8b65d64a372d0ada0ad13498a
.\train_yolo_gpu.bat
(epoch, imgsz, batch, device, workers augments are in the bat file)
