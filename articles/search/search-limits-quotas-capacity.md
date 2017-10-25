---
title: "Limites de serviço no Azure Search | Microsoft Docs"
description: "Limites do serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para o Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/07/2017
ms.author: heidist
ms.openlocfilehash: 60e63401e3915e62e1ec5ac03cd548c291580b24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço na Pesquisa do Azure
Os limites máximos de armazenamento, cargas de trabalho, quantidades de índices, documentos e outros objetos dependem do tipo de preço ao qual o [Azure Search é provisionado](search-create-service-portal.md), que pode ser **Gratuito**, **Básico** ou **Standard**.

* **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. É uma opção sem custo adicional para assinantes existentes que permite experimentar o serviço antes de se inscrever para obter recursos dedicados.
* **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor.
* **Standard** é executado em computadores dedicados, com mais capacidade de armazenamento e processamento em cada nível. Standard vem em quatro níveis: S1, S2, S3 e S3 Alta Densidade (S3 HD).

> [!NOTE]
> Um serviço é provisionado em uma camada específica. Se você precisar pular camadas para obter mais capacidade, provisione um novo serviço (não há nenhuma atualização in-loco). Para obter mais informações, confira [Escolher uma camada ou SKU](search-sku-tier.md). Para saber mais sobre o ajuste de capacidade em um serviço já provisionado, consulte [Dimensionar níveis de recursos para cargas de trabalho de indexação e consulta](search-capacity-planning.md).
>

## <a name="per-subscription-limits"></a>Por limites de assinatura
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="per-service-limits"></a>Por limites do serviço
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="per-index-limits"></a>Por limites de índice
Há uma correspondência entre os limites em índices e aqueles em indexadores. Dado um limite de 200 índices, o limite máximo de indexadores também é 200 para o mesmo serviço.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Índice: campos máximos por índice |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Índice: máximo de perfis de pontuação por índice |100 |100 |100 |100 |100 |100 |
| Índice: funções máximas por perfil |8 |8 |8 |8 |8 |8 |
| Indexadores: carga de indexação máxima por invocação |10.000 documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |N/D <sup>2</sup> |
| Indexadores: tempo de execução máximo | 1 a 3 minutos <sup>3</sup> |24 horas |24 horas |24 horas |24 horas |N/D <sup>2</sup> |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/D <sup>2</sup> |
| Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob |32.000 |64.000 |4 milhões |4 milhões |4 milhões |N/D <sup>2</sup> |

<sup>1</sup> A camada do tipo Básico é a única SKU que tem limite inferior de 100 campos por índice.

<sup>2</sup> O S3 HD atualmente não dá suporte a indexadores. Contate o suporte do Azure se você tiver uma necessidade urgente para essa funcionalidade.

<sup>3</sup> O tempo máximo de execução do indexador para a Camada gratuita é de três minutos para fontes de blob e um minuto para todas as outras fontes de dados.

## <a name="document-size-limits"></a>Limites de tamanho do documento
| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Tamanho do documento individual por API do Índice |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |

Refere-se ao tamanho máximo do documento ao chamar uma API do Índice. O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como você pode passar um lote de vários documentos para uma API de uma só vez, o limite de tamanho depende na realidade de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos é de 16 MB de JSON.

Para reduzir o tamanho do documento, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## <a name="workload-limits-queries-per-second"></a>Limites de carga de trabalho (consultas por segundo)
| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| QPS |N/D  |~3 por réplica |~15 por réplica |~60 por réplica |>60 por réplica |>60 por réplica |

QPS (consultas por segundo) é uma aproximação baseada em heurística, usando as cargas de trabalho de cliente real e simulada para derivar os valores estimados. A taxa de transferência de QPS exata varia dependendo dos dados e da natureza da consulta.

Embora essas estimativas aproximadas sejam apresentadas acima, é difícil determinar a taxa real, especialmente no serviço Gratuito compartilhado, no qual a taxa de transferência tem base na largura de banda disponível na competição por recursos do sistema. Na camada gratuita, os recursos de computação e armazenamento são compartilhados por vários assinantes; portanto, o QPS de sua solução sempre vai variar conforme o número de outras cargas de trabalho em execução ao mesmo tempo.

No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Veja a seção de práticas recomendadas em [Gerenciar sua solução de pesquisa](search-manage.md) para obter diretrizes sobre como calcular o QPS para suas cargas de trabalho.

## <a name="api-request-limits"></a>Limites de Solicitação da API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> no Azure Search, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições e composição de campos).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

* Máximo de duas chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
