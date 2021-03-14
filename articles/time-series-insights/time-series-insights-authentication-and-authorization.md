---
title: Autenticação e autorização de API – Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 88fd575d40cc31f12f052158bda0aed9a5335555
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009259"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Dependendo das suas necessidades de negócios, sua solução pode incluir um ou mais aplicativos cliente que você usa para interagir com as [APIs](/rest/api/time-series-insights/reference-data-access-overview)do seu ambiente de Azure Time Series insights. Azure Time Series Insights executa a autenticação usando [tokens de segurança do Azure AD com base no OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Para autenticar seus clientes, você precisará obter um token de portador com as permissões corretas e passá-lo junto com suas chamadas à API. Este documento descreve vários métodos para obter credenciais que você pode usar para obter um token de portador e autenticar, incluindo o uso de identidade gerenciada e o registro de aplicativo Azure Active Directory.

## <a name="managed-identities"></a>Identidades gerenciadas

As seções a seguir descrevem como usar uma identidade gerenciada do Azure Active Directory (AD do Azure) para acessar a API do Azure Time Series Insights. No Azure, as identidades gerenciadas eliminam a necessidade de os desenvolvedores precisarem gerenciar credenciais fornecendo uma identidade para o recurso do Azure no Azure AD e a usando para obter tokens do Azure AD (Azure Active Directory). Aqui estão alguns dos benefícios de usar identidades gerenciadas:

- Você não precisa gerenciar credenciais. As credenciais nem sequer estão acessíveis para você.
- Você pode usar identidades gerenciadas para fazer a autenticação em qualquer serviço do Azure que dê suporte à autenticação do Azure AD incluindo o Azure Key Vault.
- Identidades gerenciadas podem ser usadas sem nenhum custo adicional.

