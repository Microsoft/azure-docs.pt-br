---
title: Criar uma zona e um registro DNS do Azure – modelo do ARM (Azure Resource Manager)
titleSuffix: Azure DNS
description: Saiba como criar uma zona e registro DNS no DNS do Azure. Este é um guia de início rápido passo a passo para criar e gerenciar sua primeira zona e registro DNS usando um modelo do ARM (Azure Resource Manager).
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539011"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Criar uma zona e um registro DNS do Azure usando um modelo ARM

Este guia de início rápido descreve como usar um modelo do Azure Resource Manager (modelo do ARM) para criar uma zona DNS que contém um registro `A`.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

Neste guia de início rápido, você criará uma zona DNS exclusiva com o sufixo `azurequickstart.org`. Um registro `A` apontando para dois endereços IP também será colocado na zona.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Dois recursos do Azure foram definidos no modelo:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): Usado para criar um registro `A` na zona.

Para encontrar mais modelos relacionados ao Gerenciador de Tráfego do Azure, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Aguarde até ver o prompt do console.

1. Selecione **Copiar** no bloco de códigos anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do shell e selecione **Colar**.

1. Insira os valores.

    A implantação de modelo cria uma zona com um registro `A` que aponta para dois endereços IP. O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**.

    O modelo leva alguns segundos para ser implantado. Quando tiver concluído, a saída deverá ser semelhante a:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Saída da implantação no PowerShell do modelo do Resource Manager de zona DNS do Azure":::

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com o acréscimo de **rg**.

1. O grupo de recursos deve conter os seguintes recursos vistos aqui:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Grupo de recursos da implantação de zona DNS":::

1. Selecione a zona DNS com o sufixo `azurequickstart.org` para verificar se a zona foi criada corretamente com um registro `A` referenciando os valores `1.2.3.4` e `1.2.3.5`.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Implantação de zona DNS":::

1. Copie um dos nomes de servidor de nomes da etapa anterior.

1. Abra um prompt de comando e execute o seguinte:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Por exemplo:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Você deverá ver algo semelhante à seguinte captura de tela:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Pesquisa de zona DNS":::

O nome do host `www.2lwynbseszpam.azurequickstart.org` resolve para `1.2.3.4` e `1.2.3.5` conforme você o configurou. Esse resultado verifica se a resolução do nome está funcionando corretamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com a zona DNS, exclua o grupo de recursos. Isso remove a zona DNS e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

- Zona DNS
- Registro `A`

Agora que você criou sua primeira zona e o registro DNS usando um modelo do ARM, poderá criar registros para um aplicativo Web em um domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
