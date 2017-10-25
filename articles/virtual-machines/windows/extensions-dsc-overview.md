---
title: "Visão geral de configuração de estado desejado para o Azure | Microsoft Docs"
description: "Visão geral para usar o manipulador de extensão do Microsoft Azure para configuração de estado desejado do PowerShell. Incluindo pré-requisitos, arquitetura e cmdlets."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
ms.openlocfilehash: c05c2d541a5f526f362f9cd72fe6d878374112b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de configuração do estado desejado do Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O agente de VM do Azure e as extensões associadas são parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são componentes de software que estendem a funcionalidade da VM e simplificam várias operações de gerenciamento de VM. Por exemplo, a extensão VMAccess pode ser usada para redefinir uma senha de administrador, ou a extensão de Script Personalizado pode ser usada para executar um script na VM.

Este artigo apresenta a extensão de configuração de estado desejado (DSC) do PowerShell para VMs do Azure como parte do SDK do Azure PowerShell. Você pode usar os novos cmdlets para carregar e aplicar uma DSC do PowerShell em uma VM do Azure habilitada com a extensão de DSC do PowerShell. A extensão de DSC do PowerShell chama a DSC do PowerShell para aplicar a configuração DSC recebida na VM. Essa funcionalidade também está disponível por meio do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
**Máquina local** Para interagir com a extensão de VM do Azure, você precisa usar o Portal do Azure ou o SDK do Azure PowerShell. 

