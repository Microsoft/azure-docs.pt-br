---
title: Recursos e SDK do .NET Standard da API de Tabela do Azure Cosmos DB
description: Saiba tudo sobre a API de Tabela do Azure Cosmos DB e o SDK do .NET Standard, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: f41a895aef3476386b8b530fc3e265ffaa21e8c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597571"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API do .NET Standard de Tabela do Azure Cosmos DB: download e notas de versão
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Links  |
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Amostra**|[Exemplo de Cosmos DB API de Tabela .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Relatar Problema**|[Relatar Problema](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Notas de versão da série 2.0.0
a série 2.0.0 usa a dependência do [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), com melhorias de desempenho e consolidação de namespace para Cosmos DB ponto de extremidade.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview
* visualização inicial do SDK da tabela 2.0.0 que usa a dependência de [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), com melhorias de desempenho e consolidação de namespace para Cosmos DB ponto de extremidade. A API pública permanece a mesma.

## <a name="release-notes-for-100-series"></a>Notas de versão da série 1.0.0
a série 1.0.0 usa a dependência de [Microsoft.Azure.DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Adicionar suporte para definir a propriedade TTL se for cosmosdb ponto de extremidade 
* Honrar política de repetição após tempo limite e exceção de tarefa cancelada
* Corrigir a exceção de tarefa cancelada intermitente em aplicativos ASP .net
* Corrigir o armazenamento de tabela do Azure recuperar do modo de localização somente de ponto de extremidade secundário
* Atualizar `Microsoft.Azure.DocumentDB.Core` a versão de dependência para 2.11.2 que corrige a exceção de referência nula intermitente
* Atualizar `Odata.Core` a versão de dependência para 7.6.4 que corrige o conflito de compatibilidade com o Shell do Azure

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Melhoria de desempenho definindo o nível de rastreamento padrão do SDK de tabela como SourceLevels. off, que pode ser aceito por meio de app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Introduza a nova configuração em TableClientConfiguration para usar o executor REST para se comunicar com Cosmos DB API de Tabela

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5-visualização
* Correções de bug

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Correções de bug
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4-visualização
* Correções de bug
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Correções de bug

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Versão de disponibilidade geral

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0-visualização
* Foram feitas alterações em como o CloudTableClient pode ser configurado. Agora, ele pega um objeto TableClientConfiguration durante a construção. O TableClientConfiguration fornece propriedades diferentes para configurar o comportamento do cliente dependendo se o ponto de extremidade de destino é Cosmos DB API de Tabela ou API de Tabela de armazenamento do Azure.
* Adicionado suporte a TableQuery para retornar resultados em uma coluna personalizada. Esse recurso só tem suporte em pontos de extremidade de tabela Cosmos DB.
* Adicionado suporte para expor RequestCharges em vários tipos de resultados. Esse recurso só tem suporte em pontos de extremidade de tabela Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1-preview
* Adicione suporte para o token SAS, operações de TablePermissions, ServiceProperties e ServiceStats nos pontos de extremidade da tabela de Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0-preview
* Adicione suporte para as principais operações CRUD, lote e consulta nos pontos de extremidade de tabela do Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1-preview
* O SDK do .NET Standard da Tabela do Azure Cosmos DB é uma biblioteca .NET de plataforma cruzada que fornece acesso eficiente ao modelo de dados Tabela no Cosmos DB. A versão inicial dá suporte ao conjunto completo de funcionalidades de CRUD de Tabela e Entidade + Consulta com APIs semelhantes aos do [SDK da Tabela do Cosmos DB para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos de extremidade da Tabela de Armazenamento do Azure ainda não têm suporte na versão 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Esta biblioteca de .NET Standard entre plataformas [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) substituirá a biblioteca de .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>série 2.0.0
| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>série 1.0.0
| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 de setembro de 2019 |--- |
| [1.0.5-visualização](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-visualização](#1.0.4-preview) |26 de julho de 2019 |--- |
| 1.0.2 – versão prévia |2 de maio de 2019 |--- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |13 de março de 2019 |--- |
| [0.11.0-visualização](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-visualização](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md).
