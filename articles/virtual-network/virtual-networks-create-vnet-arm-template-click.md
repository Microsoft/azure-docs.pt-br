---
title: Criar uma rede virtual | Modelo do Azure Resource Manager | Microsoft Docs
description: Saiba como criar uma rede virtual usando um modelo do Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Criar uma rede virtual usando um modelo do Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implantação: Azure Resource Manager e clássico. A Microsoft recomenda criar recursos por meio do modelo de implantação do Gerenciador de Recursos. Para saber mais sobre as diferenças entre os dois modelos, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Este artigo explica como criar uma rede virtual por meio do modelo de implantação do Gerenciador de Recursos usando um modelo do Azure Resource Manager. Você também pode criar uma rede virtual por meio do Gerenciador de Recursos usando outras ferramentas ou criar uma rede virtual por meio do modelo de implantação clássico, selecionando uma opção diferente na seguinte lista:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modelo](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (clássico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)

Você aprenderá a baixar e a modificar um modelo ARM existente do GitHub e implantar o modelo do GitHub, PowerShell e da CLI do Azure.

Se você estiver simplesmente implantando o modelo ARM diretamente do GitHub, sem nenhuma alteração, vá para [implantar um modelo do github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Baixar e compreender o modelo do Gerenciador de Recursos do Azure
No GitHub, você pode baixar o modelo existente para criar uma rede virtual e duas sub-redes; faça as alterações que desejar e reutilize-o. Para concluir as etapas abaixo, você precisa:

1. Navegue até [a página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Salve o arquivo em uma pasta local do computador.
4. Se você estiver familiarizado com modelos, pule para a etapa 7.
5. Abra o arquivo que você acabou de salvar e examine o conteúdo em **parameters** na linha 5. Os parâmetros do modelo ARM fornecem um espaço reservado para valores que podem ser preenchidos durante a implantação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | **local** |Região do Azure em que a rede virtual será criada |
   | **vnetName** |Nome para a nova rede virtual |
   | **addressPrefix** |Espaço de endereço para a rede virtual, no formato CIDR |
   | **subnet1Name** |Nome da primeira rede virtual |
   | **subnet1Prefix** |Bloco CIDR da primeira sub-rede |
   | **subnet2Name** |Nome da segunda rede virtual |
   | **subnet2Prefix** |Bloco CIDR da segunda sub-rede |
   
   > [!IMPORTANT]
   > Os modelos do Gerenciador de Recursos do Azure mantidos no GitHub podem mudar ao longo do tempo. Certifique-se de verificar o modelo antes de usá-lo.
   > 
   > 
6. Verifique o conteúdo em **resources** e observe o seguinte:
   
   * **type**. Tipo de recurso que está sendo criado pelo modelo. Nesse caso, **Microsoft.Network/virtualNetworks**, que representa uma rede virtual.
   * **name**. Nome do recurso. Observe o uso de **[parameters('vnetName')]**, que significa que o nome será fornecido como entrada pelo usuário ou por um arquivo de parâmetro durante a implantação.
   * **properties**. Lista de propriedades do recurso. Esse modelo usa as propriedades de sub-rede e espaço de endereço durante a criação da rede virtual.
7. Navegue de volta para [a página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Salve o arquivo em uma pasta local do computador.
10. Abra o arquivo que acabou de salvar e edite os valores dos parâmetros. Use os valores abaixo para implantar a rede virtual descrita no cenário:

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Salve o arquivo.


## <a name="deploy-the-template-using-powershell"></a>Implantar o modelo usando o PowerShell

Conclua as etapas abaixo para implantar o modelo baixado usando o PowerShell:

1. Instalar e configurar o Azure PowerShell executando as etapas do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. Execute o seguinte comando para criar um novo grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    O comando cria um grupo de recursos chamado *TestRG* na região *EUA Central* do Azure. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Saída esperada:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Execute o comando a seguir para implantar a nova rede virtual usando os arquivos de modelo e parâmetro que você baixou e modificou acima:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Saída esperada:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Execute o comando a seguir para exibir as propriedades da nova rede virtual:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Saída esperada:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Implantar o modelo usando clique para implantar

Você pode reutilizar o carregamento de modelos do Azure Resource Manager predefinidos para um repositório GitHub mantido pela Microsoft e aberto para a comunidade. Esses modelos podem ser implantados diretamente do GitHub ou descarregados e modificados para atender às suas necessidades. Para implantar um modelo que cria uma Rede Virtual com duas sub-redes, conclua as seguintes etapas:

1. Em um navegador, navegue até [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Role para baixo na lista de modelos e clique em **101-vnet-two-subnets**. Verifique o arquivo **README.md** , como mostrado abaixo.

    ![Arquivo READEME.md no github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Clique em **Implantar no Azure**. Se necessário, insira suas credenciais de logon do Azure. 
4. Na folha **Parâmetros**, insira os valores que você deseja usar para criar sua nova Rede Virtual e clique em **OK**. A figura abaixo mostra os valores para o cenário:
   
    ![Parâmetros de modelo ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Clique em **Grupo de recursos** e selecione um grupo de recursos ao qual adicionar a Rede Virtual ou clique em **Criar novo** para adicionar a Rede Virtual a um novo grupo de recursos. A figura abaixo mostra as configurações de um novo grupo de recursos denominado **TestRG**:

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Se necessário, altere as configurações de **Assinatura** e **Local** da sua rede virtual.
7. Se não deseja ver a rede virtual como um bloco no **Quadro Inicial**, desabilite **Fixar no Quadro Inicial**.
8. Clique em **Termos legais**, leia os termos e clique em **Comprar** para concordar. 
9. Clique em **Criar** para criar a VNet.
   
    ![Enviando bloco de implantação no portal de visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Depois que a implantação for concluída, no portal do Azure, clique em **Mais serviços**, digite *redes virtuais* na caixa de filtro exibida e clique em Redes virtuais para ver a folha Redes virtuais. Na folha, clique em *TestVNet*. Na folha *TestVNet*, clique em **Sub-redes** para ver as sub-redes criadas, conforme mostrado na figura abaixo:
    
     ![Criar rede virtual no portal de visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como se conectar:

- Uma VM (máquina virtual) em uma rede virtual lendo os artigos [Criar uma VM Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM Linux](../virtual-machines/linux/quick-create-portal.md). Em vez de criar uma rede virtual e sub-rede nas etapas dos artigos, você pode selecionar uma rede virtual e uma sub-rede existente para se conectar a uma VM.
- A rede virtual para outras redes virtuais, lendo o artigo [Conectar VNets](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- A rede virtual para uma rede local usando uma VPN (rede privada virtual) site a site ou um circuito ExpressRoute. Saiba como lendo os artigos [Conectar uma VNet a uma rede local usando uma VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Vincular uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
