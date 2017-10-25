---
title: Configurar o grupo de disponibilidade Always On em uma VM do Azure usando o PowerShell | Microsoft Docs
description: "Este tutorial usa recursos que foram criados com o modelo de implantação clássico. Use o PowerShell para criar um grupo de disponibilidade Always On no Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b99cf767fb931d3f7fe14fcbe7990126244613ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurar o grupo de disponibilidade Always On em uma VM do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Clássico: Interface de usuário](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere que agora você pode concluir esta tarefa no modelo do Azure Resource Manager. O modelo do Azure Resource Manager é recomendável para novas implantações. Confira, [Introdução aos grupos de disponibilidade Always On do SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Recomendamos que a maioria das novas implantações use o modelo do Resource Manager. O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica.

As VMs (máquinas virtuais) do Azure podem ajudar os administradores de banco de dados a reduzir o custo de um sistema SQL Server de alta disponibilidade. Este tutorial mostra como implementar um grupo de disponibilidade usando o SQL Server Always On de ponta a ponta dentro de um ambiente do Azure. Ao final do tutorial, sua solução SQL Server AlwaysOn no Azure consistirá nos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo uma de front-end e uma de back-end.
* Um controlador de domínio com um domínio do Active Directory.
* Duas VMs do SQL Server implantadas na sub-rede de back-end e adicionadas ao domínio do Active Directory.
* Um cluster de failover do Windows de três nós com o modelo de quorum Maioria dos Nós.
* Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade.

Esse cenário é uma boa escolha por sua simplicidade no Azure, não pela economia ou outros fatores. Por exemplo, é possível minimizar o número de VMs de um grupo de disponibilidade de duas réplicas para economizar horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivos de quorum em um cluster de failover de dois nós. Esse método reduz a contagem de VMs em uma em comparação com a configuração acima.

O objetivo deste tutorial é mostrar as etapas necessárias para configurar a solução descrita acima sem detalhar demais cada etapa. Portanto, em vez de fornecer as etapas de configuração da GUI, ele usa scripts do PowerShell para percorrer rapidamente cada etapa. Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure com a assinatura de máquina virtual.
* Você instalou os [cmdlets do Azure PowerShell](/powershell/azure/overview).
* Você já tem uma compreensão sólida dos grupos de disponibilidade Always On para soluções locais. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conectar-se à sua assinatura do Azure e criar a rede virtual
1. Em uma janela do PowerShell no computador local, importe o módulo do Azure, baixe o arquivo de configurações de publicação no seu computador e conecte sua sessão do PowerShell à sua assinatura do Azure importando as configurações de publicação baixadas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O comando **Get-AzurePublishSettingsFile** gera automaticamente um certificado de gerenciamento com o Azure e o baixa para o seu computador. Um navegador é aberto automaticamente e você é solicitado a inserir as credenciais da conta da Microsoft da sua assinatura do Azure. O arquivo **.publishsettings** baixado contém todas as informações necessárias para gerenciar sua assinatura do Azure. Depois de salvar este arquivo em um diretório local, importe-o usando o comando **Import-AzurePublishSettingsFile** .

   > [!NOTE]
   > O arquivo .publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A melhor prática de segurança para esse arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, excluí-lo após a conclusão da importação. Um usuário mal-intencionado que obtenha acesso ao arquivo .publishsettings pode editar, criar e excluir os serviços do Azure.

2. Defina uma série de variáveis que você usará para criar sua infraestrutura de TI na nuvem.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Preste atenção ao seguinte para garantir que os comandos tenham êxito posteriormente:

   * As variáveis **$storageAccountName** e **$dcServiceName** devem ser exclusivas, pois são usadas para identificar sua conta de armazenamento de nuvem e servidor de nuvem, respectivamente, na Internet.
   * Os nomes que você especifica para as variáveis **$affinityGroupName** e **$virtualNetworkName** são configurados no documento de configuração da rede virtual que você usará mais tarde.
   * **$sqlImageName** especifica o nome atualizado da imagem da VM que contém o SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para simplificar, **Contoso!000** é a mesma senha usada durante todo o tutorial.

