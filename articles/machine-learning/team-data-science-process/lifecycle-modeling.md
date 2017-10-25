---
title: "Estágio de modelagem do Ciclo de Vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados para o estágio de modelagem dos seus projetos de ciência de dados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>Modelagem

Este tópico descreve as metas, as tarefas e os resultados associados ao **estágio de modelagem** do Processo de Ciência de Dados da Equipe. Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Metas
* Recursos de dados ideais para o modelo de machine learning.
* Um modelo de ML informativo que prevê a meta com mais precisão.
* Um modelo de ML adequado para a produção.

## <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

* **Engenharia de recursos**: crie recursos de dados dos dados brutos para facilitar o treinamento do modelo.
* **Treinamento do modelo**: encontre o modelo que responde à pergunta com mais precisão, comparando suas métricas de sucesso.
* Determine se o modelo é **adequado para produção**.

### <a name="31-feature-engineering"></a>3.1 Engenharia de recursos
A engenharia de recursos envolve a inclusão, agregação e transformação de variáveis brutas para criar os recursos usados na análise. Se você desejar obter análises sobre o que está guiando um modelo, você precisa entender como os recursos estão relacionados entre si e como os algoritmos de machine learning devem ser usar esses recursos. Esta etapa exige uma combinação criativa das competências de domínio e análises obtidas na etapa de exploração de dados. Esse é um equilíbrio entre encontrar e incluir variáveis informativas, evitando o excesso de variáveis não relacionadas. Variáveis informativas melhoram nossos resultados; variáveis não relacionadas introduzem ruído desnecessário no modelo. Você também precisa gerar esses recursos para os novos dados obtidos durante a pontuação. Portanto, a geração desses recursos pode depender apenas dos dados que estão disponíveis no momento da pontuação. Para obter diretrizes técnicas sobre a engenharia de recursos ao usar várias tecnologias de dados do Azure, consulte [Engenharia de recursos no Processo de Ciência de Dados](create-features.md). 

### <a name="32-model-training"></a>3.2 Treinamento do modelo
Dependendo do tipo da pergunta que você está tentando responder, há vários algoritmos de modelagem disponíveis. Para obter diretrizes sobre como escolher algoritmos, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo tenha sido escrito para o Azure Machine Learning, as diretrizes fornecidas aqui são úteis para outros projetos de aprendizado de máquina. 

O processo de treinamento do modelo inclui as seguintes etapas: 

* **Dividir os dados de entrada** aleatoriamente para modelagem em um conjunto de dados de treinamento e um conjunto de dados de teste.
* **Criar os modelos** usando o conjunto de dados de treinamento.
* **Avaliar** (conjunto de dados de treinamento e de teste) uma série de algoritmos de aprendizado de máquina concorrentes, juntamente com os vários parâmetros de ajustes associados (conhecidos como limpeza de parâmetros), voltados para responder à pergunta de interesse com os dados atuais.
* **Determinar a “melhor” solução** para responder à pergunta, comparando a métrica de sucesso entre os métodos alternativos.

> [!NOTE]
> **Prevenir a perda**: a perda de dados pode ser causada pela inclusão de dados que estão fora do conjunto de dados de treinamento, que permite a um modelo ou ao algoritmo de aprendizado de máquina fazer previsões boas de forma irrealista. A perda é um motivo comum pelo qual os cientistas de dados ficam preocupados quando obtêm resultados preditivos que parecem bons demais para serem verdadeiros. Essas dependências podem ser difíceis de serem detectadas. Para evitar vazamentos, geralmente é necessária a iteração entre a criação de um conjunto de dados de análise, criação de um modelo e avaliação da precisão. 
> 
> 

Fornecemos uma [Ferramenta automatizada de Modelagem e Relatório](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com o TDSP, que pode ser executada por meio de vários algoritmos e limpezas de parâmetros para produzir um modelo de linha de base. Ela também produz um relatório de modelagem de linha de base, que fornece um resumo do desempenho de cada combinação de modelo e parâmetro, incluindo a importância da variável. Esse processo também é iterativo, pois pode gerar mais engenharia de recursos. 

## <a name="artifacts"></a>Artefatos
Os artefatos produzidos neste estágio incluem:

* [**Conjuntos de Recursos**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): os recursos desenvolvidos para a modelagem são descritos na seção **Conjuntos de Recursos** do relatório **Definição de Dados**. Contém ponteiros para o código para gerar os recursos e a descrição de como o recurso foi gerado.
* [**Relatório de Modelo**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo testado, é produzido um relatório padrão baseado em modelo que fornece detalhes sobre cada experimento.
* **Decisão de ponto de verificação**: avalie se o modelo tem um bom desempenho, suficiente para implantá-lo em um sistema de produção. Algumas perguntas importantes a serem feitas são:
  * O modelo responde à pergunta com confiança suficiente, considerando os dados de teste? 
  * Experimente abordagens alternativas: coletar dados adicionais, realizar mais engenharia de recursos ou fazer experimentos com outros algoritmos?

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do Processo de Ciência de Dados da Equipe:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2. Aquisição de Dados e Entendimento](lifecycle-data.md)
* [3. Modelagem](lifecycle-modeling.md)
* [4. Implantação](lifecycle-deployment.md)
* [5. Aceitação do cliente](lifecycle-acceptance.md)

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess). 