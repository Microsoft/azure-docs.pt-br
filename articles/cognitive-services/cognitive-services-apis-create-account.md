---
title: Criar uma conta de Serviços Cognitivos no portal do Azure
titlesuffix: Azure Cognitive Services
description: Como criar uma conta de APIs de serviços Cognitivos do Azure no portal do Azure.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 6950cba5ac958233e7ea77c8dc783ca86cc5a386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829362"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Início Rápido: Criar uma conta de Serviços Cognitivos no portal do Azure

Neste início rápido, você aprenderá a se inscrever nos Serviços Cognitivos do Azure e criar uma assinatura de serviço único ou de vários serviços. Esses serviços são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure, que permitem a conexão com uma ou mais APIs de Serviços Cognitivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Criar e assinar um recurso dos Serviços Cognitivos do Azure

Antes de começar, é importante saber que há dois tipos de assinaturas dos Serviços Cognitivos do Azure. A primeira é uma assinatura de um único serviço, como Pesquisa Visual Computacional ou Serviços de Fala. Uma assinatura de serviço único fica restrita ao recurso em questão. A segunda é uma assinatura de vários serviços para os Serviços Cognitivos do Azure. Esta assinatura permite que você use uma única assinatura para a maioria dos Serviços Cognitivos do Azure. Essa opção também consolida a cobrança. Consulte [Preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter mais informações.

>[!WARNING]
> No momento, estes serviços **não** têm suporte para chaves de vários serviços: O QnA Maker, serviços de fala, visão personalizada e Detector de anomalias.

As seções a seguir detalham como criar uma assinatura de um ou de vários serviços.


### <a name="multi-service-subscription"></a>Assinatura de vários serviços

1. Entrar para o [portal do Azure](https://portal.azure.com), clique em  **criar+ um recurso**.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Localize a barra de pesquisa e digite: **Serviços Cognitivos**.

    ![Pesquise pelos Serviços Cognitivos](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Selecione **Serviços Cognitivos**.

    ![Selecione Serviços Cognitivos](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Na página **Criar**, forneça as seguintes informações:

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. É recomendável usar um nome descritivo, por exemplo *MyCognitiveServicesAccount*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Tipo de preços** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) que conterá o recurso de serviços Cognitivos. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>Assinatura de serviço único

1. Entrar para o [portal do Azure](https://portal.azure.com), clique em  **criar+ um recurso**.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Em Azure Marketplace, selecione **AI + Machine Learning**. Se você não encontrar o serviço em que está interessado, clique em **Veja todos** para visualizar o catálogo inteiro de APIs de Serviços Cognitivos.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na página **Criar**, forneça as seguintes informações:

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. É recomendável usar um nome descritivo, por exemplo *MyNameFaceAPIAccount*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Tipo de preços** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) que conterá o recurso de serviços Cognitivos. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Acessar seu recurso

> [!NOTE]
> Proprietários de assinatura podem desabilitar a criação de contas de Serviços Cognitivos para grupos de recursos e inscrições, aplicando [política do Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), atribuindo uma definição de política "Não permitido a tipos de recursos" e especificando **Microsoft.CognitiveServices / accounts** como o tipo de recurso de destino.

Depois de criar seu recurso, você pode acessá-lo no painel do Azure se você fixou-o. Caso contrário, você pode encontrá-lo em **Grupos de Recursos**.

Em seu recurso de Serviços Cognitivos, você pode usar a URL e as chaves do Nó de Extremidade na seção **Visão Geral** para começar a fazer chamadas de API em seus aplicativos.

![Tela de recursos](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)

## <a name="see-also"></a>Consulte também

* [Início Rápido: Extrair texto manuscrito de uma imagem](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Tutorial: Criar um aplicativo para detectar e enquadrar faces em uma imagem](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Criar uma página da Web de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrar Luis (reconhecimento vocal a linguagem) com um bot usando a estrutura do Bot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
