---
title: Recursos e SDK do .NET da API de Tabela do Azure Cosmos DB
description: Saiba tudo sobre o Azure Cosmos DB API de Tabela para .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566822"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET de Tabela do Azure CosmosDB: download e notas de versão
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Links|
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Início rápido**|[Azure CosmosDB: Compilar um aplicativo com o .NET e a API de Tabela](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> O SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) do .NET Framework está no modo de manutenção e será preterido em breve. Atualize para [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) da nova biblioteca do .NET Standard para continuar a obter os recursos mais recentes com suporte na API de Tabela.

> Se você criou uma conta da API de Tabela durante a versão prévia, crie uma [nova conta da API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs da API de Tabela disponíveis para o público em geral.
>

## <a name="release-notes"></a>Notas de versão

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Correções de bug

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Correções de bug

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Adição de suporte de gravação de várias regiões
* Correção das dependências de pacotes NuGet em Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm e Microsoft.Spatial

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Corrigidas as dependências de pacote NuGet no Microsoft.Azure.Storage.Common e Microsoft.Azure.DocumentDB.
* Correções de bugs na serialização de tabelas quando o JsonConvert.DefaultSettings é configurado.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Validação adicional para ETAGs malformadas no Modo Direto.
* Bug de consulta LINQ fixado no Modo de Gateway.
* As APIs síncronas agora são executadas no pool de threads com SynchronizationContext.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de bugs

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Versão de disponibilidade geral

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0-preview

* Versão prévia inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação

A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

`Microsoft.Azure.CosmosDB.Table`Atualmente, a biblioteca está disponível somente para .NET Framework e está no modo de manutenção e será preterida em breve. Novos recursos e funcionalidades e otimizações são adicionados somente à biblioteca de .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), por isso é recomendável que você atualize para [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

O pacote de visualização [WindowsAzure. Storage-premiumtable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido. O SDK WindowsAzure.Storage-PremiumTable será desativado em 15 de novembro de 2018, quando as solicitações para o SDK obsoleto não serão permitidas.

| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 de setembro de 2019| |
| [2.1.0](#2.1.0) |22 janeiro de 2019|1º de abril de 2020 |
| [2.0.0](#2.0.0) |26 de setembro de 2018|1º de março de 2020 |
| [1.1.3](#1.1.3) |17 de julho de 2018|1º de dezembro de 2019 |
| [1.1.1](#1.1.1) |26 de março de 2018|1º de dezembro de 2019 |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|1º de dezembro de 2019 |
| [1.0.0](#1.0.0) |15 de novembro de 2017|15 de novembro de 2019 |
| 0.9.0-preview |11 de novembro de 2017 |11 de novembro de 2019 |

## <a name="troubleshooting"></a>Solução de problemas

Se você receber o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar usar o pacote Microsoft.Azure.CosmosDB.Table NuGet, você tem duas opções para corrigir o problema:

* Use o Console de Gerenciamento de Pacote para instalar o pacote Microsoft.Azure.CosmosDB.Table e suas dependências. Para fazer isso, digite o seguinte no Console do Gerenciador de Pacotes para sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Usando a ferramenta de gerenciamento de pacotes NuGet preferencial, instale o pacote NuGet Microsoft.Azure.Storage.Common antes de instalar o Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também

Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md). 
