# Retinal Lesion Detection for Diabetic Retinopathy

[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/downloads/release/python-3100/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.1-red.svg)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An experimental pilot that applies computational intelligence techniques 
to automatically identify ocular lesions associated with diabetic retinopathy (DR) 
in fundus images, achieving **Dice Score 0.717** on the IDRiD benchmark dataset.

## Results Summary

| Model | Task | Best Metric | Configuration |
|-------|------|-------------|---------------|
| YOLOv8 | Detection | mAP@0.5: 0.86, F1: 0.77 | 960×960, Adam |
| U-Net + EfficientNetB3 | Segmentation | Dice: 0.717, IoU: 0.717 | PyTorch, JointLoss |
| MobileNetV2 | Classification | Accuracy: 86.2%, F1: 0.84 | Fine-tuning, SGD |

### Segmentation Results vs Baseline

| Architecture | Mean IoU | Parameters | Status |
|---|---|---|---|
| U-Net + EfficientNetB3 | **0.717** | 12.2M | ✅ Best |
| FPN + ResNet34 | 0.690 | 22.1M | Competitive |
| FPN + EfficientNetB3 | 0.690 | 13.4M | Stable |
| U-Net + VGG16 (baseline) | 0.683 | 23.7M | Reference |
| U-Net + ResNet34 | 0.671 | 21.8M | Limited |

## Problem Statement

Diabetic retinopathy (DR) is one of the leading causes of preventable 
blindness in adults. Traditional diagnosis through manual fundus image 
inspection is limited by subjectivity, time, and specialist availability — 
especially critical in Latin America, where specialist access is severely 
limited in rural areas.

This system provides automated detection, localization, and pixel-level 
segmentation of DR lesions to support clinical decision-making.

## Architecture

The system uses a **three-module hybrid approach**:
```
Fundus Image → [MobileNetV2: Severity Classification]
             → [YOLOv8: Lesion Detection + Bounding Boxes]  
             → [U-Net + EfficientNetB3: Pixel-level Segmentation]
             → Clinical Report
```

### Lesion Classes

| Class | Description | Challenge |
|---|---|---|
| Microaneurysms (MA) | <125μm red dots | Extreme scale, ~0.01% pixels |
| Hemorrhages (HE) | Variable size bleeds | High morphological variability |
| Hard Exudates (EX) | Lipid deposits | Grouped border saturation |
| Soft Exudates (SE) | Cotton-wool spots | Diffuse boundaries |
| Optic Disc (OD) | Anatomical reference | Confusion with exudates |

## Installation
```bash
git clone https://github.com/pedroalexandertenezaca/lesiones-oculares-rd-ftm
cd lesiones-oculares-rd-ftm
pip install -r requirements.txt
```

## Dataset

[IDRiD (Indian Diabetic Retinopathy Image Dataset)](https://ieee-dataport.org/open-access/indian-diabetic-retinopathy-image-dataset-idrid)

- 81 patients, 54 training / 27 test images
- Original resolution: 4288×2848 px
- Pixel-level annotations for 5 lesion classes
- Disease grading labels (0–4 severity scale)

**Important:** Dataset not included in this repo. Download from IEEE DataPort 
and place in `data/` directory following the structure in `data/README.md`.

## Training

**Segmentation (best model):**
```bash
python src/segmentation/train.py \
  --model unet \
  --encoder efficientnet-b3 \
  --epochs 50 \
  --batch-size 4 \
  --img-size 512 \
  --loss joint  # Dice + Focal
```

**Detection:**
```bash
python src/detection/train.py \
  --epochs 200 \
  --img-size 960 \
  --batch-size 8 \
  --optimizer adam
```

## Key Technical Decisions

**Why U-Net over FPN for this task?**  
FPN theoretically handles multi-scale objects better, but U-Net's direct 
skip connections preserve high-frequency spatial information critical for 
microaneurysm boundaries. With only 54 training images, U-Net's 
reconstruction approach generalizes better than FPN's pyramid summation.

**Why EfficientNet-B3 over ResNet34?**  
Squeeze-and-Excitation blocks in MBConv layers act as implicit attention 
mechanisms, helping the network distinguish subtle lesions from retinal 
background noise. ResNet34 without SE blocks overfit faster on IDRiD's 
small dataset.

**Why Joint Loss (Dice + Focal)?**  
With class imbalance of background ~98.5% vs MA ~0.01%, standard 
cross-entropy makes the model ignore minority classes. Focal Loss down-weights 
easy examples (background) while Dice Loss directly optimizes the evaluation 
metric.

**Why CLAHE preprocessing?**  
Contrast Limited Adaptive Histogram Equalization enhances local contrast in 
fundus images, making soft exudates (diffuse, low-contrast lesions) visible 
to the network. Improved Soft Exudate Dice by +19% over baseline.

## Results Visualization

[Prediction vs Ground Truth images go here]
[Training curves go here]

## Limitations

- Dataset size: 81 images (production systems require thousands)
- Single-center dataset: potential geographic/demographic bias
- No multi-center clinical validation
- MA segmentation remains challenging (Dice 0.42)

## Future Work

- [ ] ONNX export + FastAPI serving endpoint
- [ ] Semi-supervised learning with EyePACS (100k+ images)
- [ ] Attention U-Net for improved microaneurysm detection
- [ ] Mobile deployment with TFLite/CoreML
- [ ] HL7 FHIR integration for EHR systems

## Citation

If you use this work, please cite:
```
Araujo, E., Punguil, J., Tenezaca, P. (2026). 
Identificación de lesiones oculares empleando técnicas de Inteligencia Computacional.
Master's Thesis, Universidad Internacional de La Rioja (UNIR).
```

## License

MIT License — see LICENSE file for details.