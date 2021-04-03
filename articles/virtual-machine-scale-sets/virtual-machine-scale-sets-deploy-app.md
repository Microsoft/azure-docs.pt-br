---
title: Implantar um aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba como implantar aplicativos em instâncias de máquina virtual Linux e Windows em um conjunto de dimensionamento
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079584"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais

Para executar aplicativos em instâncias de VM (máquina virtual) em um conjunto de dimensionamento, primeiro é necessário instalar os componentes de aplicativo e os arquivos necessários. Este artigo apresenta maneiras de criar uma imagem de VM personalizada para instâncias em um conjunto de dimensionamento ou executar scripts de instalação automaticamente em instâncias de VM existentes. Você também aprenderá a gerenciar aplicativos ou atualizações do SO em um conjunto de dimensionamento.


## <a name="build-a-custom-vm-image"></a>Compilar uma imagem de VM personalizada
Quando você usa uma das imagens de plataforma do Azure para criar as instâncias no seu conjunto de dimensionamento, nenhum software adicional é instalado ou configurado. Você pode automatizar a instalação desses componentes, no entanto, isso aumenta o tempo necessário para provisionar as instâncias de VM para seus conjuntos de dimensionamento. Se você aplicar várias alterações de configuração às instâncias de VM, haverá sobrecarga de gerenciamento com esses scripts de configuração e tarefas.

Para reduzir o gerenciamento de configuração e a hora para provisionar uma VM, você pode criar uma imagem de VM personalizada que está pronta para executar o aplicativo assim que uma instância é provisionada no conjunto de dimensionamento. Para obter mais informações sobre como criar e usar uma imagem de VM personalizada com um conjunto de dimensionamento, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-use-custom-image-cli.md)
- [PowerShell do Azure](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instalar um aplicativo com a Extensão de Script Personalizado
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. Para obter mais informações sobre como instalar um aplicativo com uma Extensão de Script Personalizado, confira os seguintes tutoriais:

- [CLI do Azure](tutorial-install-apps-cli.md)
- [PowerShell do Azure](tutorial-install-apps-powershell.md)
- [Modelo do Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalar um aplicativo em uma VM do Windows com o DSC do PowerShell
A [DSC (Configuração de Estado Desejado) do PowerShell](/powershell/scripting/dsc/overview/overview) é uma plataforma de gerenciamento para definir a configuração dos computadores de destino. As configurações de DSC definem o que instalar em um computador e como configurar o host. Um mecanismo do LCM (Gerenciador de Configurações Local) é executado em cada nó de destino que processa as ações solicitadas com base em configurações enviadas por push.

A extensão de DSC do PowerShell permite que você personalize as instâncias de VM em um conjunto de dimensionamento com o PowerShell. O exemplo a seguir:

- Instrui as instâncias da VM a baixar um pacote de DSC do GitHub – *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação – `configure-http.ps1`
- Obtém informações sobre um conjunto de dimensionamento com [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Aplica a extensão nas instâncias da VM com [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

A extensão de DSC é aplicada a instâncias de VM de *myScaleSet* no grupo de recursos denominado *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização em seu conjunto de dimensionamento for *manual*, atualize as instâncias da VM com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Este cmdlet aplica a configuração de conjunto de dimensionamento atualizado para as instâncias de VM e instala o aplicativo.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalar um aplicativo em uma VM Linux com o cloud-init
[Inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/index.html) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como a inicialização de nuvem é executada durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Para obter mais informações, incluindo um arquivo de exemplo *cloud-init.txt*, consulte [Usar cloud-init para personalizar as VMs do Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de dimensionamento e usar um arquivo cloud-init, adicione o parâmetro `--custom-data` para o comando [az vmss create](/cli/azure/vmss) e especifique o nome de um arquivo de cloud-init. O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* em *myResourceGroup* e configura as instâncias de VM com um arquivo chamado *cloud-init.txt*. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Instalar aplicativos com atualizações de SO
Quando novas versões de sistema operacional estão disponíveis, você pode usar ou compilar uma nova imagem personalizada e [implantar atualizações do SO](virtual-machine-scale-sets-upgrade-scale-set.md) em um conjunto de dimensionamento. Cada instância VM é atualizada para a imagem mais recente que você especificar. Você pode usar uma imagem personalizada com o aplicativo já instalado, a Extensão de Script Personalizado ou DSC do PowerShell para que seu aplicativo fique disponível automaticamente conforme você realiza a atualização. Talvez seja necessário planejar a manutenção de aplicativos conforme você executa esse processo para garantir que não haja nenhum problema de compatibilidade de versão.

Se você usar uma imagem VM personalizada com o aplicativo já instalado, você poderá integrar as atualizações de aplicativo com um pipeline de implantação para compilar as novas imagens e implantar atualizações de SO em todo o conjunto de dimensionamento. Essa abordagem permite que o pipeline pegue os builds mais recentes do aplicativo, criar e validar uma imagem de VM e atualizar as instâncias de VM no conjunto de dimensionamento. Para executar um pipeline de implantação que compila e implanta atualizações de aplicativos em imagens de VM personalizadas, você pode [criar uma imagem do Packer e implantar com o Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset) ou usar outra plataforma como [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Próximas etapas
Conforme você compila e implanta aplicativos em seus conjuntos de dimensionamento, você pode examinar a [Visão geral de design do conjunto de dimensionamento](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerenciar o conjunto de dimensionamento, confira [Usar o PowerShell para gerenciar o conjunto de dimensionamento](./virtual-machine-scale-sets-manage-powershell.md).
