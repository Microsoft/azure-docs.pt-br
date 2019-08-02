---
title: Recursos e extensões da VM do Azure para Linux | Microsoft Docs
description: Saiba quais extensões estão disponíveis para as máquinas virtuais do Azure, agrupadas pelas funcionalidades fornecidas ou aperfeiçoadas.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3f22da9eabc6f539ef37009f565f073b9de89319
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706736"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Recursos e extensões da máquina virtual para Linux

As extensões da máquina virtual (VM) do Azure são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, proteção antivírus ou a execução de um script dentro dela, uma extensão da VM poderá ser usada. As extensões da VM do Azure podem ser executadas com a CLI do Azure, o PowerShell, modelos do Azure Resource Manager e o portal do Azure. As extensões podem ser agrupadas com uma nova implantação de VM ou executar qualquer sistema existente.

Este artigo fornece uma visão geral das extensões da VM, pré-requisitos para utilização das extensões da VM do Azure e diretrizes sobre como detectar, gerenciar e remover as extensões da VM. Este artigo fornece informações generalizadas, pois há muitas extensões de VM disponíveis, cada uma delas tem uma configuração possivelmente exclusiva. Encontre detalhes específicos sobre cada extensão na documentação individual.

## <a name="use-cases-and-samples"></a>Casos de uso e exemplos

Há várias extensões de VM do Azure diferentes disponíveis, cada uma com um caso de uso específico. Eis alguns exemplos:

