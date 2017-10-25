---
title: "Estágio de aceitação do cliente do Ciclo de Vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados para o estágio de aceitação do cliente dos seus projetos de ciência de dados."
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>Aceitação do cliente

Este tópico descreve as metas, as tarefas e os resultados associados ao **estágio de aceitação do cliente** do Processo de Ciência de Dados da Equipe. Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
* **Finalizar as entregas do projeto**: confirme se o pipeline, o modelo e a implantação em um ambiente de produção atendem aos objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazer isso
Há duas tarefas principais abordadas neste estágio:

* **Validação do sistema**: confirme se o modelo implantado e o pipeline atendem às necessidades do cliente.
* **Entrega do projeto**: para a entidade que deverá executar o sistema na produção.

O cliente deverá validar se o sistema atende às suas necessidades de negócios e se ele responde às perguntas com uma precisão aceitável para implantar o sistema em produção para uso pelo aplicativo cliente. Toda a documentação é finalizada e revisada. Uma entrega do projeto para a entidade responsável pelas operações é concluída. Essa entidade poderá ser, por exemplo, uma equipe de TI ou de ciência de dados do cliente ou um agente do cliente responsável por executar o sistema na produção. 

## <a name="artifacts"></a>Artefatos
O principal artefato produzido neste estágio final é o **Relatório de Saída do Projeto para o Cliente**. Esse é o relatório técnico que contém todos os detalhes do projeto que são úteis para conhecer e operar o sistema. Um modelo [Sair do Relatório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) é fornecido pelo TDSP, que pode ser usado no estado em que se encontra ou personalizado, para as necessidades específicas do cliente. 


## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do Processo de Ciência de Dados da Equipe:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2. Aquisição de Dados e Entendimento](lifecycle-data.md)
* [3. Modelagem](lifecycle-modeling.md)
* [4. Implantação](lifecycle-deployment.md)
* [5. Aceitação do cliente](lifecycle-acceptance.md)

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).