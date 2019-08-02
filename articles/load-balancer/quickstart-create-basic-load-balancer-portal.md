---
title: 'Início Rápido: criar um balanceador de carga Básico público usando o portal do Azure'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um balanceador de carga Básico público usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 9819111c8264493648233f40252db4fb4410aaf1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274097"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Início Rápido: Criar um Load Balancer Básico usando o portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala com a distribuição das solicitações recebidas entre VMs (máquinas virtuais). Você pode usar o portal do Azure para criar um balanceador de carga e usá-lo entre VMs. Este início rápido mostra como criar e configurar um balanceador de carga, servidores back-end e um recursos de rede no tipo de preço Básico.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para realizar todas as tarefas deste início rápido, entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Criar o Load Balancer Básico

Primeiro, crie um Load Balancer Básico usando o portal. O nome e o endereço IP público que você criar são configurados automaticamente como o front-end do balanceador de carga.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Assinatura               | Selecione sua assinatura.    |    
    | Grupo de recursos         | Selecione **Criar** e digite *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Basic**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | *MyPublicIP*   |
    | Atribuição| Estático|

3. Na guia **Revisar + criar**, clique em **Criar**.   


## <a name="create-back-end-servers"></a>Criar servidores back-end

Depois, crie uma rede virtual e duas máquinas virtuais para o pool de back-end do balanceador de carga Básico. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
   
1. No painel **Criar rede virtual**, insira ou selecione estes valores:
   
   - **Nome**: digite *MyVnet*.
   - **ResourceGroup**: abra a lista suspensa **Selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **Nome**: digite *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**. 
   
1. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:
   - **Assinatura** > **Grupo de Recursos**: abra a lista suspensa e selecione **MyResourceGroupLB**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: digite *MyVM1*.
   - **Detalhes da Instância** > **Opções de Disponibilidade**: 
     1. Abra a lista suspensa e selecione **Conjunto de disponibilidade**. 
     2. Selecione **Criar novo**, insira *MyAvailabilitySet* e selecione **OK**.
  
1. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**. 
   
   Selecione os itens abaixo:
   - **Rede virtual**: **MyVnet**
   - **Sub-rede**: **MyBackendSubnet**
   - **IP público**: **MyVM1-ip**
   
   Para criar um novo NSG (grupo de segurança de rede), um tipo de firewall, em **Grupo de segurança de rede**, selecione **Avançado**. 
   1. No campo **Configurar grupo de segurança de rede**, selecione **Criar novo**. 
   1. Insira *MyNetworkSecurityGroup* e selecione **OK**. 
   
1. Selecione a guia **Gerenciamento** ou selecione **Próxima** > **Gerenciamento**. Em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.
   
1. Selecione **Examinar + criar**.
   
1. Examine as configurações e selecione **Criar**. 

1. Siga as etapas para criar uma segunda VM denominada *MyVM2*, com um endereço de **IP público** do *MyVM2-ip* e todas as outras configurações iguais a MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Criar regras NSG para as VMs

Nesta seção, você criará um NSG (grupo de segurança de rede) para as VMs para permitir conexões de entrada de HTTP (Internet) e RDP (área de trabalho remota).

1. Selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione **MyNetworkSecurityGroup** no grupo de recursos **MyResourceGroupLB**.
   
1. Em **Configurações**, selecione **Regras de segurança de entrada** e, em seguida, **Adicionar**.
   
1. Na caixa de diálogo **Adicionar regra de segurança de entrada**, insira ou selecione o seguinte para a regra de HTTP:
   
   - **Fonte**: selecione **Marca de Serviço**.  
   - **Marca de serviço de origem**: selecione **Internet**. 
   - **Intervalos de portas de destino**: digite *80*.
   - **Protocolo**: selecione **TCP**. 
   - **Ação**: selecione **Permitir**.  
   - **Prioridade**: digite *100*. 
   - **Nome**: digite *MyHTTPRule*. 
   - **Descrição**: digite *Permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra do NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Repita as etapas para a regra de RDP de entrada, com diferentes valores a seguir:
   - **Intervalos de portas de destino**: digite *3389*.
   - **Prioridade**: digite *200*. 
   - **Nome**: digite *MyRDPRule*. 
   - **Descrição**: digite *Permitir RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Criar recursos para o balanceador de carga

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end, uma investigação de integridade e uma regra de balanceamento.

### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as VMs, o balanceador de carga usa um pool de endereços de back-end. O pool de endereços de back-end contém os endereços IP das interfaces de rede virtual (NICs) conectadas ao balanceador de carga. 

**Para criar um pool de endereços de back-end que inclui VM1 e VM2:**

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar um pool de back-end**, insira ou selecione os seguintes valores:
   
   - **Nome**: digite *MyBackEndPool*.
   - **Associado a**: Abra a lista suspensa e selecione **Conjunto de disponibilidade**.
   - **Conjunto de disponibilidade**: selecione **MyAvailabilitySet**.
   
