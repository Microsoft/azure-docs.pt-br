---
title: Gerenciamento de versão de API para o SDK do .NET e APIs REST – Azure Search
description: Política de versão para APIs REST do Azure Search e a biblioteca de cliente no SDK do .NET.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c07a0c8f5440033455c69fe40806adf9b548c16f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126950"
---
# <a name="api-versions-in-azure-search"></a>Versões de API no Azure Search
O Azure Search lança atualizações de recurso regularmente. Às vezes, mas não sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, a equipe do Azure Search tenta publicar novas versões somente quando necessário, pois isso pode envolver esforços para atualizar seu código, a fim de usar uma nova versão de API. Uma nova versão será necessária somente se alguns aspectos da API tiverem sido alterados de uma forma que interrompa a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

A mesma regra se aplica às atualizações do SDK. O SDK do Azure Search segue as regras do [controle de versão semântico](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de build (por exemplo, 1.1.0). Uma nova versão principal do SDK será liberada somente no caso de alterações que interrompam a compatibilidade com versões anteriores. Para atualizações contínuas de recursos, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!NOTE]
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

## <a name="snapshot-of-current-versions"></a>Instantâneo das versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação do Azure Search.

| Interfaces | Versão principal mais recente | Status |
| --- | --- | --- |
| [SDK .NET](https://aka.ms/search-sdk) |5.0 |Geralmente disponível, liberado em abril de 2018 |
| [Preview do SDK do .NET](https://aka.ms/search-sdk-preview) |4.0.1-preview |Versão prévia, lançada em maio de 2017 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |11-11-2017 |Disponível |
| [API REST de Serviço 2017-11-11-Versão prévia](search-api-2017-11-11-preview.md) |2017-11-11-Versão prévia |Visualização |
| [SDK do Gerenciamento do .NET](https://aka.ms/search-mgmt-sdk) |2,0 |Disponível |
| [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível |

Para as APIs REST, é necessário incluir a `api-version` em cada chamada. Usar `api-version` facilita o direcionamento de uma versão específica, como uma API de versão prévia. O seguinte exemplo ilustra como o parâmetro `api-version` é especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso ocorre especificamente quando novas versões de API introduzem atributos ou operações que não são reconhecidos por versões anteriores. A combinação de versões de API pode trazer consequências indesejadas e deve ser evitada.
>
> A API REST do Serviço e a API REST de Gerenciamento têm controle de versão que não dependem entre si. Qualquer semelhança nos números de versão é uma coincidência.

APIs GA (ou Disponíveis) podem ser usadas na produção e estão sujeitas aos SLAs do Azure. Versões prévias têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável evitar APIs de versão prévia em aplicativos de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões Prévias e Disponíveis para o Público em Geral
O Azure Search sempre faz o pré-lançamento de recursos experimentais por meio da API REST primeiro, e, então, por meio de versões de pré-lançamento do SDK do .NET.

Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários sobre o design e a implementação de recursos. Por esse motivo, os recursos de versão prévia podem ser alterados ao longo do tempo, possivelmente de maneiras que interrompam a compatibilidade com versões anteriores. Isso é diferente de recursos em uma versão de GA, que são estáveis e têm pouca probabilidade de serem alterados, com a exceção de aprimoramentos e pequenas correções de compatibilidade com versões anteriores. Além disso, os recursos de versão prévia nem sempre são liberados para GA.

Por esses motivos, não recomendamos escrever um código de produção que dependa de versões prévias. Caso esteja usando uma versão prévia mais antiga, é recomendável migrar para a versão GA (disponível).

Para o SDK do .NET: Diretrizes para a migração de código podem ser encontradas em [Atualizar o SDK do .NET](search-dotnet-sdk-migration.md).

Disponibilidade geral significa que o Azure Search agora está vinculada aos termos do Contrato de Nível de Serviço (SLA). O SLA pode ser encontrado em [Contratos de Nível de Serviço do Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