3. Crie um grupo de afinidades.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Crie uma rede virtual importando um arquivo de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O arquivo de configuração contém o seguinte documento XML. Em resumo, ele especifica uma rede virtual chamada **ContosoNET** no grupo de afinidades chamado **ContosoAG**. Ele tem o espaço de endereço **10.10.0.0/16** e duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24**, que são as sub-redes anterior e posterior, respectivamente. A sub-rede anterior é onde você pode colocar aplicativos cliente, como o Microsoft SharePoint. A sub-rede posterior é onde você colocará as VMs do SQL Server. Se você alterar as variáveis **$affinityGroupName** e **$virtualNetworkName** no início, também será necessário alterar os nomes correspondentes abaixo.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Crie uma conta de armazenamento associada ao grupo de afinidades que você criou e defina-a como a conta de armazenamento atual na sua assinatura.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Crie o servidor controlador de domínio no novo serviço de nuvem e conjunto de disponibilidade.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Esses comandos conectados têm as seguintes funções:

   * **New-AzureVMConfig** cria uma configuração de VM.
   * **Add-AzureProvisioningConfig** fornece os parâmetros de configuração de um servidor do Windows autônomo.
   * **Add-AzureDataDisk** adiciona o disco de dados que você usará para armazenar os dados do Active Directory, com a opção de caching definida como Nenhum.
   * **New-AzureVM** cria um novo serviço de nuvem e a nova VM do Azure no novo serviço de nuvem.

7. Aguarde até que a nova VM seja totalmente provisionada e baixe o arquivo da área de trabalho remota no seu diretório de trabalho. Como a nova VM do Azure leva muito tempo para ser provisionada, o loop `while` continua sondando a nova VM até que ela esteja pronta para uso.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

O servidor do controlador de domínio foi provisionado com êxito. Em seguida, você vai configurar o domínio do Active Directory nesse servidor do controlador de domínio. Deixe a janela do PowerShell aberta no seu computador local. Você vai usá-la novamente mais tarde para criar duas VMs do SQL Server.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
1. Conecte-se ao servidor do controlador de domínio iniciando o arquivo da área de trabalho remota. Use o nome de usuário AzureAdmin do administrador do computador e a senha **Contoso!000**, que você especificou quando criou a nova VM.
2. Abra uma janela do PowerShell no modo de administrador.
3. Execute o comando **DCPROMO.EXE** a seguir para instalar o domínio **corp.contoso.com**, com os diretórios de dados na unidade M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Depois que o comando for finalizado, a VM será reiniciada automaticamente.

4. Conecte-se ao servidor do controlador de domínio novamente iniciando o arquivo da área de trabalho remota. Desta vez, faça logon como **CORP\Administrador**.
5. Abra uma janela do PowerShell no modo de administrador e importe o módulo do Active Directory PowerShell usando o seguinte comando:

        Import-Module ActiveDirectory

6. Execute os seguintes comandos para adicionar três usuários ao domínio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** é usado para configurar qualquer coisa relacionada às instâncias de serviço do SQL Server, ao cluster de failover e ao grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são usados como as contas de serviço do SQL Server para duas VMs do SQL Server.
7. Em seguida, execute os comandos a seguir para conceder a **CORP\Install** as permissões para criar objetos de computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    O GUID especificado acima é o GUID para o tipo de objeto de computador. A conta **CORP\Install** precisa das permissões **Ler Todas as Propriedades** e **Criar Objetos de Computador** para criar objetos do Active Direct para o cluster de failover. A permissão **Ler Todas as Propriedades** já foi fornecida a CORP\Install por padrão, portanto, você não precisa concedê-la explicitamente. Para saber mais sobre as permissões necessárias para criar o cluster de failover, confira o [Failover Cluster Step-by-Step Guide: Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx) (Guia passo a passo do cluster de failover: configurando contas no Active Directory).

    Concluída a configuração do Active Directory e dos objetos de usuário, você criará duas VMs do SQL Server e as ingressará nesse domínio.

