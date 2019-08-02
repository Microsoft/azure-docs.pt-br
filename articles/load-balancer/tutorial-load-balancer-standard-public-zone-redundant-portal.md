---
title: 'Tutorial: VMs do balanceador de carga entre zonas de disponibilidade – portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial demonstra como criar um Balanceador de Carga Standard com front-end com redundância de zona para balancear a carga de VMs entre zonas de disponibilidade usando o portal do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 5b024321a18c6dec4f56a7cbc12c5a8fa748f903
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273471"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Tutorial: Balancear carga de VMs entre zonas de disponibilidade com um Load Balancer Standard utilizando o Portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade, distribuindo as solicitações de entrada em várias máquinas virtuais. Este tutorial descreve as etapas necessárias para criar um Standard Load Balancer público que balanceia a carga de VMs entre zonas de disponibilidade. Isso ajuda a proteger seus aplicativos e dados contra uma improvável falha ou perda de um datacenter inteiro. Com redundância de zona, uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobrevive, desde que uma zona da região permaneça íntegra. Você aprenderá como:

> [!div class="checklist"]
> * Criar um Balanceador de Carga Standard
> * Criar grupos de segurança de rede para definir as regras do tráfego de entrada
> * Criar VMs com redundância de zona em várias zonas e anexá-las a um balanceador de carga
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Criar um site básico do IIS
> * Exibir um balanceador de carga em ação

Para obter mais informações sobre o uso de Zonas de Disponibilidade com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md).

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](load-balancer-standard-public-zone-redundant-cli.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Um balanceador de carga padrão só oferece suporte a um endereço IP público padrão. Ao criar um novo IP público durante a criação do balanceador de carga, ele é configurado automaticamente como uma versão de SKU Standard e, além disso, é automaticamente com redundância de zona.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite *MyResourceGroupLBAZ* na caixa de texto.|
    | NOME                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **Com redundância de zona**.    |
   

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria uma rede virtual, máquinas virtuais em diferentes regiões para a região e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga com redundância de zona. Portanto, se uma região falhar, a investigação de integridade da VM na mesma zona falhará e o tráfego continuará sendo atendido pelas VMs nas outras regiões.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para implantar os servidores back-end.

1. Na parte superior esquerda da tela, clique em **Criar um recurso** > **Rede** > **Rede virtual** e insira esses valores para a rede virtual:
    - *myVNet* – para o nome da rede virtual.
    - *myResourceGroupLBAZ* - para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para definir conexões de entrada para sua rede virtual.

1. No canto superior esquerdo da tela, clique em **Criar um recurso**, na caixa de pesquisa digite *Grupo de Segurança de Rede* e, na página do grupo de segurança de rede, clique em **Criar**.
2. Na página Criar grupo de segurança de rede, insira estes valores:
    - *myNetworkSecurityGroup*  - para o nome do grupo de segurança de rede.
    - *myResourceGroupLBAZ* - para o nome do grupo de recursos existente.
   
![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Criar regras do grupo de segurança de rede

Nesta seção, você cria regras do grupo de segurança de rede para permitir conexões de entrada usando HTTP e RDP por meio do portal do Azure.

1. No Portal do Azure, clique em **Todos os recursos** no menu esquerdo e, em seguida, pesquise e clique em **myNetworkSecurityGroup** localizado no grupo de recursos **myResourceGroupLBAZ**.
2. Em **Configurações**, clique em **Regras de segurança de entrada** e clique em **Adicionar**.
3. Insira esses valores para a regra de segurança de entrada denominada *myHTTPRule* para permitir conexões de entrada HTTP usando a porta 80:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *80* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *100* - para **Prioridade**
    - *myHTTPRule* – para o nome da regra do balanceador de carga.
    - *Permitir HTTP* – para descrição da regra de balanceador de carga.
4. Clique em **OK**.
 
   ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Repita as etapas 2 a 4 para criar outra regra denominada *myRDPRule* para permitir uma conexão de RDP de entrada usando a porta 3389 com os seguintes valores:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *3389* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *200* - para **Prioridade**
    - *myRDPRule* - para nome
    - *Allow RDP* - para descrição

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie máquinas virtuais em diferentes regiões (zona 1, zona 2 e zona 3) para a região que pode atuar como servidores back-end para o balanceador de carga.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e insira esses valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *azureuser* – para o nome de usuário do administrador.    
    - *myResourceGroupLBAZ* - para **Grupo de recursos**, selecione **Usar existente** e, em seguida, selecione *myResourceGroupLBAZ*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - *zona 1* - para a zona onde você coloca a VM.
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
    - *myNetworkSecurityGroup* - para o nome do grupo de segurança de rede (firewall).
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
  
   ![Criar uma máquina virtual](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Crie uma segunda VM, nomeada *VM2* na Zona 2 e a terceira VM na Zona 3 e, com *myVnet* como a rede virtual, *myBackendSubnet* como a sub-rede e **myNetworkSecurityGroup* como o grupo de segurança de rede, utilizando as etapas de 1 a 6.

### <a name="install-iis-on-vms"></a>Instalar IIS em VMs

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em **myVM1** localizado no grupo de recursos *myResourceGroupLBAZ*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM com nome de usuário *azureuser*.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
5. Na janela do PowerShell, execute os comandos a seguir para instalar o servidor IIS, remova o arquivo padrão iisstart.htm, e adicione um novo arquivo iisstart.htm que exibe o nome da VM:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Feche a sessão RDP com *myVM1*.
7. Repita as etapas de 1 a 6 para instalar o IIS e o arquivo iisstart.htm atualizado em *myVM2* e *myVM3*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade e especifica as regras NAT e o balanceador de carga.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1*, *VM2* e *VM3*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
    - Para nome, digite *myBackEndPool*, como o nome do pool de back-end.
    - Para **Rede virtual**, no menu suspenso, clique em **myVNet**
    - Para **Máquina virtual**, no menu suspenso, clique em **myVM1**.
    - Para **Endereço IP**, no menu suspenso, clique no endereço IP de myVM1.
4. Clique em **Adicionar novo recurso de back-end** para adicionar cada máquina virtual (*myVM2* e *myVM3*) para adicionar no pool de back-end do balanceador de carga.
5. Clique em **Adicionar**.

    ![Adicionando ao pool de endereços de back-end –](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Verifique se a configuração do pool de back-end do balanceador de carga exibe todas as três VMs - **myVM1**, **myVM2** e **myVM3**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Investigação de integridade** e clique em **Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - *myHealthProbe* – para o nome da investigação de integridade.
    - **HTTP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *15* – para o número de **Intervalo** em segundos entre tentativas de investigação.
    - *2* – para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Clique em **OK**.

   ![Adicionando uma investigação](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, clique em **Regras de balanceamento de carga** e em **Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    - *myHTTPRule* – para o nome da regra de balanceamento de carga.
    - **TCP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *80* – para a porta de back-end.
    - *myBackendPool* – para o nome do pool de back-end.
    - *myHealthProbe* – para o nome da investigação de integridade.
4. Clique em **OK**.
    
    ![Adicionando uma regra de balanceamento de carga](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os recursos**, depois clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre as VMs distribuídas pela zona, você poderá forçar a atualização do navegador da Web.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
