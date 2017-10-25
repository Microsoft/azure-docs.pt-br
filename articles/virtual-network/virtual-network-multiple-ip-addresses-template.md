---
title: "Diversos endereços IP para máquinas virtuais do Azure – modelo | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual usando um modelo do Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Atribuir vários endereços IP a máquinas virtuais usando um modelo do Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do modelo de implantação do Azure Resource Manager usando um modelo do Resource Manager. Não é possível atribuir vários endereços IP públicos e privados à mesma NIC ao implantar uma máquina virtual por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Descrição do modelo

A implantação de um modelo permite a você criar de forma rápida e consistente recursos do Azure com valores diferentes de configuração. Leia o artigo [Passo a passo do modelo do Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) caso não esteja familiarizado com os modelos do Azure Resource Manager. O modelo [Implantar uma VM com vários endereços IP](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) é utilizado neste artigo.

<a name="resources"></a>A implantação do modelo cria os seguintes recursos:

|Recurso|Nome|Descrição|
|---|---|---|
|Interface de rede|*myNic1*|As três configurações IP descritas na seção de cenário deste artigo são criadas e atribuídas a essa NIC.|
|Recurso de endereço IP público|2 são criados: *myPublicIP* e *myPublicIP2*|Esses recursos são atribuídos a endereços IP públicos estáticos e são atribuídos às configurações IP *IPConfig-1* e *IPConfig-2* descritas no cenário.|
|VM|*myVM1*|Uma VM DS3 Standard.|
|Rede virtual|*myVNet1*|Crie uma rede virtual com uma sub-rede denominada *mySubnet*.|
|Conta de armazenamento|Exclusivo para a implantação|Uma conta de armazenamento.|

<a name="parameters"></a>Ao implantar o modelo, você deve especificar os valores para os seguintes parâmetros:

