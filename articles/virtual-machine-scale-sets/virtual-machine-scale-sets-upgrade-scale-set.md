---
title: "Atualizar um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Atualizar um conjunto de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: guybo
ms.openlocfilehash: c7093e221ff8fe69ded1cfbce4f3ddeb1a195666
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>Atualizar um conjunto de escala de máquina virtual
Este artigo descreve como você pode implementar uma atualização de sistema operacional para um conjunto de escala de máquina virtual do Azure sem nenhum tempo de inatividade. Nesse contexto, uma atualização do SO envolve uma alteração da versão ou SKU do SO, ou alteração do URI de uma imagem personalizada. Atualizar sem tempo de inatividade significa atualizar uma máquina virtual por vez ou em grupos (por exemplo, um domínio de falha por vez), em vez de todas de uma só vez. Ao fazer isso, todas as máquinas virtuais que não estiverem sendo atualizadas podem continuar em execução.

Para evitar ambiguidade, vamos distinguir quatro tipos de atualização do SO que talvez você queira fazer:

* Alteração da versão ou da SKU de uma imagem de plataforma. Por exemplo, alterar a versão 14.04.2-LTS do Ubuntu de 14.04.201506100 para 14.04.201507060, ou alterar a SKU 15.10/mais recente do Ubuntu para 16.04.0-LTS/mais recente. Esse cenário é abordado neste artigo.
* Alterar o URI que aponta para uma nova versão de uma imagem personalizada que você cria (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Esse cenário é abordado neste artigo.
* Alterar a referência de imagem de um conjunto de dimensionamento que foi criado utilizando o Azure Managed Disks.
* A aplicação de patch no SO de dentro de uma máquina virtual (exemplos disso incluem instalar um patch de segurança e executar o Windows Update). Esse cenário tem suporte, mas não é abordado neste artigo.

Conjuntos de escala de máquina virtual implantados como parte de um cluster do [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) não são abordados aqui. Consulte [Patch no SO do Windows no seu cluster de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patch-orchestration-application) para obter mais informações sobre aplicação de patch do Service Fabric.

A sequência básica para alterar a versão/SKU do sistema operacional de uma imagem de plataforma, ou o URI de uma imagem personalizada será semelhante ao seguinte:

1. Obtenha o modelo do conjunto de escala de máquina virtual.
2. Altere a versão, SKU, referência de imagem ou valor URI no modelo.
3. Atualize o modelo.
4. Faça uma chamada a *manualUpgrade* nas máquinas virtuais no conjunto de escala. Esta etapa só será relevante se *upgradePolicy* for definida como **Manual** em seu conjunto de escala. Se ela estiver definida como **Automática**, todas as máquinas virtuais serão atualizadas ao mesmo tempo, causando tempo de inatividade.

Com essas informações em mente, vamos ver como você pode atualizar a versão de um conjunto de dimensionamento no PowerShell, e usando a API REST. Esses exemplos abordam o caso de uma imagem de plataforma, mas este artigo fornece informações suficientes para adaptar esse processo a uma imagem personalizada.

## <a name="powershell"></a>PowerShell
Esse exemplo atualiza um conjunto de dimensionamento de máquina virtual do Windows (criando para a nova versão 4.0.20160229. Depois de atualizar o modelo, ele atualiza uma instância da máquina virtual por vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Se você estiver atualizando o URI para uma imagem personalizada em vez de alterar uma versão da imagem de plataforma, substitua a linha "definir a nova versão" com um comando que atualizará o URI da imagem de origem. Por exemplo, se o conjunto de dimensionamento foi criado sem utilizar o Azure Managed Disks, a atualização seria semelhante à seguinte:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Se um conjunto de dimensionamento baseado na imagem personalizada foi criado utilizando o Azure Managed Disks, então, a referência da imagem seria atualizada. Por exemplo:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>A API REST
Veja alguns exemplos de Python que usam a API REST do Azure para implantar uma atualização de versão do sistema operacional. Ambos usam a biblioteca leve de funções de wrapper da API REST do Azure, [azurerm](https://pypi.python.org/pypi/azurerm) , para executar um GET no modelo de conjunto de escala, seguido por um PUT com um modelo atualizado. Eles também examinam os modos de exibição de instâncias da máquina virtual para identificar as máquinas virtuais por domínio de atualização.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) é o script em Python usado para distribuir uma atualização do SO para um conjunto de escala de máquina virtual em execução, um domínio de atualização por vez.

![Script Vmssupgrade para escolher máquinas virtuais ou um domínio de atualização](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Esse script permite que você escolha as máquinas virtuais específicas para atualizar ou especificar um domínio de atualização. Ele dá suporte ao alterar uma versão da imagem de plataforma ou o URI de uma imagem personalizada.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) é um editor para fins gerais para conjuntos de escala de máquina virtual que mostra o status da máquina virtual como um mapa de dados no qual uma linha representa um domínio de atualização. Entre outras coisas, você pode atualizar o modelo de um conjunto de escala com uma nova versão, SKU ou URI de imagem personalizada e, em seguida, selecionar domínios de falha para atualização. Ao fazer isso, todas as máquinas virtuais nesse domínio de atualização são atualizadas para o novo modelo. Como alternativa, você pode fazer uma atualização sem interrupção com base no tamanho de lote de sua escolha.  

A captura de tela a seguir mostra um modelo de um conjunto escala para o Ubuntu 14.04-2LTS versão 14.04.201507060. Muitas outras opções já foram adicionadas a essa ferramenta desde a captura dessa tela.

![Modelo de Vmsseditor de um conjunto de escala para o Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Depois de clicar em **Atualizar** e em **Obter Detalhes**, as máquinas virtuais em UD 0 começam a ser atualizadas.

![Vmsseditor mostrando atualização em andamento](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

