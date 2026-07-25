# Desafio 2 — Reconhecimento Facial

Este README documenta o que foi feito neste notebook do Desafio 2 da Trilha de Visão Computacional, para servir de referência de estudo e comparação.

## Objetivo do desafio (igual em ambos)

> Implementar uma solução simples de reconhecimento facial, semelhante às utilizadas em sistemas de controle de acesso a áreas restritas.

## O que permanece igual ao gabarito

Para não alterar o essencial do exercício, os **mesmos três modelos pré-treinados** do gabarito foram mantidos:

| Modelo | Arquivo | Função |
|---|---|---|
| Detector de rosto | `deploy.prototxt` + `res10_300x300_ssd_iter_140000.caffemodel` | Encontra a posição do(s) rosto(s) na imagem (SSD com backbone ResNet-10, via `cv2.dnn`) |
| Extrator de embedding | `openface_nn4_small2_v1.t7` | Transforma o rosto recortado em um vetor de 128 posições (rede OpenFace, via `cv2.dnn`) |

O fluxo geral também continua o mesmo do gabarito: **detectar → extrair embedding → decidir quem é a pessoa**. Nenhuma rede neural foi treinada do zero — igual ao gabarito, tudo aqui usa pesos já prontos.

## O que foi implementado de forma diferente

A diferença está concentrada nas partes **de código e de lógica de decisão**, não nos modelos pesados. Resumo:

| Aspecto | Gabarito | Esta solução |
|---|---|---|
| Classificador | Treina um **SVM** (`sklearn.svm.SVC`, kernel linear) sobre todos os embeddings individuais | Calcula o **centróide** (vetor médio, normalizado) dos embeddings de cada pessoa — **sem treinar nenhum modelo de classificação** |
| Decisão de identidade | `recognizer.predict_proba(vec)` do SVM | **Similaridade de cosseno** entre o embedding testado e cada centróide |
| Rosto desconhecido | O SVM **sempre** escolhe uma das pessoas cadastradas, mesmo com baixíssima confiança | Um **limiar** (`LIMIAR_RECONHECIMENTO`) decide: se a maior similaridade não passar do limiar, o rosto é classificado como `"Desconhecido"` |
| Organização do código | O bloco de "detectar rosto + gerar embedding" aparece **duplicado**: uma vez na célula que processa o dataset, outra (quase idêntica) na célula que testa uma imagem | A mesma lógica foi escrita **uma única vez**, em duas funções reutilizáveis: `detectar_rostos()` e `extrair_embedding()` — usadas tanto no treino quanto no teste |
| Escala do teste | Testa **uma única imagem**, escolhida manualmente (`test/modelo_2_1.jpeg`) | Testa **automaticamente todas as imagens** da pasta `test/`, mostrando os resultados em um grid |
| Persistência | Salva `embeddings.pickle`, `recognizer.pickle` e `le.pickle` (3 arquivos) | Salva um único `centroides.pickle` (nome da pessoa → vetor centróide) |
| Adicionar uma pessoa nova | Precisa reprocessar o dataset inteiro e **re-treinar o SVM do zero** | Basta calcular o centróide da pessoa nova; as demais pessoas não são afetadas |

## Por que essas mudanças

- **Resolve a maior lacuna funcional do gabarito**: em um sistema de controle de acesso de verdade, é tão importante reconhecer quem tem permissão quanto **admitir quando não reconhece ninguém cadastrado**. O gabarito, por depender só de um SVM com `argmax`, nunca faz isso — ele sempre "aposta" em uma das pessoas do dataset. A similaridade de cosseno com limiar resolve isso de forma nativa, sem gambiarra em cima do classificador.
- **Reduz duplicação de código**: mais fácil de manter e de explicar (mudar o limiar de confiança, por exemplo, é uma alteração em um único lugar).
- **Mantém o custo de implementação baixo**: como os modelos pesados (detector + embedder) continuam os mesmos do gabarito, não há necessidade de instalar bibliotecas extras (como faria uma migração para FaceNet/ArcFace/InsightFace) nem de lidar com formatos de modelo diferentes — só muda a lógica de decisão, que é a parte mais barata e didática de trocar.

## Sobre as alternativas mais modernas (FaceNet / ArcFace / InsightFace)

Durante o desenvolvimento, também foi analisada a possibilidade de substituir o par SSD+OpenFace por arquiteturas mais recentes (FaceNet, ArcFace via InsightFace, RetinaFace, MediaPipe, YOLO Face) e métodos clássicos (LBPH, EigenFaces, FisherFaces). A conclusão dessa análise:

- **ArcFace + detector com landmarks (via InsightFace)** seria a opção tecnicamente mais forte, pois resolve as duas maiores fraquezas do gabarito de forma nativa: falta de **alinhamento facial** (o gabarito só recorta o rosto, sem alinhar olhos/boca a uma pose padrão) e ausência de tratamento de **"desconhecido"**. Só que exige trocar toda a etapa de detecção, instalar bibliotecas adicionais (`insightface`, `onnxruntime`) e sair do escopo "manter os mesmos arquivos do gabarito".
- **FaceNet + SVM** seria um meio-termo (embedder diferente, mesmo classificador do gabarito) — evolução direta do OpenFace usado no gabarito, mas mantém a limitação de nunca dizer "desconhecido" a menos que se remende essa lógica por fora do SVM.
- A abordagem final escolhida (**mesmos modelos do gabarito + centróide/cosseno**) foi a que melhor equilibrou: diferença real de implementação, correção da lacuna mais importante (desconhecido), e menor esforço de setup — adequada ao pedido de "seguir o gabarito sem alterar muita coisa, mas implementar de forma diferente".

## Estrutura de pastas deste pacote

```
VC_Desafio_2_Alternativo.ipynb
face_detection_model/
    deploy.prototxt
    res10_300x300_ssd_iter_140000.caffemodel
openface_nn4_small2_v1.t7
dataset/
    pessoa_1/   → 7 fotos
    pessoa_2/   → 7 fotos
    pessoa_3/   → 7 fotos
test/           → fotos para testar o reconhecimento
output/         → onde o notebook salva os centróides calculados (centroides.pickle)
```

## Limitações conhecidas (mesmas apontadas dentro do notebook)

- O `LIMIAR_RECONHECIMENTO` (0.5) foi escolhido de forma arbitrária; idealmente deveria ser calibrado testando fotos de pessoas conhecidas e desconhecidas.
- Sem alinhamento facial (herdado do gabarito) — o embedding pode variar mais do que o ideal em fotos com ângulo de cabeça muito diferente do frontal.
- Um classificador treinado (SVM ou k-NN) ainda pode aprender fronteiras de decisão mais sofisticadas do que "distância até a média"; a abordagem por centróide prioriza simplicidade e interpretabilidade em vez de poder de decisão máximo.
