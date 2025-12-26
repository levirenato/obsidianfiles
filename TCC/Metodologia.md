1. Extração de Features: Foco Espectral e Baixa Dimensionalidade

Este passo é o coração da sua metodologia de eficiência, garantindo que o seu classificador trabalhe com um conjunto de dados pequeno e altamente discriminativo, em vez de processar pixels brutos em alta dimensão.

• **LNP e DFT/FFT:** 

A abordagem de **Análise de Padrão de Ruído Local (LNP)**, seguida pela **Discrete Fourier Transform (DFT)** , é explicitamente projetada para extrair a "comunalidade" das imagens reais ou as características periódicas das imagens geradas. A amplitude do espectro de ruído (LNP DFT Amp) é considerada a _feature_ mais forte para detecção. O objetivo de obter **vetores de características de baixa dimensão** é atingido com sucesso por este método; por exemplo, para uma imagem de 256x256, a _feature_ final F(m,n) pode ter **apenas 64 dimensões**


• **Filtros de Alta Passagem (Cross-Difference):** Alternativamente, o método _Synthbuster_ utiliza um filtro de **diferença cruzada** (_cross-difference_), que atua como um filtro passa-altas simples, para realçar artefatos de frequência

. A **Fast Fourier Transform (FFT)** é então aplicada ao resíduo, e o classificador usa apenas a **magnitude dos picos** em frequências específicas (períodos 0, 2, 4 e 8). Este também é um método de baixa dimensionalidade (por exemplo, 135 magnitudes)


• **Conclusão da Etapa:** Você usaria um desses mecanismos (LNP+DFT ou Filtro Residual+FFT) para gerar uma representação de dados que é inerentemente mais rápida de processar do que o uso de CNNs no domínio espacial


2. Modelo de Classificação Eficiente: Classificação de Uma Classe (OC)

O uso da Classificação de Uma Classe (_One-class Classification_ - OC) é o segundo pilar da sua estratégia de eficiência, otimizando o tempo e a necessidade de dados de treinamento.

• **Foco Apenas em Imagens Reais:** A metodologia OC se concentra em mapear imagens reais (T) para um subespaço denso (S)

. Isso é feito treinando o modelo **apenas com imagens reais**. Imagens geradas são então detectadas como anomalias por caírem fora desse subespaço


• **Eficiência de Treinamento e Dados:** Essa abordagem é notavelmente eficiente, pois requer **99.9% menos dados de treinamento sintéticos** do que os métodos tradicionais de _deep learning_

. A exclusão de imagens geradas para o treinamento (o que é um obstáculo em métodos de classificação binária tradicionais) torna a abordagem mais concisa e eficiente para a criação do classificador

.

3. Comparação: Quantificação do Ganho de Eficiência

Este passo é crucial para validar a sua proposta de TCC, pois demonstrará o benefício do seu foco metodológico (análise espectral) sobre as abordagens convencionais (redes neurais convolucionais complexas).

• **Velocidade de Inferência (Imagens/s):** Você deve medir a velocidade do seu modelo (por exemplo, OC-SVM treinado com _features_ DFT/FFT) em termos de imagens por segundo. O artigo que propõe a metodologia baseada em LNP+DFT/OC-SVM atinge um **alto rendimento de inferência de 413.2 imagens/s** (ao usar o módulo de denoising CycleISP)


• **Linha de Base de Acurácia (CNN Complexa):** Você pode usar os resultados de acurácia de modelos de classificação binária que utilizam CNNs complexas, como a que você mencionou. Por exemplo, o estudo CIFAKE alcançou **92.98% de acurácia** (e F1-score de 0.936) com uma CNN (duas camadas de 128 filtros) para a classificação binária de imagens reais versus imagens geradas por Modelo de Difusão Latente (LDM).



---

### Metodologia: Detecção Eficiente Baseada na Análise Espectral

A metodologia proposta inverte o foco da detecção, concentrando-se nas características intrínsecas das imagens reais e na análise dos artefatos de frequência, buscando alta **eficiência** e **generalização**.

#### FASE 1: Extração de Features Espectrais de Baixa Dimensionalidade

