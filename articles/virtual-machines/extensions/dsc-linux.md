---
title: Extensão de DSC do Azure para Linux
description: Instala os pacotes OMI e DSC para permitir que uma VM do Linux do Azure ser configurada usando a Configuração de Estado Desejado.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.openlocfilehash: 3a9478f93a2a6f8e31be39a58a18e0e394b9d5c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560068"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão de DSC para Linux (Microsoft. OSTCExtensions. DSCForLinux)

A DSC (configuração de estado desejado) é uma plataforma de gerenciamento que você pode usar para gerenciar sua infraestrutura de ti e de desenvolvimento com a configuração como código.

> [!NOTE]
> A extensão de DSC para Linux e a [extensão de máquina virtual log Analytics para Linux](./oms-linux.md) atualmente apresentam um conflito e não tem suporte em uma configuração lado a lado. Não use as duas soluções juntas na mesma VM.

A extensão DSCForLinux é publicada e tem suporte da Microsoft. A extensão instala o agente OMI e DSC em máquinas virtuais do Azure. A extensão de DSC também pode executar as seguintes ações:

- Registre a VM do Linux em uma conta de automação do Azure para efetuar pull das configurações do serviço de automação do Azure (registrar Extensionaction).
- Envie por push configurações do MOF para a VM do Linux (extensão Pushaction).
- Aplique a configuração do metamof à VM do Linux para configurar um servidor de pull para efetuar pull da configuração do nó (extensão Pullaction).
- Instale módulos DSC personalizados para a VM do Linux (instalar Extensionaction).
- Remova os módulos DSC personalizados da VM do Linux (remover Extensionaction).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

Para nós que executam o Linux, a extensão de DSC do Linux dá suporte a todas as distribuições do Linux listadas na [documentação do DSC do PowerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão DSCForLinux requer que a máquina virtual de destino esteja conectada à Internet. Por exemplo, a extensão de registro requer conectividade com o serviço de automação.
Para outras ações como pull, pull, install requer conectividade com o armazenamento do Azure e o GitHub. Depende das configurações fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="public-configuration"></a>Configuração pública

Aqui estão todos os parâmetros de configuração pública com suporte:

* `FileUri`: (opcional, Cadeia de caracteres) o URI do arquivo MOF, meta do arquivo MOF ou arquivo zip de recurso personalizado.
* `ResourceName`: (opcional, Cadeia de caracteres) o nome do módulo de recurso personalizado.
* `ExtensionAction`: (opcional, cadeia de caracteres) Especifica o que faz uma extensão. Os valores válidos são registrar, enviar por push, efetuar pull, instalar e remover. Se não for especificado, ele será considerado uma ação de envio por Push por padrão.
* `NodeConfigurationName`: (opcional, Cadeia de caracteres) o nome de uma configuração de nó a ser aplicada.
* `RefreshFrequencyMins`: (opcional, int) especifica com que frequência (em minutos) o DSC tenta obter a configuração do servidor de pull.
       Se a configuração no servidor de pull for diferente da atual no nó de destino, ela será copiada para a loja pendente e aplicada.
* `ConfigurationMode`: (opcional, cadeia de caracteres) Especifica como DSC deve aplicar a configuração. Os valores válidos são ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) DSC garante que a configuração está no estado desejado.

> [!NOTE]
> Se você usar uma versão anterior à 2,3, o parâmetro mode será o mesmo que Extensionaction. O modo parece ser um termo sobrecarregado. Para evitar confusão, Extensionaction é usado da versão 2,3 em diante. Para compatibilidade com versões anteriores, a extensão dá suporte ao modo e ExtensionAction.
>

### <a name="protected-configuration"></a>Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos com suporte:

* `StorageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento que contém o arquivo
* `StorageAccountKey`: (opcional, Cadeia de caracteres) a chave da conta de armazenamento que contém o arquivo
* `RegistrationUrl`: (opcional, Cadeia de caracteres) a URL da conta de automação do Azure
* `RegistrationKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de automação do Azure

## <a name="scenarios"></a>Cenários

### <a name="register-an-azure-automation-account"></a>Registrar uma conta de automação do Azure

protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formato do PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração MOF (em uma conta de armazenamento do Azure) à VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formato do PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração MOF (no armazenamento público) à VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formato do PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração meta MOF (em uma conta de armazenamento do Azure) à VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formato do PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração do MOF meta (no armazenamento público) à VM

public.json

```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```

Formato do PowerShell

```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalar um módulo de recurso personalizado (um arquivo zip em uma conta de armazenamento do Azure) para a VM

protected.json

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formato do PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalar um módulo de recurso personalizado (um arquivo zip no armazenamento público) para a VM

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}

```

Formato do PowerShell

```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remover um módulo de recurso personalizado de VM

public.json

```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```

Formato do PowerShell

```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Os modelos são ideais quando você implanta uma ou mais máquinas virtuais que exigem a configuração pós-implantação, como a integração à automação do Azure.

É o modelo do Resource Manager [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais informações sobre o modelo de Azure Resource Manager, consulte [criando modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

### <a name="use-azure-cliazure-cli"></a>Usar [CLI do Azure] [Azure-CLI]

Antes de implantar a extensão DSCForLinux, configure seu `public.json` e `protected.json` de acordo com os diferentes cenários na seção 3.

#### <a name="classic"></a>Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O modo de implantação clássico também é chamado de modo de gerenciamento de serviços do Azure. Você pode alternar para ele, executando:

```
$ azure config mode asm
```

Você pode implantar a extensão DSCForLinux executando:

```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão mais recente da extensão disponível, execute:

```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Gerenciador de Recursos

Você pode mudar para o modo do Gerenciador de Recursos do Azure executando:

```
$ azure config mode arm
```

Você pode implantar a extensão DSCForLinux executando:

```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

> [!NOTE]
> No modo de Azure Resource Manager, o `azure vm extension list` não está disponível por enquanto.
>

### <a name="use-azure-powershellazure-powershell"></a>Usar [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Clássico

Você pode entrar em sua conta do Azure no modo de gerenciamento de serviços do Azure executando:

```powershell
Add-AzureAccount
```

E implante a extensão DSCForLinux executando:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na seção anterior.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Gerenciador de Recursos

Você pode entrar em sua conta do Azure no modo de Azure Resource Manager executando:

```powershell
Login-AzAccount
```

Para saber mais sobre como usar Azure PowerShell com Azure Resource Manager, consulte [gerenciar recursos do Azure usando Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Você pode implantar a extensão DSCForLinux executando:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na seção anterior.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando usando o CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa a distribuição sem suporte ou a ação de extensão sem suporte.
Em alguns casos, a extensão do DSC do Linux falha ao instalar o OMI quando uma versão mais recente do OMI já existe no computador. [resposta de erro: não permitido de Downgrade (000003)]

### <a name="support"></a>Suporte

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).