|Nome|Descrição|
|---|---|
|adminUsername|Nome de usuário do administrador. O nome de usuário deve atender aos [requisitos de nome de usuário do Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Senha de administrador A senha deve atender aos [requisitos de senha do Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nome DNS para PublicIPAddressName1. O nome DNS resolverá para um dos endereços IP públicos atribuídos à VM. O nome deve ser exclusivo dentro da região do Azure (local) em que você criar a VM.|
|dnsLabelPrefix1|Nome DNS para PublicIPAddressName2. O nome DNS resolverá para um dos endereços IP públicos atribuídos à VM. O nome deve ser exclusivo dentro da região do Azure (local) em que você criar a VM.|
|OSVersion|A versão do Windows/Linux para a VM. O sistema operacional é uma imagem completamente corrigida da versão do Windows/Linux selecionada.|
|imagePublisher|O editor de imagem do Windows/Linux para a VM selecionada.|
|imageOffer|A imagem do Windows/Linux para a VM selecionada.|

Cada um dos recursos implantados pelo modelo é configurado com várias configurações padrão. Você pode exibir essas configurações por meio de um destes métodos:

- **Exibir o modelo no GitHub:** se você estiver familiarizado com os modelos, é possível exibir as configurações no [modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Exibir as configurações após a implantação:** se você não estiver familiarizado com os modelos, é possível implantar o modelo usando as etapas em uma das seções a seguir e, em seguida, exibir as configurações após a implantação.

Você pode usar o Portal do Azure, o PowerShell ou a CLI (interface de linha de comando) do Azure para implantar o modelo. Todos os métodos produzem o mesmo resultado. Para implantar o modelo, conclua as etapas em uma das seções a seguir:

## <a name="deploy-using-the-azure-portal"></a>Implantar usando o Portal do Azure

Para implantar o modelo usando o Portal do Azure, siga as etapas a seguir:

1. Modificar o modelo, se desejado. O modelo implanta os recursos e configurações listados na seção [recursos](#resources) deste artigo. Para saber mais sobre os modelos e como criá-los, leia o artigo [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Implante o modelo com um dos seguintes métodos:
    - **Selecionar o modelo no portal:** siga as etapas descritas no artigo [Implantar recursos de modelo personalizado](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template). Escolha o modelo pré-existente denominado *101-vm-multiple-ipconfig*.
    - **Diretamente:** clique no seguinte botão para abrir o modelo diretamente no portal: <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Independentemente do método escolhido, você precisará fornecer valores para os [parâmetros](#parameters) listados anteriormente neste artigo. Depois que a VM for implantada, conecte-se à VM e adicione os endereços IP privados ao sistema operacional implantado, seguindo as etapas na seção [Adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

## <a name="deploy-using-powershell"></a>Implantar usando o PowerShell

Para implantar o modelo usando o PowerShell, siga as etapas a seguir:

1. Implante o modelo seguindo as etapas descritas no artigo [Implantar um modelo com o PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md). O artigo descreve várias opções para implantar um modelo. Se você escolher implantar usando o `-TemplateUri parameter`, o URI deste modelo será *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se você optar por implantar usando o parâmetro `-TemplateFile`, copie o conteúdo do [arquivo de modelo](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) do GitHub para um novo arquivo em seu computador. Modifique o conteúdo do modelo, se desejado. O modelo implanta os recursos e configurações listados na seção [recursos](#resources) deste artigo. Para saber mais sobre os modelos e como criá-los, leia o artigo [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Independentemente da opção escolhida para implantar o modelo, você deve fornecer valores para os valores dos parâmetros listados na seção [parâmetros](#parameters) deste artigo. Se você escolher fornecer parâmetros usando um arquivo de parâmetros, copie os conteúdos do [arquivo de parâmetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) do GitHub para um novo arquivo no seu computador. Modifique os valores no arquivo. Use o arquivo que você criou como o valor para o parâmetro `-TemplateParameterFile`.

    Para determinar os valores válidos para os parâmetros OSVersion, ImagePublisher e imageOffer, siga as etapas no artigo [Navegar e selecionar imagens de VM do Windows](../virtual-machines/windows/cli-ps-findimage.md).

    >[!TIP]
    >Caso não tenha certeza de se um dnslabelprefix está disponível, digite o comando `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` para descobrir. Se ele estiver disponível, o comando retornará `True`.

2. Depois que a VM for implantada, conecte-se à VM e adicione os endereços IP privados ao sistema operacional implantado, seguindo as etapas na seção [Adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

## <a name="deploy-using-the-azure-cli"></a>Implantar usando a CLI do Azure

Para implantar o modelo usando a CLI 1.0 do Azure, siga as etapas a seguir:

1. Implante o modelo seguindo as etapas descritas no artigo [Implantar um modelo com a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md). O artigo descreve várias opções para implantar o modelo. Se você escolher implantar usando o `--template-uri` (-f), o URI deste modelo será *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se você optar por implantar usando o parâmetro `--template-file` (-f), copie o conteúdo do [arquivo de modelo](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) do GitHub para um novo arquivo em seu computador. Modifique o conteúdo do modelo, se desejado. O modelo implanta os recursos e configurações listados na seção [recursos](#resources) deste artigo. Para saber mais sobre os modelos e como criá-los, leia o artigo [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Independentemente da opção escolhida para implantar o modelo, você deve fornecer valores para os valores dos parâmetros listados na seção [parâmetros](#parameters) deste artigo. Se você escolher fornecer parâmetros usando um arquivo de parâmetros, copie os conteúdos do [arquivo de parâmetros](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) do GitHub para um novo arquivo no seu computador. Modifique os valores no arquivo. Use o arquivo que você criou como o valor para o parâmetro `--parameters-file` (-e).

    Para determinar os valores válidos para os parâmetros OSVersion, ImagePublisher e imageOffer, siga as etapas no artigo [Navegar e selecionar imagens de VM do Windows](../virtual-machines/windows/cli-ps-findimage.md).

2. Depois que a VM for implantada, conecte-se à VM e adicione os endereços IP privados ao sistema operacional implantado, seguindo as etapas na seção [Adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
