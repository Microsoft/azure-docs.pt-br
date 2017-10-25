---
title: "Versões de API do Azure Search | Microsoft Docs"
description: "Política de versão para APIs REST da Pesquisa do Azure e a biblioteca de cliente no SDK do .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: a14131455ad94cbc4b729077568b12043401c08e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="api-versions-in-azure-search"></a>Versões de API na Pesquisa do Azure
O Azure Search lança atualizações de recurso regularmente. Às vezes, mas não sempre, essas atualizações exigem que publiquemos uma nova versão de nossa API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, tentamos publicar novas versões somente quando necessário, pois isso pode envolver esforços para atualizar seu código, a fim de usar uma nova versão de API. Apenas publicaremos uma nova versão se for necessário alterar alguns aspectos da API de uma forma que interrompe a compatibilidade com versões anteriores. Isso pode ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

Seguimos a mesma regra para atualizações do SDK. O SDK da Pesquisa do Azure segue as regras do [controle de versão semântico](http://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de build (por exemplo, 1.1.0). Liberaremos uma nova versão principal do SDK somente no caso de alterações que interrompem a compatibilidade com versões anteriores. Para atualizações de recursos contínuas, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!NOTE]
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

## <a name="snapshot-of-current-versions"></a>Instantâneo das versões atuais
Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação da Pesquisa do Azure.

| Interfaces | Versão principal mais recente | Status |
| --- | --- | --- |
| [SDK .NET](https://aka.ms/search-sdk) |3.0 |Disponível, liberado em novembro de 2016 |
| [Preview do SDK do .NET](https://aka.ms/search-sdk-preview) |2.0-preview |Preview, lançada em agosto de 2016 |
| [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Disponível |
| [Preview da API REST do Serviço](search-api-2015-02-28-preview.md) |2015-02-28-Preview |Visualização |
| [SDK do Gerenciamento do .NET](https://aka.ms/search-mgmt-sdk) |2015-08-19 |Disponível |
| [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponível |

Para as APIs REST, é necessário incluir a `api-version` em cada chamada. Isso facilita direcionar uma versão específica, como uma API de preview. O seguinte exemplo ilustra como o parâmetro `api-version` é especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso ocorre especificamente quando novas versões de API introduzem atributos ou operações que não são reconhecidos por versões anteriores. A combinação de versões de API pode trazer consequências indesejadas e deve ser evitada.
>
> A API REST do Serviço e a API REST de Gerenciamento têm controle de versão que não dependem entre si. Qualquer semelhança nos números de versão é uma coincidência.

APIs GA (ou Disponíveis) podem ser usadas na produção e estão sujeitas aos SLAs do Azure. Versões prévias têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável não usar APIs de preview em aplicativos de produção.**

## <a name="about-preview-and-generally-available-versions"></a>Sobre versões Prévias e Disponíveis para o Público em Geral
A Pesquisa do Azure sempre faz o pré-lançamento de recursos experimentais por meio da API REST primeiro, e, então, por meio de versões de pré-lançamento do SDK do .NET.

Não há nenhuma garantia de que os recursos de visualização serão migrados para uma versão GA. Enquanto os recursos em uma versão GA são considerados estáveis e não têm a probabilidade de ser alterados, com a exceção de pequenas melhorias e correções de compatibilidade com versões anteriores, os recursos de visualização estão disponíveis para teste e experimento, com o objetivo de coletar comentários sobre o design e a implementação do recurso.

No entanto, como os recursos de visualização estão sujeitos a alterações, não recomendamos escrever um código de produção que dependa de versões prévias. Caso esteja usando uma versão prévia mais antiga, é recomendável migrar para a versão GA (disponível).

Para o SDK do .NET: diretrizes para a migração de código podem ser encontradas em [Atualizar o SDK do .NET](search-dotnet-sdk-migration.md).

Disponibilidade geral significa que a Pesquisa do Azure agora está vinculada aos termos do Contrato de Nível de Serviço (SLA). O SLA pode ser encontrado em [Contratos de Nível de Serviço da Pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