## <a name="create-the-sql-server-vms"></a>Criar VMs do SQL Server
1. Continue usando a janela do PowerShell que é aberta no computador local. Defina as seguintes variáveis adicionais:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    O endereço IP **10.10.0.4** normalmente é atribuído à primeira VM que você cria na sub-rede **10.10.0.0/16** da rede virtual do Azure. Você deve verificar se esse é o endereço do servidor do controlador de domínio executando **IPCONFIG**.
2. Execute os seguintes comandos redirecionados para criar a primeira VM no cluster de failover, chamada **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Observe as seguintes informações sobre o comando acima:

   * **New-AzureVMConfig** cria uma configuração de VM com o nome do conjunto de disponibilidade desejado. As VMs subsequentes serão criadas com o mesmo nome de conjunto de disponibilidade para que elas sejam adicionadas ao mesmo conjunto de disponibilidade.
   * **Add-AzureProvisioningConfig** ingressa a VM no domínio do Active Directory que você criou.
   * **Set-AzureSubnet** coloca a VM na sub-rede posterior.
   * **New-AzureVM** cria um novo serviço de nuvem e a nova VM do Azure no novo serviço de nuvem. O parâmetro **DnsSettings** especifica que o servidor DNS para os servidores no novo serviço de nuvem tem o endereço IP **10.10.0.4**. Esse é o endereço IP do servidor do controlador de domínio. Esse parâmetro é necessário para habilitar que as novas VMs no serviço de nuvem ingressem no domínio do Active Directory com êxito. Sem esse parâmetro, você deve definir manualmente as configurações de IPv4 na sua VM para usar o servidor do controlador de domínio como o servidor DNS primário depois que a VM é provisionada e, em seguida, adicionar a VM ao domínio do Active Directory.
3. Execute os seguintes comandos redirecionados para criar VMs do SQL Server, chamadas **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Observe as seguintes informações sobre os comandos acima:

   * **New-AzureVMConfig** usa o mesmo nome de conjunto de disponibilidade do servidor do controlador de domínio e usa a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na galeria de máquinas virtuais. Ele também define o disco do sistema operacional para caching somente leitura (sem cache de gravação). É recomendável migrar os arquivos de banco de dados para um disco de dados separado que você conecta à VM e configurá-lo sem caching de leitura nem de gravação. No entanto, a segunda melhor opção é remover o caching de gravação do disco do sistema operacional, pois não é possível remover o caching de leitura do disco do sistema operacional.
   * **Add-AzureProvisioningConfig** ingressa a VM no domínio do Active Directory que você criou.
   * **Set-AzureSubnet** coloca a VM na sub-rede posterior.
   * **Add-AzureEndpoint** adiciona pontos de extremidade de acesso para que os aplicativos cliente possam acessar essas instâncias de serviços do SQL Server na Internet. Portas diferentes são atribuídas para ContosoSQL1 e ContosoSQL2.
   * **New-AzureVM** cria a nova VM do SQL Server no mesmo serviço de nuvem que o ContosoQuorum. Você deve colocar as VMs no mesmo serviço de nuvem se desejar que elas estejam no mesmo conjunto de disponibilidade.
4. Aguarde até que cada VM seja totalmente provisionada e que cada uma delas baixe seu arquivo de área de trabalho remota no diretório de trabalho. O loop `for` percorre as três VMs novas e executa os comandos dentro das chaves de nível superior para cada uma delas.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    As VMs do SQL Server agora estão provisionadas e em execução, mas estão instaladas com o SQL Server com as opções padrão.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializar as VMs do cluster de failover
Nesta seção, você precisa modificar os três servidores que usará no cluster de failover e na instalação do SQL Server. Especificamente:

* Todos os servidores: é necessário instalar o recurso **Clustering de Failover**.
* Todos os servidores: é necessário adicionar **CORP\Install** como o **administrador** do computador.
* Somente ContosoSQL1 e ContosoSQL2: é necessário adicionar **CORP\Install** como uma função **sysadmin** no banco de dados padrão.
* Somente ContosoSQL1 e ContosoSQL2: é necessário adicionar **NT AUTHORITY\System** como uma conexão com as seguintes permissões:

  * Alterar qualquer grupo de disponibilidade
  * Conectar o SQL
  * Exibir o estado do servidor
