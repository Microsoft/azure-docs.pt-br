---
title: "Estágio de entendimento de negócios do Ciclo de Vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados do estágio de entendimento de negócios dos seus projetos de ciência de dados."
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>Noções básicas sobre negócios

Este tópico descreve as metas, as tarefas e os resultados associados ao **estágio de entendimento de negócios** do Processo de Ciência de Dados da Equipe. Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Metas
* As **principais variáveis** são especificadas, que devem servir como **metas modelo**, e cujas métricas relacionadas são usadas para determinar o sucesso do projeto.
* As **fontes de dados** relevantes são identificadas, às quais a empresa tem acesso ou às quais precisa obter acesso.

## <a name="how-to-do-it"></a>Como fazer isso
Há duas tarefas principais abordadas neste estágio: 

* **Definir os objetivos**: trabalhe com o cliente e outros stakeholders para compreender e identificar problemas de negócios. Formule perguntas que definem as metas de negócios e que as técnicas de ciência de dados podem ter como meta.
* **Identificar as fontes de dados**: encontre os dados relevantes que ajudam a responder as perguntas que definem os objetivos do projeto.

### <a name="11-define-objectives"></a>1.1 Definir os objetivos

1. Um objetivo central desta etapa é identificar as principais **variáveis de negócios** que a análise precisa prever. Essas variáveis são denominadas as **metas modelo** e as métricas associadas a elas são usadas para determinar o sucesso do projeto. Dois exemplos dessas metas são a previsão de vendas ou a probabilidade de que um pedido seja fraudulento.

2. Defina as **metas do projeto**, fazendo perguntas e refinando perguntas “inteligentes” que são relevantes, específicas e inequívocas. A ciência de dados é o processo de usar nomes e números para responder a essas perguntas. Para saber mais sobre como fazer perguntas inteligentes, veja o blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Como realizar a Ciência de Dados). A ciência de dados e o machine learning são normalmente usados para responder a cinco tipos de perguntas:
 
   * Quanto custa ou quantos? (regressão)
   * Qual categoria? (classificação)
   * Qual grupo? (clustering)
   * Isso é estranho? (detecção de anomalias)
   * Qual opção deve ser escolhida? (recomendação)

    Determine quais dessas perguntas você está fazendo e como suas respostas alcançam suas metas de negócios.

3. Defina a **equipe do projeto** especificando as funções e responsabilidades dos membros. Desenvolva um plano de marcos de alto nível que pode ser usado para iteração, conforme mais informações são descobertas.  

4. **Defina as métricas de sucesso**. Por exemplo: obter a precisão da previsão de rotatividade de clientes de X% até o final deste projeto de 3 meses, para que possamos oferecer promoções para reduzir a rotatividade. As métricas devem ser **SMART** (INTELIGENTES): 
   * E**S**pecíficas 
   * **M**ensuráveis
   * **A**lcançáveis 
   * **R**elevantes 
   * Com limite de **T**empo 

### <a name="12-identify-data-sources"></a>1.2 Identificar as fontes de dados
Identifique as fontes de dados que contêm exemplos conhecidos de respostas para as perguntas inteligentes. Procure os seguintes dados:

* Dados que são **Relevantes** à pergunta. Temos medidas da meta e recursos relacionados a ela?
* Dados que são uma **Medida precisa** de nossa meta modelo e os recursos de interesse.

Por exemplo, não é incomum descobrir que os sistemas existentes precisam coletar e registrar tipos de dados adicionais para solucionarem o problema e atingirem os objetivos do projeto. Nesse caso, talvez você deseje procurar fontes de dados externas ou atualizar os sistemas para coletar novos dados.

## <a name="artifacts"></a>Artefatos
Estas são as entregas deste estágio:

* [**Documento de estatuto**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): um modelo padrão é fornecido na definição de estrutura do projeto TDSP. Este é um documento dinâmico atualizado ao longo do projeto, conforme novas descobertas são feitas e os requisitos de negócios mudam. O segredo é a iteração neste documento, com a adição de mais detalhes, à medida que você avança no processo de descoberta. Mantenha o cliente e outros stakeholders envolvidos no processo de mudança e comunique os motivos das mudanças de forma clara.  
* [**Fontes de Dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): essa é a seção **Fontes de Dados Brutos** do relatório **Definições de Dados** encontrado na pasta **Relatório de Dados** do projeto do TDSP. Ela especifica os locais de origem e destino dos dados brutos. Em estágios posteriores, você preenche outros detalhes como scripts para mover os dados para seu ambiente de análise.  
* [**Dicionários de Dados**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): este documento fornece descrições dos dados fornecidos pelo cliente. Essas descrições incluem informações sobre o esquema (tipos de dados, informações sobre regras de validação, se houver) e os diagramas de relação de entidade, se disponíveis.

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do Processo de Ciência de Dados da Equipe:

* [1. Noções básicas sobre negócios](lifecycle-business-understanding.md)
* [2. Aquisição de Dados e Entendimento](lifecycle-data.md)
* [3. Modelagem](lifecycle-modeling.md)
* [4. Implantação](lifecycle-deployment.md)
* [5. Aceitação do cliente](lifecycle-acceptance.md)

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).