- Aplique as configurações de Estado Desejado do PowerShell a uma VM usando a extensão de DSC para Linux. Para saber mais, confira [Extensão de configuração de Estado Desejado do Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure o monitoramento de uma VM com a extensão de VM do Microsoft Monitoring Agent. Para saber mais, confira [Como monitorar uma VM Linux](../linux/tutorial-monitoring.md).
- Configure o monitoramento da infraestrutura do Azure com a extensão Chef ou Datadog. Para obter mais informações, consulte os [documentos do Chef](https://docs.chef.io/azure_portal.html) ou o [Blog do Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Além de extensões específicas ao processo, uma extensão de Script Personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script Personalizado para Linux permite a execução de qualquer script Bash em uma VM. Scripts personalizados são úteis para a criação de implantações do Azure que exigem uma configuração que vai além da capacidade das ferramentas nativas do Azure. Para saber mais, confira [Extensão de Script Personalizado de VM do Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, é necessário ter o Agente Linux do Microsoft Azure instalado. Algumas extensões individuais têm pré-requisitos, como acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente de VM do Azure

O agente de VM do Azure gerencia a interação entre uma VM do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais de implantação e gerenciamento de VMs do Azure, incluindo a execução de extensões da VM. O agente de VM do Azure é pré-instalado em imagens do Microsoft Azure Marketplace e pode ser instalado manualmente em sistemas operacionais com suporte. O Agente de VM do Azure para Linux é conhecido como o agente para Linux.

Para saber mais sobre os sistemas operacionais com suporte e as instruções de instalação, confira [Agente de máquina virtual do Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versões do agente com suporte

Para fornecer a melhor experiência possível, há versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais com suporte

O agente para Linux executa em vários sistemas operacionais. No entanto, a estrutura de extensões tem um limite para os sistemas operacionais com extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não têm suporte em todos os sistemas de operacionais e podem emitir *Código de Erro 51, 'SO sem suporte'* . Consulte a documentação da extensão individual sobre a capacidade de suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são baixados do repositório de extensão do Armazenamento do Microsoft Azure, e os carregamentos de status de extensão são postados no Armazenamento do Microsoft Azure. Se usar a versão com [suporte](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, você não precisará permitir o acesso ao Armazenamento do Microsoft Azure na região da VM, já que poderá usar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações do agente. Se você estiver usando uma versão sem suporte do agente, será necessário permitir o acesso de saída no armazenamento do Microsoft Azure nessa região por meio da VM.

> [!IMPORTANT]
> Se você tiver bloqueado o acesso a *168.63.129.16* usando o firewall convidado, as extensões falharão independentemente das informações acima.

Os agentes só podem ser usados para baixar os pacotes de extensão e o status do relatório. Por exemplo, se uma instalação da extensão precisar baixar um script do GitHub (Script Personalizado) ou precisar ter acesso ao Armazenamento do Microsoft Azure (Backup do Azure), então outras portas de firewall/Grupo de Segurança de Rede precisarão ser abertas. Diferentes extensões têm requisitos diferentes, já que são aplicativos por si só. Para extensões que exigem acesso ao Armazenamento do Microsoft Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Para redirecionar as solicitações de tráfego do agente, o Agente para Linux tem suporte de servidor proxy. No entanto, esse suporte de servidor proxy não aplica extensões. É necessário configurar cada extensão individual para trabalhar com um proxy.

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Muitas extensões de VM diferentes estão disponíveis para uso com as VMs do Azure. Para consultar uma lista completa, use [az vm extension image list](/cli/azure/vm/extension/image#az-vm-extension-image-list). O exemplo a seguir lista todas as extensões disponíveis no local *westus*:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões da VM do Azure podem ser executadas em VMs existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com implantações de modelo do Azure Resource Manager. Ao usar extensões com modelos do Resource Manager, as VMs do Azure podem ser implantadas e configuradas sem intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma VM existente.

### <a name="azure-cli"></a>CLI do Azure

As extensões da VM do Azure podem executar em uma VM existente com o comando [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set). O exemplo a seguir executa a extensão Custom Script em uma VM nomeada *myVM* em um grupo de recursos nomeado *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando a extensão executa corretamente, a saída é semelhante ao exemplo a seguir:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

É possível aplicar extensões de VM a uma VM existente por meio do portal do Azure. Selecione a VM no portal, escolha **Extensões** e depois selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

A imagem a seguir mostra a instalação da extensão Script Personalizado do Linux no portal do Azure:

![Instalar a extensão de script personalizado](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

É possível adicionar extensões de VM a um modelo do Azure Resource Manager e executá-las com a implantação do modelo. Ao implantar uma extensão com um modelo, você pode criar implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager que implanta um conjunto de VMs com balanceamento de carga e um banco de dados SQL do Azure e, em seguida, instala um aplicativo .NET Core em cada VM. A extensão da VM se encarrega da instalação do software.

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

Para obter mais informações sobre os modelos do Resource Manager, consulte [Criando modelos do Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão da VM

Ao executar uma extensão de VM, talvez seja necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e os descriptografa somente dentro da VM de destino. Cada extensão tem um esquema específico de configuração protegida, e cada um é detalhado na documentação específica à extensão.

O exemplo a seguir mostra uma instância da extensão de Script Personalizado para Linux. O comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não é criptografado:

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

A movimentação da propriedade **comando para execução** para a configuração **protegida** protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como agentes e extensões são atualizados?

Os Agentes e as Extensões compartilham o mesmo mecanismo de atualização. Algumas atualizações não exigem regras de firewall adicionais.

Quando uma atualização estiver disponível, ela só será instalada na VM quando houver uma alteração nas extensões e outras alterações de Modelo de VM como:

- Discos de dados
- Extensões
- Contêiner de diagnóstico de inicialização
- Segredos do sistema operacional convidado
- Tamanho da VM
- Perfil de rede

Os editores tornam atualizações disponíveis para regiões em momentos diferentes, então é possível ter VMs em regiões e versões diferentes.

#### <a name="agent-updates"></a>Atualizações de agentes

O Agente de VM Linux contém *Código de Agente de Provisionamento* e *Código de Manipulação de Extensão* em um pacote, que não pode ser separado. É possível desabilitar o *Agente de Provisionamento* quando quiser provisionar no Azure utilizando cloud-init. Para fazer isso, consulte [usando cloud-init](../linux/using-cloud-init.md).

Versões com suporte para Agentes podem usar atualizações automáticas. O único código que pode ser atualizado é o *Código de Manipulação de Extensão*, não o código de provisionamento. O *Código de Agente de Provisionamento* é código de execução única.

O *código de Tratamento de Extensão* é responsável por se comunicar com a malha do Azure e manipular as operações de extensões da VM, como instalações, relatando status, atualizando as extensões individuais e removendo-as. As atualizações contêm correções de segurança, correções de bug e aprimoramentos para o *código de Tratamento de Extensão*.

Quando o agente é instalado, um daemon pai é criado. Esse pai, então, gera um processo filho que é usado para manipular as extensões. Se uma atualização estiver disponível para o agente, ele será baixado, o pai interromperá o processo filho, fará o upgrade e o reinicializará. Se houver um problema com a atualização, o processo pai será revertido para a versão filho anterior.

O processo pai não pode ser atualizado automaticamente. O pai somente pode ser atualizado por uma atualização do pacote de distribuição.

Para verificar qual versão está em execução, verifique `waagent`, conforme a seguir:

```bash
waagent --version
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Na saída do exemplo anterior, o pai ou a 'versão implantada por pacote' é *WALinuxAgent-2.2.17*

O 'Agente de estado de meta' é a versão de atualização automática.

É altamente recomendável sempre ter uma atualização automática para o agente, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Não ter isso habilitado significa que será necessário manter a atualização manual do agente e não receber correções de bugs e de segurança.

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o Agente para Linux baixa e atualiza a extensão. Atualizações automáticas de extensão são *Secundárias* ou *Hotfix*. Você pode aceitar ou recusar atualizações de extensões *Secundárias* ao provisionar a extensão. O exemplo a seguir mostra como atualizar automaticamente as versões secundárias em um modelo do Resource Manager com *autoUpgradeMinorVersion": true,'* :

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Para obter as correções de bug de versão secundária mais recentes, é altamente recomendável que você selecione sempre a atualização automática em suas implantações de extensão. As atualizações de hotfix que realizam correções de bug essenciais ou de segurança não podem ser recusadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar as atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão foi definida com autoUpgradeMinorVersion em uma VM

Você pode ver no modelo da VM se a extensão foi provisionada com 'autoUpgradeMinorVersion'. Para verificar, use [az vm show](/cli/azure/vm#az-vm-show) e forneça o grupo de recursos e o nome da VM, conforme a seguir:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

O seguinte exemplo de saída mostra que *autoUpgradeMinorVersion* foi definido como *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma autoUpgradeMinorVersion

Para ver quando ocorreu uma atualização na extensão, revise os logs do agente na VM em */var/log/waagent.log*.

No exemplo abaixo, a VM tinha o *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* instalado. Um hotfix estava disponível para *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Permissões de agente

Para executar as tarefas, o agente precisa executar como *raiz*.

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas à extensão. Por exemplo, ao usar a extensão Script Personalizado, detalhes de execução do script poderão ser encontrados localmente na VM na qual a extensão foi executada. As etapas de solução de problemas específicas à extensão são detalhadas na documentação associada.

As seguintes etapas de solução de problemas aplicam-se a todas as extensões da VM.

1. Para verificar o Log do agente do Linux, observe a atividade em que a extensão estava sendo provisionada em */var/log/waagent.log*

2. Verifique os logs de extensão real para obter mais detalhes no */var/log/azure/\<extensionName >*

3. Verifique as seções de solução de problemas da documentação específica da extensão para códigos de erro, problemas conhecidos etc.

3. Examine os logs do sistema. Verifique se há outras operações que podem ter interferido na extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso exclusivo ao gerenciador de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas na extensão

1. As extensões têm 20 minutos para serem executadas (exceções são as extensões de CustomScript, Chef e DSC que têm 90 minutos). Se a implantação exceder esse tempo, será marcada como um tempo limite. Isso pode ocorrer devido a poucas VMs de recursos, outras configurações da VM/tarefas de inicialização consumindo grande quantidade de recursos durante a extensão que está tentando provisionar.

2. Pré-requisitos mínimos não atendidos. Algumas extensões têm dependências em SKUs da VM, como imagens HPC. As extensões podem exigir certos requisitos de acesso à rede, como comunicação com o Armazenamento do Microsoft Azure ou serviços públicos. Outros exemplos podem ser o acesso a repositórios de pacote, ficando sem espaço em disco, ou restrições de segurança.

3. Acesso exclusivo ao gerenciador de pacotes. Em alguns casos, você pode encontrar uma configuração da VM de longa execução e uma instalação da extensão em conflito, nas quais ambas precisam de acesso exclusivo ao gerenciador de pacotes.

### <a name="view-extension-status"></a>Exibir o status da extensão

Depois que uma extensão da VM tiver sido executada em uma VM, use [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) para retornar o status da extensão, conforme a seguir:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A saída é semelhante ao seguinte exemplo de saída:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

O status de execução da extensão também pode ser encontrado no Portal do Azure. Para exibir o status de uma extensão, selecione a VM, escolha **Extensões** e selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Executar novamente uma extensão de VM

Pode haver casos nos quais uma extensão da VM precisa ser executada novamente. Você pode executar novamente uma extensão removendo-a e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, use [az vm extension delete](/cli/azure/vm/extension#az-vm-extension-delete), conforme a seguir:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Você também pode remover uma extensão no portal do Azure da seguinte maneira:

1. Selecione uma VM.
2. Escolha **Extensões**.
3. Selecione a extensão desejada.
4. Escolha **Desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência à extensão VM comum

| Nome da extensão | DESCRIÇÃO | Mais informações |
| --- | --- | --- |
| Extensão de Script Personalizado para Linux |Executar scripts em uma máquina virtual do Azure |[Extensão de Script Personalizado para Linux](custom-script-linux.md) |
| Extensão de acesso à VM |Restabelecer o acesso a uma máquina virtual do Azure |[Extensão de acesso à VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão de Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de Acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de Acesso à VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre extensões de VM, consulte [Visão geral de recursos e extensões de máquina virtual do Azure](overview.md).