* Somente ContosoSQL1 e ContosoSQL2: o protocolo **TCP** já está habilitado na VM do SQL Server. No entanto, ainda é necessário abrir o firewall para o acesso remoto do SQL Server.

Agora você está pronto para começar. Começando com o **ContosoQuorum**, siga as etapas abaixo:

1. Conecte-se ao **ContosoQuorum** iniciando os arquivos da área de trabalho remota. Use o nome de usuário **AzureAdmin** do administrador do computador e a senha **Contoso!000**, que você especificou quando criou as VMs.
2. Verifique se os computadores ingressaram com êxito em **corp.contoso.com**.
3. Aguarde a instalação do SQL Server concluir a execução das tarefas automatizadas de inicialização antes de continuar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale o recurso do Clustering de Failover do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Saia de ContosoQuorum. A tarefa nesse servidor foi concluída.

        logoff.exe

Em seguida, inicialize **ContosoSQL1** e **ContosoSQL2**. Siga as etapas abaixo, que são idênticas para ambas as VMs do SQL Server.

1. Conecte-se às duas VMs do SQL Server iniciando os arquivos da área de trabalho remota. Use o nome de usuário **AzureAdmin** do administrador do computador e a senha **Contoso!000**, que você especificou quando criou as VMs.
2. Verifique se os computadores ingressaram com êxito em **corp.contoso.com**.
3. Aguarde a instalação do SQL Server concluir a execução das tarefas automatizadas de inicialização antes de continuar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale o recurso do Clustering de Failover do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Importe o Provedor do SQL Server PowerShell.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adicione **CORP\Install** como a função sysadmin para a instância padrão do SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adicione **NT AUTHORITY\System** como uma conexão com as três permissões descritas acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Abra o firewall para acesso remoto do SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Saia de ambas as VMs.

         logoff.exe

Agora você está pronto para configurar o grupo de disponibilidade. Você usará o Provedor do SQL Server PowerShell para executar todo o trabalho em **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade
1. Conecte-se a **ContosoSQL1** novamente iniciando os arquivos da área de trabalho remota. Em vez de entrar usando a conta do computador, entre usando **CORP\Install**.
2. Abra uma janela do PowerShell no modo de administrador.
3. Defina as seguintes variáveis:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importe o Provedor do SQL Server PowerShell.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Altere a conta de serviço do SQL Server de ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Altere a conta de serviço do SQL Server de ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Baixe **CreateAzureFailoverCluster.ps1** em [Criar cluster de failover para Grupos de Disponibilidade AlwaysOn em uma VM do Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Você usará este script para ajudar na criação de um cluster de failover funcional. Para obter informações importantes sobre como o Clustering de Failover do Windows interage com a rede do Azure, confira [Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Altere para o diretório de trabalho e crie o cluster de failover com o script baixado.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Habilite os grupos de disponibilidade Always On para as instâncias padrão do SQL Server em **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Crie um diretório de backup e conceda permissões para as contas de serviço do SQL Server. Você usará esse diretório para preparar o banco de dados de disponibilidade na réplica secundária.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Crie um banco de dados em **ContosoSQL1** chamado **MyDB1**, faça um backup completo e um backup de log e restaure-os em **ContosoSQL2** com a opção **WITH NORECOVERY**.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Crie pontos de extremidade do grupo de disponibilidade em VMs do SQL Server e defina as permissões apropriadas nos pontos de extremidade.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Crie as réplicas de disponibilidade.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Por fim, crie o grupo de disponibilidade e ingresse a réplica secundária no grupo de disponibilidade.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Próximas etapas
Agora você implementou com êxito o SQL Server Always On criando um grupo de disponibilidade no Azure. Para configurar um ouvinte para este grupo de disponibilidade, veja [Configurar um ouvinte de ILB para grupos de disponibilidade Always On no Azure](../classic/ps-sql-int-listener.md).

Para obter outras informações sobre como usar o SQL Server no Azure, veja [SQL Server nas Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
