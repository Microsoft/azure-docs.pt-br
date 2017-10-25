---
title: "Recursos e extensões da máquina virtual para Linux | Microsoft Docs"
description: "Saiba quais extensões estão disponíveis para as máquinas virtuais do Azure, agrupadas pelas funcionalidades fornecidas ou aperfeiçoadas."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 8a5b39351f665c51ae7d83f755329e54ff3cf786
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Recursos e extensões da máquina virtual para Linux

Extensões da máquina virtual do Azure são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, proteção antivírus ou a configuração do Docker, uma extensão da VM poderá ser usada para concluir essas tarefas. As extensões da VM do Azure podem ser executadas usando a CLI do Azure, o PowerShell, modelos do Azure Resource Manager e o Portal do Azure. Extensões podem ser agrupadas com uma nova implantação de máquina virtual ou executar qualquer sistema existente.

Este documento fornece uma visão geral das extensões da VM, pré-requisitos para utilização das extensões da VM do Azure e orientação sobre como detectar, gerenciar e remover extensões da VM. Este documento fornece informações generalizadas, pois há muitas extensões de VM disponíveis e cada uma delas tem uma configuração possivelmente exclusiva. Encontre detalhes específicos sobre cada extensão na documentação individual.

## <a name="use-cases-and-samples"></a>Casos de uso e exemplos

Há várias extensões de VM do Azure diferentes disponíveis, cada uma com um caso de uso específico. Alguns exemplos incluem:

- Aplique as configurações de Estado de Desejado do PowerShell a uma máquina virtual usando a extensão de DSC para Linux. Para saber mais, confira [Extensão de configuração de Estado Desejado do Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure o monitoramento de uma máquina virtual com a extensão de VM do Agente de Monitoramento da Microsoft. Para saber mais, confira [Como monitorar uma VM Linux](tutorial-monitoring.md).
- Configure o monitoramento de sua infraestrutura do Azure com a extensão Datadog. Para saber mais, confira [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure um host do Docker em uma máquina virtual do Azure usando a extensão de VM do Docker. Para saber mais, confira [Extensão de VM do Docker](dockerextension.md).

Além de extensões específicas ao processo, uma extensão de Script Personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script Personalizado para Linux permite a execução de qualquer script Bash em uma máquina virtual. Scripts personalizados são úteis para a criação de implantações do Azure que exigem uma configuração que vai além da capacidade das ferramentas nativas do Azure. Para saber mais, confira [Extensão de Script Personalizado de VM do Linux](extensions-customscript.md).


## <a name="prerequisites"></a>Pré-requisitos

Cada extensão da máquina virtual pode ter seu próprio conjunto de pré-requisitos. Por exemplo, a extensão de VM do Docker tem um pré-requisito de uma distribuição Linux com suporte. Os requisitos específicos das extensões estão detalhados na documentação associada.

### <a name="azure-vm-agent"></a>Agente de VM do Azure

O agente de VM do Azure gerencia a interação entre uma máquina virtual do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais de implantação e gerenciamento de máquinas virtuais do Azure, incluindo a execução de extensões da VM. O agente de VM do Azure é pré-instalado em imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operacionais com suporte.

Para saber mais sobre os sistemas operacionais com suporte e as instruções de instalação, confira [Agente de máquina virtual do Azure](../windows/classic/agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Muitas extensões de VM diferentes estão disponíveis para uso com as máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com a CLI do Azure, substituindo o local do exemplo pelo local de sua escolha.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões da máquina virtual do Azure podem ser executadas em máquinas virtuais existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com implantações de modelo do Azure Resource Manager. Ao usar extensões com modelos do Resource Manager, as máquinas virtuais do Azure podem ser implantadas e configuradas sem intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma máquina virtual existente.

### <a name="azure-cli"></a>CLI do Azure

As extensões da máquina virtual do Azure podem ser executadas em uma máquina virtual existente usando o comando `az vm extension set`. Este exemplo executa a extensão de script personalizado em uma máquina virtual.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

O script produz uma saída semelhante ao texto a seguir:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

É possível aplicar extensões de VM a uma máquina virtual existente por meio do Portal do Azure. Para fazer isso, selecione a máquina virtual, escolha **Extensões** e clique em **Adicionar**. Selecione a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

A imagem a seguir mostra a instalação da extensão Script Personalizado do Linux no Portal do Azure.

![Instalar a extensão de script personalizado](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure

É possível adicionar extensões de VM a um modelo do Azure Resource Manager e executá-las com a implantação do modelo. Ao implantar uma extensão com um modelo, você pode criar implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager. O modelo implanta um conjunto de máquinas virtuais com balanceamento de carga e um banco de dados SQL do Azure e, em seguida, instala um aplicativo .NET Core em cada VM. A extensão da VM se encarrega da instalação do software.

Para saber mais, confira o [Modelo completo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Para obter mais informações, confira [Criação de modelos do Azure Resource Manager](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão da VM

Quando você estiver executando uma extensão de VM, talvez seja necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e os descriptografa somente dentro da máquina virtual de destino. Cada extensão tem um esquema específico de configuração protegida, e cada um é detalhado na documentação específica à extensão.

O exemplo a seguir mostra uma instância da extensão de Script Personalizado para Linux. Observe que o comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não será criptografado.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

A movimentação da propriedade **comando para execução** para a configuração **protegida** protege a cadeia de caracteres de execução.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas à extensão. Por exemplo, quando você estiver usando a extensão Script Personalizado, será possível encontrar detalhes de execução do script localmente na máquina virtual na qual a extensão foi executada. As etapas de solução de problemas específicas à extensão são detalhadas na documentação associada.

As etapas de solução de problemas a seguir aplicam-se a todas as extensões da máquina virtual.

### <a name="view-extension-status"></a>Exibir o status da extensão

Após a execução da extensão da máquina virtual em uma máquina virtual, use o seguinte comando da CLI do Azure para retornar o status da extensão. Substitua os nomes de parâmetro de exemplo por seus próprios valores.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída se parece com o seguinte texto:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

O status de execução da extensão também pode ser encontrado no Portal do Azure. Para exibir o status de uma extensão, selecione a máquina virtual, escolha **Extensões** e selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Executar novamente uma extensão de VM

Pode haver casos nos quais uma extensão da máquina virtual precisa ser executada novamente. Você pode executar novamente uma extensão removendo-a e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, execute o seguinte comando com a CLI do Azure. Substitua os nomes de parâmetro de exemplo por seus próprios valores.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Você pode remover uma extensão usando as seguintes etapas no Portal do Azure:

1. Selecione uma máquina virtual.
2. Escolha **Extensões**.
3. Selecione a extensão desejada.
4. Escolha **Desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência à extensão VM comum
| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script Personalizado para Linux |Executar scripts em uma máquina virtual do Azure |[Extensão de Script Personalizado para Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensão do Docker |Instale o daemon Docker para oferecer suporte a comandos Docker remotos. |[Extensão de VM do Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensão de acesso à VM |Restabelecer o acesso a uma máquina virtual do Azure |[Extensão de acesso à VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão de Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de Acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de Acesso à VM para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
