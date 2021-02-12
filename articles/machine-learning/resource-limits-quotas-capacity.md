---
title: Limites de serviço
titleSuffix: Azure Machine Learning
description: Limites de serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930796"
---
# <a name="service-limits-in-azure-machine-learning"></a>Limites de serviço no Azure Machine Learning

Esta seção lista as cotas básicas e os limites de limitação no Azure Machine Learning. Para saber como aumentar as cotas de recursos, consulte ["gerenciar e aumentar as cotas de recursos"](how-to-manage-quotas.md)

## <a name="workspaces"></a>Workspaces
| Limite | Valor |
| --- | --- |
| Nome do workspace | 2-32 caracteres |

## <a name="runs"></a>Execuções
| Limite | Valor |
| --- | --- |
| Execuções por espaço de trabalho | 10 milhões |
| RunId/ParentRunId | 256 caracteres |
| Datacontêinerid | 261 caracteres |
| DisplayName |256 caracteres|
| Descrição |5.000 caracteres|
| Número de propriedades |50 |
| Comprimento da chave de propriedade |100 caracteres |
| Comprimento do valor da propriedade |1.000 caracteres |
| Número de marcas |50 |
| Comprimento da chave de marca |100 |
| Comprimento do valor da marca |1.000 caracteres |
| CancelUri / CompleteUri / DiagnosticsUri |1.000 caracteres |
| Comprimento da mensagem de erro |3.000 caracteres |
| Comprimento da mensagem de aviso |300 caracteres |
| Número de conjuntos de dados de entrada |200 |
| Número de conjuntos de resultados de saída |20 |


## <a name="metrics"></a>Métricas
| Limite | Valor |
| --- | --- |
| Nomes de métrica por execução |50|
| Linhas de métricas por nome de métrica |10 milhões|
| Colunas por linha de métrica |15|
| Comprimento do nome da coluna de métrica |255 caracteres |
| Comprimento do valor da coluna de métrica |255 caracteres |
| Linhas de métrica por lote carregadas | 250 |

> [!NOTE]
> Se você estiver atingindo o limite de nomes de métricas por execução porque você está Formatando variáveis no nome da métrica, considere usar uma métrica de linha em que uma coluna é o valor da variável e a segunda coluna é o valor da métrica.

## <a name="artifacts"></a>Artifacts

| Limite | Valor |
| --- | --- |
| Número de artefatos por execução |10 milhões|
| Comprimento máximo do caminho do artefato |5.000 caracteres |

## <a name="limit-increases"></a>Aumentos de limite
Alguns limites podem ser aumentados para espaços de trabalho individuais [entrando em contato com o suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Próximas etapas

- [Configurar seu ambiente de Azure Machine Learning](how-to-configure-environment.md)
- Saiba como aumentar as cotas de recursos em ["gerenciar e aumentar as cotas de recursos"](how-to-manage-quotas.md).

