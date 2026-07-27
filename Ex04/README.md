# Desafio 4 — Trilha de Visão Computacional
## Reconhecimento de Poses com YOLO11

Este projeto implementa uma solução de **reconhecimento de poses humanas** (pose estimation), técnica amplamente utilizada em análises esportivas, aplicações de saúde, segurança e interação humano-computador.

---

## 📁 Conteúdo do pacote

| Arquivo | Descrição |
|---|---|
| `VC_Desafio_4.ipynb` | Notebook Jupyter com a solução completa e comentada |
| `yolo11n-pose.pt` | Modelo pré-treinado da YOLO (Ultralytics) para estimativa de poses |
| `desafio_4.jpeg` | Imagem de exemplo usada para o teste (atletas de diferentes esportes) |
| `README.md` | Este arquivo |

---

## 🎯 Objetivo do desafio

Usar a biblioteca **Ultralytics YOLO** para detectar pessoas em uma imagem e extrair seus **keypoints** (pontos-chave do corpo: ombros, cotovelos, joelhos, etc.), desenhando o "esqueleto" sobre a imagem original.

---

## 🧩 Etapas implementadas

### 1. Instalação e importação das bibliotecas
```python
!pip install ultralytics matplotlib torch
import ultralytics
import matplotlib.pyplot as plt
import torch
```

### 2. Carregamento do modelo e definição do dispositivo

```python
model = ultralytics.YOLO("yolo11n-pose.pt")

device = "cuda" if torch.cuda.is_available() else "cpu"
model = model.to(device)

results = model("desafio_4.jpeg")
```

**O que foi implementado (TODO 1):**
A linha `device = "cuda" if torch.cuda.is_available() else "cpu"` verifica se há uma GPU (CUDA) disponível na máquina. Se houver, o modelo roda na GPU (muito mais rápido); caso contrário, cai automaticamente para a CPU. Isso evita erro caso o notebook seja rodado em máquinas sem placa de vídeo.

### 3. Visualização dos resultados

```python
im_array = results[0].plot()
rgb_img = cv2.cvtColor(im_array, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(10, 10))
plt.imshow(rgb_img)
plt.axis('off')
plt.show()
```

**O que foi implementado (TODO 2):**
O método `results[0].plot()` do Ultralytics devolve a imagem já com o esqueleto desenhado, mas no formato **BGR** (padrão do OpenCV — Blue, Green, Red). Como o `matplotlib` espera **RGB** (Red, Green, Blue), é necessário converter com `cv2.cvtColor(im_array, cv2.COLOR_BGR2RGB)`. Sem essa conversão, as cores da imagem sairiam trocadas (ex: azul e vermelho invertidos).

---

## 🔍 Diferenças em relação ao gabarito oficial

A **lógica central** (as duas linhas dos TODOs) é **idêntica** ao gabarito:

```python
device = "cuda" if torch.cuda.is_available() else "cpu"
rgb_img = cv2.cvtColor(im_array, cv2.COLOR_BGR2RGB)
```

A única diferença está na **forma de exibir a imagem final**:

| Gabarito | Esta implementação |
|---|---|
| `plt.imshow(rgb_img)` (deixa o Jupyter exibir automaticamente o retorno da célula) | `plt.figure(figsize=(10,10))` + `plt.imshow(rgb_img)` + `plt.axis('off')` + `plt.show()` |
| Sem controle de tamanho da figura | Figura ampliada (10x10) para melhor visualização |
| Eixos (x, y) aparecem no gráfico | Eixos removidos com `plt.axis('off')`, já que não fazem sentido numa foto |
| Depende do comportamento implícito do Jupyter (só funciona no notebook interativo) | Chamada explícita `plt.show()`, funciona também em scripts `.py` ou ao converter o notebook (`nbconvert`) |

**Resumo:** o resultado funcional é o mesmo (imagem com o esqueleto das poses detectadas), mas esta versão exibe a imagem de forma mais legível e de maneira mais robusta, independente do ambiente de execução.

---

## ▶️ Como executar

1. Extraia todos os arquivos do zip na mesma pasta.
2. Crie/ative o ambiente virtual `.env-trilha-vc` (ou outro de sua preferência).
3. Abra o notebook `VC_Desafio_4.ipynb` no Jupyter.
4. Execute as células em ordem.

> A imagem `desafio_4.jpeg` e o modelo `yolo11n-pose.pt` precisam estar na **mesma pasta** do notebook, pois são referenciados por caminho relativo.

---

## 📚 Referência

- [Documentação oficial — Ultralytics Pose Estimation](https://docs.ultralytics.com/tasks/pose)
