---
title: Como habilitar a virtualização aninhada em Máquinas Virtuais do Azure | Microsoft Docs
description: Como habilitar a virtualização aninhada em Máquinas Virtuais do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 843dfa64cdf0af3ad6cfd3a9f83c16f0ce85fcd0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720220"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Como habilitar a virtualização aninhada em uma VM do Azure

A virtualização aninhada tem suporte em várias famílias de máquinas virtuais do Azure. Essa funcionalidade proporciona uma grande flexibilidade no suporte a cenários como ambientes de desenvolvimento, teste, treinamento e demonstração.   

Este artigo demonstra como habilitar o Hyper-V em uma VM do Azure e configurar a conectividade de Internet com essa máquina virtual convidada.

## <a name="create-a-nesting-capable-azure-vm"></a>Criar uma VM do Azure com capacidade de aninhamento

Crie uma nova VM do Windows Server 2016 do Azure.  Para referência rápida, todas as máquinas virtuais de v3 dão suporte a virtualização aninhada. Para obter uma lista completa dos tamanhos de máquinas virtuais que dão suporte para aninhamento, confira o [artigo da Unidade de Computação do Azure](acu.md).

Escolha um tamanho de VM grande o suficiente para dar suporte às demandas de uma máquina virtual convidada. Neste exemplo, estamos usando uma VM do Azure com um tamanho D3_v3. 

