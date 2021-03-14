---
title: Visão geral dos Serviços de Mídia do Azure | Microsoft Docs
description: Os Serviços de Mídia do Microsoft Azure tratam-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Este artigo fornece uma visão geral dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008222"
---
# <a name="azure-media-services-overview"></a>Visão geral dos Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/media-services-overview.md)
> * [Versão 2](media-services-overview.md)

> [!NOTE]
> Não estão sendo adicionados novos recursos aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

O AMS (Serviços de Mídia do Microsoft Azure) trata-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia. Você pode optar por transmitir seu conteúdo ao vivo ou fornecer conteúdo sob demanda. 


## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você precisará respeitar todas as leis aplicáveis em seu uso dos Serviços de Mídia do Azure e não poderá usar os Serviços de Mídia ou qualquer serviço do Azure de maneira que viole os direitos de outras pessoas ou que possa ser prejudicial a outras pessoas.

Antes de carregar qualquer vídeo/imagem nos Serviços de Mídia, você precisará ter todos os direitos adequados para usar o vídeo/a imagem, incluindo, quando exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/na imagem, para uso, processamento e armazenamento dos respectivos dados nos Serviços de Mídia e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a coleta, o processamento online e o armazenamento de determinadas categorias de dados, como dados biométricos. Antes de usar os Serviços de Mídia e o Azure para o processamento e o armazenamento de dados sujeitos a requisitos legais especiais, você precisará garantir a conformidade com os requisitos legais que possam se aplicar a você.

Para saber mais sobre conformidade, privacidade e segurança nos Serviços de Mídia, acesse a [Central de Confiabilidade](https://www.microsoft.com/trust-center/?rtc=1) da Microsoft. Para as obrigações de privacidade da Microsoft, a manipulação de dados e as práticas de retenção, incluindo como excluir seus dados, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os [termos de serviços online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e o [adendo de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao usar os Serviços de Mídia, você concorda em vincular-se ao OST, ao DPA e à política de privacidade.
 
## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Media Services, você deve possuir o seguinte:

* Uma conta do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-portal-create-account.md).
* (Opcional) Configure o ambiente de desenvolvimento. Escolha .NET ou API REST para seu ambiente de desenvolvimento. Para obter mais informações, veja [Configurar ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [conectar-se programaticamente à API do AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Um ponto de extremidade de streaming padrão ou premium em estado iniciado.  Para obter mais informações, consulte [Gerenciando pontos de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de serviços de mídia, você pode usar:

* [API REST dos Serviços de Mídia](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs de cliente disponíveis:
    * SDK dos serviços de mídia do Azure para .NET
    
        * [Pacote NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Código-fonte do GitHub](https://github.com/Azure/azure-sdk-for-media-services)
    * [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java),
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Serviços de Mídia do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (esta é uma versão de um SDK do Node.js que não foi criada pela Microsoft. Ele é mantido por uma comunidade e atualmente não tem cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Portal do Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) ([AMSE] Gerenciador de Serviços de Mídia do Azure é um aplicativo Winforms/C# para Windows)

> [!NOTE]
> Para obter a versão mais recente do SDK do Java e começar a desenvolver com Java, confira [Introdução ao SDK de cliente Java para Serviços de Mídia](./media-services-java-how-to-use.md). <br/>
> Para baixar o SDK mais recente do PHP para os Serviços de Mídia, procure a versão 0.5.7 do pacote do Microsoft/WindowAzure no [Repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Exemplos de código

Localize vários exemplos de código na galeria **Exemplos de Código do Azure**: [exemplos de código dos Serviços de Mídia do Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Conceitos

Para conferir os conceitos dos Serviços de Mídia do Azure, confira [Conceitos](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Cenários com suporte e disponibilidade dos Serviços de Mídia em data centers

Para obter mais informações sobre os cenários comuns do Azure, consulte [cenários do AMS](scenarios-and-availability.md).
Para obter informações sobre a disponibilidade regional, consulte [disponibilidade do serviço de mídia](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para obter mais informações, veja [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Suporte

[Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo os Serviços de Mídia.

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
