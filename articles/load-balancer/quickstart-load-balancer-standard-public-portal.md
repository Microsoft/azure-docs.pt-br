---
title: 'Início Rápido: Criar um Standard Load Balancer – portal do Azure'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um Standard Load Balancer usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c8df0daac25a79bbbd67577c30b0a2da62d037da
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273836"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Início Rápido: Criar um balanceador de carga padrão para VMs usando o Portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala por distribuir as solicitações de entrada entre várias máquinas virtuais. Você pode usar o Portal do Azure para criar um balanceador de carga para balancear a carga de máquinas virtuais (VMs). Este início rápido mostra como carregar a carga de VMs usando um balanceador de carga padrão.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta seção, você cria um Standard Load Balancer que ajuda a balancear a carga de máquinas virtuais. Um balanceador de carga padrão só oferece suporte a um endereço IP público padrão. Ao criar um balanceador de carga padrão, você também deve criar um novo endereço IP público padrão configurado como o front-end (nomeado como *LoadBalancerFrontend* por padrão) para o balanceador de carga padrão. 

1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite *myResourceGroupSLB* na caixa de texto.|
    | NOME                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **Com redundância de zona**.    |
3. Na guia **Examinar + criar**, selecione **Criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do Load Balancer

Nesta seção, você define as configurações do Load Balancer para um pool de endereços de back-end e uma investigação de integridade, além de especificar uma regra de balanceamento.

### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego entre as VMs, um pool de endereços de back-end contém os endereços IP dos NICs virtuais conectados ao Load Balancer. Crie o pool de endereços de back-end *myBackendPool* para incluir máquinas virtuais para balanceamento de carga de tráfego da Internet.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
3. Na página **Adicionar um pool de back-end**, insira *myBackendPool* como o nome para o pool de back-end e depois selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o Load Balancer monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do Load Balancer com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
    
    | Configuração | Valor |
    | ------- | ----- |
    | NOME | Insira *myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira *80*.|
    | Intervalo | Insira *15* para o número de **Intervalo** em segundos entre tentativas de investigação. |
    | Limite não íntegro | Insira **2** para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra do Load Balancer
Uma regra do Load Balancer é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do Load Balancer *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    
    | Configuração | Valor |
    | ------- | ----- |
    | NOME | Insira *myHTTPRule*. |
    | Protocolo | selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porta de back-end | Insira *80*. |
    | Pool de back-end | Selecione *myBackendPool*.|
    | Investigação de integridade | Selecione *myHealthProbe*. |
4. Deixe o restante dos padrões e selecione **OK**.


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, crie uma rede virtual, crie três máquinas virtuais para o pool de back-end do Load Balancer e, em seguida, instalar o IIS nas máquinas virtuais para ajudar a testar o Load Balancer.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Rede virtual**.

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | NOME | Insira *myVNet*. |
    | Espaço de endereço | Insira *10.1.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione o recurso existente – *myResourceGroupSLB*. |
    | Location | Selecione **Europa Ocidental**.|
    | Sub-rede – Nome | Insira *myBackendSubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.1.0.0/24*. |
1. Deixe o restante dos padrões e selecione **Criar**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
O Standard Load Balancer só dá suporte a VMs com endereços IP Standard no pool de back-end. Nesta seção, você criará três VMs (*myVM1*, *myVM2* e *myVM3*) com um endereço IP público Standard em três regiões diferentes (*Zona 1*, *Zona 2* e *Zona 3*) que serão adicionados ao pool de back-end do Standard Load Balancer que foi criado anteriormente.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Windows Server 2019 Datacenter**. 
   
1. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupSLB**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: Digite *myVM1*.
   - **Detalhes da Instância** > **Região** > selecione **Oeste da Europa**.
   - **Detalhes da Instância** > **Opções de Disponibilidade** > selecione **Zonas de disponibilidade**. 
   - **Detalhes da Instância** > **Zona de disponibilidade** > selecione **1**.
   - **Conta de administrador**> Insira as informações de **Nome de usuário**, **Senha** e **Confirmar senha**.
   - Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
