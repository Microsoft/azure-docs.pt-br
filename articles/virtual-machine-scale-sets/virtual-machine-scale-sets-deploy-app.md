---
title: "Implantar um aplicativo em conjuntos de escala de máquina virtual"
description: "Use as extensões para implantar um aplicativo nos Conjuntos de Dimensionamento de Máquinas Virtuais do Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 371295efea1eab66361b9aba21a55bbd2826c69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais

Este artigo descreve diferentes maneiras de instalar software no momento em que o conjunto de dimensionamento é provisionado.

Pode ser necessário revisar o artigo [Visão geral do design do conjunto de dimensionamento](virtual-machine-scale-sets-design-overview.md), o qual descreve alguns dos limites impostos pelos conjuntos de dimensionamento de máquinas virtuais.

## <a name="capture-and-reuse-an-image"></a>Capturar e reutilizar uma imagem

É possível utilizar uma máquina virtual que você tem no Azure para preparar uma imagem base para o conjunto de dimensionamento. Esse processo cria um disco gerenciado na sua conta de armazenamento, o qual é possível referenciar como a imagem base para o conjunto de dimensionamento. 

Execute as seguintes etapas:

1. Crie uma Máquina Virtual do Azure
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. Acesse remotamente a máquina virtual e personalize o sistema de acordo com seu interesse.

   Se você quiser, é possível instalar o aplicativo agora. No entanto, saiba que ao instalar o aplicativo agora, a atualização do seu aplicativo poderá ficar mais complicada porque talvez seja necessário removê-lo primeiro. Em vez disso, é possível utilizar esta etapa para instalar quaisquer pré-requisitos que possam ser necessários ao aplicativo, como um tempo de execução específico ou recurso do sistema operacional.

3. Siga o tutorial "capturar uma máquina" tanto para [Linux][linux-vm-capture] como para [Windows][windows-vm-capture].

4. Crie um [Conjunto de Dimensionamento de Máquinas Virtuais][vmss-create] com o URI de imagem capturado na etapa anterior.

Para obter mais informações, consulte [Visão geral do Managed Disks](../virtual-machines/windows/managed-disks-overview.md) e [Utilizar discos de dados anexados](virtual-machine-scale-sets-attached-disks.md).

## <a name="already-provisioned"></a>Instalar quando o conjunto de dimensionamento for provisionado

