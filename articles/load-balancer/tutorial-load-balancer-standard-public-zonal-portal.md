---
title: 'Tutorial: VMs do balanceador de carga em uma zona – portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial demonstra como criar um Standard Load Balancer com front-end zonal para balancear carga de VMs em uma zona de disponibilidade usando o portal do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 0ec9fae1ce4ef976d5f50e1d8d8412354706c5f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273393"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Tutorial: balancear carga de VMs em uma zona de disponibilidade com Standard Load Balancer usando o portal do Azure

Este tutorial cria uma [instância pública do Standard Load Balancer do Azure](https://aka.ms/azureloadbalancerstandard) com um front-end zonal que usa um endereço IP público padrão usando o portal do Azure. Nesse cenário, você especifica uma zona específica para as instâncias de back-end e front-end, a fim de alinhar o caminho de dados e os recursos com uma zona específica. Você aprenderá como executar as funções a seguir:

> [!div class="checklist"]
> * Criar uma instância do Standard Load Balancer com um front-end zonal.
> * Criar grupos de segurança de rede para definir as regras do tráfego de entrada.
> * Criar VMs (Máquinas Virtuais) zonais e anexá-las a um balanceador de carga.
> * Criar uma investigação de integridade do balanceador de carga.
> * Criar regras de tráfego para o balanceador de carga.
> * Criar um site do IIS (Serviços de Informações da Internet) básico.
> * Exibir um balanceador de carga em ação.

Para obter mais informações sobre o uso de zonas de disponibilidade com Standard Load Balancer, consulte [Zonas de disponibilidade e Standard Load Balancer](load-balancer-standard-availability-zones.md).

Se preferir, utilize a [CLI do Azure](load-balancer-standard-public-zonal-cli.md) para concluir este tutorial.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Criar uma instância do Standard Load Balancer público

O Standard Load Balancer dá suporte somente a um endereço IP público padrão. Ao criar um novo IP público durante a criação do balanceador de carga, ele será configurado automaticamente como uma versão de SKU Standard. Também é automaticamente com redundância de zona.

1. No canto superior esquerdo da tela, selecione  **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite *myResourceGroupZLB* na caixa de texto.|
    | NOME                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **1**.    |
3. Na guia **Revisar + criar**, clique em **Criar**.   

   ## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você criará uma rede virtual. Além disso, criará duas máquinas virtuais na mesma zona (ou seja, zona 1) para a região adicionar ao pool de back-end do balanceador de carga. Em seguida, instalará o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga com redundância de zona. Se uma VM falhar, a investigação de integridade da VM na mesma zona falhará. O tráfego continua sendo atendido por outras VMs na mesma zona.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.  Insira estes valores para a rede virtual:
    - **myVnet**, para o nome da rede virtual.
    - **myResourceGroupZLB**, para o nome do grupo de recursos existente.
    - **myBackendSubnet**, para o nome da sub-rede.
2. Selecione **Criar** para criar uma rede virtual.

    ![Criar uma rede virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. No canto superior esquerdo da tela, selecione **Crie um recurso**. Na caixa de pesquisa, insira **Grupo de Segurança de Rede**. Na página do grupo de segurança de rede, selecione **Criar**.
2. Na página **Criar grupo de segurança de rede**, insira estes valores:
   - **myNetworkSecurityGroup**, para o nome do grupo de segurança de rede.
   - **myResourceGroupLBAZ**, para o nome do grupo de recursos existente.
   
     ![Criar um grupo de segurança de rede](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Criar regras NSG

Nesta seção, você criará regras de NSG para permitir conexões de entrada que usam HTTP e RDP da Microsoft, usando o portal do Azure.

1. No portal do Azure, selecione **Todos os recursos** no menu esquerdo. Em seguida, pesquise e selecione **myNetworkSecurityGroup**. Ele está localizado no grupo de recursos **myResourceGroupZLB**.
2. Em **Configurações**, selecione **Regras de segurança de entrada**. Em seguida, selecione**Adicionar**.
3. Insira estes valores para a regra de segurança de entrada nomeada **myHTTPRule** para permitir conexões HTTP de entrada que usam a porta 80:
    - **Marca de Serviço**, para **Origem**.
    - **Internet**, para **Marca de serviço de origem**.
    - **80**, para **Intervalos de porta de destino**.
    - **vTCP**, para **Protocolo**.
    - **Permitir**, para **Ação**.
    - **100**, para **Prioridade**.
    - **myHTTPRule**, para **Nome**.
    - **Permitir HTTP**, para **Descrição**.
4. Selecione **OK**.
 
   ![Criar regras NSG](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Repita as etapas de 2 a 4 para criar outra regra nomeada **myRDPRule**. Essa regra permite uma conexão RDP de entrada que usa a porta 3389, com os valores a seguir:
    - **Marca de Serviço**, para **Origem**.
    - **Internet**, para **Marca de serviço de origem**.
    - **3389**, para **Intervalos de porta de destino**.
    - **TCP**, para **Protocolo**.
    - **Permitir**, para **Ação**.
    - **200**, para **Prioridade**.
    - **myRDPRule**, para **Nome**.
    - **Permitir RDP**, para **Descrição**.

      ![Criar uma regra de RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**. Insira esses valores para a máquina virtual:
    - **myVM1**, para o nome da máquina virtual.        
    - **azureuser**, para o nome de usuário do administrador.    
    - **myResourceGroupZLB**, para **Grupo de Recursos**. Selecione **Usar existente** e, em seguida, selecione **myResourceGroupZLB**.
2. Selecione **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual. Escolha **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - **zona 1**, para a zona de disponibilidade onde você coloca a VM.
    -  **myVNet**. Certifique-se de que esse valor esteja selecionado como a rede virtual.
    - **myVM1PIP**, para o endereço IP público padrão que você cria. Selecione **Criar novo**. Em seguida, para o tipo de nome, selecione **myVM1PIP**. Para **Zona**, selecione **1**. A SKU do endereço IP é Standard por padrão.
    - **myBackendSubnet**. Certifique-se de que esse valor esteja selecionado como a sub-rede.
    - **myNetworkSecurityGroup**, para o nome do firewall do grupo de segurança de rede que já existe.
5. Selecione **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Selecione **OK**. Revise as configurações na página de resumo. Em seguida, selecione **Criar**.
7. Repita as etapas de 1 a 6 para criar uma segunda VM, nomeada **myVM2**, na Zona 1. Faça **myVnet** a rede virtual. Faça **myVM2PIP** o endereço IP público padrão. Faça **myBackendSubnet** a sub-rede. E faça **myNetworkSecurityGroup** o grupo de segurança de rede.

    ![Criar máquinas virtuais](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalar IIS em VMs

1. Selecione **Todos os recursos** no menu esquerdo. Em seguida, na lista de recursos, selecione **myVM1**. Ele está localizado no grupo de recursos **myResourceGroupZLB**.
2. Na página **Visão Geral**, selecione **Conectar** para usar o RDP na VM.
3. Entre na VM com o nome de usuário e senha que você especificou quando criou a VM. Para especificar as credenciais inseridas ao criar a VM, talvez seja necessário selecionar **Mais escolhas**. Em seguida, selecione **Usar uma conta diferente**. E, em seguida, selecione **OK**. Você pode receber um aviso de certificado durante o processo de entrada. Selecione **Sim** para prosseguir com a conexão.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows** > **Windows PowerShell**.
6. Na janela**PowerShell**, execute os comandos a seguir para instalar o servidor IIS. Esses comandos também removem o arquivo iisstart.htm padrão e, em seguida, adicionam um novo arquivo iisstart.htm que exibe o nome da VM:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Feche a sessão RDP com **myVM1**.
8. Repita as etapas de 1 a 7 para instalar o IIS no **myVM2**.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você definirá as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade. Você também pode especificar o balanceador de carga e as regras de conversão de endereços de rede.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as VMs, um pool de endereços de back-end contém os endereços IP das placas de adaptador de rede virtual conectadas ao balanceador de carga. Crie o pool de endereços de back-end **myBackendPool** para incluir **VM1** e **VM2**.

1. Selecione **Todos os recursos** no menu esquerdo. Em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end**. Em seguida, selecione**Adicionar**.
3. Na página **Adicionar um pool de back-end**, execute as seguintes ações:
    - Para nome, insira **myBackEndPool** como o nome do pool de back-end.
    - Para **Rede virtual**, no menu suspenso, selecione **myVNet**. 
    - Para **Máquina virtual** e **Endereço IP**, adicione **myVM1** e **myVM2** e os endereços IP públicos correspondentes.
4. Selecione **Adicionar**.
5. Verifique se a configuração do pool de back-end do balanceador de carga exibe ambas as VMs: **myVM1** e **myVM2**.
 
    ![Crie um pool de back-end](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Use uma investigação de integridade para que o balanceador de carga possa monitorar o status do aplicativo. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade **myHealthProbe** para monitorar a integridade das VMs.

1. Selecione **Todos os recursos** no menu esquerdo. Em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, selecione **Investigações de integridade**. Em seguida, selecione**Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - **myHealthProbe**, para o nome da investigação de integridade.
    - **HTTP**, para o tipo de protocolo.
    - **80**, para o número da porta.
    - **15**, para o número de **Intervalo** em segundos entre as tentativas de investigação.
    - **2**, para o número de **Limite não íntegro** ou falhas consecutivas da investigação que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Selecione **OK**.

   ![Adicionar uma investigação de integridade](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balanceador de carga define como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga **myLoadBalancerRuleWeb**, para ouvir a porta 80 no **FrontendLoadBalancer** de front-end. A regra envia tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool**, também usando a porta 80. 

1. Selecione **Todos os recursos** no menu esquerdo. Em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, selecione **Regras de balanceamento de carga**. Em seguida, selecione**Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    - **myHTTPRule**, para o nome da regra de balanceamento de carga.
    - **TCP**, para o tipo de protocolo.
    - **80**, para o número da porta.
    - **80**, para a porta backend.
    - **myBackendPool**, para o nome do pool de back-end.
    - **myHealthProbe**, para o nome da investigação de integridade.
4. Selecione **OK**.
    
    ![Adicionar uma regra de balanceamento de carga](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Encontre o endereço IP público para o balanceador de carga na tela **Visão Geral**. Selecione **Todos os recursos**. Em seguida, selecione **myPublicIP**. 

2. Copie o endereço IP público. Em seguida, cole-o na barra de endereços do navegador. A página padrão que inclui o nome da página do servidor Web é exibida no navegador.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Para ver o balanceador de carga em ação, force a parada da VM exibida. Atualize o navegador para ver o outro nome do servidor exibido no navegador.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos que contém o balanceador de carga. Em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- [Balancear carga de VMs em zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
