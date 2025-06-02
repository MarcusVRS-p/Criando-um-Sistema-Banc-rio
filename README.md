# Criando-um-Sistema-Banc-rio
from PIL import Image, ImageFilter

# image_utils/core.py

def abrir_imagem(caminho):
    """Abre uma imagem a partir do caminho."""
    return Image.open(caminho)

def aplicar_blur(imagem):
    """Aplica um filtro de desfoque (blur) à imagem."""
    return imagem.filter(ImageFilter.BLUR)

def redimensionar(imagem, tamanho):
    """Redimensiona a imagem para o tamanho especificado (largura, altura)."""
    return imagem.resize(tamanho)

# image_utils/__init__.py
from .core import abrir_imagem, aplicar_blur, redimensionar

# tests/test_core.py
from image_utils import redimensionar
from PIL import Image

def test_redimensionar():
    imagem = Image.new("RGB", (100, 100))
    nova = redimensionar(imagem, (50, 50))
    assert nova.size == (50, 50)

# setup.py
from setuptools import setup, find_packages

setup(
    name="image-utils",
    version="0.1.0",
    packages=find_packages(),
    install_requires=[
        "Pillow>=9.0.0"
    ],
    author="Seu Nome",
    description="Pacote utilitário para processamento de imagens com Pillow",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/seu-usuario/image-utils",
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent"
    ],
    python_requires='>=3.7',
)

# README.md
"""
# Image Utils

Pequeno pacote de utilitários para processamento de imagens com Pillow.

## Instalação

```bash
pip install git+https://github.com/seu-usuario/image-utils.git
```

## Uso

```python
from image_utils import abrir_imagem, aplicar_blur

img = abrir_imagem("exemplo.jpg")
img_blur = aplicar_blur(img)
img_blur.show()
```
"""

# .gitignore
__pycache__/
*.pyc
.DS_Store
venv/
.env

# LICENSE (MIT)
"""
MIT License

Copyright (c) 2025 Seu Nome

Permission is hereby granted, free of charge, to any person obtaining a copy
... [completo depois]
"""

