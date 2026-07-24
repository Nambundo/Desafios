# Desafio 1 da Trilha de Visão Computacional --- Solução 

Este notebook resolve o **mesmo desafio** (borrar o fundo de uma imagem contendo uma pessoa, tipo efeito de vídeo chamada), mas com uma **implementação diferente** da que aparece no gabarito oficial, para fins de estudo e comparação.

**Principais diferenças em relação ao gabarito:**

1. Em vez de recortar a face com fatiamento retangular (`image[y:h, x:w]`) e colar de volta, aqui construímos uma **máscara** (imagem preta e branca) que marca onde está a face, e usamos essa máscara para combinar a imagem original com a imagem borrada. Isso é o mesmo princípio usado em ferramentas de recorte/composição em Visão Computacional (ex: `cv2.bitwise_and`, blending com máscara).

2. A máscara usa uma **elipse** ao invés de um retângulo, e passa por um **blur** para suavizar as bordas — isso evita o "corte" abrupto e visível entre a área nítida do rosto e o fundo borrado que aparece na solução do gabarito.

3. O código funciona corretamente para **múltiplas faces** na imagem. No gabarito, se `detectMultiScale` encontrar mais de um rosto, o `for` sobrescreve as variáveis `x, y, width, height, face_img` a cada iteração e, no final, só a **última face detectada** fica nítida — as demais ficam borradas. Aqui, cada face detectada é adicionada à máscara.

4. No final, comparamos visualmente a imagem original, a máscara, a versão totalmente borrada e o resultado final lado a lado.

Ao final do notebook há uma seção explicando essas diferenças em mais detalhes.

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
