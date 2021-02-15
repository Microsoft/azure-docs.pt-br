---
title: Consultar dados de um ambiente Gen2 usando C#-Azure Time Series Insights | Microsoft Docs
description: Saiba como consultar dados de um ambiente de Azure Time Series Insights Gen2 usando um aplicativo escrito em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 81725a28102caf0d69a9fb303eaccdcf2151587d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020020"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Consultar dados do ambiente de Azure Time Series Insights Gen2 usando C Sharp

Este exemplo de C# demonstra como consultar dados das [APIs de acesso a dados do Gen2](/rest/api/time-series-insights/reference-data-access-overview) em ambientes Azure Time Series insights Gen2.

> [!TIP]
> Veja exemplos de código do Gen2 C# em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Resumo

O código de exemplo abaixo demonstra os seguintes recursos:

* Suporte para geração automática do SDK por meio do [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso por meio do Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar esse token de acesso adquirido no cabeçalho `Authorization` das solicitações seguintes da API de Acesso a Dados.
* O exemplo fornece uma interface de console que demonstra como as solicitações HTTP são feitas para o seguinte:
  * [API de ambientes Gen2](/rest/api/time-series-insights/reference-environments-apis)
    * [API de Obtenção da Disponibilidade de Ambientes](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) e [API de Obtenção do Esquema de Evento](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [API de consulta do Gen2](/rest/api/time-series-insights/reference-query-apis)
    * [API de Obtenção de Eventos](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [API de Obtenção da Série](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) e [API de Obtenção da Série Agregada](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [APIs de Modelo de Série Temporal](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [API de Obtenção de Hierarquias](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) e [API do Lote de Hierarquias](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [API de Obtenção de Tipos](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) e [API do Lote de Tipos](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [API de Obtenção de Instâncias](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) e [API do Lote de Instâncias](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Funcionalidades avançadas de [Pesquisa](/rest/api/time-series-insights/reference-model-apis#search-features) e [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax).

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisionar um ambiente de Azure Time Series insights Gen2](./how-to-provision-manage.md#create-the-environment) .
1. Configurar o ambiente do Azure Time Series Insights para o Azure Active Directory, conforme descrito em [Autenticação e autorização](time-series-insights-authentication-and-authorization.md).
1. Execute o [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [README.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) para gerar as dependências de cliente do Azure Time Series insights Gen2.
1. Abrir a solução `TSIPreviewDataPlaneclient.sln` e definir `DataPlaneClientSampleApp` como o projeto padrão no Visual Studio.
1. Instalar as dependências de projeto necessárias usando as etapas descritas [abaixo](#project-dependencies) e compilar o exemplo em um arquivo `.exe` executável.
1. Executar o arquivo `.exe` clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências do projeto

Recomendamos usar a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/): versão 16.4.2 e posterior

O código de exemplo tem várias dependências necessárias que podem ser exibidas no arquivo [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config).

Baixe os pacotes no Visual Studio 2019 selecionando a opção **Compilar** > **Compilar Solução**.

Como alternativa, adicione cada pacote usando o [NuGet 2.12 e posterior](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de exemplo C#

Consulte o repositório de [Azure Time Series insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) para acessar o código de exemplo do C#.

> [!NOTE]
>
> * O exemplo de código pode ser executado sem alterar as variáveis de ambiente padrão.
> * O exemplo de código será compilado em um aplicativo de console executável .NET.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre consultas, leia a [referência da API de Consulta](/rest/api/time-series-insights/reference-query-apis).

* Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Azure Time Series insights.