1. Na guia **Rede** verifique se os itens abaixo estão selecionados:
   - **Rede virtual**: *myVnet*
   - **Sub-rede**: *myBackendSubnet*
   - **IP público** > selecione **Criar** e, na janela **Criar endereço IP público**, para **SKU**, selecione **Standard** e, para **Zona de disponibilidade**, selecione **Com redundância de zona** e, sem seguida, selecione **OK**.
   - Para criar um novo NSG (grupo de segurança de rede), um tipo de firewall, em **Grupo de segurança de rede**, selecione **Avançado**. 
       1. No campo **Configurar grupo de segurança de rede**, selecione **Criar novo**. 
       1. Insira *myNetworkSecurityGroup* e selecione **OK**.
   - Para tornar a VM em uma parte do pool de back-end do Load Balancer, conclua as seguintes etapas:
        - Em **Balanceamento de Carga**, para **Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente?** , selecione **Sim**.
        - Em **Configurações de balanceamento de carga**, para **Opções de balanceamento de carga**, selecione **Azure Load Balancer**.
        - Para **Selecionar um balanceador de carga**, escolha *myLoadBalancer*.
        - Selecione a guia **Gerenciamento** ou selecione **Avançar** > **Gerenciamento**.
2. Na guia **Gerenciamento**, em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**. 
1. Selecione **Examinar + criar**.   
1. Examine as configurações e selecione **Criar**.
1. Siga as etapas de 2 a 6 para criar duas VMs adicionais com os seguintes valores e todas as outras configurações iguais à *myVM1*:

    | Configuração | VM 2| VM 3|
    | ------- | ----- |---|
    | NOME |  *myVM2* |*myVM3*|
    | Zona de disponibilidade | 2 |3|
    |IP público| SKU **Standard**|SKU **Standard**|
    | IP público – Zona de disponibilidade| **Redundância de zona** |**Redundância de zona**|
    | Grupo de segurança de rede | Selecionar o *Grupo myNetworkSecurity* existente| Selecionar o *Grupo myNetworkSecurity* existente|

 ### <a name="create-nsg-rule"></a>Como criar regra NSG

Nesta seção, você criará uma regra de grupo de segurança de rede para permitir conexões de entrada usando HTTP.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, na lista de recursos, selecione **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB**.
2. Em **Configurações**, selecione **Regras de segurança de entrada** e, em seguida, **Adicionar**.
3. Insira esses valores para a regra de segurança de entrada denominada *myHTTPRule* para permitir conexões de entrada HTTP usando a porta 80:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *80* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *100* - para **Prioridade**
    - *myHTTPRule* - para nome
    - *Allow HTTP* - para descrição
4. Selecione **Adicionar**.
 
### <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, na lista de recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB*.
2. Na página **Visão Geral**, selecione **Conectar** para habilitar o RDP na VM.
5. Faça logon na VM com as credenciais que você forneceu durante a criação dessa VM. Isso inicia uma sessão de área de trabalho remota com a máquina virtual - *myVM1*.
6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
7. Na janela do PowerShell, execute os comandos a seguir para instalar o servidor IIS, remova o arquivo padrão iisstart.htm, e adicione um novo arquivo iisstart.htm que exibe o nome da VM:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Feche a sessão RDP com *myVM1*.
7. Repita as etapas de 1 a 6 para instalar o IIS e o arquivo iisstart.htm atualizado em *myVM2* e *myVM3*.

## <a name="test-the-load-balancer"></a>Testar o Load Balancer
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Load Balancer distribuir o tráfego entre as três VMs, você pode personalizar a página padrão do servidor Web do IIS de cada VM e forçar a atualização de seu navegador da web no computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o Load Balancer e todos os recursos relacionados. Para isso, selecione o grupo de recursos (*myResourceGroupSLB*) que contém o Load Balancer e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Standard Load Balancer, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, a investigação de integridade e testou o balanceador de carga. Para saber mais sobre o Azure Load Balancer, continue nos tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
