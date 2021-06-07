---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 96c67327a8272a02e677cc40b46c3177e470b684
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125872"
---
O Azure Data Factory é um serviço multilocatário que tem os seguintes limites padrão em vigor para garantir que as assinaturas dos clientes permaneçam protegidas das cargas de trabalho umas das outras. Para aumentar os limites até o máximo para sua assinatura, entre em contato com o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite padrão | Limite máximo |
| -------- | ------------- | ------------- |
| Data factories em uma assinatura do Azure | 800 | 800 |
| Número total de entidades, como pipelines, conjuntos de dados, gatilhos, serviços vinculados, pontos de extremidade privados e runtimes de integração, dentro de um data factory | 5\.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para os Azure-SSIS Integration Runtimes em uma assinatura | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipeline simultâneas por data factory que são compartilhadas entre todos os pipelines na fábrica | 10.000  | 10.000 |
| Execuções de atividades externas simultâneas por assinatura por [Região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>As atividades externas são gerenciadas no runtime de integração, mas executadas em serviços vinculados, incluindo Databricks, procedimento armazenado, HDInsights, Web e outros. Esse limite não se aplica ao IR auto-hospedado.</small> | 3\.000 | 3.000 |
| Execuções de atividades de pipeline simultâneas por assinatura por [Região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>As atividades de pipeline são executadas no runtime de integração, incluindo Lookup, GetMetadata e Delete. Esse limite não se aplica ao IR auto-hospedado.</small> | 1,000 | 1,000                                                        |
| Operações de criação simultâneas por assinatura por [Região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Incluindo testar conexão, procurar em lista de pastas e lista de tabelas, visualizar dados. Esse limite não se aplica ao IR auto-hospedado.</small> | 200 | 200                                                          |
| Consumo de unidades de integração de dados simultâneas<sup>1</sup> por assinatura por [região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo de regiões 1<sup>2</sup>: 6.000<br>Grupo de regiões 2<sup>2</sup>: 3.000<br>Grupo de regiões 3<sup>2</sup>: 1.500 | Grupo de regiões 1<sup>2</sup>: 6.000<br/>Grupo de regiões 2<sup>2</sup>: 3.000<br/>Grupo de regiões 3<sup>2</sup>: 1.500 |
| Máximo de atividades por pipeline, que inclui atividades internas para contêineres | 40 | 40 |
| Número máximo de runtimes de integração vinculados que podem ser criados usando apenas um runtime de integração auto-hospedada | 100 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo de parâmetros por pipeline | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| Para cada paralelismo | 20 | 50 |
| Máximo de execuções na fila por pipeline | 100 | 100 |
| Caracteres por expressão | 8\.192 | 8\.192 |
| Intervalo mínimo de gatilho da janela em cascata | 15 min | 15 min |
| Tempo limite máximo para execuções de atividade de pipeline | 7 dias | 7 dias |
| Bytes por objeto para objetos do pipeline<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para objetos de conjunto de dados e serviço vinculado<sup>3</sup> | 100 KB | 2\.000 KB |
| Bytes por conteúdo para cada execução de atividade<sup>4</sup> | 896 KB | 896 KB |
| Unidades de integração de dados<sup>1</sup> por execução de atividade de cópia | 256 | 256 |
| Gravar chamadas à API | 1\.200/h | 1\.200/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. |
| Ler chamadas à API | 12.500/h | 12.500/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. |
| Consultas de monitoramento por minuto | 1,000 | 1,000 |
| Tempo máximo da sessão de depuração do fluxo de dados | 8 horas | 8 horas |
| Número simultâneo de fluxos de dados por runtime de integração | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número simultâneo de sessões de depuração do fluxo de dados por usuário por alocador | 3 | 3 |
| Limite de TTL do Azure IR no Fluxo de Dados | 4 horas |  4 horas |
| Limite de tamanho da entidade de dados de metadados em uma fábrica | 2 GB | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> A DIU (unidade de integração de dados) é usada em uma operação de cópia de nuvem para nuvem, saiba mais em [Unidades de integração de dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre a cobrança, confira [Preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> O [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade de dados, a eficiência e os custos de saída de rede reduzidos. 

| Grupo de regiões | Regiões |
| -------- | ------ |
| Grupo de regiões 1 | EUA Central, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Oeste da Europa, Oeste dos EUA, Oeste dos EUA 2 |
| Grupo de regiões 2 | Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Índia Central, Leste do Japão, Centro-Norte dos EUA, Centro-Sul dos EUA, Sudeste Asiático, Centro-Oeste dos EUA |
| Grupo de regiões 3 | Outras regiões |

<sup>3</sup> Objetos de pipeline, conjunto de dados e serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites desses objetos não estão relacionados à quantidade de dados que você pode mover e processar com o Azure Data Factory. O Data Factory foi desenvolvido para ser dimensionado de modo a lidar com petabytes de dados.

<sup>4</sup> O conteúdo para cada execução de atividade inclui a configuração da atividade, os conjuntos de dados associados e as configurações de serviços vinculados, se houver, e uma pequena parte das propriedades do sistema geradas por tipo de atividade. Os limites de tamanho desse conteúdo não estão relacionados à quantidade de dados que você pode mover e processar com o Azure Data Factory. Saiba mais sobre [os sintomas e as recomendações](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) se atingir esse limite.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Pipelines em um data factory |2\.500 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados em um data factory |5\.000 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos do pipeline<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para objetos de conjunto de dados e serviço vinculado<sup>1</sup> |100 KB |2\.000 KB |
| Núcleos de cluster sob demanda do Azure HDInsight em uma assinatura<sup>2</sup> |60 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimentação de dados de nuvem por execução de atividade de cópia<sup>3</sup> |32 |32 |
| Contagem novas tentativas de execução de atividade do pipeline |1,000 |MaxInt (32 bits) |

<sup>1</sup> Objetos de pipeline, conjunto de dados e serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites desses objetos não estão relacionados à quantidade de dados que você pode mover e processar com o Azure Data Factory. O Data Factory foi desenvolvido para ser dimensionado de modo a lidar com petabytes de dados.

<sup>2</sup> Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite anterior é o limite de núcleos imposto pelo Data Factory para os núcleos do HDInsight sob demanda. Ele é diferente do limite de núcleos associado à sua assinatura do Azure.

<sup>3</sup> A DMU (unidade de movimentação de dados) de nuvem para a versão 1 é usada em uma operação de cópia de nuvem para nuvem. Saiba mais em [Unidades de movimentação de dados de nuvem (versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre a cobrança, confira [Preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
