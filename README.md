# Projeto Avaliativo - Módulo 1: Previsão de Churn - Análise Estratégica e Machine Learning para Retenção de Clientes


## 📄 1. Descrição do Projeto

Este projeto, desenvolvido como parte do Módulo 1 (Fundamentos de Programação, Dados e Machine Learning) do curso **Machine Learning e Visão Computacional** do programa **SCTEC** 2026, implementa o desenvolvimento de um pipeline completo de Ciência de Dados voltado para a identificação precoce e mitigação do *churn* (cancelamento de clientes) em uma plataforma de e-commerce. O projeto abrange desde a análise exploratória e tratamento estatístico dos dados até o treinamento, tunagem e avaliação de modelos preditivos com foco no impacto financeiro do negócio.


## 📁 2. Estrutura do Projeto

- `src/`: Diretório destinado ao armazenamento do arquivo principal com o desenvolvimento do código passo a passo:
    - `ecommerce_churn_prediction.ipynb`: Notebook contendo o pipeline completo documentado, desde o carregamento até as matrizes de confusão finais.
- `data/`: Diretório local destinado ao armazenamento do conjunto de dados.


## 🎯 3. Problema de Negócio

No cenário altamente competitivo do e-commerce, reter um cliente ativo é consideravelmente mais barato e estrategicamente mais viável do que adquirir um novo. O custo de aquisição de cliente (CAC) agressivo, somado à perda do valor do tempo de vida do cliente (*Lifetime Value* - LTV), faz com que o *churn* seja uma das métricas mais críticas para a saúde financeira e sustentabilidade de uma operação digital.

**Objetivo Estratégico:** Construir um modelo preditivo capaz de identificar clientes com alta propensão ao cancelamento antes que a evasão ocorra de fato. Com esses alarmes antecipados e explicáveis, a equipe de marketing e CRM pode disparar campanhas de retenção direcionadas (como cupons de desconto, ofertas exclusivas e priorização no atendimento), otimizando o retorno sobre o investimento (ROI) e estancando a perda de receita.


## 📊 4. Principais Insights da Análise Exploratória (EDA)

A análise de correlação linear (Pearson) e o estudo das distribuições trouxeram respostas fundamentais sobre os gatilhos que levam o consumidor a deixar a plataforma:

*   **A Atrito no Atendimento como Gatilho Direto (`Complain`):** A variável de reclamações formais registrou a maior correlação positiva com o *churn* (~0.3). Isso prova que falhas na experiência de compra ou problemas não resolvidos pelo suporte são motivadores imediatos para a perda do cliente. **Ação:** Priorização em tempo real e tratamento ágil para chamados de clientes que abriram reclamações.
*   **A Curva de Adaptação do Novo Cliente (`Tenure`):** Identificou-se uma forte correlação negativa (~-0.3) entre o tempo de casa do cliente e o *churn*. Consumidores recém-chegados possuem uma probabilidade significativamente maior de abandonar a plataforma se enfrentarem fricções iniciais (navegação confusa, problemas na primeira entrega). **Ação:** Fortalecimento do *onboarding*, garantias de sucesso nas primeiras compras e cupons focados em reativar clientes recentes.
*   **Alerta de Multicolinearidade:** O mapa de calor revelou dependências lineares importantes entre variáveis explicativas, em especial entre `CouponUsed` e `OrderCount` (correlação forte de 0.7), além de `Tenure` e `CashbackAmount` (0.5). Essa proximidade evidencia riscos de redundância estatística, exigindo monitoramento ou técnicas de regularização durante a modelagem para evitar instabilidade nos coeficientes.


## 🛠️ 5. Pipeline de Engenharia de Dados e Modelagem

Para garantir que os modelos aprendessem regras de generalização robustas sem o risco de vazamento de dados (*data leakage*), o pipeline foi estruturado da seguinte forma:

1.  **Split Estratificado Antecipado:** Divisão em 80% treino e 20% teste mantendo estritamente a proporção original de *churn* (~16.8%).
2.  **Imputação Dinâmica de Nulos:** Aplicação da Média para variáveis com distribuição simétrica ($|skew| < 0.5$) e Mediana para distribuições assimétricas ($|skew| \ge 0.5$).
3.  **Tratamento de Outliers:** Aplicação de técnica de *Clipping* baseada nos limites da Amplitude Interquartílica (IQR).
4.  **Engenharia de Features:** Criação da métrica `cashback_por_pedido` para mensurar a percepção de valor por transação.
5.  **Tratamento do Desbalanceamento:** Utilização do algoritmo SMOTE exclusivamente nos dados de treino para equilibrar o aprendizado das classes.


## 🏆 6. Veredito do Melhor Modelo

Após testes extensivos de hiperparâmetros rastreando curvas de aprendizado contra o *overfitting*, as duas melhores configurações finalistas foram avaliadas na base de testes desbalanceada (cenário real):

| Métrica (Classe 1 - Churn) | KNN ($k=3$) | Árvore de Decisão (`max_depth=15`) |
| :--- | :---: | :---: |
| **Precisão** | 52% | **77%** |
| **Recall (Sensibilidade)** | **96%** | 77% |
| **F1-Score** | 68% | **77%** |
| **Acurácia Geral** | 84% | **92%** |

### Justificativa de Negócio para a Escolha da Árvore de Decisão:

Embora o KNN ($k=3$) tenha apresentado um *Recall* isolado mais alto (96%), sua baixa Precisão (52%) geraria um volume insustentável de alarmes falsos (Falsos Positivos). Na prática, isso significaria desperdiçar o orçamento de marketing disparando cupons agressivos de retenção para uma enorme quantidade de clientes que continuariam fiéis de qualquer maneira, corroendo desnecessariamente a margem de lucro da empresa.

A **Árvore de Decisão com `max_depth=15`** provou ser a solução ideal por três motivos centrais:
1.  **Equilíbrio Ótimo (F1-Score de 78%):** O modelo entrega alta assertividade tanto para identificar quem vai sair quanto para não gerar ruído com alarmes falsos.
2.  **Mitigação do Pior Erro (Falso Negativo):** Deixar de identificar um cliente em risco de *churn* custa o valor total do LTV e exige um CAC elevado para substituição. Com um *Recall* robusto de 77%, a árvore mitiga de forma eficiente essas omissões financeiramente dolorosas.
3.  **Interpretabilidade e Ação Comercial:** Estruturas de árvore geram nós lógicos inteligíveis (*"Se o cliente tem menos de 3 meses de casa AND abriu uma reclamação..."*). Isso permite que o time de CRM implemente réguas lógicas transparentes e automatizadas diretamente no sistema, personalizando a abordagem preventiva conforme o real motivo do atrito.
