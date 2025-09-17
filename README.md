# 🔬 Mejora de Videos de Laser Metal Deposition

Este repositorio proporciona una **pipeline para el preprocesamiento y mejora de videos de alta frecuencia** de procesos de **Laser Metal Deposition (LMD)**.  
Los videos contienen el **movimiento rectilíneo de una piscina de metal fundido** grabado en **blanco y negro con muy alto contraste**.  

Los principales objetivos de este proyecto son:
- 🖼️ **Recorte automático** de la Región de Interés (ROI) (~50 × 40 píxeles) para reducir el procesamiento innecesario.  
- 🎚️ **Mejora del contraste** usando ecualización adaptativa de histograma (CLAHE).  
- 🎥 **Reconstrucción de video a partir de frames** después del preprocesamiento.  
- 🚀 **Aumento de resolución espacial** de la pequeña ROI utilizando modelos de super-resolución de última generación (Real-ESRGAN, DRCT, RVRT).  

---

## 📂 Descripción del Flujo

1. **Detección y recorte automático de ROI**  
   - Detecta la piscina de metal a lo largo del video usando umbralización y contornos.  
   - Recorta todos los frames a una caja delimitadora consistente.  

2. **Umbralización (pre-CLAHE y post-CLAHE)**  
   - Reduce el ruido de fondo y resalta la piscina de metal fundido.  

3. **CLAHE (Contrast Limited Adaptive Histogram Equalization)**  
   - Mejora el contraste local y resalta detalles en la ROI.  

4. **Super-resolución**  
   - Aumenta la resolución espacial de la piscina de metal usando modelos de deep learning.  
   - Compatible con **Real-ESRGAN**, **DRCT** y **RVRT**.  

---

## ⚙️ Requerimientos

- Python 3.8+
- [OpenCV](https://opencv.org/)  
- [NumPy](https://numpy.org/)  
- [PyTorch](https://pytorch.org/)  
- [tqdm](https://github.com/tqdm/tqdm)  
- [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) (opcional para super-resolución)  
- [RVRT](https://github.com/JingyunLiang/RVRT) (opcional para super-resolución)  

Instalar dependencias:

```bash
pip install opencv-python numpy torch tqdm
```
## Uso
1. Recorte de ROI
```python
input_video = "data/raw/pool_original.avi"
output_frames_folder = "inputs"
output_video = "videos/recorte_auto.mp4"
umbral_recorte = 70

recorte_auto(input_video, output_frames_folder, output_video, umbral_recorte)
```
2. Umbralización (Pre-CLAHE)
```python
umbral_pre = 55
umbral("inputs", "results/threshold_pre", umbral_pre)
frames_to_video("results/threshold_pre", "videos/preCLAHE.mp4")
```
3. Aplicar CLAHE
```python
CLAHE("results/threshold_pre", "results/CLAHE", tg=8, cl=5)
frames_to_video("results/CLAHE", "videos/CLAHE.mp4")
```
4. Umbralización (Post-CLAHE)
```python
umbral_post = 30
umbral("results/CLAHE", "results/threshold_post", umbral_post)
frames_to_video("results/threshold_post", "videos/postCLAHE.mp4")
```
5. Super-Resolución (ejemplo con Real-ESRGAN)
```python
python inference_realesrgan.py -n RealESRGAN_x4plus \
    -i results/threshold_post \
    --fp32 --tile 200 --gpu-id 0
```
## 📊 Flujo del Proceso
1. A[Video de entrada] --> B[Recorte automático de ROI]
2. B --> C[Umbralización (Pre-CLAHE)]
3. C --> D[Mejora con CLAHE]
4. D --> E[Umbralización (Post-CLAHE)]
5. E --> F[Modelos de Super-Resolución]
6. F --> G[Video de salida mejorado]

## 📁 Estructura del Proyecto
```bash
├── inputs/                  # Frames recortados
├── results/
│   ├── threshold_pre/       # Frames umbralizados pre-CLAHE
│   ├── CLAHE/               # Frames mejorados con CLAHE
│   ├── threshold_post/      # Frames umbralizados post-CLAHE
│   └── sr_results/          # Resultados de super-resolución
├── videos/                  # Videos generados en cada etapa
├── main.py                  # Script principal con la pipeline de procesamiento
└── README.md
```
## 🚀 Resultados
- Tamaño de ROI de entrada: ~50 × 40 píxeles

- Salida final después de super-resolución x4: ~200 × 160 -píxeles

- Contraste mejorado y mayor visibilidad de la dinámica de la piscina de metal fundido.

## 📌 Notas
- Para aceleración con GPU, PyTorch debe detectar CUDA (torch.cuda.is_available()).

- Los modelos de super-resolución requieren instalación adicional (Real-ESRGAN, DRCT, RVRT).

- Esta pipeline puede adaptarse a otras tareas de imágenes industriales de alto contraste.


