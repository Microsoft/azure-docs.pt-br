---
title: Criar um recurso dos Serviços Cognitivos com a CLI do Azure
titleSuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso usando a interface de linha de comando do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços de IA
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: c52d935738916ebc546315d9913d7a48c77cbf9a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945008"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Início rápido: criar um recurso de serviços cognitivas usando a CLI (interface de Command-Line do Azure)

Use este guia de início rápido para começar a usar os serviços cognitivas do Azure usando a [CLI (interface de linha de comando) do Azure](/cli/azure/install-azure-cli).

Os Serviços Cognitivos do Azure são serviços baseados em nuvem com APIs REST e SDKs de biblioteca de cliente disponíveis para ajudar os desenvolvedores a incutir a inteligência cognitiva nos aplicativos, sem ter conhecimentos ou habilidades diretas sobre os domínios de IA (inteligência artificial) ou ciência de dados. Os Serviços Cognitivos do Azure permitem que os desenvolvedores adicionem com facilidade recursos cognitivos a seus aplicativos, com soluções cognitivas capazes de ver, ouvir, falar, entender e até mesmo começar a racionalizar.

Os serviços cognitivas são representados pelos [recursos](../azure-resource-manager/management/manage-resources-portal.md) do Azure que você cria em sua assinatura do Azure. Depois de criar o recurso, use as chaves e o ponto de extremidade gerados para autenticar seus aplicativos.

Neste guia de início rápido, você aprenderá a se inscrever nos serviços cognitivas do Azure e criar uma conta que tenha uma assinatura de serviço único ou de vários serviços, usando a [CLI (interface de linha de comando) do Azure](/cli/azure/install-azure-cli). Esses serviços são representados pelos [recursos](../azure-resource-manager/management/manage-resources-portal.md)do Azure, que permitem que você se conecte a um ou mais dos API de serviços cognitivos do Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma](https://azure.microsoft.com/free/cognitive-services) gratuitamente.
* A [CLI (interface de linha de comando) do Azure](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Instalar o CLI do Azure e entrar

Instale a [CLI do Azure](/cli/azure/install-azure-cli). Para entrar na instalação local da CLI, execute o comando [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

Você também pode usar o botão de **teste** verde para executar esses comandos em seu navegador.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Criar um novo grupo de recursos dos serviços cognitivas do Azure

Antes de criar um recurso de serviços cognitivas, você deve ter um grupo de recursos do Azure para conter o recurso. Ao criar um novo recurso, você tem a opção de criar um novo grupo de recursos ou usar um existente. Este artigo mostra como criar um grupo de recursos.

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

No exemplo a seguir, substitua o local do Azure `westus2` por um dos locais do Azure disponíveis para sua assinatura.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Escolha um serviço cognitiva e um tipo de preço

Ao criar um novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com o tipo de [preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso.

### <a name="multi-service"></a>Vários serviços

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Vários serviços. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter mais detalhes.            | `CognitiveServices`     |


> [!NOTE]
> Muitos dos serviços cognitivas abaixo têm uma camada gratuita que você pode usar para experimentar o serviço. Para usar a camada gratuita, use `F0` como o SKU para seu recurso.

### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Pesquisa Visual Computacional            | `ComputerVision`          |
| Visão Personalizada – Previsão | `CustomVision.Prediction` |
| Visão Personalizada – Treinamento   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Reconhecimento de Formulários            | `FormRecognizer`          |
| Reconhecimento de Tinta Digital             | `InkRecognizer`           |

### <a name="search"></a>Search

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidade do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Fala

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Fala    | `SpeechServices`     |
| Reconhecimento de fala | `SpeakerRecognition` |

### <a name="language"></a>Idioma

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de Formulário | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de texto     | `TextAnalytics`     |
| Tradução de texto   | `TextTranslation`   |

### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detector de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |

Você pode encontrar uma lista de "tipos" de serviço cognitiva disponíveis com o comando [AZ cognitivaservices Account List-tipos](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adicionar um novo recurso ao seu grupo de recursos

Para criar e assinar um novo recurso de serviços cognitivas, use o comando [AZ cognitivaservices Account Create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . Esse comando adiciona um novo recurso cobrável ao grupo de recursos criado anteriormente. Ao criar o novo recurso, você precisará saber o "tipo" de serviço que deseja usar, juntamente com seu tipo de preço (ou SKU) e um local do Azure:

Você pode criar um recurso F0 (gratuito) para o detector de anomalias, chamado `anomaly-detector-resource` com o comando a seguir.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

Para fazer logon em sua instalação local da CLI (interface de Command-Line), use o comando [AZ login](/cli/azure/reference-index#az-login) .

```azurecli-interactive
az login
```

Use o comando [AZ cognitivaservices da lista de chaves de conta](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) para obter as chaves para o recurso de serviço cognitiva.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Tipos de preço e cobrança

Os tipos de preço (e o valor que é cobrado de você) são baseados no número de transações que você envia usando suas informações de autenticação. Cada tipo de preço especifica:
* o número máximo de transações permitidas por segundo (TPS).
* os recursos de serviço habilitados no tipo de preço.
* O custo de uma quantidade predefinida de transações. Ficar acima desse valor causará um encargo extra, conforme especificado nos [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

## <a name="get-current-quota-usage-for-your-resource"></a>Obter o uso de cota atual para seu recurso

Use o comando [AZ cognitivaservices Account List-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) para obter o uso de seu recurso de serviço cognitiva.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser limpar e remover um recurso de serviços cognitivas, poderá excluí-lo ou o grupo de recursos. Excluir o grupo de recursos também exclui outros recursos contidos nele.

Para remover o grupo de recursos e seus recursos associados, use o comando AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Veja também

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que são os Serviços Cognitivos do Azure?](./what-are-cognitive-services.md)
* [Suporte para idioma natural](language-support.md)
* [Suporte ao contêiner do Docker](cognitive-services-container-support.md)
