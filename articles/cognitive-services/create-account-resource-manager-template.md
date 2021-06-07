---
title: Criar um recurso dos Serviços Cognitivos do Azure usando modelos do ARM | Microsoft Docs
description: Crie um recurso de Serviço Cognitivo do Azure com o modelo do ARM.
keywords: serviços cognitivos, soluções cognitivas, inteligência cognitiva, inteligência artificial cognitiva
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 161c5779926acad8814ec057f24e36f371738483
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864356"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Início Rápido: Criar um recurso dos Serviços Cognitivos usando um modelo do ARM

Este início rápido descreve como usar um modelo do ARM (modelo do Azure Resource Manager) para criar Serviços Cognitivos.

Os Serviços Cognitivos do Azure são serviços baseados em nuvem com APIs REST e SDKs de biblioteca de cliente disponíveis para ajudar os desenvolvedores a incutir a inteligência cognitiva nos aplicativos, sem ter conhecimentos ou habilidades diretas sobre os domínios de IA (inteligência artificial) ou ciência de dados. Os Serviços Cognitivos do Azure permitem que os desenvolvedores adicionem com facilidade recursos cognitivos a seus aplicativos, com soluções cognitivas capazes de ver, ouvir, falar, entender e até mesmo começar a racionalizar.

Crie um recurso usando um modelo do ARM (modelo do Azure Resource Manager). Este recurso de vários serviços permite:

* Acessar vários Serviços Cognitivos do Azure com uma chave e um ponto de extremidade.
* Reúna todos os serviços que você usa em uma cobrança.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos do ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar o serviço cognitivo no Azure](../media/template-deployments/deploy-to-azure.svg "Implantar o serviço cognitivo no Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

Um recurso do Azure é definido no modelo:
* [Microsoft.CognitiveServices/accounts](/azure/templates/microsoft.cognitiveservices/accounts): cria um recurso dos Serviços Cognitivos.

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Clique no botão **Implantar no Azure**.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Insira os valores a seguir.

    |Valor  |Descrição  |
    |---------|---------|
    | **Assinatura** | Selecione uma assinatura do Azure. |
    | **Grupo de recursos** | Selecione **Criar**, insira um nome exclusivo para o grupo de recursos e clique em **OK**. |
    | **Região** | Selecione uma região.  Por exemplo, **Leste dos EUA** |
    | **Nome do Serviço Cognitivo** | Substitua por um nome exclusivo para seu recurso. Você precisará do nome na próxima seção ao validar a implantação. |
    | **Localidade** | Substitua pela região usada acima. |
    | **Sku** | O [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) do seu recurso. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Tela de criação do recurso.":::

3. Selecione **Examinar + Criar** e **Criar**. Depois que o recurso tiver concluído a implantação com êxito, o botão **Ir para o recurso** estará realçado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

> [!NOTE]
> A criação do `az deployment group` requer a CLI do Azure versão 2.6 ou mais recente. Para exibir a versão, digite `az --version`. Para obter mais informações, confira a [documentação](/cli/azure/deployment/group).

Execute o script a seguir usando a CLI (interface de linha de comando) do Azure [em seu computador local](/cli/azure/install-azure-cli) ou em um navegador com o botão **Experimentar**. Insira um nome e uma localização (por exemplo, `centralus`) para um novo grupo de recursos e o modelo do ARM será usado para implantar um recurso dos Serviços Cognitivos dentro dele. Lembre-se do nome que você usar. Você o usará posteriormente para validar a implantação.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/portal)

Quando a implantação for concluída, você poderá clicar no botão **Ir para o recurso** para ver o novo recurso. Também é possível encontrar o grupo de recursos:

1. Selecionando **Grupos de recursos** no menu de navegação esquerdo.
2. Selecionando o nome do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Usando a CLI do Azure, execute o script a seguir e insira o nome do grupo de recursos criado anteriormente.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui outros recursos contidos nele.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos que contém o recurso a ser excluído
3. Clique com o botão direito do mouse na listagem do grupo de recursos. Selecione **Excluir grupo de recursos** e confirme.

# <a name="azure-cli"></a>[CLI do Azure](#tab/CLI)

Usando a CLI do Azure, execute o script a seguir e insira o nome do grupo de recursos criado anteriormente.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="see-also"></a>Veja também

* Confira **[Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)** para saber como trabalhar com segurança com os Serviços Cognitivos.
* Confira **[O que são os Serviços Cognitivos do Azure?](./what-are-cognitive-services.md)** para obter uma lista de categorias diferentes dentro dos Serviços Cognitivos.
* Confira **[Suporte para linguagem natural](language-support.md)** para ver a lista de linguagens naturais a que os Serviços Cognitivos dão suporte.
* Confira **[Usar os Serviços Cognitivos como contêineres](cognitive-services-container-support.md)** para saber como usar os Serviços Cognitivos no local.
* Confira **[Planejar e gerenciar custos para os Serviços Cognitivos](plan-manage-costs.md)** para estimar o custo de uso dos Serviços Cognitivos.