Exiba a disponibilidade regional das máquinas virtuais da série Dv3 ou Ev3 [aqui](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Para obter instruções detalhadas sobre como criar uma nova máquina virtual, consulte [Criar e gerenciar VMs Windows com o módulo do Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Conectar-se à VM do Azure

Inicie uma conexão da área de trabalho remota para a máquina virtual.

1. Clique no botão **Conectar** nas propriedades da máquina virtual. Um arquivo do protocolo RDP (.rdp) é criado e baixado.

2. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

3. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **Ok**.

4. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Habilitar o recurso Hyper-V na VM do Azure
Defina essas configurações manualmente ou use um script do PowerShell que fornecemos para automatizar a configuração.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opção 1: Usar um script do PowerShell para configurar a virtualização aninhada
Um script do PowerShell para habilitar a virtualização aninhada em um host do Windows Server 2016 está disponível no [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). O script verifica os pré-requisitos e, em seguida, configura a virtualização aninhada na VM do Azure. Uma reinicialização da VM do Azure é necessária para concluir a configuração. Esse script pode funcionar em outros ambientes, mas não há garantia disso. Confira a postagem no blog do Azure com uma demonstração de vídeo ao vivo sobre a virtualização aninhada em execução no Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opção 2: Configurar a virtualização aninhada manualmente

1. Na VM do Azure, abra o PowerShell como Administrador. 

2. Habilite o recurso Hyper-V e as Ferramentas de Gerenciamento.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Este comando reinicia a VM do Azure. Você perderá sua conexão RDP durante o processo de reinicialização.
    
3. Depois que a VM do Azure for reiniciada, reconecte-se à VM usando o RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade com a Internet para a máquina virtual convidada
Crie um novo adaptador de rede virtual para a máquina virtual convidada e configure um Gateway de NAT para habilitar a conectividade com a Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Criar um comutador de rede virtual NAT

1. Na VM do Azure, abra o PowerShell como Administrador.
   
2. Crie um comutador interno.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Exiba as propriedades do comutador e anote o ifIndex do novo adaptador.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Anote o “ifIndex” do comutador virtual recém-criado.
    
4. Crie um endereço IP para o Gateway de NAT.
    
Para configurar o gateway, você precisa de algumas informações sobre a rede:    
  * IPAddress – o IP do Gateway de NAT especifica o endereço IPv4 ou IPv6 a ser usado como o endereço de gateway padrão da sub-rede da rede virtual. O formulário genérico é a.b.c. 1 (por exemplo, “192.168.0.1”). Embora a posição final não precise ser .1, geralmente ela é assim (com base no tamanho do prefixo). Normalmente, você deve usar um espaço de endereço de rede privada RFC 1918. 
  * PrefixLength – o tamanho do prefixo da sub-rede define o tamanho da sub-rede local (máscara de sub-rede). O tamanho do prefixo de sub-rede será um valor inteiro entre 0 e 32. O valor 0 mapeará toda a Internet e 32 permitirá somente um IP mapeado. Os valores comuns variam de 24 a 12, dependendo de quantos IPs precisam ser anexados ao NAT. Um PrefixLength comum é 24 – essa é uma máscara de sub-rede igual a 255.255.255.0.
  * InterfaceIndex – **ifIndex** é o índice de interface do comutador virtual criado na etapa anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Criar a rede NAT

Para configurar o gateway, você precisará fornecer informações sobre a rede e o Gateway de NAT:
  * Name – esse é o nome da rede NAT. 
  * InternalIPInterfaceAddressPrefix – o prefixo de sub-rede NAT descreve o prefixo de IP do Gateway de NAT acima, bem como o Tamanho de Prefixo da Sub-rede NAT acima. O formato genérico será a.b.c.0/Tamanho de Prefixo da Sub-rede NAT. 

No PowerShell, crie uma nova rede NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Criar a máquina virtual convidada

>[!IMPORTANT] 
>
>O agente convidado do Azure não tem suporte em VMs aninhadas e pode causar problemas no host e VMs aninhadas. Não instale o agente do Azure em VMs aninhadas e não use uma imagem para a criação de VMs aninhadas que já tenha o agente convidado do Azure instalado.

1. Abra o Gerenciador do Hyper-V e crie uma nova máquina virtual. Configure a máquina virtual para usar a nova rede Interna criada.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale um sistema operacional na máquina virtual convidada.
    
    >[!NOTE] 
    >
    >Você precisa de uma mídia de instalação de um sistema operacional para instalar na VM. Nesse caso, estamos usando o Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Atribuir um endereço IP à máquina virtual convidada

Atribua um endereço IP à máquina virtual convidada definindo um endereço IP estático na máquina virtual convidada manualmente ou configurando o DHCP na VM do Azure para atribuir o endereço IP dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opção 1: Configurar o DHCP para atribuir dinamicamente um endereço IP à máquina virtual convidada
Siga as etapas abaixo para configurar o DHCP na máquina virtual host para a atribuição de endereço dinâmico.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalar o Servidor DHCP na VM do Azure

1. Abra o Gerenciador de Servidor. No Painel, clique em **Adicionar funções e recursos**. O Assistente de Adição de Funções e Recursos será exibido.
  
2. No assistente, clique em **Avançar** até a página Funções de Servidor.
  
3. Clique para selecionar a caixa de seleção **Servidor DHCP**, clique em **Adicionar Recursos** e, em seguida, clique em **Avançar** até concluir o assistente.
  
4. Clique em **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configurar um novo escopo do DHCP

1. Abra o Gerenciador DHCP.
  
2. No painel de navegação, expanda o nome do servidor, clique com o botão direito do mouse em **IPv4** e clique em **Novo Escopo**. O Assistente para Novos Escopos será exibido. Clique em **Avançar**.
  
3. Insira um Nome e uma Descrição para o escopo e clique em **Avançar**.
  
4. Defina um Intervalo de IP para o Servidor DHCP (por exemplo, 192.168.0.100 a 192.168.0.200).
  
5. Clique em **Avançar** até a página Gateway Padrão. Insira o Endereço IP criado anteriormente (por exemplo, 192.168.0.1) como o Gateway Padrão e, em seguida, clique em **Adicionar**.
  
6. Clique em **Avançar** até a conclusão do assistente, deixando todos os valores padrão e, em seguida, clique em **Concluir**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opção 2: Definir um endereço IP estático manualmente na máquina virtual convidada
Se você não configurou o DHCP para atribuir dinamicamente um endereço IP à máquina virtual convidada, siga estas etapas para configurar um endereço IP estático.

1. Na VM do Azure, abra o PowerShell como Administrador.

2. Clique com o botão direito do mouse na máquina virtual convidada e clique em Conectar.

3. Entrar para a máquina virtual convidada.

4. Na máquina virtual convidada, abra a Central de Rede e Compartilhamento.

5. Configure o adaptador de rede para um endereço dentro do intervalo da rede NAT criada na seção anterior.

Neste exemplo, você usará um endereço no intervalo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testar a conectividade na máquina virtual convidada

Na máquina virtual convidada, abra o navegador e navegue para uma página da Web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Configurar a conectividade com a intranet para a máquina virtual convidada

Para obter instruções sobre como habilitar a conectividade transparente entre as VMs convidadas e VMs do Azure, consulte [este documento](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
