---
title: "Criar uma máquina virtual do Azure com Rede Acelerada | Microsoft Docs"
description: "Saiba como criar uma máquina virtual com Rede Acelerada."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e887230a102f5c6289ca2eec0e4700a0e1fdfde
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual com Rede Acelerada

Neste tutorial, você aprenderá a criar uma VM (máquina Virtual) do Azure com Rede Acelerada. A Rede Acelerada é GA para Windows e em uma Visualização Pública para distribuições específicas do Linux. Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM com suporte. A figura abaixo mostra a comunicação entre duas VMs (máquinas virtuais) com e sem rede acelerada:

![Comparação](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre comutadores virtuais, leia o artigo [Virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com Rede Acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica sem rede acelerada são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios da rede acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

> [!WARNING]
> A Visualização Pública do Linux pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para obter as notificações mais atualizadas sobre a disponibilidade e o status desse recurso, confira a página de atualizações da Rede Virtual do Azure.

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="Limitations"></a>Limitações
Existem as seguintes limitações ao usar essa funcionalidade:

* **Criação de interface de rede:** rede acelerada só pode ser habilitada para uma NIC nova. Não pode ser habilitada para uma NIC existente.
* **Criação da VM:** uma NIC com rede acelerada habilitada somente poderá ser conectada a uma VM quando a VM for criada. A NIC não pode ser anexada a uma VM existente.
* **Regiões:** VMs do Windows com rede acelerada são oferecidas na maioria das regiões do Azure. VMs do Linux com rede acelerada são oferecidas em várias regiões. As regiões em que essa capacidade está disponível estão em expansão. Consulte o blog de Atualizações de Rede Virtual do Azure abaixo para encontrar as informações mais recentes.   
* **Sistemas operacionais com suporte:** Windows: Microsoft Windows Server 2012 R2 Datacenter e Windows Server 2016. Linux: Ubuntu Server 16.04 LTS com kernel 4.4.0-77 ou superior, SLES 12 SP2, RHEL 7.3 e CentOS 7.3 (publicado por "Rogue Wave Software").
* **Tamanho da VM:** tamanhos de instância com computação otimizada e de finalidade geral com oito ou mais núcleos. Para obter mais informações, consulte os artigos de tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O conjunto de tamanhos de instância VM com suporte será expandido no futuro.
* **Implantação somente por meio do ARM (Azure Resource Manager):** a Rede Acelerada não está disponível para implantação por meio do ASM/RDFE.

Alterações a essas limitações serão anunciadas na página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview/).

## <a name="create-a-windows-vm"></a>Criar uma VM do Windows
Você pode usar o portal do Azure ou o Azure [PowerShell](#windows-powershell) para criar a VM.

### <a name="windows-portal"></a>Portal

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ Novo** > **Computação** > **Windows Server Datacenter 2016**. 
3. Na folha do **Windows Server Datacenter 2016** exibida, deixe *Resource Manager* selecionado em **Selecionar um modelo de implantação** e clique em **Criar**.
4. Na folha **Fundamentos** que aparece, digite os seguintes valores, deixe as opções padrão restantes ou selecione ou insira seus próprios valores e clique no botão **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome|MyVm|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite *MyResourceGroup*|
    |Local|Oeste dos EUA 2|

    Se você for novo no Azure, saiba mais sobre [Grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (que também são chamados de regiões).
5. Na folha **Escolher um tamanho** que aparece, digite *8* na caixa **Mínimo de núcleos** e, em seguida, clique em **Exibir tudo**.
6. Clique em **DS4_V2 Standard** ou em qualquer VM com suporte e clique no botão **Selecionar**.
7. Na folha **Configurações** exibida, deixe todas as configurações como estão, porém, clique em **Habilitado** em **Rede acelerada** e depois clique no botão **OK**. **Observação:** se, nas etapas anteriores, você tiver selecionado valores de tamanho, sistema operacional ou local da VM que não estejam listados na seção [Limitações](#Limitations) deste artigo, **Rede acelerada** não estará visível.
8. Na folha **Resumo** que aparece, clique no botão **OK**. O Azure começa a criar a VM. A criação da VM leva alguns minutos.
9. Para instalar o driver de rede acelerado para Windows, conclua as etapas na seção [Configurar o Windows](#configure-windows) deste artigo.

### <a name="windows-powershell"></a>PowerShell
1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **powershell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. No navegador, copie o script a seguir:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Na janela do PowerShell, clique com o botão direito do mouse para colar o script e começar a executá-lo. Você será solicitado a informar um nome de usuário e senha. Use essas credenciais para fazer logon na VM ao conectar-se a ela na próxima etapa. Se o script falhar e você tiver alterado os valores no script antes de executá-lo, verifique se os valores usados para tamanho, sistema operacional e local da VM estão listados na seção [Limitações](#Limitations) deste artigo.
6. Para instalar o driver de rede acelerado para Windows, conclua as etapas na seção [Configurar o Windows](#configure-windows) deste artigo.

### <a name="configure-windows"></a>Configurar Windows
Depois de criar a VM no Azure, você deve instalar o driver de rede acelerada para Windows. Antes de concluir as etapas a seguir, você deve ter criado uma VM do Windows com rede acelerada usando as etapas do [portal](#windows-portal) ou do [PowerShell](#windows-powershell) neste artigo. 

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *MyVm*. Quando **MyVm** aparecer nos resultados da pesquisa, clique nela.
3. Na folha **MyVm** que é exibida, clique no botão **Conectar** no canto superior esquerdo da folha. **Observação:** se **Criando** estiver visível sob o botão **Conectar**, o Azure ainda não terminou de criar a VM. Clique em **Conectar** somente depois que **Criando** não for mais exibido sob o botão **Conectar**.
4. Permite ao navegador baixar o arquivo **MyVm.rdp**.  Após o download, clique no arquivo para abri-lo. 
5. Clique no botão **Conectar** na caixa **Conexão de área de trabalho remota** exibida, informando que o editor da conexão remota não pode ser identificado.
6. Na caixa **Segurança do Windows** que aparece, clique em **Mais opções** e, em seguida, clique em **Usar uma conta diferente**. Digite o nome de usuário e a senha digitados na etapa 4 e, em seguida, clique no botão **OK**.
7. Clique no botão **Sim** na caixa de Conexão de Área de Trabalho Remota exibida, informando-o de que não é possível verificar a identidade do computador remoto.
8. Clique com o botão direito do mouse em Iniciar do Windows e clique em **Gerenciador de Dispositivos**. Expanda o nó **Adaptadores de rede**. Verifique se o **Adaptador de Ethernet de Função Virtual Mellanox ConnectX-3** aparece, como mostrado na figura a seguir:
   
    ![Gerenciador de Dispositivos](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. A Rede Acelerada agora está habilitada para sua VM.

## <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Você pode usar o Portal do Azure ou o Azure [PowerShell](#linux-powershell) para criar uma VM Ubuntu ou SLES. Para VMs RHEL e CentOS, há um fluxo de trabalho diferente.  Veja as instruções abaixo.

### <a name="linux-portal"></a>Portal
1. Registre-se na rede acelerada para a versão prévia do Linux concluindo as etapas 1 a 5 da seção [Criar uma VM do Linux – PowerShell](#linux-powershell) deste artigo.  Você não pode se registrar para a versão prévia no portal.
2. Conclua as etapas 1 a 8 na seção [Criar uma VM do Windows – portal](#windows-portal) deste artigo. Na etapa 2, clique em **Ubuntu Server 16.04 LTS**, em vez de **Windows Server 2016 Datacenter**. Para este tutorial, opte por usar uma senha, em vez de uma chave SSH, embora você pode usar qualquer uma para implantações de produção. Caso **Rede acelerada** não apareça quando você conclui a etapa 7 da seção [Criar uma VM do Windows – portal](#windows-portal) deste artigo, é provável que isso seja devido a um dos seguintes motivos:
    - Você ainda não está registrado para a versão prévia. Verifique se o seu estado do registro é **Registrado**, conforme explicado na etapa 4 da seção [Criar uma VM do Linux – Powershell](#linux-powershell) deste artigo. **Observação:** se você tiver participado da rede Acelerada para a versão prévia de VMs do Windows (não é mais necessário registrar-se para usar a rede Acelerada para VMs do Windows), você não estará automaticamente registrado para a rede Acelerada para a versão prévia de VMs do Linux. Você deverá registrar-se na rede acelerada para a versão prévia de VMs do Linux para participar delas.
    - Você não selecionou um tamanho de VM, sistema operacional nem local listado na seção [Limitações](#limitations) deste artigo.
3. Para instalar o driver de rede acelerado para Linux, conclua as etapas na seção [Configurar o Linux](#configure-linux) deste artigo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Se você criar VMs do Linux com rede acelerada em uma assinatura e, em seguida, tentar criar uma VM do Windows com rede acelerada na mesma assinatura, a criação da VM do Windows poderá falhar. Durante essa versão prévia, é recomendável testar VMs do Linux e do Windows com rede acelerada em assinaturas diferentes.
>

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **powershell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Registre-se para rede acelerada para versão prévia do Azure concluindo as seguintes etapas:
    - Envie um email para [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura do Azure e o uso pretendido. Aguarde um email de confirmação da Microsoft sobre sua assinatura ser habilitada.
    - Digite o seguinte comando para confirmar que você está registrado para a versão prévia:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Não continue com a etapa 5 até **Registrado** aparecer na saída após a execução do comando anterior. A saída deve se parecer com a saída a seguir antes de continuar:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Se você tiver participado da Rede acelerada para versão prévia de VMs do Windows (não é mais necessário registrar-se para usar Rede Acelerada para VMs do Windows), você não estará automaticamente registrado para a Rede acelerada para a versão prévia de VMs do Linux. Você deverá registrar-se na rede acelerada para a versão prévia de VMs do Linux para participar delas.
      >
5. No navegador, copie o script a seguir substituindo Ubuntu ou SLES conforme desejado.  Novamente, o Redhat e o CentOS têm um fluxo de trabalho diferente, descrito abaixo:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. Na janela do PowerShell, clique com o botão direito do mouse para colar o script e começar a executá-lo. Você será solicitado a informar um nome de usuário e senha. Use essas credenciais para fazer logon na VM ao conectar-se a ela na próxima etapa. Se o script falhar, verifique:
    - Se você está registrado para a versão prévia, conforme explicado na etapa 4
    - Se você tiver alterado o tamanho, o tipo de sistema operacional ou os valores de local da VM no script antes de executá-lo, se os valores estão listados na seção [Limitações](#Limitations) deste artigo.
7. Para instalar o driver de rede acelerado para Linux, conclua as etapas na seção [Configurar o Linux](#configure-linux) deste artigo.

### <a name="configure-linux"></a>Configurar Linux

Depois de criar a VM no Azure, você deve instalar o driver de rede acelerada para Linux. Antes de concluir as etapas a seguir, você deve ter criado uma VM do Linux com rede acelerada usando as etapas do [portal](#linux-portal) ou do [PowerShell](#linux-powershell) neste artigo. 

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior do portal, à direita da barra *Pesquisar recursos*, clique no ícone **>_** para iniciar um cloud shell de Busca (Versão Prévia). O painel do cloud shell de Busca é exibido na parte inferior do portal e, depois de alguns segundos, apresenta um prompt **username@Azure:~$**. Embora você possa usar SSH para a VM do seu computador, em vez do cloud shell, as instruções neste tutorial presumem que você esteja usando o cloud shell.
3. Na parte superior do portal, na caixa que contém o texto *Pesquisar recursos*, digite *MyVm*. Quando **MyVm** aparecer nos resultados da pesquisa, clique nela.
4. Na folha **MyVm** que é exibida, clique no botão **Conectar** no canto superior esquerdo da folha. **Observação:** se **Criando** estiver visível sob o botão **Conectar**, o Azure ainda não terminou de criar a VM. Clique em **Conectar** somente depois que **Criando** não for mais exibido sob o botão **Conectar**.
5. O Azure abre uma caixa pedindo para você inserir o `ssh adminuser@<ipaddress>`. Digite esse comando no cloud shell (ou copie-o da caixa exibida na etapa 4 e cole-o no cloud shell) e, em seguida, pressione Enter.
6. Insira **Sim** para a pergunta consultando se você deseja continuar a conexão e, em seguida, pressione Enter.
7. Digite a senha inserida ao criar a VM. Depois de fazer logon com êxito na VM, você verá um prompt adminuser@MyVm:~$. Agora você está conectado à VM por meio da sessão de cloud shell. **Observação:** as sessões de cloud shell atingem o tempo limite depois de 10 minutos de inatividade.

Neste ponto, as instruções variam de acordo com a distribuição que você está usando. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. No prompt, digite `uname -r` e confirme a versão para:

    * O Ubuntu é "4.4.0-77-generic" ou superior
    * O SLES é "4.4.59-92.20-default" ou superior

2. Crie um vínculo entre a vNIC de rede padrão e a vNIC de rede acelerada executando os comandos a seguir. O tráfego de rede usa a vNIC de rede acelerada de maior desempenho, enquanto o vínculo garante que o tráfego de rede não seja interrompido em determinadas alterações de configuração.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. Depois de executar o script, a VM será reiniciada após uma pausa de 60 segundos.
4. Depois que a VM for reiniciada, reconecte-a ao concluir as etapas 5 a 7 novamente.
5. Execute o comando `ifconfig` e confirme se bond0 surgiu e se a interface está aparecendo como UP. 
 
 >[!NOTE]
      >Os aplicativos usando a rede acelerada devem se comunicar por meio da interface *bond0*, não *eth0*.  O nome da interface pode mudar antes de a rede acelerada atingir disponibilidade geral.

#### <a name="rhelcentos"></a>RHEL/CentOS

Criar uma VM Red Hat Enterprise Linux ou CentOS 7.3 requer algumas etapas adicionais para carregar os drivers mais recentes necessários para SR-IOV e o driver VF (Função Virtual) para a placa de rede. A primeira fase das instruções prepara uma imagem que pode ser usada para fazer uma ou mais máquinas virtuais com os drivers pré-carregados.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>Fase um: preparar uma imagem base do Red Hat Enterprise Linux ou CentOS 7.3. 

1.  Provisione uma VM CentOS 7.3 não SRIOV no Azure

2.  Instale o LIS 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Baixe os arquivos de configuração
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Desprovisione essa VM

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  No Portal do Azure, pare essa VM e vá para a captura “Discos” da VM no URI do VHD do OSDisk. Esse URI contém o nome do VHD da imagem base e sua conta de armazenamento. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Fase dois: provisionar novas VMs no Azure

1.  Provisione novas VMs com base em New-AzureRMVMConfig usando a imagem base do VHD capturada na fase um, com AcceleratedNetworking habilitado na vNIC:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  Depois de as VMs inicializarem, verifique o dispositivo VF por “Ispci” e verifique a entrada Mellanox. Por exemplo, devemos ver esse item na saída de Ispci:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Execute o script de associação por:

    ```bash
    sudo bondvf.sh
    ```

4.  Reinicialize as novas VMs:

    ```bash
    sudo reboot
    ```

A VM deve começar com bond0 configurado e o caminho da Rede Acelerada habilitado.  Execute `ifconfig` para confirmar.