As extensões da máquina virtual podem ser aplicadas a um conjunto de dimensionamento de máquinas virtuais. Com uma extensão da máquina virtual, é possível personalizar as máquinas virtuais em um conjunto de dimensionamento como um grupo inteiro. Para obter mais informações, consulte [Extensões da máquina virtual](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Há três extensões principais que podem ser utilizadas, dependendo se o sistema operacional é baseado no Linux ou baseado no Windows.

### <a name="windows"></a>Windows

Para um sistema operacional baseado no Windows, utilize a extensão de **Script personalizado v1.8** ou a extensão **DSC do PowerShell**.

#### <a name="custom-script"></a>Custom Script

A extensão de script personalizado executa um script em cada instância da máquina virtual no conjunto de dimensionamento. Um arquivo de configuração ou variável indica quais arquivos são baixados para a máquina virtual e, em seguida, o comando é executado. Você pode usar isso para executar um instalador, um script, um arquivo em lotes, quaisquer executáveis, por exemplo.

O PowerShell utiliza uma tabela de hash para as configurações. Esse exemplo configura a extensão de script personalizado para executar um script do PowerShell que instala o IIS.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Utilize a opção `-ProtectedSetting` para as configurações que podem conter informações confidenciais.

---------


A CLI do Azure utiliza um arquivo json para as configurações. Esse exemplo configura a extensão de script personalizado para executar um script do PowerShell que instala o IIS. Salve o arquivo json a seguir como _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

Em seguida, execute esse comando da CLI do Azure.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilize a opção `--protected-settings` para as configurações que podem conter informações confidenciais.

### <a name="powershell-dsc"></a>DSC do PowerShell

Você pode utilizar DSC do PowerShell para personalizar as instâncias das VMs do conjunto de dimensionamento. A extensão **DSC** publicada pelo **Microsoft.Powershell** implanta e executa a configuração DSC fornecida em cada instância da máquina virtual. Um arquivo de configuração ou variável informa a extensão onde o pacote *.zip* está e qual a combinação _script-function_ a executar.

O PowerShell utiliza uma tabela de hash para as configurações. Esse exemplo implanta um pacote de DSC que instala o IIS.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Utilize a opção `-ProtectedSetting` para as configurações que podem conter informações confidenciais.

-----------

A CLI do Azure utiliza um json para as configurações. Esse exemplo implanta um pacote de DSC que instala o IIS. Salve o arquivo json a seguir como _settings.json_.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

Em seguida, execute esse comando da CLI do Azure.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilize a opção `--protected-settings` para as configurações que podem conter informações confidenciais.

### <a name="linux"></a>Linux

O Linux pode utilizar a extensão de **Script personalizado v2.0** ou usar a **inicialização de nuvem** durante a criação.

O script personalizado é uma extensão simples que baixa arquivos para as instâncias da máquina virtual e executa um comando.

#### <a name="custom-script"></a>Custom Script

Salve o arquivo json a seguir como _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Utilize a CLI do Azure para adicionar essa extensão a um conjunto de dimensionamento de máquinas virtuais existentes. Cada máquina virtual no conjunto de dimensionamento executa automaticamente a extensão.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Utilize a opção `--protected-settings` para as configurações que podem conter informações confidenciais.

#### <a name="cloud-init"></a>Inicialização de nuvem

A inicialização de nuvem é utilizada quando o conjunto de dimensionamento é criado. Primeiro, crie um arquivo local chamado _cloud-init.txt_ e adicione sua configuração a esse aquivo. Por exemplo, consulte [essas linhas gerais](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)

Utilize a CLI do Azure para criar um conjunto de dimensionamento. O `--custom-data` campo aceita o nome de arquivo de um script de inicialização de nuvem.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>Como gerenciar atualizações de aplicativos?

Se o aplicativo foi implantado através de uma extensão, altere a definição da extensão de alguma forma. Essa alteração faz com que a extensão seja reimplantada a todas as instâncias das máquina virtuais. Algo **deverá** ser alterado sobre a extensão, como renomear um arquivo referenciado, caso contrário, o Azure não reconhecerá que a extensão foi alterada.

Se o aplicativo foi incorporado em sua própria imagem do sistema operacional, utilize uma pipeline de implantação automatizada para atualizações do aplicativo. Projete sua arquitetura para facilitar a troca rápida de um conjunto de dimensionamento em etapas na produção. Um bom exemplo dessa abordagem é o [trabalho de driver do Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

[Packer](https://www.packer.io/) e [Terraform](https://www.terraform.io/) suportam o Azure Resource Manager para que seja possível definir suas imagens "como código" e compilá-las no Azure, então, utilize o VHD no seu conjunto de dimensionamento. No entanto, isso seria problemático para imagens do marketplace, nas quais extensões/scripts personalizados se tornam mais importantes, já que você não manipula diretamente os bits do marketplace.

## <a name="what-happens-when-a-scale-set-scales-out"></a>O que acontece quando um conjunto de dimensionamento é escalado horizontalmente?
Quando você adiciona uma ou mais máquinas virtuais a um conjunto de dimensionamento, o aplicativo é instalado automaticamente. Por exemplo, se o conjunto de dimensionamento tiver extensões definidas, elas serão executadas em uma nova máquina virtual sempre que for criada. Se o conjunto de dimensionamento for baseado em uma imagem personalizada, qualquer nova máquina virtual será uma cópia da imagem personalizada de origem. Se as máquinas virtuais do conjunto de dimensionamento forem hosts de contêineres, então, será possível ter o código de inicialização para carregar os contêineres em uma extensão de script personalizada. Ou, uma extensão poderá instalar um agente que se registre com um orquestrador de cluster, como o Serviço de Contêiner do Azure.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Como você implementa uma atualização de SO em domínios de atualização?
Suponha que você deseja atualizar a imagem do seu SO enquanto mantém o conjunto de dimensionamento de máquinas virtuais em execução. O PowerShell e a CLI do Azure podem atualizar as imagens da máquina virtual, uma máquina virtual de cada vez. O artigo [Atualizar um conjunto de dimensionamento de máquinas virtuais](./virtual-machine-scale-sets-upgrade-scale-set.md) também fornece mais informações sobre quais opções estão disponíveis para executar uma atualização do sistema operacional através de um conjunto de dimensionamento de máquinas virtuais.

## <a name="next-steps"></a>Próximas etapas

* [Utilizar o PowerShell para gerenciar seu conjunto de dimensionamento.](virtual-machine-scale-sets-windows-manage.md)
* [Criar um modelo de conjunto de dimensionamento.](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md