1. Selecione **Adicionar uma configuração de IP de rede de destino**. 
   1. Adicione cada máquina virtual (**MyVM1** e **MyVM2**) que você criou para o pool de back-end.
   2. Depois de adicionar cada máquina, abra a lista suspensa e selecione suas **Configurações de IP de rede**. 
   
1. Selecione **OK**.
   
   ![Adicionar o pool de endereços de back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Na página de **Pools de back-end**, expanda **MyBackendPool** e verifique se as opções **VM1** e **VM2** estão listadas.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status da VM, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. 

**Para criar uma investigação de integridade e monitorar a integridade das VMs:**

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar uma investigação de integridade**, insira ou selecione os seguintes valores:
   
   - **Nome**: insira *MyHealthProbe*.
   - **Protocolo**: abra a lista suspensa e selecione **HTTP**. 
   - **Porta**: digite *80*. 
   - **Caminho**: aceite */* como o URI padrão. Você pode substituir esse valor por qualquer outro URI. 
   - **Intervalo**: digite *15*. O intervalo é o número de segundos entre as tentativas de investigação.
   - **Limite não íntegro**: digite *2*. Esse valor é a quantidade de falhas de investigação consecutivas que ocorrem antes que uma VM seja considerada não íntegra.
   
1. Selecione **OK**.
   
   ![Adicionar uma investigação](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balanceador de carga define como o tráfego é distribuído para as VMs. A regra define a configuração de IP de front-end para o tráfego de entrada, o pool de IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de balanceador de carga chamada **MyLoadBalancerRule** escuta a porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede ao pool de endereços de back-end **MyBackEndPool**, também na porta 80. 

**Para criar a regra de balanceador de carga:**


1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar regra de balanceamento de carga**, insira ou selecione os seguintes valores:
   
   - **Nome**: digite *MyLoadBalancerRule*.
   - **Endereço IP de front-end:** digite *LoadBalancerFrontend*.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: digite *80*.
   - **Porta de back-end**: digite *80*.
   - **Pool de back-end**: selecione **MyBackendPool**.
   - **Investigação de integridade**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra do balanceador de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Você usará o endereço IP público para testar o balanceador de carga nas VMs. 

No portal, na página **Visão geral** do **MyLoadBalancer**, localize seu endereço IP público em **Endereço IP público**. Passe o mouse sobre o endereço e selecione o ícone **Copiar** para copiá-lo. 

### <a name="install-iis-on-the-vms"></a>Instalar o IIS nas VMs

Instale o IIS (Serviços de Informações da Internet) nas máquinas virtuais para ajudar a testar o balanceador de carga.

**Para a RDP (área de trabalho remota) na VM:**

1. No portal, selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione **MyVM1** no grupo de recursos **MyResourceGroupLB**.
   
1. Na página **Visão geral**, selecione **Conectar**, depois selecione **Baixar arquivo RDP**. 
   
1. Abra o arquivo RDP baixado e selecione **Conectar**.
   
1. Na tela Segurança do Windows, selecione **Mais opções** e depois **Usar uma conta diferente**. 
   
   Insira o nome de usuário e a senha e selecione **OK**.
   
1. Responda **Sim** a qualquer solicitação de certificado. 
   
   A área de trabalho da VM é aberta em uma nova janela. 
   
**Para instalar o IIS**

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, na lista de recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB*.
2. Na página **Visão Geral**, selecione **Conectar** para habilitar o RDP na VM.
5. Faça logon na VM com as credenciais que você forneceu durante a criação dessa VM. Isso inicia uma sessão de área de trabalho remota com a máquina virtual - *myVM1*.
6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
7. Na janela do PowerShell, execute os comandos a seguir para instalar o servidor IIS, remova o arquivo padrão iisstart.htm, e adicione um novo arquivo iisstart.htm que exibe o nome da VM:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Feche a sessão RDP com *myVM1*.
7. Repita as etapas de 1 a 6 para instalar o IIS e o arquivo iisstart.htm atualizado em *myVM2*.
   
1. Repita as etapas da máquina virtual **MyVM2**, mas configure o servidor de destino para **MyVM2**.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Abra um navegador e cole o endereço IP público do balanceador de carga na barra de endereços do navegador. A página padrão do servidor Web do IIS deve aparecer no navegador.

![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre ambas as VMs que executam o aplicativo, você poderá forçar a atualização do navegador da Web.
## <a name="clean-up-resources"></a>Limpar recursos

Para excluir o balanceador de carga e todos os recursos relacionados quando não precisar mais deles, abra o grupo de recursos **MyResourceGroupLB** e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um balanceador de carga de camada básica. Você criou e configurou um grupo de recursos, recursos de rede, servidores de back-end, uma investigação de integridade e as regras para usar com o balanceador de carga. Você instalou o IIS nas VMs e o usou para testar o balanceador de carga. 

Para saber mais sobre o Azure Load Balancer, progrida até os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
