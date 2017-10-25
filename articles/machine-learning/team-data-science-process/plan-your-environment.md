---
title: "Identificar cenários e planejar o processo de análise - Azure | Microsoft Docs"
description: "Planeje a análise avançada considerando uma série de perguntas importantes."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 42d1de8a27f8979db5662094e8bd74a6f5ccac55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planejar o processamento de dados analíticos avançados
Quais recursos você deve planejar incluir ao configurar um ambiente para o processamento de análise avançada em um conjunto de dados? Este artigo sugere uma série de perguntas a serem feitas e que ajudarão a identificar as tarefas e os recursos relevantes ao seu cenário. A ordem das etapas de alto nível para análise preditiva é descrita em [O que é o TDSP (Processo de Ciência de Dados de Equipe)?](overview.md). Cada uma dessas etapas exigirá recursos específicos para as tarefas relevantes ao seu cenário específico. As principais perguntas para identificar seu cenário dizem respeito à logística de dados, às características, à qualidade dos conjuntos de dados e às ferramentas e linguagens que você prefere para fazer a análise.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas sobre logística: locais e movimentação de dados
As perguntas sobre logística dizem respeito à localização da **fonte de dados**, ao **destino** no Azure e aos requisitos para mover os dados, inclusive o cronograma, a quantidade e os recursos envolvidos. Pode ser necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover os dados locais para alguma forma de armazenamento no Azure e então para o Machine Learning Studio.

1. **Qual é a sua fonte de dados?** Ela é local ou fica na nuvem? Por exemplo:
   
   * Os dados estão disponíveis publicamente em um endereço HTTP.
   * Os dados residem em um local de arquivo local/na rede.
   * Os dados estão em um banco de dados do SQL Server.
   * Os dados são armazenados em um contêiner de armazenamento do Azure.
2. **Qual é o destino do Azure?** Onde ele precisa estar para processamento ou modelagem? Por exemplo:
   
   * Armazenamento do Blobs do Azure
   * Bancos de dados do SQL Azure
   * SQL Server em VM do Azure
   * Tabelas do HDInsight (Hadoop no Azure) ou do Hive
   * Azure Machine Learning
   * Discos rígidos virtuais montáveis do Azure.
3. **Como você moverá os dados?** Os procedimentos e os recursos disponíveis para ingestão ou carregamento de dados em uma variedade de ambientes de armazenamento e de processamento diferentes são descritos nos tópicos a seguir.
   
   * [Carregar dados em ambientes de armazenamento para análise](ingest-data.md)
   * [Importar os dados de treinamento para o Azure Machine Learning Studio de diferentes fontes de dados](../studio/import-data.md).
4. **Os dados precisam ser movidos em um cronograma regular ou ser modificados durante a migração?** Considere usar o ADF (Azure Data Factory) quando os dados precisarem ser migrados continuamente, em especial se um cenário híbrido que acesse os recursos locais e de nuvem estiver envolvido ou quando os dados forem transacionados ou precisarem ser modificados ou ter lógica de negócios adicionada a eles durante a migração. Para saber mais, consulte [Mover dados de um SQL Server local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md)
5. **Quanto dos dados será movido para o Azure?** Os conjuntos de dados muito grandes podem exceder a capacidade de armazenamento de determinados ambientes. Para obter um exemplo, confira a discussão sobre os limites de tamanho do Machine Learning Studio na próxima seção. Nesses casos, uma amostra dos dados poderá ser usada durante a análise. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes do Azure, confira [Sample data in the Team Data Science Process](sample-data.md)(Dados de exemplo no Processo de Ciência de Dados de Equipe).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas sobre características de dados: tipo, formato e tamanho
Essas perguntas são essenciais para o planejamento de seus ambientes de armazenamento e de processamento, cada um apropriado aos diversos tipos de dados e cada um com determinadas restrições.

1. **Quais são os tipos de dados?** Por exemplo:
   
   * Numérico
   * Categóricos
   * Cadeias de caracteres
   * Binário
2. **Como seus dados estão formatados?** Por exemplo:
   
   * Arquivos simples separados por vírgulas (CSV) ou separados por tabulações (TSV)
   * Compactados ou descompactados
   * Blobs do Azure
   * Tabelas do Hive do Hadoop
   * Tabelas do SQL Server
3. **Qual o tamanho dos seus dados?**
   
   * Pequeno: menos de 2 GB
   * Médio: mais de 2 GB e menos de 10 GB
   * Grande: maior que 10 GB

Tome o ambiente do Azure Machine Learning Studio como exemplo:

* Para obter uma lista dos formatos e dos tipos de dados com suporte no Azure Machine Learning Studio, confira a seção [Formatos de dados e tipos de dados com suporte](../studio/import-data.md#data-formats-and-data-types-supported) .
* Para obter informações sobre as limitações de dados para o Azure Machine Learning Studio, confira a seção **De que tamanho deverá ser o conjunto de dados para os meus módulos?** de [Importando e exportando dados para o Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, confira [Assinatura do Azure e Limites de Serviço, Cotas e Restrições](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas sobre qualidade de dados: exploração e pré-processamento
1. **O que você sabe sobre seus dados?** Explore os dados quando precisar compreender suas características básicas. Quais padrões ou tendências eles exibem, quais exceções que possuem ou quantos valores estão ausentes. Esta etapa é importante para a determinação da extensão do pré-processamento necessário, para a formulação de hipóteses que poderiam sugerir os recursos mais apropriados ou o tipo de análise e para a formulação de planos para coleta de dados adicionais. O cálculo de estatísticas descritivas e a plotagem de visualizações são técnicas úteis para a inspeção de dados. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes do Azure, confira [Sample data in the Team Data Science Process (Dados de exemplo no Processo de Ciência de Dados de Equipe)](explore-data.md).
2. **Os dados exigem pré-processamento ou limpeza?**
   O pré-processamento e a limpeza de dados são tarefas importantes e geralmente devem ser realizadas antes que o conjunto de dados possa ser usado com eficiência para o aprendizado de máquina. Dados brutos costumam conter ruídos e não são confiáveis, e pode haver valores ausentes. Usar esses dados para a modelagem pode produzir resultados incorretos. Para obter uma descrição, confira [Tarefas para preparar dados para o aprendizado de máquina avançado](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas sobre ferramentas e linguagens
Há muitas opções, dependendo de quais linguagens e ambientes ou ferramentas de desenvolvimento você precisa ou com as quais se sente mais confortável.

1. **Quais linguagens você prefere usar para análise?**  
   
   * R
   * Python
   * SQL
2. **Quais ferramentas você deve usar para a análise de dados?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) - uma linguagem de script usada para administrar os recursos do Azure em uma linguagem de script.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools para Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Notebooks Jupyter](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar seu cenário de análise avançada
Depois de responder às perguntas na seção anterior, você está pronto para determinar qual cenário melhor se adapta a seu caso. Os cenários de exemplo são descritos em [Cenários para análise avançada no Azure Machine Learning](plan-sample-scenarios.md).

