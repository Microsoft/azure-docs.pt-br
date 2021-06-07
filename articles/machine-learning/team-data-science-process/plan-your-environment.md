---
title: Identificar cenários e planejar o processo de análise – Processo de Ciência de Dados de Equipe | Azure Machine Learning
description: Identifique cenários e planeje o processamento de dados analíticos avançado, considerando uma série de questões fundamentais.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322389"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planejar o processamento de dados analíticos avançados

Quais recursos são necessários para que você possa criar um ambiente que pode executar o processamento de análise avançado em um conjunto de dados? Este artigo sugere uma série de perguntas a serem feitas que podem ajudar a identificar as tarefas e os recursos relevantes ao seu cenário.

Para saber mais sobre a ordem das etapas de alto nível para análise preditiva, confira [O que é o TDSP (Processo de Ciência de Dados de Equipe)?](overview.md). Cada etapa exige recursos específicos para as tarefas relevantes ao seu cenário específico.

Responda a perguntas-chave nas áreas a seguir para identificar seu cenário:

* logística de dados
* características de dados
* qualidade do conjunto de dados
* ferramentas e linguagens preferidas

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas sobre logística: locais e movimentação de dados

As perguntas sobre logística abordam os seguintes itens:

* localização da fonte de dados
* destino no Azure
* requisitos para a movimentação de dados, incluindo o agendamento, a quantidade e os recursos envolvidos

Pode ser necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover os dados locais para alguma forma de armazenamento no Azure e então para o Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Qual é a sua fonte de dados?

Seus dados são locais ou ficam na nuvem? Os possíveis locais incluem:

* um endereço HTTP publicamente disponível
* uma localização de arquivo de rede ou local
* um banco de dados do SQL Server
* um contêiner de armazenamento do Azure

### <a name="what-is-the-azure-destination"></a>Qual é o destino do Azure?

Onde seus dados precisam estar para processamento ou modelagem? 

* Armazenamento do Blobs do Azure
* Bancos de dados do SQL Azure
* SQL Server na VM do Azure
* Tabelas do HDInsight (Hadoop no Azure) ou do Hive
* Azure Machine Learning
* Discos rígidos virtuais montáveis do Azure

### <a name="how-are-you-going-to-move-the-data"></a>Como você moverá os dados?

Para os procedimentos e os recursos para a ingestão ou o carregamento de dados em uma variedade de ambientes de armazenamento e de processamento diferentes, confira:

* [Carregar dados em ambientes de armazenamento para análise](ingest-data.md)
* [Importe seus dados de treinamento para Azure Machine Learning Studio (clássico) de várias fontes de dados](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados precisam ser movidos em um cronograma regular ou ser modificados durante a migração?

Considere o uso do ADF (Azure Data Factory) quando os dados precisam ser migrados continuamente. O ADF pode ser útil para:

* um cenário híbrido que envolve tanto recursos locais quanto na nuvem
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica de negócios durante a migração

Para obter mais informações, consulte [mover dados de um banco de SQL Server para SQL Azure com Azure data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados será movido para o Azure?

Grandes conjuntos de dado podem exceder a capacidade de armazenamento de determinados ambientes. Para obter um exemplo, consulte a discussão sobre limites de tamanho para Machine Learning Studio (clássico) na próxima seção. Nesses casos, você pode usar uma amostra dos dados durante a análise. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes do Azure, confira [Sample data in the Team Data Science Process](sample-data.md)(Dados de exemplo no Processo de Ciência de Dados de Equipe).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas sobre características de dados: tipo, formato e tamanho

Essas perguntas são essenciais para planejar os ambientes de armazenamento e processamento. Elas ajudarão a escolher o cenário apropriado para seu tipo de dados e entender possíveis restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numérico
* Categóricos
* Cadeias de caracteres
* Binário

### <a name="how-is-your-data-formatted"></a>Como seus dados estão formatados?

* Arquivos simples separados por vírgulas (CSV) ou separados por tabulações (TSV)
* Compactados ou descompactados
* Blobs do Azure
* Tabelas do Hive do Hadoop
* Tabelas do SQL Server

### <a name="how-large-is-your-data"></a>Qual o tamanho dos seus dados?

* Pequeno: menor que 2 GB
* Médio: maior que 2 GB e menor que 10 GB
* Grande: maior que 10 GB

Considere o ambiente Azure Machine Learning Studio (clássico), por exemplo:

* Para obter uma lista dos formatos e dos tipos de dados com suporte no Azure Machine Learning Studio, confira a seção [Formatos de dados e tipos de dados com suporte](../classic/import-data.md#supported-data-formats-and-data-types) .
* Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, confira [Assinatura do Azure e Limites de Serviço, Cotas e Restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas sobre qualidade de dados: exploração e pré-processamento

### <a name="what-do-you-know-about-your-data"></a>O que você sabe sobre seus dados?

Entenda as características básicas sobre seus dados:

* Quais padrões ou tendências eles exibem
* Quais exceções eles têm
* Quantos valores estão ausentes

Essa etapa é importante para ajudá-lo a:

* Determinar a quantidade de pré-processamento necessária
* Formular hipóteses que sugerem os recursos ou tipos de análises mais apropriados
* Formular planos para coleta de dados adicionais

As técnicas úteis para a inspeção de dados incluem gráficos de visualização e cálculo de estatísticas descritivas. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes do Azure, confira [Sample data in the Team Data Science Process (Dados de exemplo no Processo de Ciência de Dados de Equipe)](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados exigem pré-processamento ou limpeza?

Talvez você precise pré-processar e limpar seus dados antes de poder usar o conjunto de dados com eficiência para o aprendizado de máquina. Dados brutos geralmente contém ruídos e não são confiáveis. Eles podem ter valores ausentes. Usar esses dados para a modelagem pode produzir resultados incorretos. Para obter uma descrição, confira [Tarefas para preparar dados para o aprendizado de máquina avançado](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas sobre ferramentas e linguagens

Há muitas opções para ferramentas, ambientes de desenvolvimento e linguagens. Lembre-se de suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Quais linguagens você prefere usar para análise?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Quais ferramentas você deve usar para a análise de dados?

* [Microsoft Azure PowerShell](/powershell/azure/) -uma linguagem de script usada para administrar os recursos do Azure em uma linguagem de script
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Ferramentas Python para Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Blocos de anotações do Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar seu cenário de análise avançada

Depois de responder às perguntas na seção anterior, você está pronto para determinar qual cenário melhor se adapta a seu caso. Os cenários de exemplo são descritos em [Cenários para análise avançada no Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que é o TDSP (Processo de Ciência de Dados de Equipe)?](overview.md)