Esta fase concentra-se em isolar os artefatos visuais (o "ruído") deixados pelos modelos de geração e transformá-los em vetores de dados pequenos e discriminativos (baixa dimensionalidade).

1. **Isolamento do Ruído (LNP - Learned Noise Pattern):**
    
    - A imagem de entrada ($I$) é processada para isolar seu padrão de ruído ($n(x, y)$), que é a diferença entre a imagem original e sua versão "limpa" (filtrada).
    - $$n(x, y) = I(x, y)− \Xi(I(x, y))$$, onde $\Xi(\cdot)$ é um filtro de _denoising_ (como um modelo inspirado no CycleISP).
    - O ruído de imagens geradas por I.A. frequentemente exibe **artefatos de grade** (_grid artifacts_) no domínio espacial que são ausentes em imagens reais.
2. **Transformação para o Domínio da Frequência:**
    
    - O padrão de ruído ($n(x, y)$) é transformado para o domínio da frequência utilizando a **Discrete Fourier Transform (DFT)**.
    - Para garantir a **eficiência** computacional, a **DFT** é implementada através do algoritmo **Fast Fourier Transform (FFT)**.
    - A **Magnitude** do espectro de amplitude do LNP é extraída, pois ela revela a **periodicidade** característica de imagens sintéticas, o que é a _feature_ mais forte para detecção.
3. **Redução de Dimensionalidade:**
    
    - Para evitar redundância e maximizar a eficiência, um vetor de características de **baixa dimensão** $F(m, n)$ é construído por **amostragem** (_sampling_) do espectro de magnitude do LNP usando uma função de impulso 2D ($\delta(\cdot)$). Esta etapa pode reduzir drasticamente as _features_ para dimensões muito menores (por exemplo, 64 dimensões para uma imagem 256x256).

#### FASE 2: Modelo de Classificação Eficiente (One-Class Classification - OC)

Em vez de treinar um modelo para distinguir "Real" de "Falso" (o que exigiria retreinamento constante com novos modelos falsos), esta fase foca na **Classificação de Uma Classe (OC)**, treinando o modelo **apenas em imagens reais**.

1. **Treinamento Focado em Imagens Reais:**
    
    - A nova perspectiva proposta é **começar pelas imagens reais**. O modelo (tipicamente um classificador de uma classe como o OC-SVM) é treinado com os vetores de _features_ espectrais de baixa dimensão obtidos **apenas de imagens reais** ($T$).
    - O objetivo é mapear as características de imagens reais para um **subespaço denso** ($S$), utilizando um hiperplano como limite de decisão.
2. **Detecção por Anomalia (Classificação):**
    
    - Durante a inferência, qualquer imagem que não possua as características espectrais esperadas do domínio real (ou seja, imagens geradas por I.A.) será projetada **fora do subespaço $S$**.
    - Este método oferece **excelente desempenho** de detecção e generalização contra modelos não vistos.
3. **Vantagem na Eficiência de Treinamento:**
    
    - Esta abordagem supera o problema de alto custo de treinamento e inferência dos métodos tradicionais, alcançando bons resultados com o uso de **99.9% menos dados de treinamento sintéticos**.

#### FASE 3: Avaliação e Comparação de Desempenho

O passo final da metodologia consiste em quantificar o ganho de eficiência do modelo espectral/OC em comparação com métodos que dependem de redes neurais convolucionais (CNNs) complexas.

1. **Métricas de Eficiência (Velocidade de Inferência):**
    
    - Medir a **velocidade de inferência** do modelo (em imagens/s). Abordagens similares baseadas em LNP/DFT demonstram um **alto rendimento de inferência** de até **413.2 imagens/s**.
    - Comparar esta velocidade com a de arquiteturas mais complexas que realizam a classificação binária.
2. **Métricas de Desempenho (Acurácia/Generalização):**
    
    - Avaliar a Acurácia (ACC), F1-Score, e a **Robustez** do modelo contra operações de pós-processamento como _Blurring_ ou compressão.
    - Comparar o desempenho com o de modelos de classificação binária (_deep learning_ no domínio espacial), como as CNNs que atingem aproximadamente **92.98% de acurácia** e F1-scores de **0.936** em datasets como CIFAKE.