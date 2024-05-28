# UTS Pengolahan Citra

## Anggota Kelompok
| Nim  | Nama | Kelas | 
| ------------- | ------------- | ------------- |
| 312210224 | Johanes Mula Febrian Sihombing | TI. 22. A2  |
| 312210191 | Riski Probo Sadewo | TI. 22. A2  |
| 312210223 | Faiz Dzaki Ramadhani | TI. 22. A2  |

## Project Web App untuk memanipulasi gambar citra menggunakan Streammlite dengan mengubah 
#### - RGB menjadi HSV
#### - Menghitung Histogram
#### - Brignest dan Contras
#### - Contour

### Menginstal library yang diperlukan, yaitu Streamlit untuk membuat aplikasi web, OpenCV untuk manipulasi citra, dan Matplotlib untuk menampilkan histogram.

```
pip install streamlit opencv-python matplotlib numpy
```

### Buat file Python (misalnya main.py) dan impor library.

```
import streamlit as st
import cv2
from matplotlib import pyplot as plt
import numpy as np
```

### Membuat Fungsi Manipulasi Citra, untuk 'Konversi RGB ke HSV'

```
def convert_rgb_to_hsv(image):
    return cv2.cvtColor(image, cv2.COLOR_RGB2HSV)
```

### Menghitung Histogram

```
def plot_histogram(image):
    color = ('b', 'g', 'r')
    for i, col in enumerate(color):
        hist = cv2.calcHist([image], [i], None, [256], [0, 256])
        plt.plot(hist, color=col)
        plt.xlim([0, 256])
    plt.title('Histogram')
    return plt
```

### Mengatur Brightness dan Contrast

```
def adjust_brightness_contrast(image, brightness=0, contrast=0):
    new_image = np.zeros(image.shape, image.dtype)
    alpha = contrast * 0.01
    beta = brightness

    # Adjustment made here
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            for c in range(image.shape[2]):
                new_image[y,x,c] = np.clip(alpha*image[y,x,c] + beta, 0, 255)
    
    return new_image
```

### Mendeteksi Kontur

```
def find_contours(image):
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    blurred = cv2.GaussianBlur(gray, (5, 5), 0)
    edged = cv2.Canny(blurred, 50, 150)
    contours, _ = cv2.findContours(edged.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    image_with_contours = image.copy()
    cv2.drawContours(image_with_contours, contours, -1, (0, 255, 0), 3)
    return image_with_contours
```

### Langkah Selanjutnya Streamlit Interface

```
st.title('Image Manipulation App')

uploaded_file = st.file_uploader("Choose an image...", type=["jpg", "jpeg", "png"])
if uploaded_file is not None:
    file_bytes = np.asarray(bytearray(uploaded_file.read()), dtype=np.uint8)
    image = cv2.imdecode(file_bytes, cv2.IMREAD_COLOR)

    st.image(image, caption='Original Image', use_column_width=True)

    if st.button('Convert RGB to HSV'):
        hsv_image = convert_rgb_to_hsv(image)
        st.image(hsv_image, caption='HSV Image', use_column_width=True)

    if st.button('Show Histogram'):
        plt = plot_histogram(image)
        st.pyplot(plt)

    brightness = st.slider("Brightness", min_value=-100, max_value=100, value=0)
    contrast = st.slider("Contrast", min_value=-100, max_value=100, value=0)
    if st.button('Adjust Brightness and Contrast'):
        bc_image = adjust_brightness_contrast(image, brightness, contrast)
        st.image(bc_image, caption='Brightness and Contrast Adjusted Image', use_column_width=True)

    if st.button('Find Contours'):
        contours_image = find_contours(image)
        st.image(contours_image, caption='Image with Contours', use_column_width=True)
```

### Untuk menjalankan aplikasi, bisa menggunakan terminal CMD dengan perintah :

```
streamlit run app.py
```
