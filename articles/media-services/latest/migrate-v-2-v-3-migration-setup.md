---
title: Configuração de migração dos serviços de mídia v2 para v3
description: Este artigo ajudará você a configurar seu ambiente para migrar dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do Azure, migração, fluxo, difusão, ao vivo, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 648003c042d4c8118af566a6c985d2fd6e26b04e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563031"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Etapa 3-configurar para migrar para a API REST V3 ou o SDK do cliente

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-3.svg)

Veja a seguir uma descrição das etapas a serem seguidas para configurar seu ambiente para usar a API dos serviços de mídia v3.

## <a name="sdk-model"></a>Modelo SDK

Na API v2, havia dois SDKs de cliente diferentes, um para a API de gerenciamento, que permitia a criação programática de contas e outra para o gerenciamento de recursos.

Anteriormente, os desenvolvedores trabalharam com uma ID de cliente da entidade de serviço do Azure e o segredo do cliente, juntamente com um ponto de extremidade de API REST do v2 específico para sua conta do AMS.

A API v3 é baseada no ARM (gerenciamento de recursos do Azure). Ele usa as IDs e chaves da entidade de serviço do Azure Active Directory (AD do Azure) para se conectar à API. Os desenvolvedores precisarão criar entidades de serviço ou identidades gerenciadas para se conectar à API. Na API v3, a API usa pontos de extremidade padrão do ARM, usa um modelo semelhante e consistente para todos os outros serviços do Azure.

Os clientes que usavam a versão 2015-10-01 da API de gerenciamento do ARM para gerenciar suas contas v2 devem usar a versão 2020-05-01 da API de gerenciamento ARM com suporte para acesso à API v3.

## <a name="create-a-new-media-services-account-for-testing"></a>Criar uma nova conta dos serviços de mídia para teste

Siga as etapas de início rápido para [configurar seu ambiente](how-to-set-azure-subscription.md?tabs=portal) usando o portal do Azure. Selecione acesso à API e autenticação da entidade de serviço para gerar uma nova ID do aplicativo do Azure AD e segredos para uso com esta conta de teste.

[Crie uma conta dos serviços de mídia](create-account-howto.md?tabs=portal).
[Obtenha credenciais para acessar a API dos serviços de mídia](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Baixe o SDK do cliente de sua escolha e configure seu ambiente

- SDKs disponíveis para [.net](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)e [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- [CLI do Azure](/cli/azure/ams)   integração para suporte a scripts simples.

> [!NOTE]
> Um SDK da comunidade PHP não está mais disponível para os serviços de mídia do Azure na v3. Se você estiver usando PHP na v2, deverá migrar para a API REST diretamente no seu código.

## <a name="open-api-specifications"></a>Especificações abertas da API

- O V3 se baseia em uma superfície de API unificada, que expõe a funcionalidade de gerenciamento e operações criada em Azure Resource Manager. Azure Resource Manager modelos podem ser usados para criar e implantar transformações, pontos de extremidade de streaming, eventos ao vivo e muito mais.

- O documento [especificação openapi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (anteriormente chamado de Swagger) explica o esquema para todos os componentes de serviço.

- Todos os SDKs do cliente são derivados e gerados a partir da especificação da API aberta publicada no GitHub. No momento da publicação deste artigo, as especificações de API aberta mais recentes são mantidas publicamente no GitHub. A versão 2020-05-01 é a versão [mais recente estável](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Usar o [postmaster](./media-rest-apis-with-postman.md) para chamadas da API REST dos serviços de mídia v3.
Leia as [páginas de referência da API REST](/rest/api/media/).

Você deve usar a cadeia de caracteres da versão 2020-05-01 na coleção do postmaster.

## <a name="net"></a>[.NET](#tab/net)

Leia o artigo [conectar-se à API dos serviços de mídia v3 com .net](configure-connect-dotnet-howto.md) para configurar seu ambiente.

Se você simplesmente deseja instalar o SDK mais recente usando o PackageManager, use o seguinte comando:

```Install-Package Microsoft.Azure.Management.Media```

Ou para instalar o SDK mais recente usando a CLI do .NET, use o seguinte comando:

```dotnet add package Microsoft.Azure.Management.Media```

Além disso, os exemplos completos do .NET estão disponíveis no [Azure-Samples/Media-Services-V3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) para vários cenários. Os projetos neste repositório mostram como implementar diferentes cenários de serviços de mídia do Azure usando a versão v3.

### <a name="get-started-adjusting-your-code"></a>Comece a ajustar seu código

Pesquise sua base de código para instâncias de `CloudMediaContext` uso para iniciar o processo de atualização para a API v3.

O código a seguir mostra como a API v2 foi acessada anteriormente usando o SDK do .NET v2. Os desenvolvedores começaram a criar um `CloudMediaContext` e criar uma instância com um `AzureAdTokenCredentials` objeto.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Leia o artigo [conectar-se à API dos serviços de mídia v3 com Java](configure-connect-java-howto.md) para configurar seu ambiente.

## <a name="python"></a>[Python](#tab/python)

Leia o artigo [conectar-se ao Azure Media Services V3 API-Python](configure-connect-python-howto.md) para configurar seu ambiente.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Leia o artigo [conectar-se ao API dos serviços de mídia do Azure v3-Node.js](configure-connect-nodejs-howto.md) para configurar seu ambiente.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Obtenha o SDK do [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) no github.

## <a name="go"></a>[Go](#tab/go)

Baixe o SDK [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) .

---
