---
title: Recursos e SDK .NET da API do Graph do BD Cosmos do Azure | Microsoft Docs
description: "Saiba tudo sobre a API do Graph do BD Cosmos do Azure, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: b84fe1036a50b5672f5b5f4d6df2f67a25a1be4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>API .NET Graph do BD Cosmos do Azure: download e notas de versão

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Documentação da API**|[Documentação de referência de API .NET](https://aka.ms/acdbgraphapiref)|
|**Início rápido**|[BD Cosmos do Azure: criar um aplicativo gráfico usando .NET e a API do Graph](create-graph-dotnet.md)|
|**Tutorial**|[Azure CosmosDB: Criar um contêiner com a API do Graph](tutorial-develop-graph-dotnet.md)|
|**Framework atualmente com suporte**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Notas de versão

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>Novidades
* Adicionado o suporte para `.netstandard 1.6`
  * Requer `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Adicionado um novo analisador `gremlin-groovy` para substituir o analisador existente. Esse analisador dá suporte a um subconjunto da sintaxe `gremlin-groovy` do Tinkerpop e inclui:
  * Desempenho de análise melhorado em 2x.
  * Vários problemas resolvidos, relacionados ao escape de caractere em cadeias de caracteres, valores literais manipulados incorretamente e outras irregularidades no analisador antigo.
* Otimizações adicionadas para travessias com predicados de borda.
  *  Os saltos de travessia com filtros devem perceber essa melhoria, por exemplo: `g.V('1').outE().has('name', 'marko').inV()`.
* Otimizações adicionadas para travessias com etapa `limit()`.

#### <a name="breaking-changes"></a>Alterações significativas
* Foi removido o suporte para o .NET Framework 4.5.1

* O novo analisador alinha-se com a gramática `gremlin-groovy`. Como resultado, algumas expressões que funcionavam anteriormente são ambíguas para o novo analisador. Um caso de a ser observado:
  * `in` e `as` são palavras-chave reservadas em `gremlin-groovy`, portanto, essas etapas devem ser qualificadas com `.in()` ou `.as()` para evitar erros de sintaxe. Por exemplo: `g.V().repeat(in()).times(2)` ->  _lançará um erro de sintaxe_  
 `g.V().repeat(__.in()).times(2)` ->  _será bem-sucedida_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Versão prévia inicial.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao BD Cosmos do Azure usando um SDK obsoleto será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [0.3.0-preview](#0.3.0-preview) |2 de outubro de 2017 |--- |
| [0.2.4-preview](#0.2.4-preview) |4 de agosto de 2017 |--- |
| [0.2.2-preview](#0.2.2-preview) |23 de junho de 2017 |--- |
| [0.2.1-preview](#0.2.2-preview) |8 de junho de 2017 |--- |
| [0.2.0-preview](#0.2.2-preview) |10 de maio de 2017 |--- |
| [0.1.0-preview](#0.1.0-preview) |8 de maio de 2017 |--- |

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API do Graph do BD Cosmos do Azure, consulte [Introdução ao BD Cosmos do Azure: API do Graph](graph-introduction.md). 
