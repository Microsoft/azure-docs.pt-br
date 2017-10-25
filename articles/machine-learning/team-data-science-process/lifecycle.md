---
title: "Ciclo de vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As etapas necessárias para executar os projetos de ciência de dados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-lifecycle"></a>Ciclo de vida do Processo de Ciência de Dados de Equipe

O TDSP (Processo de Ciência de Dados de Equipe) fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados. Se você estiver usando outro ciclo de vida de ciência de dados, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou o próprio processo personalizado de sua organização, você ainda poderá usar o TDSP baseado em tarefas com esses ciclos de vida de desenvolvimento. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de machine learning ou de inteligência artificial para análise preditiva. Os projetos de ciência de dados exploratórios e os projetos de análise ad hoc também podem se beneficiar do uso desse processo. Mas, nesses casos, algumas etapas descritas podem não ser necessárias.    

O ciclo de vida do TDSP é composto por cinco estágios principais executados de forma iterativa. Estão incluídos:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2.Aquisição de dados e entendimento](lifecycle-data.md)
* [3.Modelagem](lifecycle-modeling.md)
* [4.Implantação](lifecycle-deployment.md)
* [5.Aceitação do cliente](lifecycle-acceptance.md)

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida do Processo de Ciência de Dados da Equipe é modelado como uma sequência de etapas iteradas que fornecem diretrizes sobre as tarefas necessárias para usar os modelos preditivos. Esses modelos podem ser implantados em um ambiente de produção para serem utilizados para criar aplicativos inteligentes. O objetivo deste ciclo de vida do processo é dar continuidade a um projeto de ciência de dados em direção a um ponto final claro de engajamento. Embora seja verdade que a ciência de dados é um exercício em pesquisas e descobertas, a capacidade de comunicar essas tarefas com clareza para sua equipe e seus clientes usando um conjunto bem definido de artefatos que utiliza modelos padronizados pode ajudar a evitar divergências e aumentar a probabilidade de uma conclusão bem-sucedida de um projeto complexo de ciência de dados.

Para cada estágio, fornecemos as seguintes informações:

* **Metas**: os objetivos específicos.
* **Como fazer isso**: as tarefas específicas descritas e as diretrizes fornecidas para concluí-las.
* **Artefatos**: as entregas e o suporte para produzi-las.

## <a name="next-steps"></a>Próximas etapas
Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).

