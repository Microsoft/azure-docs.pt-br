---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: c92bb8b2adb3e91e691e9fd1c17a7ee43e84fd11
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362440"
---
Azure Data Factory é um serviço multilocatário que tem os seguintes limites padrão em vigor para garantir que as assinaturas de cliente sejam protegidas das cargas de trabalho das outras. Para aumentar os limites até o máximo de sua assinatura, entre em contato com o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite padrão | Limite máximo |
| -------- | ------------- | ------------- |
| Data factories em uma assinatura do Azure | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, gatilhos, serviços vinculados e tempos de execução de integração, dentro de um data factory | 5\.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para tempos de execução de integração do Azure-SSIS em uma assinatura | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipeline simultâneas por data factory que são compartilhadas entre todos os pipelines na fábrica | 10.000  | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividade externa simultânea por assinatura por [região de Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>As atividades externas são gerenciadas no Integration Runtime, mas são executadas em serviços vinculados, incluindo databricks, procedimento armazenado, HDInsights e outros.</small> | 3000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividade de pipeline simultânea por assinatura por [região de Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>As atividades de pipeline são executadas no Integration Runtime, incluindo Lookup, GetMetadata e Delete.</small>| 1000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações de criação simultâneas por assinatura por [região de Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Incluindo conexão de teste, lista de pastas de pesquisa e lista de tabelas, Visualizar dados. | 200 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de integração de dados simultâneas<sup>1</sup> consumo por assinatura por [região de Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo de região 1<sup>2</sup>: 6000<br>Grupo de região 2<sup>2</sup>: 3000<br>Grupo de região 3<sup>2</sup>: 1500 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo de atividades por pipeline, que inclui atividades internas para contêineres | 40 | 40 |
| Número máximo de tempos de execução de integração vinculados que podem ser criados em um único Integration Runtime de hospedagem interna | 100 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo de parâmetros por pipeline | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| Para cada paralelismo | 20 | 50 |
| Caracteres por expressão | 8\.192 | 8\.192 |
| Intervalo mínimo de gatilho de janela em cascata | 15 min | 15 min |
| Tempo limite máximo para execuções de atividade de pipeline | 7 dias | 7 dias |
| Bytes por objeto para objetos de pipeline<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para objetos de conjunto de e de serviço vinculado<sup>3</sup> | 100 KB | 2\.000 KB |
| Unidades de integração de dados<sup>1</sup> por execução de atividade de cópia | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gravar chamadas à API | 1200/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas à API | 12500/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Consultas de monitoramento por minuto | 1\.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidade por minuto | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> a DIU (unidade de integração de dados) é usada em uma operação de cópia de nuvem para nuvem, saiba mais em [unidades de integração de dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre cobrança, consulte [preços de Azure data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e os custos de saída de rede reduzidos. 

| Grupo de região | Regions | 
| -------- | ------ |
| Grupo de região 1 | EUA Central, leste dos EUA, leste dos EUA 2, Europa Setentrional, Europa Ocidental, oeste dos EUA, oeste dos EUA 2 |
| Grupo de região 2 | Leste da Austrália, sudeste da Austrália, sul do Brasil, Índia central, leste do Japão, Northcentral dos EUA, centro dos EUA, Sudeste Asiático, Oeste EUA Central |
| Grupo de região 3 | Canadá central, Ásia Oriental, França central, Coreia Central Sul do Reino Unido |

<sup>3</sup> os objetos pipeline, conjunto de dados e serviço vinculado representam um agrupamento lógico da carga de trabalho. Os limites para esses objetos não se relacionam com a quantidade de dados que você pode mover e processar com Azure Data Factory. A Data Factory foi projetada para ser dimensionada para lidar com petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Data factories em uma assinatura do Azure |50 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines em um data factory |2\.500 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados em um data factory |5\.000 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos de pipeline<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para o conjunto de dados e objetos de serviço vinculado<sup>1</sup> |100 KB |2\.000 KB |
| Núcleos de cluster sob demanda do Azure HDInsight em uma assinatura<sup>2</sup> |60 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimentação de dados na nuvem por atividade de cópia<sup>3</sup> |32 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Contagem novas tentativas de execução de atividade do pipeline |1\.000 |MaxInt (32 bits) |

<sup>1</sup> pipeline, conjunto de dados e objetos de serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites para esses objetos não se relacionam com a quantidade de dados que você pode mover e processar com Azure Data Factory. A Data Factory foi projetada para ser dimensionada para lidar com petabytes de dados.

<sup>2</sup> Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite anterior é o limite de núcleos Data Factory imposto para os núcleos do HDInsight sob demanda. Ele é diferente do limite de núcleo associado à sua assinatura do Azure.

<sup>3</sup> a DMU (unidade de movimentação de dados de nuvem) para a versão 1 é usada em uma operação de cópia de nuvem para nuvem, saiba mais em [unidades de movimentação de dados de nuvem (versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre cobrança, consulte [preços de Azure data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