Para obter mais informações sobre os dois tipos de identidades gerenciadas, leia [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Você pode usar identidades gerenciadas do seu:

- VMs do Azure
- Serviços de Aplicativo do Azure
- Azure Functions
- Instâncias de contêiner do Azure
- e muito mais...

Consulte [Serviços do Azure que dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) para obter a lista completa.

## <a name="azure-active-directory-app-registration"></a>Registro de aplicativo do Azure Active Directory

É recomendável usar identidades gerenciadas sempre que possível para que você não precise gerenciar credenciais. Se o aplicativo cliente não estiver hospedado em um serviço do Azure que dá suporte a identidades gerenciadas, você poderá registrar seu aplicativo com um locatário do Azure AD. Ao registrar seu aplicativo com o Azure AD, você está criando uma configuração de identidade para seu aplicativo que permite que ele se integre ao Azure AD. Ao registrar um aplicativo no [portal do Azure](https://portal.azure.com/), você escolhe se ele é um único locatário (acessível somente em seu locatário) ou multilocatário (acessível em outros locatários) e pode, opcionalmente, definir um URI de redirecionamento (para o qual o token de acesso é enviado).

Quando você concluir o registro do aplicativo, terá uma instância globalmente exclusiva do aplicativo (o objeto Application) que reside em seu diretório ou locatário inicial. Você também tem uma ID globalmente exclusiva para seu aplicativo (a ID do aplicativo ou do cliente). No portal, você pode adicionar segredos ou certificados e escopos para fazer seu aplicativo funcionar, personalizar a identidade visual do aplicativo na caixa de diálogo de entrada e muito mais.

Se você registrar um aplicativo no portal, um objeto de aplicativo, bem como um objeto de entidade de serviço, será criado automaticamente em seu locatário inicial. Se você registrar/criar um aplicativo usando as APIs de Microsoft Graph, a criação do objeto de entidade de serviço será uma etapa separada. Um objeto de entidade de serviço é necessário para solicitar tokens.

Certifique-se de examinar a lista de verificação de [segurança](../active-directory/develop/identity-platform-integration-checklist.md#security) para seu aplicativo. Como prática recomendada, você deve usar [credenciais de certificado](../active-directory/develop/active-directory-certificate-credentials.md), não credenciais de senha (segredos de cliente).

Consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) para obter mais detalhes.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Etapa 1: criar sua identidade gerenciada ou registro de aplicativo

Depois de identificar se você usará uma identidade gerenciada ou um registro de aplicativo, a próxima etapa será provisionar uma.

### <a name="managed-identity"></a>Identidade gerenciada

As etapas que você usará para criar uma identidade gerenciada variam de acordo com o local em que o código está localizado e se você está criando ou não uma identidade atribuída pelo sistema ou pelo usuário. Leia [tipos de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para entender a diferença. Depois de selecionar o tipo de identidade, localize e siga o tutorial correto na [documentação](../active-directory/managed-identities-azure-resources/index.yml)de identidades gerenciadas do Azure AD. Lá, você encontrará instruções sobre como configurar identidades gerenciadas para o:

- [VMs do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [Serviço de aplicativo e Azure Functions](../app-service/overview-managed-identity.md)
- [Instâncias de Contêiner do Azure](../container-instances/container-instances-managed-identity.md)
- e muito mais...

### <a name="application-registration"></a>Registro de aplicativo

Siga as etapas listadas em [registrar um aplicativo](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Etapa 2: conceder acesso

Quando o ambiente de Azure Time Series Insights recebe uma solicitação, primeiro o token de portador do chamador é validado. Se a validação for aprovada, o chamador foi autenticado e outra verificação será feita para garantir que o chamador esteja autorizado a executar a ação solicitada. Para autorizar qualquer usuário ou entidade de serviço, primeiro você deve conceder a eles acesso ao ambiente, atribuindo a função de leitor ou colaborador.

- Para conceder acesso por meio da interface do usuário do [portal do Azure](https://portal.azure.com/) , siga as instruções listadas no artigo [conceder acesso a dados a um ambiente](concepts-access-policies.md) . Ao selecionar o usuário, você pode pesquisar a identidade gerenciada ou o registro do aplicativo por seu nome ou por ID.

- Para conceder acesso usando o CLI do Azure, execute o comando a seguir. Examine a documentação [aqui](/cli/azure/ext/timeseriesinsights/tsi/access-policy) para obter a lista completa de comandos disponíveis para gerenciar o acesso.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> A extensão timeseriesinsights para CLI do Azure requer a versão 2.11.0 ou superior. A extensão será instalada automaticamente na primeira vez que você executar um comando AZ TSI Access-Policy. [Saiba mais](/cli/azure/azure-cli-extensions-overview) sobre extensões.

## <a name="step-3-requesting-tokens"></a>Etapa 3: solicitando tokens

Depois que a identidade gerenciada ou o registro do aplicativo tiver sido provisionado e atribuído uma função, você estará pronto para começar a usá-lo para solicitar tokens de portador OAuth 2,0. O método usado para obter um token será diferente dependendo de onde seu código está hospedado e do idioma escolhido. Ao especificar o recurso (também conhecido como "público" do token), você pode identificar Azure Time Series Insights por sua URL ou GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Se você usar a URL como a ID do recurso, o token deverá ser emitido exatamente para `https://api.timeseries.azure.com/` . A barra à direita é necessária.

> * Se estiver usando o [postmaster](https://www.getpostman.com/) , seu **AuthURL** será: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

### <a name="managed-identities"></a>Identidades gerenciadas

Ao acessar de Azure App serviço ou funções, siga as orientações em [obter tokens para recursos do Azure](../app-service/overview-managed-identity.md).

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio da [biblioteca de cliente de identidade do Azure](/dotnet/api/overview/azure/identity-readme) para .net. Essa biblioteca de cliente é popular devido a seus benefícios de simplicidade e de segurança. Os desenvolvedores podem escrever código uma vez e permitir que a biblioteca de cliente determine como autenticar com base no ambiente de aplicativo – seja em uma estação de trabalho de desenvolvedor usando a conta de um desenvolvedor ou implantada no Azure usando uma identidade de serviço gerenciada. Para obter diretrizes de migração da biblioteca predecessora AppAuthentication, leia [AppAuthentication para o Azure. diretrizes de migração de identidade](/dotnet/api/overview/azure/app-auth-migration).

Solicite um token para Azure Time Series Insights usando C# e a biblioteca de cliente de identidade do Azure para .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Registro do aplicativo

* Os desenvolvedores podem usar a MSAL ( [biblioteca de autenticação da Microsoft](../active-directory/develop/msal-overview.md) ) para obter tokens para registros de aplicativo.

O MSAL pode ser usado em muitos cenários de aplicativos, incluindo, mas não se limitando a:

* [Aplicativos de página única (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Aplicativo Web que conecta um usuário e chama uma API Web em nome dele](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [API Web que chama outra API Web downstream em nome do usuário conectado](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Aplicativo de desktop chamando uma API da Web em nome do usuário conectado](../active-directory/develop/scenario-desktop-overview.md)
* [Aplicativo móvel chamando uma API da Web em nome do usuário que está conectado interativamente](../active-directory/develop/scenario-mobile-overview.md).
* [Aplicativo daemon da área de trabalho/de serviço que chama uma API Web em seu próprio nome](../active-directory/develop/scenario-daemon-overview.md)

Para exemplo de código C# mostrando como adquirir um token como um registro de aplicativo e dados de consulta de um ambiente Gen2, exiba o aplicativo de exemplo no [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Se você estiver usando a [Adal (biblioteca de autenticação Azure Active Directory)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) , leia sobre [a migração para o MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP comuns e os parâmetros usados para fazer consultas em relação às APIs de Azure Time Series Insights Gen1 e Gen2. Os requisitos específicos da API são abordados com mais detalhes na [documentação de referência da API REST do Azure Time Series insights](/rest/api/time-series-insights/).

> [!TIP]
> Leia a [Referência da API REST do Azure](/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer solicitações HTTP e manipular respostas HTTP.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de solicitação necessários são descritos abaixo.

| Cabeçalho de solicitação necessário | Descrição |
| --- | --- |
| Autorização | Para autenticar com Azure Time Series Insights, um token de portador OAuth 2,0 válido deve ser passado no [cabeçalho Authorization](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Leia a visualização de [exemplo do SDK do cliente](https://tsiclientsample.azurewebsites.net/) Azure Time Series insights hospedado para saber como autenticar com as APIs de Azure Time Series insights por meio de programação usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos.

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

| Versão | Valores de versão da API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Os parâmetros opcionais de cadeia de consulta de URL incluem a definição de um tempo limite para os tempos de execução da solicitação HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo limite do lado do servidor para a execução da solicitação HTTP. Aplicável somente às APIs [Obter Eventos de Ambiente](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Obter Agregações de Ambiente](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). O valor do tempo limite deve estar no formato de duração ISO 8601, por exemplo, `"PT20S"` e deve estar no intervalo `1-30 s`. O valor padrão é `30 s`. | Gen1 |
| `storeType=<storeType>` | Para ambientes Gen2 com armazenamento quente habilitado, a consulta pode ser executada no ou no `WarmStore` `ColdStore` . Esse parâmetro na consulta define em qual armazenamento a consulta deve ser executada. Se ele não estiver definido, a consulta será executada no armazenamento cold. Para consultar o armazenamento warm, **storeType** precisa ser definido como `WarmStore`. Se ele não estiver definido, a consulta será executada no armazenamento cold. | Gen2 |

## <a name="next-steps"></a>Próximas etapas

* Para obter o código de exemplo que chama a API de Azure Time Series Insights do Gen1, leia [consultar dados do Gen1 usando C#](./time-series-insights-query-data-csharp.md).

* Para obter o código de exemplo que chama os exemplos de código de API Gen2 Azure Time Series Insights, leia [consultar dados do Gen2 usando C#](./time-series-insights-update-query-data-csharp.md).

* Para obter informações de referência da API, leia a documentação de [Referência da API de Consulta](/rest/api/time-series-insights/reference-query-apis).
