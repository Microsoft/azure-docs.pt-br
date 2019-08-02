---
title: Criar um recurso de serviços cognitivas usando o CLI do Azure
titleSuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso usando a interface de linha de comando do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: aahi
ms.openlocfilehash: 8f3d30866ac6db0cdece9af8144fcfa266871172
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697901"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Criar um recurso de serviços cognitivas usando a CLI (interface de linha de comando) do Azure

Use este guia de início rápido para começar a usar os serviços cognitivas do Azure usando a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Os serviços cognitivas são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure que você cria em sua assinatura do Azure. Depois de criar o recurso, use as chaves e o ponto de extremidade gerados para autenticar seus aplicativos. 


Neste guia de início rápido, você aprenderá a se inscrever nos serviços cognitivas do Azure e criar uma conta que tenha uma assinatura de serviço único ou de vários serviços, usando a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Esses serviços são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)do Azure, que permitem que você se conecte a um ou mais dos API de serviços cognitivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma](https://azure.microsoft.com/free/) gratuitamente.
* A [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Instalar o CLI do Azure e entrar 

Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Para entrar na instalação local da CLI, execute o comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) :

```console
az login
```

Você também pode usar o botão de **teste** verde para executar esses comandos em seu navegador.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Criar um novo grupo de recursos dos serviços cognitivas do Azure

Antes de criar um recurso de serviços cognitivas, você deve ter um grupo de recursos do Azure para conter o recurso. Ao criar um novo recurso, você tem a opção de criar um novo grupo de recursos ou usar um existente. Este artigo mostra como criar um novo grupo de recursos.

### <a name="choose-your-resource-group-location"></a>Escolha o local do grupo de recursos

Para criar um recurso, você precisará de um dos locais do Azure disponíveis para sua assinatura. Você pode recuperar uma lista de locais disponíveis com o comando [AZ Account List-Locations](/cli/azure/account#az-account-list-locations) . A maioria dos serviços cognitivas pode ser acessada de vários locais. Escolha o mais próximo a você ou veja quais locais estão disponíveis para o serviço.

> [!IMPORTANT]
> * Lembre-se do local do Azure, pois você precisará dele ao chamar os serviços cognitivas do Azure.
> * A disponibilidade de alguns serviços cognitivas pode variar por região. Para obter mais informações, consulte [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Depois de ter o local do Azure, crie um novo grupo de recursos no CLI do Azure usando o comando [AZ Group Create](/cli/azure/group#az-group-create) .

No exemplo a seguir, substitua o local `westus2` do Azure por um dos locais do Azure disponíveis para sua assinatura.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolha um serviço cognitiva e um tipo de preço

Ao criar um novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com o tipo de [preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso.

> [!NOTE]
> Muitos serviços cognitivas têm uma camada gratuita que você pode usar para experimentar o serviço. Para usar a camada gratuita, use `F0` como o SKU para seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | Espécie                      |
|----------------------------|---------------------------|
| Pesquisa Visual Computacional            | `ComputerVision`          |
| Visão Personalizada-previsão | `CustomVision.Prediction` |
| Visão Personalizada-treinamento   | `CustomVision.Training`   |
| API de Detecção Facial                   | `Face`                    |
| Reconhecimento de Formulários            | `FormRecognizer`          |
| Reconhecimento de Tinta Digital             | `InkRecognizer`           |

### <a name="search"></a>Pesquisar

| Serviço            | Espécie                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidade do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Fala

| Serviço            | Espécie                 |
|--------------------|----------------------|
| Serviços de Fala    | `SpeechServices`     |
| Reconhecimento de fala | `SpeakerRecognition` |

### <a name="language"></a>Idioma

| Serviço            | Espécie                |
|--------------------|---------------------|
| Compreensão do formulário | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de Texto     | `TextAnalytics`     |
| Tradução de Texto   | `TextTranslation`   |

### <a name="decision"></a>Decisão

| Serviço           | Espécie               |
|-------------------|--------------------|
| Detector de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |

Você pode encontrar uma lista de "tipos" de serviço cognitiva disponíveis com o comando [AZ cognitivaservices Account List-tipos](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicionar um novo recurso ao seu grupo de recursos

Para criar e assinar um novo recurso de serviços cognitivas, use o comando [AZ cognitivaservices Account Create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) . Esse comando adiciona um novo recurso cobrável ao grupo de recursos criado anteriormente. Ao criar o novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com seu tipo de preço (ou SKU) e um local do Azure:

Você pode criar um recurso F0 (gratuito) para o detector de anomalias `anomaly-detector-resource` , chamado com o comando a seguir.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

Para fazer logon em sua instalação local da CLI (interface de linha de comando), use o comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```console
az login
```

Use o comando [AZ cognitivaservices da lista de chaves de conta](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) para obter as chaves para o recurso de serviço cognitiva.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Tipos de preço e cobrança

Os tipos de preço (e o valor que você recebe é cobrado) baseiam-se no número de transações que você envia usando suas informações de autenticação. Cada tipo de preço especifica:
* número máximo de transações permitidas por segundo (TPS).
* recursos de serviço habilitados no tipo de preço.
* O custo de uma quantidade predefinida de transações. Ficar acima desse valor causará um encargo extra, conforme especificado nos [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.


## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover um recurso de serviços cognitivas, poderá excluí-lo ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos contidos no grupo.

Para remover o grupo de recursos e seus recursos associados, use o comando AZ Group Delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Consulte também

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que são os serviços cognitivas do Azure?](Welcome.md)
* [Suporte a idioma natural](language-support.md)
* [Suporte a contêiner do Docker](cognitive-services-container-support.md)
