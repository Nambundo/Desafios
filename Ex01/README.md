# Desafio 1 da Trilha de Visão Computacional --- Solução Alternativa

## Descrição

Este projeto apresenta uma solução alternativa para o desafio de Visão
Computacional cujo objetivo é aplicar o efeito de fundo desfocado
(background blur) em uma imagem contendo uma pessoa, semelhante ao
utilizado em aplicativos de videoconferência.

A implementação utiliza **OpenCV** e o classificador **Haar Cascade**
para detectar o rosto. A partir da posição do rosto é criada uma região
de interesse que representa a pessoa, preservando essa área nítida
enquanto o restante da imagem recebe um filtro Gaussiano.

## Como a solução funciona

1.  Carrega a imagem de entrada.
2.  Converte a imagem para escala de cinza.
3.  Detecta o rosto usando `haarcascade_frontalface_default.xml`.
4.  Cria uma máscara ao redor do rosto, expandindo a região para incluir
    parte do corpo.
5.  Aplica um desfoque Gaussiano em toda a imagem.
6.  Combina a imagem original com a imagem desfocada usando a máscara,
    mantendo a pessoa em foco e o fundo borrado.
7.  Exibe e/ou salva o resultado final.

## Tecnologias utilizadas

-   Python 3
-   OpenCV (cv2)
-   NumPy
-   Matplotlib
-   Haar Cascade (OpenCV)

## Estrutura

-   `VC_Desafio_1_resolvido.ipynb` -- Notebook com toda a implementação.
-   `haarcascade_frontalface_default.xml` -- Modelo de detecção facial.
-   `desafio_1.jpeg` -- Exemplo de imagem de entrada.

## Como executar

### 1. Instale as dependências

``` bash
pip install opencv-python numpy matplotlib
```

### 2. Coloque os arquivos na mesma pasta

-   VC_Desafio_1_resolvido.ipynb
-   haarcascade_frontalface_default.xml
-   desafio_1.jpeg

### 3. Execute o notebook

Abra o notebook no Jupyter Notebook ou Google Colab e execute todas as
células em ordem.

## Resultado esperado

Ao final da execução: - O rosto é detectado automaticamente. - A região
da pessoa permanece nítida. - O fundo recebe um efeito de desfoque
suave, simulando o efeito de vídeo chamada.

## Observações

Esta implementação foi desenvolvida como uma alternativa ao gabarito
oficial para fins de estudo e comparação de abordagens. O método pode
ser aprimorado utilizando técnicas modernas de segmentação, como
MediaPipe Selfie Segmentation, U²-Net ou YOLO-Seg.
