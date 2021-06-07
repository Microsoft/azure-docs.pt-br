---
title: Wrangling de dados em Azure Data Factory
description: Uma visão geral dos Wrangling de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738162"
---
# <a name="what-is-data-wrangling"></a>O que é o Wrangling de dados?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As organizações precisam da capacidade de explorar seus dados críticos de negócios para a preparação de dados e Wrangling a fim de fornecer uma análise precisa de dados complexos que continuam crescendo todos os dias. A preparação de dados é necessária para que as organizações possam usar os dados em vários processos de negócios e reduzir o tempo de implantação.

Data Factory capacita você com a preparação de dados sem código em escala de nuvem iterativamente usando Power Query. Data Factory integra-se com o [Power Query online](/power-query/) e faz Power Query funções M disponíveis como uma atividade de pipeline.

Data Factory converte M gerado pelo editor de mashup do Power Query online no código do Spark para a execução da escala de nuvem, convertendo M em Azure Data Factory fluxos de dados. Os dados do Wrangling com Power Query e fluxos de dados são especialmente úteis para engenheiros de dados ou "integradores de dados do cidadão".

> [!NOTE]
> A atividade de Power Query no Azure Data Factory está disponível atualmente na visualização pública

## <a name="use-cases"></a>Casos de uso

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploração e preparação rápida de dados interativos

Vários engenheiros de dados e integradores de dados do cidadão podem explorar e preparar interativamente conjuntos de dados em escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em data lagos, os usuários precisam de uma maneira eficaz de explorar e preparar conjuntos de dados. Por exemplo, talvez seja necessário criar um conjunto de dados que ' tenha todas as informações demográficas do cliente para novos clientes desde 2017 '. Você não está mapeando para um destino conhecido. Você está explorando, Wrangling e preparando conjuntos de valores para atender a um requisito antes de publicá-lo no Lake. O Wrangling é geralmente usado para cenários de análise menos formal. Os conjuntos de informações de uso podem ser usados para fazer transformações e operações de aprendizado de máquina downstream.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágil sem código

Os integradores de dados do cidadão gastam mais de 60% do tempo procurando e preparando dados. Eles estão procurando fazer isso de forma gratuita de código para melhorar a produtividade operacional. Permitir que os integradores de dados do cidadão enriquecem, formam e publiquem dados usando ferramentas conhecidas como Power Query online de forma escalonável aumentam drasticamente sua produtividade. O Wrangling no Azure Data Factory permite que o conhecido editor de mashup do Power Query online permita aos integradores de dados do cidadão corrigir erros rapidamente, padronizar dados e produzir dados de alta qualidade para dar suporte às decisões de negócios.

### <a name="data-validation-and-exploration"></a>Validação e exploração de dados

Examine visualmente seus dados de forma livre de código para remover exceções, anomalias e ti de acordo com uma forma para análise rápida.

## <a name="supported-sources"></a>Fontes compatíveis

| Connector | Formato de dados | Tipo de autenticação |
| -- | -- | --|
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | CSV, parquet | Chave de conta |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entidade de Serviço |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, parquet | Chave de conta, entidade de serviço |
| [Banco de Dados SQL do Azure](connector-azure-sql-database.md) | - | Autenticação SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticação SQL |

## <a name="the-mashup-editor"></a>O editor de mashup

Quando você cria uma atividade de Power Query, todos os conjuntos de fontes de origem se tornam consultas de conjunto de linhas e são colocados na pasta **ADFResource** Por padrão, o userquery apontará para a primeira consulta de DataSet. Todas as transformações devem ser feitas no userquery, pois as alterações nas consultas de conjunto de consulta não são suportadas, nem serão persistidas. No momento, não há suporte para a renomeação, adição e exclusão de consultas.

![Wrangling](media/wrangling-data-flow/editor.png)

Atualmente, não há suporte para todas as funções Power Query M para Wrangling de dados, apesar de estarem disponíveis durante a criação. Ao criar suas atividades de Power Query, a seguinte mensagem de erro será exibida se não houver suporte para uma função:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Para obter mais informações sobre as transformações com suporte, consulte [Data Wrangling Functions](wrangling-functions.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um data wrangling Power Query combinar](wrangling-tutorial.md).