**Agente convidado** A VM do Azure a configurar pela configuração do DSC precisa ter um sistema operacional compatível com Windows Management Framework (WMF) 4.0 ou 5.0. A lista completa de versões com suporte do sistema operacional pode ser encontrada no [Histórico de versões da extensão de DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termos e conceitos
Este guia presume familiaridade com os seguintes conceitos:

Configuração - um documento de configuração DSC. 

Nó - um destino para uma configuração de DSC. Neste documento, "nó" sempre faz referência a uma VM do Azure.

Dados de configuração - um arquivo .psd1 contendo dados ambientais para uma configuração

## <a name="architectural-overview"></a>Visão geral da arquitetura
A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. A extensão de DSC espera um arquivo .zip contendo pelo menos um documento de configuração e um conjunto de parâmetros fornecidos por meio do SDK do Azure PowerShell ou do portal do Azure.

Quando a extensão é chamada pela primeira vez, executa um processo de instalação. Esse processo instala uma versão do Windows Management Framework (WMF) usando a lógica a seguir:

1. Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação é executada. O Windows Server 2016 já possui a versão mais recente do PowerShell instalada.
2. Se a propriedade `wmfVersion` for especificada, essa versão do WMF é instalada, a menos que ele não seja compatível com o sistema operacional da VM.
3. Se nenhuma propriedade `wmfVersion` for especificada, a versão mais recente do WMF aplicável é instalada.

A instalação do WMF requer uma reinicialização. Após a reinicialização, a extensão baixa o arquivo .zip especificado na propriedade `modulesUrl` . Se esse local estiver no armazenamento de blobs do Azure, um token SAS pode ser especificado na propriedade `sasToken` para acessar o arquivo. Depois que o arquivo .zip for baixado e descompactado, a função de configuração definida em `configurationFunction` é executada para gerar o arquivo .MOF. Em seguida, a extensão executa `Start-DscConfiguration -Force` no arquivo MOF gerado. A extensão captura a saída e grava de volta para no canal de status do Azure. Desse ponto em diante, o LCM de DSC lida com o monitoramento e correção da maneira normal. 

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Os cmdlets do PowerShell podem ser usados com o Azure Resource Manager ou o modelo clássico de implementação para empacotar, publicar e monitorar implantações de extensão de DSC. Os cmdlets listados a seguir são os módulos de implantação clássicos, mas "Azure" pode ser substituído por "AzureRm" para usar o modelo do Azure Resource Manager. Por exemplo, `Publish-AzureVMDscConfiguration` usa o modelo de implantação clássico, mas `Publish-AzureRmVMDscConfiguration` usa o Azure Resource Manager. 

`Publish-AzureVMDscConfiguration` recebe um arquivo de configuração, verifica a existência de recursos dependentes de DSC e cria um arquivo .zip contendo a configuração e os recursos de DSC necessários para aplicar a configuração. Também pode criar o pacote localmente usando o parâmetro `-ConfigurationArchivePath` . Caso contrário, ele publicará o arquivo .zip no Armazenamento de Blobs do Azure e o protegerá com um token SAS.

O arquivo .zip criado por esse cmdlet possui o script de configuração .ps1 na raiz da pasta de arquivamento. Os recursos possuem a pasta de módulo colocada na pasta de arquivo morto. 

`Set-AzureVMDscExtension` injeta as configurações necessárias pela extensão de DSC do PowerShell em um objeto de configuração da VM. No modelo de implantação clássico, as alterações da VM devem ser aplicadas a uma VM do Azure com `Update-AzureVM`. 

O `Get-AzureVMDscExtension` recupera o status da extensão de DSC de uma VM específica. 

`Get-AzureVMDscExtensionStatus` recupera o status da configuração DSC imposta pelo manipulador de extensão de DSC. Essa ação pode ser executada em uma única VM ou em um grupo de VMs.

`Remove-AzureVMDscExtension` remove o manipulador de extensão de uma determinada máquina virtual. Esse cmdlet **não** remove a configuração, desinstala o WMF ou altera as configurações aplicadas na máquina virtual. Apenas remove o manipulador de extensão. 

**Principais diferenças nos cmdlets do ASM e do Azure Resource Manager**

* Os cmdlets do Azure Resource Manager são síncronos. Os cmdlets do ASM são assíncronos.
* ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location são todos parâmetros obrigatórios no Azure Resource Manager.
* ArchiveResourceGroupName é um novo parâmetro opcional para o Azure Resource Manager. Você pode especificar esse parâmetro quando sua conta de armazenamento pertencer a um grupo de recursos diferente daquele no qual a máquina virtual foi criada.
* ConfigurationArchive é chamado de ArchiveBlobName no Azure Resource Manager
* ContainerName é chamado de ArchiveContainerName no Azure Resource Manager
* StorageEndpointSuffix é chamado de ArchiveStorageEndpointSuffix no Azure Resource Manager
* A opção AutoUpdate foi adicionada ao Azure Resource Manager para habilitar a atualização automática do manipulador de extensão na versão mais recente e quando estiver disponível. Observe que esse parâmetro tem o potencial de causar reinicializações na VM quando uma nova versão do WMF for lançada. 

## <a name="azure-portal-functionality"></a>Funcionalidade do portal do Azure
Navegue até uma VM. Em Configurações -> Geral, clique em "Extensões". Um novo painel é criado. Clique em "Adicionar" e selecione DSC do PowerShell.

O portal precisa de entrada.
**Script ou módulos de configuração**: esse campo é obrigatório. Requer um arquivo. ps1 contendo um script de configuração ou um arquivo .zip com um script de configuração .ps1 na raiz e todos os recursos dependentes em pastas de módulo dentro do .zip. Ele pode ser criado com o cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluído no SDK do Azure PowerShell. O arquivo .zip será carregado em seu armazenamento de blobs de usuário protegido por um token SAS. 

**Arquivo de configuração PSD1 de dados**: esse campo é opcional. Se sua configuração exigir um arquivo de dados de configuração em .psd1, use este campo para selecioná-lo e carregá-lo no armazenamento de blobs de usuário, onde eles serão protegidos por um token SAS. 

**Nome de configuração qualificado por módulo**: os arquivos .ps1 podem ter várias funções de configuração. Insira o nome do script .ps1 de configuração seguido por um '\'' e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver o nome "configuration.ps1" e se a configuração for "IisInstall", insira: `configuration.ps1\IisInstall`

**Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato `argumentName1=value1,argumentName2=value2`. Observe que esse formato é diferente daquele como argumentos de configuração são aceitos por meio de cmdlets do PowerShell ou modelos do Resource Manager. 

## <a name="getting-started"></a>Introdução
A extensão de DSC do Azure usa documentos de configuração DSC e impõe os mesmos em VMs do Azure. A seguir está um exemplo simples de uma configuração. Salve localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

As etapas a seguir colocam o script IisInstall.ps1 na VM especificada, executam a configuração e relatam o status.
###<a name="classic-model"></a>Modelo clássico
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Modelo do Azure Resource Manager

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Registro em log
Os logs são colocados em:

C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Número de versão]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine o [modelo do Azure Resource Manager para a extensão de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para encontrar a funcionalidade adicional que você pode gerenciar com a DSC do PowerShell, [navegue na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para conhecer mais recursos da DSC.

Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

