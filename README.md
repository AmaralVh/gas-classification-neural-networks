# **Detecção e Classificação Multimodal de Gases com Redes Neurais**

## **1. Descrição Geral do Projeto**

O presente trabalho visa desenvolver um sistema de inteligência artificial para a **detecção e classificação de gases**, um problema de grande relevância para a segurança industrial, monitoramento da qualidade do ar e automação residencial. A capacidade de identificar a presença e o tipo de gás em um ambiente é crucial para prevenir acidentes, como intoxicações ou explosões, e para acionar sistemas de alerta ou ventilação.

Para alcançar uma detecção mais robusta e precisa, este projeto adota uma abordagem **multimodal**, combinando dados de duas fontes distintas: imagens de câmeras térmicas e medições de um conjunto de sensores de gás. A hipótese central é que, ao fundir informações de diferentes naturezas (visual e tabular), o sistema final se torna mais preciso e confiável do que seria ao utilizar cada fonte de dados isoladamente.

O projeto foi desenvolvido em duas etapas principais, culminando em um modelo de fusão que classifica um ambiente em uma de quatro categorias: **Sem Gás**, **Perfume**, **Fumaça** ou uma **Mistura** de perfume e fumaça.

---

## **2. Metodologia e Arquitetura**

A solução foi construída de forma incremental, desenvolvendo e avaliando modelos para cada modalidade individualmente antes de combiná-los.

### **Etapa 1: Classificação Unimodal com Imagens Térmicas**

O ponto de partida do projeto (Entrega 1) foi desenvolver um modelo capaz de classificar o ambiente apenas com base em sua imagem térmica. A hipótese é que as diferentes condições geram **assinaturas térmicas distintas** – seja pela dispersão de partículas ou por variações de temperatura – que podem ser capturadas por uma rede neural convolucional (CNN).

Para isso, utilizamos a técnica de **Aprendizagem por Transferência (Transfer Learning)** com a rede **MobileNetV3 Small**, pré-treinada no ImageNet. A estratégia consistiu em congelar as camadas de extração de características e treinar apenas uma nova camada classificadora, adaptada para as quatro classes do nosso problema.

### **Etapa 2: Classificação Unimodal com Sensores de Gás (Baseline)**

Paralelamente, desenvolvemos um segundo modelo especialista para a outra modalidade: os dados numéricos dos sensores de gás. Uma **Rede Neural Perceptron de Múltiplas Camadas (MLP)** foi implementada para processar as sete medições dos sensores. Este modelo, mais simples, aprende a encontrar padrões e correlações nos dados tabulares para realizar a mesma tarefa de classificação.

### **Etapa 3: Fusão Multimodal para Classificação Combinada**

A culminação do trabalho (Entrega 2) foi a integração das duas modalidades em um único sistema de decisão. Utilizamos uma arquitetura de **fusão tardia (late fusion)**, que funciona da seguinte forma:

1.  **Extração de Características:** O modelo de imagem (MobileNetV3) e o modelo de sensores (MLP), já treinados, atuam como **extratores de características** congelados. Para uma nova amostra, cada modelo processa sua respectiva entrada (imagem ou dados do sensor) e gera um vetor de características de alta dimensionalidade.

2.  **Concatenação:** As características extraídas de ambas as modalidades são **concatenadas** em um único vetor multimodal, que representa a informação combinada daquela amostra.

3.  **Classificação Final:** Este vetor combinado é então alimentado em uma nova e pequena **"cabeça de fusão"** (composta por camadas densas), que é a única parte do sistema treinada nesta etapa final. Ela aprende a interpretar as características combinadas e a tomar a decisão de classificação final.

---

## **3. Conjunto de Dados Utilizado**

Para este estudo, utilizamos o *dataset* público **"MultimodalGasData: Multimodal Dataset for Gas Detection and Classification"**. Este conjunto de dados foi ideal para o nosso propósito, pois fornece medições simultâneas de nossas duas modalidades de interesse.

-   **Fonte:** Narkhede, Parag; Walambe, Rahee ; Chandel, Pulkit; Mandaokar, Shruti; Kotecha, Ketan (2022), “MultimodalGasData: Multimodal Dataset for Gas Detection and Classification”, Mendeley Data, V2, doi: 10.17632/zkwgkjkjn9.2
-   **Modalidades:**
    -   **Imagens Térmicas:** Imagens no formato `.png` que capturam a assinatura de calor do ambiente.
    -   **Dados de Sensores:** Medições numéricas de sete sensores de gás de óxido de metal (MQ2, MQ3, MQ5, MQ6, MQ7, MQ8, MQ135).
-   **Pré-processamento:** As imagens foram redimensionadas e normalizadas. Os dados dos sensores também foram normalizados (usando `StandardScaler`) para garantir um treinamento estável na etapa de fusão.

---

## **4. Tecnologias Utilizadas**

-   **PyTorch:** Framework principal para a construção e treinamento de todas as redes neurais.
-   **Pandas:** Para carregamento, manipulação e unificação dos dados tabulares (CSVs).
-   **Scikit-learn:** Utilizado para o pré-processamento dos dados dos sensores (`StandardScaler`) e divisão dos dados.
-   **Matplotlib:** Para a visualização dos dados e geração de gráficos de acurácia.
-   **Jupyter Notebook:** Ambiente de desenvolvimento interativo utilizado para todo o projeto.
