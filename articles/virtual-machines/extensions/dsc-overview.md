---
title: Visão geral de Desired State Configuration para o Azure
description: Saiba como usar o manipulador de extensão do Microsoft Azure para Desired State Configuration (DSC) do PowerShell. O artigo inclui cmdlets, a arquitetura e os pré-requisitos.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 17ada83f6fa1b57f8dd72d591b6625f25e9a2388
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955847"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de Desired State Configuration do Azure

O agente de VM do Azure e as extensões associadas são parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são componentes de software que estendem a funcionalidade da VM e simplificam várias operações de gerenciamento de VM.

O caso de uso primário para a extensão DSC (configuração de estado desejado) do Azure é inicializar uma VM para o [serviço de configuração de estado da automação do Azure (DSC)](../../automation/automation-dsc-overview.md).
O serviço fornece [benefícios](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) que incluem o gerenciamento contínuo da configuração da VM e a integração com outras ferramentas operacionais, como o monitoramento do Azure.
Usar a extensão para registrar VMs no serviço fornece uma solução flexível que até funciona em assinaturas do Azure.

Você pode usar a extensão de DSC, independentemente do serviço de DSC de Automação.
No entanto, isso enviará por push apenas uma configuração para a VM.
Nenhum relatório contínuo está disponível, a não ser localmente na VM.

Este artigo fornece informações sobre cenários: usar a extensão DSC para integração da Automação e usar a extensão de DSC como uma ferramenta para atribuir configurações a VMs usando o SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Computador local**: Para interagir com a extensão de VM do Azure, você deve usar o Portal do Azure ou o SDK do Azure PowerShell.
- **Agente convidado**: A VM do Azure que é configurada pela configuração do DSC deve ter um sistema operacional compatível com Windows Management Framework (WMF) 4.0 ou posterior. Para a lista completa de versões com suporte do sistema operacional, consulte o [Histórico de versões da extensão de DSC](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia presume familiaridade com os seguintes conceitos:

- **Configuração**: um documento de configuração DSC.
- **Nó**: um destino para uma configuração DSC. Neste documento, o *nó* sempre se refere a uma VM do Azure.
- **Dados de configuração**: Um arquivo .psd1 que tem dados ambientais de uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. A extensão de DSC aceita um documento de configuração e um conjunto de parâmetros. Se nenhum arquivo for fornecido, um [script de configuração padrão](#default-configuration-script) é inserido com a extensão. O script de configuração padrão é usado somente para definir metadados no [Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Quando a extensão é chamada pela primeira vez, ela instala uma versão do WMF usando a lógica a seguir:

- Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação é executada. O Windows Server 2016 já possui a versão mais recente do PowerShell instalada.
- Se a propriedade **wmfVersion** for especificada, essa versão do WMF é instalada, a menos que essa versão não seja compatível com o sistema operacional da VM.
- Se nenhuma propriedade **wmfVersion** for especificada, a versão mais recente de WMF aplicável é instalada.

Instalar o WMF requer uma reinicialização. Após a reinicialização, a extensão faz o download do arquivo .zip que é especificado na propriedade **modulesUrl**, se fornecido. Se esse local estiver no armazenamento de blobs do Azure, você pode especificar um token SAS na propriedade **sasToken** para acessar o arquivo. Depois que o. zip é baixado e desempacotado, a função de configuração definida em **configurationFunction** é executada para gerar um arquivo. mof ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)). Em seguida, a extensão executa `Start-DscConfiguration -Force` usando o arquivo .mof gerado. A extensão captura a saída e a grava no canal de status do Azure.

### <a name="default-configuration-script"></a>Script de configuração padrão

