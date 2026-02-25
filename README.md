# Detección de Lesiones Retinianas para Retinopatía Diabética

[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/downloads/release/python-3100/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.1-red.svg)](https://pytorch.org/)
[![Licencia: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Un piloto experimental que aplica técnicas de inteligencia computacional para identificar automáticamente lesiones oculares asociadas a la retinopatía diabética (RD) en imágenes de fondo de ojo, logrando un **Dice Score de 0.717** en el conjunto de datos de referencia IDRiD.

## Resumen de Resultados

| Modelo | Tarea | Mejor Métrica | Configuración |
|-------|------|-------------|---------------|
| YOLOv8 | Detección | mAP@0.5: 0.86, F1: 0.77 | 960×960, Adam |
| U-Net + EfficientNetB3 | Segmentación | Dice: 0.717, IoU: 0.717 | PyTorch, JointLoss |
| MobileNetV2 | Clasificación | Exactitud: 86.2%, F1: 0.84 | Fine-tuning, SGD |

### Resultados de Segmentación vs. Base (Baseline)

| Arquitectura | Mean IoU | Parámetros | Estado |
|---|---|---|---|
| U-Net + EfficientNetB3 | **0.717** | 12.2M | ✅ Mejor |
| FPN + ResNet34 | 0.690 | 22.1M | Competitivo |
| FPN + EfficientNetB3 | 0.690 | 13.4M | Estable |
| U-Net + VGG16 (base) | 0.683 | 23.7M | Referencia |
| U-Net + ResNet34 | 0.671 | 21.8M | Limitado |

## Definición del Problema

La retinopatía diabética (RD) es una de las principales causas de ceguera prevenible en adultos. El diagnóstico tradicional mediante la inspección manual de imágenes de fondo de ojo está limitado por la subjetividad, el tiempo y la disponibilidad de especialistas.

Este sistema proporciona detección automatizada, localización y segmentación a nivel de píxel de las lesiones de RD para apoyar la toma de decisiones clínicas.

## Arquitectura

El sistema utiliza un **enfoque híbrido de tres módulos**:
1. **MobileNetV2**: Clasificación de severidad.
2. **YOLOv8**: Detección de lesiones y cuadros delimitadores.
3. **U-Net + EfficientNetB3**: Segmentación a nivel de píxel.

### Clases de Lesiones Analizadas

- **Microaneurismas (MA):** Puntos rojos pequeños, etapa temprana.
- **Hemorragias (HE):** Sangrados en la retina.
- **Exudados Duros (EX):** Depósitos lipídicos.
- **Exudados Blandos (SE):** Manchas algodonosas (isquemia).
- **Disco Óptico (OD):** Punto de referencia anatómico.

## Instalación
```bash
git clone [https://github.com/pedroalexandertenezaca/lesiones-oculares-rd-ftm](https://github.com/pedroalexandertenezaca/lesiones-oculares-rd-ftm)
cd lesiones-oculares-rd-ftm
pip install -r requirements.txt
```

## Conjunto de Datos (Dataset)

IDRiD (Indian Diabetic Retinopathy Image Dataset)

Importante: El dataset no está incluido en este repositorio. Descárguelo de [IEEE DataPort](https://ieee-dataport.org/open-access/indian-diabetic-retinopathy-image-dataset-idrid).

## Citación

Si utiliza este trabajo, por favor cite:
```
Araujo, E., Punguil, J., Tenezaca, P. (2026). 
Identificación de lesiones oculares empleando técnicas de Inteligencia Computacional.
Tesis de Maestría, Universidad Internacional de La Rioja (UNIR).
```

## Licencia

Este proyecto está bajo la Licencia MIT.