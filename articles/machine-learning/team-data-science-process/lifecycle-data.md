---
title: "Estágio de aquisição de dados e entendimento do Ciclo de Vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados para o estágio de aquisição de dados e entendimento dos seus projetos de ciência de dados."
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>Compreensão e aquisição de dados

Este tópico descreve as metas, as tarefas e os resultados associados ao **estágio de aquisição de dados e entendimento** do Processo de Ciência de Dados da Equipe. Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Metas
* Um conjunto de dados limpo e de alta qualidade cujas relações com as variáveis de destino são consideradas como estando localizadas no ambiente de análise apropriado, pronto para ser modelado.
* Foi desenvolvida uma arquitetura da solução do pipeline de dados para atualizar e pontuar os dados regularmente.

## <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

* **Ingestão dos dados** no ambiente de análise da meta.
* **Exploração dos dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
* **Configuração de um pipeline de dados** para pontuar dados novos ou atualizados regularmente.

### <a name="21-ingest-the-data"></a>2.1 Ingestão dos dados
Configure o processo para mover os dados dos locais de origem para os locais de destino em que as operações de análise como treinamento e previsões deverão ser executadas. Para obter detalhes técnicos e opções sobre como fazer isso com vários serviços de dados do Azure, consulte [Carregar dados em ambientes de armazenamento para análise](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 Exploração dos dados
Antes de treinar seus modelos, você precisa desenvolver uma compreensão total dos dados. Em geral, conjuntos de dados do mundo real apresentam ruído, são valores ausentes ou têm uma série de outras discrepâncias. O resumo e a visualização de dados podem ser usados para auditar a qualidade dos dados e fornecer as informações necessárias para processá-los antes que estejam prontos para modelagem. Esse processo costuma ser iterativo.

O TDSP fornece um utilitário automatizado chamado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) para ajudar a visualizar os dados e preparar relatórios de resumo de dados. Recomendamos começar com o IDEAR primeiro para explorar os dados e ajudar a desenvolver uma compreensão inicial dos dados de forma interativa e sem nenhuma codificação e, em seguida, escrever código personalizado para exploração de dados e visualização. Para obter diretrizes sobre como limpar os dados, consulte [Tarefas para preparar dados para o machine learning avançado](prepare-data.md).  

Quando estiver satisfeito com a qualidade dos dados limpos, a próxima etapa é entender melhor os padrões inerentes aos dados, que vão ajudá-lo a escolher e desenvolver um modelo preditivo apropriado para sua meta. Procure provas para descobrir se os dados estão bem conectados à meta e se há dados suficientes para continuar com as próximas etapas de modelagem. Novamente, esse processo costuma ser iterativo. Talvez você precise encontrar novas fontes de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado no estágio anterior.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 Configuração de um pipeline de dados
Além da ingestão inicial e limpeza dos dados, normalmente, você precisa configurar um processo para pontuar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizado contínuo. Faça isso configurando um pipeline de dados ou um fluxo de trabalho. Este é um [exemplo](move-sql-azure-adf.md) de como configurar um pipeline com o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Uma arquitetura da solução do pipeline de dados é desenvolvida neste estágio. O pipeline também é desenvolvido em paralelo com os próximos estágios do projeto de ciência de dados. O pipeline pode ser baseado em lote, de streaming/tempo real ou híbrido, dependendo de suas necessidades de negócios e das restrições dos sistemas existentes nos quais essa solução está sendo integrada. 

## <a name="artifacts"></a>Artefatos
Veja a seguir as entregas deste estágio.

* [**Relatório de qualidade dos dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este relatório contém resumos de dados, relações entre cada atributo e meta, classificação de variáveis, etc. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), fornecida como parte do TDSP, pode gerar esse relatório rapidamente em qualquer conjunto de dados de tabela, como um arquivo CSV ou uma tabela relacional. 
* **Arquitetura da solução**: isso pode ser um diagrama ou uma descrição do pipeline de dados usado para executar a pontuação ou as previsões em relação aos novos dados, após a criação de um modelo. Também contém o pipeline para treinar novamente seu modelo com base nos novos dados. O documento é armazenado no diretório [Projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando o modelo da estrutura de diretório do TDSP é usado.
* **Decisão de Ponto de Verificação**: antes de começar a engenharia de recursos completos e a criação do modelo, reavalie o projeto para determinar se há o valor esperado é suficiente para continuar investindo nele. Você pode, por exemplo, estar pronto para continuar, precisar coletar mais dados, ou abandonar o projeto, pois não existem dados para responder à pergunta.

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do Processo de Ciência de Dados da Equipe:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2. Aquisição de Dados e Entendimento](lifecycle-data.md)
* [3. Modelagem](lifecycle-modeling.md)
* [4. Implantação](lifecycle-deployment.md)
* [5. Aceitação do cliente](lifecycle-acceptance.md)

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.  

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).