---
title: Criar uma VM do Windows com o PowerShell | Microsoft Docs
description: "Crie máquinas virtuais do Windows usando o Azure PowerShell e o modelo de implantação clássico."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 75c6cf17ee269ae169d9f2f748d0985ca07e454e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Criar uma máquina virtual Windows com o PowerShell e o modelo de implantação clássico
> [!div class="op_single_selector"]
> * [Portal do Azure - Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como [executar estas etapas usando o modelo do Resource Manager](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Estas etapas mostram como personalizar um conjunto de comandos do Azure PowerShell que criam e pré-configuram uma máquina virtual do Azure baseada em Windows usando uma abordagem de bloco de construção. Você pode usar este processo para criar rapidamente um conjunto de comandos para uma nova máquina virtual baseada em Windows e expandir uma implantação existente ou criar vários conjuntos de comandos que criam rapidamente um ambiente personalizado para teste/desenvolvimento ou profissionais de TI.

Estas etapas seguem uma abordagem de preencher lacunas para criar conjuntos de comandos do Azure PowerShell. Esta abordagem poderá ser útil se você for novo no PowerShell ou apenas quiser saber quais valores especificar para uma configuração bem-sucedida. Os usuários avançados do PowerShell podem pegar os comandos e substituí-los por seus próprios valores de variáveis (as linhas que começam com "$").

Se você ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview) para instalar o PowerShell do Azure no computador local. Em seguida, abra um prompt de comando do Windows PowerShell.

## <a name="step-1-add-your-account"></a>Etapa 1: adicionar sua conta
1. No prompt do PowerShell, digite **Add-AzureAccount** e clique em **Enter**. 
2. Digite o endereço de email associado à sua assinatura do Azure e clique em **Continuar**. 
3. Digite a senha da sua conta. 
4. Clique em **Entrar**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Etapa 2: Definir a assinatura e a conta de armazenamento
Defina a assinatura e a conta de armazenamento do Azure executando estes comandos no prompt de comando do Windows PowerShell. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelos nomes corretos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Você pode obter o nome de assinatura correto na propriedade SubscriptionName da saída do comando **Get-AzureSubscription** . Você pode obter o nome da conta de armazenamento correto na propriedade Label da saída do comando **Get-AzureStorageAccount**, após executar o comando **Select-AzureSubscription**.

## <a name="step-3-determine-the-imagefamily"></a>Etapa 3: determinar a ImageFamily
Em seguida, você precisa determinar o valor de ImageFamily ou Label para a imagem específica correspondente à máquina virtual do Azure que deseja criar. Você pode obter a lista de valores de ImageFamily disponíveis com este comando.

    Get-AzureVMImage | select ImageFamily -Unique

Aqui estão alguns exemplos de valores de ImageFamily para computadores baseados em Windows:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise em Windows Server 2012

Se você encontrar a imagem que você está procurando, abra uma nova instância do editor de texto de sua preferência ou o ISE (ambiente de script integrado) do PowerShell. Copie o seguinte para o novo arquivo de texto ou o ISE do PowerShell, substituindo o valor de ImageFamily.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Em alguns casos, o nome da imagem está na propriedade Label, e não no valor de ImageFamily. Se você não encontrar a imagem que você está procurando usando a propriedade ImageFamily, liste as imagens por sua propriedade Label com este comando.

    Get-AzureVMImage | select Label -Unique

Se você encontrar a imagem correta com esse comando, abra uma nova instância do editor de texto de sua preferência ou o ISE do PowerShell. Copie o seguinte para o novo arquivo de texto ou o ISE do PowerShell, substituindo o valor de Rótulo.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Etapa 4: criar o conjunto de comandos
Crie o restante do seu conjunto de comandos, copiando o conjunto apropriado de blocos abaixo para o novo arquivo de texto ou o ISE e, em seguida, preenchendo os valores das variáveis e remova os caracteres < e >. Veja os dois [exemplos](#examples) no final deste artigo para ter uma ideia do resultado final.

Comece seu conjunto de comandos escolhendo um destes dois blocos de comandos (obrigatório).

Opção 1: especifique um nome e um tamanho de máquina virtual.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opção 2: especifique um nome, o tamanho e o nome do conjunto de disponibilidade.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para os valores de InstanceSize para máquinas virtuais da série D, DS ou G, confira [Tamanhos de máquina virtual e serviços de nuvem no Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Se você tiver um Enterprise Agreement com Software Assurance e quiser usufruir do [Benefício de Uso Híbrido](https://azure.microsoft.com/pricing/hybrid-use-benefit/) do Windows Server, adicione o parâmetro **-LicenseType** ao cmdlet **New-AzureVMConfig**, passando o valor **Windows_Server** para um caso de uso típico.  Verifique se você está usando uma imagem que carregou. Você não pode usar uma imagem padrão da Galeria com o Benefício de Uso Híbrido.
> 
> 

Opcionalmente, para um computador Windows autônomo, especifique a conta de administrador local e a senha.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Escolha uma senha forte. Para verificar a força da senha, consulte [Verificador de senha: usando senhas fortes](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Opcionalmente, para adicionar o computador Windows a um domínio do Active Directory existente, especifique a conta e a senha de administrador local, o domínio e o nome e a senha de uma conta de domínio.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Para opções adicionais de pré-configuração para máquinas virtuais baseadas em Windows, confira a sintaxe para os conjuntos de parâmetros **Windows** e **WindowsDomain** em [Add-AzureProvisioningConfig](/powershell/module/azure/add-azureprovisioningconfig).

Opcionalmente, atribua um endereço IP específico, conhecido como um DIP estático, à máquina virtual.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Você pode verificar se um endereço IP específico está disponível com:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Opcionalmente, atribua a máquina virtual a uma sub-rede específica de uma rede virtual do Azure.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, adicione um disco de dados único à máquina virtual.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Para um controlador de domínio Active Directory, defina $hcaching como "None".

Opcionalmente, adicione a máquina virtual a um conjunto existente de balanceamento de carga para tráfego externo.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Finalmente, escolha um desses blocos de comando necessários para criar a máquina virtual.

Opção 1: criar a máquina virtual em um serviço de nuvem existente.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

O nome curto do serviço de nuvem é o nome que aparece na lista de Serviços de Nuvem no portal do Azure ou na lista de Grupos de Recursos no portal do Azure.

Opção 2: criar a máquina virtual em um serviço de nuvem e em uma rede virtual existentes.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Etapa 5: executar o conjunto de comandos
Examine o conjunto de comandos do PowerShell do Azure criado em seu editor de texto ou o ISE do PowerShell, formado por vários blocos de comandos, da Etapa 4. Certifique-se de que você especificou todas as variáveis necessárias e que elas tenham os valores corretos. Verifique também se você removeu todos os caracteres < e >.

Se você estiver usando um editor de texto, copie o conjunto de comandos para a área de transferência e clique com o botão direito do mouse no prompt de comando aberto do Windows PowerShell. Isto emitirá o conjunto de comandos como uma série de comandos do PowerShell e criará sua máquina virtual do Azure. Como alternativa, execute o comando definido com o ISE do PowerShell.

Se pretender criar novamente essa máquina virtual ou uma semelhante, você poderá:

* Salvar este conjunto de comandos como um arquivo de script do PowerShell (*.ps1).
* Salve este conjunto de comandos como um runbook de Automação do Azure na seção **Contas de Automação** do portal do Azure.

## <a id="examples"></a>Exemplos
Aqui estão dois exemplos de uso das etapas acima para criar conjuntos de comandos do PowerShell do Azure que criam máquinas virtuais do Azure baseadas em Windows.

### <a name="example-1"></a>Exemplo 1
Preciso de um conjunto de comandos do PowerShell para criar a máquina virtual inicial para um controlador de domínio Active Directory que:

* Usa a imagem do Windows Server 2012 R2 Datacenter.
* Tem o nome de AZDC1.
* É um computador autônomo.
* Tem um disco de dados adicional de 20 GB.
* Tem o endereço IP estático 192.168.244.4.
* Está na sub-rede de Back-end da rede virtual AZDatacenter.
* Está no serviço de nuvem Azure-TailspinToys.

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual, com linhas em branco entre cada bloco para facilitar a leitura.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Exemplo 2
Preciso de um conjunto de comandos do PowerShell para criar uma máquina virtual para um servidor de linha de negócios que:

* Usa a imagem do Windows Server 2012 R2 Datacenter.
* Tem o nome de LOB1.
* É um membro do domínio corp.contoso.com.
* Tem um disco de dados adicional de 200 GB.
* Está na sub-rede de Front-end da rede virtual AZDatacenter.
* Está no serviço de nuvem Azure-TailspinToys.

Aqui está o conjunto de comandos do PowerShell do Azure correspondente para criar essa máquina virtual.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Próximas etapas
Se precisar de um disco do sistema operacional que seja maior do que 127 GB, você poderá [expandir a unidade do sistema operacional](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

