---
title: Início rápido do Azure – criar um HSM gerenciado usando um modelo do Azure Resource Manager
description: Início rápido mostrando como criar um HSM Gerenciado do Azure Key Vault usando o modelo do Resource Manager
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 33c262c61d50b45663a627e40ea186f1f0dcde41
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532957"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Início Rápido: Criar um HSM Gerenciado do Key Vault usando um modelo do Azure Resource Manager

O HSM Gerenciado é um serviço de nuvem em conformidade com os padrões de um único locatário, altamente disponível e totalmente gerenciado que permite proteger chaves criptográficas para seus aplicativos de nuvem usando HSMs validados **FIPS 140-2 Nível 3**.  

Este Início Rápido concentra-se no processo de implantação de um modelo do Resource Manager para criar um HSM Gerenciado.  O [modelo do Resource Manager](../../azure-resource-manager/templates/overview.md) é um arquivo JSON (JavaScript Object Notation) que define a infraestrutura e a configuração do projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. Caso deseje saber mais sobre como desenvolver modelos do Resource Manager, confira a [documentação do Resource Manager](../../azure-resource-manager/index.yml) e a [referência de modelos](/azure/templates/microsoft.keyvault/allversions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará ter os seguintes itens:

- Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
- A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para obter mais informações sobre as opções de logon por meio da CLI, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Criar um HSM de gerenciamento

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

O recurso do Azure definido no modelo:

* **Microsoft.KeyVault/managedHSMs**: criar um HSM gerenciado do Azure Key Vault.

Mais exemplos de modelos do Azure Key Vault podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

O modelo requer a ID de objeto associada à sua conta. Para encontrá-la, use o comando [az ad user show](/cli/azure/ad/user#az_ad_user_show) da CLI do Azure, passando seu endereço de email para o parâmetro `--id`. Você pode limitar a saída à ID de objeto somente com o parâmetro `--query`.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Talvez você também precise da sua ID de locatário. Para encontrá-la, use o comando [az ad user show](/cli/azure/account#az_account_show) da CLI do Azure. Você pode limitar a saída à ID de locatário somente com o parâmetro `--query`.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Selecione ou insira os seguintes valores.

    A menos que seja especificado, use o valor padrão para criar o cofre de chaves e um segredo.

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: Selecione **Criar**, insira um nome exclusivo para o grupo de recursos e clique em **OK**.
    - **Localização**: Selecione um local. Por exemplo, **Centro-Sul dos EUA**.
    - **managedHSMName**: Insira um nome para o HSM gerenciado.
    - **ID do locatário**: A função de modelo recupera automaticamente sua ID de locatário. Não altere o valor padrão.  Se não há nenhum valor, insira a ID de locatário que você recuperou em [Pré-requisitos](#prerequisites).
    * **initialAdminObjectIds**: insira a ID de objeto que você recuperou em [Pré-requisitos](#prerequisites).

3. Selecione **Comprar**. Depois que o cofre de chaves for implantado com êxito, você receberá uma notificação:

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um HSM gerenciado. Esse HSM gerenciado não estará totalmente funcional até ser ativado. Confira [Ativar seu HSM gerenciado](quick-create-cli.md#activate-your-managed-hsm) para saber como ativar seu HSM.

- Leia uma [Visão geral do HSM gerenciado](overview.md)
- Saiba mais sobre [Gerenciar chaves em um HSM gerenciado](key-management.md)
- Examine as [Melhores práticas do HSM Gerenciado](best-practices.md)
