---
title: "Estágio de implantação do Ciclo de Vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados para o estágio de implantação dos seus projetos de ciência de dados."
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>Implantação

Este tópico descreve as metas, as tarefas e os resultados associados à **implantação** do Processo de Ciência de Dados da Equipe. Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* Os modelos com um pipeline de dados são implantados em um ambiente de produção ou semelhante à produção para aceitação do usuário final. 

## <a name="how-to-do-it"></a>Como fazer isso
A principal tarefa abordada neste estágio:

* **Operacionalizar o modelo**: implante o modelo e o pipeline em um ambiente de produção ou semelhante à produção para consumo do aplicativo.

### <a name="41-operationalize-a-model"></a>4.1 Operacionalizar um modelo
Assim que você tiver um conjunto de modelos com um bom desempenho, eles poderão ser operacionalizados para o consumo de outros aplicativos. Dependendo dos requisitos de negócios, as previsões são feitas em tempo real ou em lotes. Modelos são implantados expondo-os com uma interface de API aberta. A interface permite que o modelo seja facilmente consumido de vários aplicativos, como sites online, planilhas, painéis ou aplicativos de linha de negócios ou de back-end. Para obter exemplos de operacionalização de modelos com um serviço Web do Azure Machine Learning, consulte [Implantar um serviço Web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Também é uma melhor prática integrar telemetria e monitoramento no modelo de produção e no pipeline de dados implantados. Essa prática ajuda com o relatório de status do sistema e a solução de problemas subsequentes.  

## <a name="artifacts"></a>Artefatos
* Painel de status de principais métricas e integridade do sistema.
* Relatório de modelagem final com detalhes da implantação.
* Documento da arquitetura da solução final.


## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do Processo de Ciência de Dados da Equipe:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2. Aquisição de Dados e Entendimento](lifecycle-data.md)
* [3. Modelagem](lifecycle-modeling.md)
* [4. Implantação](lifecycle-deployment.md)
* [5. Aceitação do cliente](lifecycle-acceptance.md)

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).