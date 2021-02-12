---
title: Autenticação e autorização de API – Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1bd3c5796658663b6111723829cbe620346002c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016234"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a nova folha do Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários se autentiquem no e estejam autorizados a usar a API do Azure Time Series Insight associada a um ambiente de Azure Time Series Insights.

## <a name="service-principal"></a>Entidade de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API de Azure Time Series Insights em nome de um aplicativo. O aplicativo pode então consultar ou publicar dados de referência no ambiente de Azure Time Series Insights usando suas próprias credenciais de aplicativo por meio de Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumo e melhores práticas

O fluxo de registro de aplicativo do Azure Active Directory envolve três etapas principais.

1. [Registrar um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorize o aplicativo a ter [acesso a dados ao ambiente de Azure Time Series insights](#granting-data-access).
1. Usar a **ID do aplicativo** e o **Segredo do cliente** para obter um token de `https://api.timeseries.azure.com/` em seu [aplicativo cliente](#client-app-initialization). O token pode ser usado para chamar a API de Azure Time Series Insights.

De acordo com a **etapa 3**, separar as credenciais do aplicativo e as do usuário permite:

* Atribuir permissões à identidade do aplicativo diferentes das suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo leia dados somente de um ambiente de Azure Time Series Insights específico.
* Isolar a segurança do aplicativo das credenciais de autenticação do usuário de criação usando um **Segredo do cliente** ou um certificado de segurança. Como resultado, as credenciais do aplicativo não dependem das credenciais de um usuário específico. Se a função do usuário for alterada, o aplicativo não precisará necessariamente de novas credenciais nem de configuração adicional. Se o usuário alterar sua senha, o acesso ao aplicativo não exigirá novas credenciais nem chaves.
* Executar um script autônomo usando um **Segredo do cliente** ou um certificado de segurança em vez das credenciais de um usuário específico (exigindo que ele esteja presente).
* Usar um certificado de segurança em vez de uma senha para proteger o acesso à API do Azure Time Series Insights.

> [!IMPORTANT]
> Siga o princípio de **Separação de escopos** (descrita para este cenário acima) ao configurar sua política de segurança do Azure Time Series Insights.

> [!NOTE]

> * Este artigo se concentra em um aplicativo de locatário único que se destina a ser executado em uma só organização.
> * Normalmente, você usa aplicativos com um locatário para os aplicativos da linha de negócios executados em sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registro de aplicativo do Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concedendo acesso a dados

1. Para o ambiente de Azure Time Series Insights, selecione **políticas de acesso a dados** e selecione **Adicionar**.

   [![Adicionar nova política de acesso a dados ao ambiente de Azure Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Selecionar Usuário**, cole o **Nome do Aplicativo** ou a **ID do Aplicativo** na seção registro do aplicativo do Azure Active Directory.

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **Leitor** para consultar dados ou **Colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolher Leitor ou Colaborador na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salve a política selecionando **OK**.

   > [!TIP]
   > Para opções de acesso a dados avançadas, leia [Concedendo acesso a dados](./concepts-access-policies.md).

### <a name="client-app-initialization"></a>Inicialização do aplicativo cliente

* Os desenvolvedores podem usar a [MSAL (biblioteca de autenticação da Microsoft) para autenticar com Azure Time Series Insights.

* Para autenticar usando MSAL:

   1. Use a **ID do Aplicativo** e o **Segredo do Cliente** (chave do aplicativo) da seção de registro do aplicativo do Azure Active Directory para adquirir o token em nome do aplicativo.

   1. Em C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo sobre como consultar dados de um ambiente Gen1, leia [dados de consulta usando C#](time-series-insights-query-data-csharp.md).

        Consulte o repositório [Azure Time Series insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] para acessar o código C#.

   1. Em seguida, o token pode ser passado no `Authorization` cabeçalho quando o aplicativo chama a API Azure Time Series insights.

> [!IMPORTANT]
> Se você estiver usando a [Adal (biblioteca de autenticação Azure Active Directory)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) , leia sobre [a migração para o MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP comuns e os parâmetros usados para fazer consultas em relação às APIs de Azure Time Series Insights Gen1 e Gen2. Os requisitos específicos da API são abordados com mais detalhes na [documentação de referência da API REST do Azure Time Series insights](/rest/api/time-series-insights/).

> [!TIP]
> Leia a [Referência da API REST do Azure](/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer solicitações HTTP e manipular respostas HTTP.

### <a name="authentication"></a>Autenticação

Para executar consultas autenticadas em relação às [APIs REST do Azure Time Series insights](/rest/api/time-series-insights/), um token de portador OAuth 2,0 válido deve ser passado no [cabeçalho Authorization](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) usando um cliente REST de sua escolha (post, JavaScript, C#).

> [!TIP]
> Leia a visualização de [exemplo do SDK do cliente](https://tsiclientsample.azurewebsites.net/) Azure Time Series insights hospedado para saber como autenticar com as APIs de Azure Time Series insights por meio de programação usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de solicitação necessários são descritos abaixo.

| Cabeçalho de solicitação necessário | Descrição |
| --- | --- |
| Autorização | Para autenticar com Azure Time Series Insights, um token de portador OAuth 2,0 válido deve ser passado no cabeçalho **Authorization** . |

> [!IMPORTANT]
> O token deve ser emitido exatamente para o recurso `https://api.timeseries.azure.com/` (também conhecido como o "público" do token).

> * Sendo assim, sua [AuthURL](https://www.getpostman.com/) do **Postman** será: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de solicitação opcionais são descritos abaixo.

| Cabeçalho de solicitação opcional | Descrição |
| --- | --- |
| Content-type | Apenas `application/json` tem suporte. |
| x-ms-client-request-id | Uma ID de solicitação do cliente. O serviço registra esse valor. Permite que o serviço rastreie a operação entre serviços. |
| x-ms-client-session-id | Uma ID de sessão do cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre serviços. |
| x-ms-client-application-name | Nome do aplicativo que gerou a solicitação. O serviço registra esse valor. |

Os cabeçalhos de resposta opcionais, mas recomendados, são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Content-type | Apenas `application/json` tem suporte. |
| x-ms-request-id | ID da solicitação gerada pelo servidor. Pode ser usada para contatar a Microsoft para investigar uma solicitação. |
| x-ms-property-not-found-behavior | Cabeçalho de resposta opcional da API de GA. Os valores possíveis são `ThrowError` (padrão) ou `UseNull`. |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Saiba mais sobre as informações de consulta necessárias e opcionais na [documentação de referência](/rest/api/time-series-insights/).

Os parâmetros necessários da cadeia de consulta de URL dependem da versão da API.

| Versão | Valores possíveis de versão da API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` e `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> A `api-version=2018-11-01-preview` versão será preterida em breve. Recomendamos que os usuários alternem para a versão mais recente.

Os parâmetros opcionais de cadeia de consulta de URL incluem a definição de um tempo limite para os tempos de execução da solicitação HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo limite do lado do servidor para a execução da solicitação HTTP. Aplicável somente às APIs [Obter Eventos de Ambiente](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Obter Agregações de Ambiente](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). O valor do tempo limite deve estar no formato de duração ISO 8601, por exemplo, `"PT20S"` e deve estar no intervalo `1-30 s`. O valor padrão é `30 s`. | Gen1 |
| `storeType=<storeType>` | Para ambientes Gen2 com armazenamento quente habilitado, a consulta pode ser executada no ou no `WarmStore` `ColdStore` . Esse parâmetro na consulta define em qual armazenamento a consulta deve ser executada. Se ele não estiver definido, a consulta será executada no armazenamento cold. Para consultar o armazenamento warm, **storeType** precisa ser definido como `WarmStore`. Se ele não estiver definido, a consulta será executada no armazenamento cold. | Gen2 |

## <a name="next-steps"></a>Próximas etapas

* Para obter o código de exemplo que chama a API de Azure Time Series Insights do Gen1, leia [consultar dados do Gen1 usando C#](./time-series-insights-query-data-csharp.md).

* Para obter o código de exemplo que chama os exemplos de código de API Gen2 Azure Time Series Insights, leia [consultar dados do Gen2 usando C#](./time-series-insights-update-query-data-csharp.md).

* Para obter informações de referência da API, leia a documentação de [Referência da API de Consulta](/rest/api/time-series-insights/gen1-query-api).

* Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).