A extensão de DSC do Azure inclui um script de configuração padrão que é destinado a ser usado quando você carrega uma VM ao serviço de DSC de Automação do Azure. Os parâmetros do script estão alinhados com as propriedades configuráveis do [Gerenciador de Configurações Locais](/powershell/scripting/dsc/managing-nodes/metaConfig). Para parâmetros de script, consulte [Script de configuração padrão](dsc-template.md#default-configuration-script) na [extensão de Desired State Configuration com modelos do Azure Resource Manager](dsc-template.md). Para o script completo, consulte o [modelo de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informações para registrar com o serviço de configuração de estado da automação do Azure (DSC)

Ao usar a extensão de DSC para registrar um nó com o serviço de configuração de estado, será necessário fornecer três valores.

- RegistrationUrl-o endereço https da conta de automação do Azure
- RegistrationKey-um segredo compartilhado usado para registrar nós com o serviço
- NodeConfigurationName-o nome da configuração de nó (MOF) a ser extraída do serviço para configurar a função de servidor

Essas informações podem ser vistas na portal do Azure ou você pode usar o PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Para o nome da configuração do nó, verifique se a configuração do nó existe na configuração de estado do Azure.  Caso contrário, a implantação da extensão retornará uma falha.  Verifique também se você está usando o nome da *configuração do nó* e não a configuração.
Uma configuração é definida em um script que é usado [para compilar a configuração de nó (arquivo MOF)](../../automation/automation-dsc-compile.md).
O nome será sempre a configuração seguida por um ponto `.` e um `localhost` nome de computador específico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão de DSC nos modelos do Resource Manager

Na maioria dos cenários, os modelos de implantação do Azure Resource Manager (ARM) são a forma esperada de trabalhar com a extensão de DSC. Para obter mais informações e exemplos sobre como incluir a extensão de DSC em modelos de implantação do Resource Manager, consulte [Extensão de Desired State Configuration com modelos do Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets do PowerShell de Extensão de DSC

Os cmdlets do PowerShell que são usados para gerenciar a extensão de DSC são melhor usados em cenários de solução de problemas interativa e coleta de informações. Você pode usar os cmdlets para empacotar, publicar e monitorar implantações de extensão de DSC. Os cmdlets para a extensão de DSC ainda não foram atualizados para funcionar com o [Script de Configuração Padrão](#default-configuration-script).

O cmdlet **Publish-AzVMDscConfiguration** recebe um arquivo de configuração, verifica o arquivo para ver se há recursos de DSC dependentes e, em seguida, cria um arquivo .zip. O arquivo. zip contém a configuração e recursos de DSC que são necessários para aplicar a configuração. O cmdlet também pode criar o pacote localmente usando o parâmetro *-OutputArchivePath*. Caso contrário, o cmdlet publica o arquivo .zip no armazenamento de Blobs e o protege com um token SAS.

O script de configuração .ps1 criado por esse cmdlet é um arquivo .zip na raiz da pasta de arquivamento. A pasta de módulo é colocada na pasta de arquivamento em recursos.

O cmdlet **Set-AzVMDscExtension** injeta as configurações que a extensão de DSC do PowerShell exige em um objeto de configuração de VM.

O cmdlet **Get-AzVMDscExtension** recupera o status da extensão de DSC de uma VM específica.

O cmdlet **Get-AzVMDscExtensionStatus** recupera o status da configuração de DSC que é imposta pelo manipulador de extensões de DSC. Essa ação pode ser executada em uma única VM ou em um grupo de VMs.

O cmdlet **Remove-AzVMDscExtension** remove o manipulador de extensões de uma VM específica. Esse cmdlet *não* remove a configuração, desinstala o WMF ou altera as configurações aplicadas na máquina virtual. Apenas remove o manipulador de extensão.

Informações importantes sobre os cmdlets de extensão de DSC do Resource Manager:

- Os cmdlets do Azure Resource Manager são síncronos.
- Os parâmetros *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* e *Location* são obrigatórios.
- *ArchiveResourceGroupName* é um parâmetro opcional. Você pode especificar esse parâmetro quando sua conta de armazenamento pertencer a um grupo de recursos diferente daquele no qual a VM foi criada.
- Use a opção **AutoUpdate** para atualizar automaticamente do manipulador de extensão para a versão mais recente quando estiver disponível. Este parâmetro tem o potencial de causar reinicializações na VM quando uma nova versão de WMF for lançada.

### <a name="get-started-with-cmdlets"></a>Introdução aos cmdlets

A extensão de DSC do Azure pode usar documentos de configuração DSC para configurar diretamente VMs do Azure durante a implantação. Essa etapa não registra o nó para Automação. O nó *não* é gerenciado centralmente.

O exemplo a seguir mostra um exemplo simples de configuração. Salve a configuração localmente como iisInstall.ps1.

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

Os comandos a seguir colocam o script iisInstall.ps1 na VM especificada. Os comandos também executar a configuração e, em seguida, reportam o status.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de DSC em uma máquina virtual existente.

Para uma máquina virtual que executa o Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Para uma máquina virtual que executa o Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funcionalidade do portal do Azure

Para configurar o DSC no portal:

1. Ir para uma VM.
2. Em **Configurações**, selecione **Extensões**.
3. Na nova página que é criada, selecione **+ Adicionar** e, em seguida, selecione **Desired State Configuration do PowerShell**.
4. Clique em **Criar**, na parte inferior da página de informações.

O portal coleta a seguinte entrada:

- **Módulos de Configuração ou Script**: este campo é obrigatório (o formulário não foi atualizado para o [script de configuração padrão](#default-configuration-script)). Módulos de configuração e scripts exigem um arquivo. ps1 que tem um script de configuração ou um arquivo. zip com um script de configuração. ps1 na raiz. Se você usar um arquivo. zip, todos os recursos dependentes devem ser incluídos em pastas de módulo no. zip. Você pode criar o arquivo. zip usando o cmdlet **Publish-AzureVMDscConfiguration - -OutputArchivePath** que está incluído no SDK do PowerShell do Azure. O arquivo .zip será carregado em seu armazenamento de blobs de usuário protegido por um token SAS.

- **Nome qualificado do módulo da configuração**: você pode incluir várias funções de configuração em um arquivo. ps1. Insira o nome do script .ps1 de configuração seguido por \\ e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver o nome configuration.ps1 e se a configuração for **IisInstall**, insira **configuration.ps1\IisInstall**.

- **Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato **argumentName1=value1,argumentName2=value2**. Observe que esse formato é diferente daquele como argumentos de configuração são aceitos nos cmdlets do PowerShell ou modelos do Resource Manager.

- **Arquivo de PSD1 de dados de configuração**: se sua configuração exigir um arquivo de dados de configuração no `.psd1` , use esse campo para selecionar o arquivo de dados e carregá-lo no armazenamento de BLOBs do usuário. O arquivo de dados de configuração é protegido por um token SAS no armazenamento de blob.

- **Versão WMF**: Especifica a versão do Windows Management Framework (WMF) que deve ser instalada em sua VM. Configurar essa propriedade como mais recente instala a versão mais recente de WMF. Atualmente, os únicos valores possíveis para essa propriedade são 4.0, 5.0, 5.1, e a mais recente. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é **latest**.

- **Coleta de Dados**: Determina se a extensão coletará temetria. Para obter mais informações, consulte [coleta de dados de extensão de DSC do Azure](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).

- **Versão**: Especifica a versão da extensão DSC para instalar. Para informações sobre as versões, veja [histórico de versão da extensão DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Versão Menor de Autoatualização**: Este campo mapeia para a troca **AutoUpdate** nos cmdlets e habilita a extensão para automaticamente atualizar para a última versão durante a instalação. **SIm** instruirá o manipulador de extensão a usar a última versão disponível e **Não** forçará a **Versão** específica ser instalada. Não selecionar nem **Sim** nem **Não** é o mesmo que selecionar **Não**.

## <a name="logs"></a>Logs

Logs para a extensão são armazenados no seguinte local: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o DSC do PowerShell, vá até o [Centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o [modelo do Resource Manager para a extensão de DSC](dsc-template.md).
- Para mais funcionalidade que você pode gerenciar usando o DSC do PowerShell e para obter mais recursos de DSC, procure na [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](dsc-credentials.md).
