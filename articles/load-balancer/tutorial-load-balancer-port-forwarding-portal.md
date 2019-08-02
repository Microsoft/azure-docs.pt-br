---
title: 'Tutorial: configurar o encaminhamento de porta no Azure Load Balancer usando o portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra como configurar o encaminhamento de porta usando o Azure Load Balancer para criar conexões às VMs em uma rede virtual do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: ee4ed818364d04f03caedc8b876ea29c41cb59b7
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273441"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Tutorial: configurar o encaminhamento de porta no Azure Load Balancer usando o portal

O encaminhamento de porta permite conectar-se a VMS (máquinas virtuais) em uma rede virtual do Azure usando um endereço IP público do Azure Load Balancer e o número da porta. 

Neste tutorial, você configura o encaminhamento de porta no Azure Load Balancer. Você aprenderá como:

> [!div class="checklist"]
> * Criar um Standard Load Balancer público para balancear o tráfego de rede em VMs. 
> * Criar uma rede virtual e VMs com uma regra de NSG (Grupo de Segurança de Rede). 
> * Adicionar as VMs ao pool de endereços de back-end do balanceador de carga.
> * Criar uma investigação de integridade do balanceador de carga e regras de tráfego.
> * Criar regras de encaminhamento de porta NAT de entrada do balanceador de carga.
> * Instalar e configurar os IIS nas VMs para exibir o balanceamento de carga e o encaminhamento de porta em ação.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para todas as etapas neste tutorial, entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Standard Load Balancer

Primeiro, crie um Standard Load Balancer público que pode balancear a carga do tráfego nas VMs. O Standard Load Balancer dá suporte somente a um endereço IP público padrão. Ao criar um Standard Load Balancer, você também cria um novo endereço IP público padrão configurado como o front-end (nomeado como **LoadBalancerFrontEnd** por padrão) do balanceador de carga. 

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na guia **Noções Básicas** da página **Criar balanceador de carga**, insira ou selecione as seguintes informações, aceite os padrões para as configurações restantes e selecione **Revisar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione sua assinatura.    |    
    | Resource group         | Selecione **Criar** e digite *MyResourceGroupLB* na caixa de texto.|
    | NOME                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **Público**.                                        |
    | SKU           | Selecione **Padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **Com redundância de zona**.    |
     
    >[!NOTE]
     >Certifique-se de criar o Load Balancer e todos os recursos para ele em uma localização que dá suporte às Zonas de Disponibilidade. Para mais informações, confira [Regiões que dão suporte às Zonas de Disponibilidade](../availability-zones/az-overview.md#services-support-by-region). 

3. Na guia **Revisar + criar**, clique em **Criar**.  
  
## <a name="create-and-configure-back-end-servers"></a>Criar e configurar servidores back-end

Crie uma rede virtual com duas máquinas virtuais e adicione as VMs ao pool de back-end do balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Rede virtual**.
   
1. No painel **Criar rede virtual**, insira ou selecione estes valores:
   
   - **Nome**: digite *MyVNet*.
   - **ResourceGroup**: abra a lista suspensa **Selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **Nome**: digite *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Crie VMs e adicione-as ao pool de back-end do balanceador de carga

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**. 
   
1. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:
   - **Assinatura** > **Grupo de Recursos**: abra a lista suspensa e selecione **MyResourceGroupLB**.
   - **Nome da máquina virtual**: digite *MyVM1*.
   - **Região**: Selecione **Europa Ocidental**. 
   - **Nome de usuário**: digite *azureuser*.
   - **Senha**: digite *Azure1234567*. 
     Digite a senha novamente no campo **Confirmar senha**.
   
1. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**. 
   
   Selecione os itens abaixo:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
1. Em **IP público**, selecione **Criar novo**, selecione **Padrão** na página **Criar endereço IP púbico** e selecione **OK**. 
   
1. Em **Grupo de segurança de rede**, selecione **Avançado** para criar um novo NSG (grupo de segurança de rede), um tipo de firewall. 
   1. No campo **Configurar grupo de segurança de rede**, selecione **Criar novo**. 
   1. Insira *MyNetworkSecurityGroup* e selecione **OK**. 
   
   >[!NOTE]
   >Observe que, por padrão, o NSG já tem uma regra de entrada para abrir a porta 3389, a RDP (porta da área de trabalho remota).
   
1. Adicione a VM a um pool de back-end do balanceador de carga que você criar:
   
   1. Em **BALANCEAMENTO DE CARGA** > **Colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente?** , selecione **Sim**. 
   1. Para **Opções de balanceamento de carga**, abra a lista suspensa e selecione **Azure Load Balancer**. 
   1. Para **Selecionar um balanceador de carga**, abra a lista suspensa e selecione **MyLoadBalancer**. 
   1. Em **Selecionar um pool de back-end**, selecione **Criar novo**, em seguida digite *MyBackendPool*e selecione **Criar**. 
   
   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecione a guia **Gerenciamento** ou selecione **Avançar** > **Gerenciamento**. Em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.
   
1. Selecione **Examinar + criar**.
   
1. Examine as configurações e, quando a validação for bem-sucedida, selecione **Criar**. 

1. Siga as etapas para criar uma segunda VM denominada *MyVM2*, com todas as outras configurações iguais às da MyVM1. 
   
   Para **Grupo de Segurança de Rede**, depois de selecionar **Avançado**, abra a lista suspensa e selecione o **MyNetworkSecurityGroup** que você já criou. 
   
   Em **Selecionar um pool de back-end**, verifique se **MyBackendPool** está selecionado. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Criar uma regra do NSG para as VMs

Crie uma regra do NSG (Grupo de Segurança de Rede) para as VMs para permitir conexões de entrada da Internet (HTTP).

>[!NOTE]
>Observe que, por padrão, o NSG já tem uma regra que abre a porta 3389, a RDP (porta da área de trabalho remota).

1. Selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione **MyNetworkSecurityGroup** no grupo de recursos **MyResourceGroupLB**.
   
1. Em **Configurações**, selecione **Regras de segurança de entrada** e, em seguida, **Adicionar**.
   
1. Na caixa de diálogo **Adicionar regra de segurança de entrada**, insira ou selecione o seguinte:
   
   - **Fonte**: selecione **Marca de Serviço**.  
   - **Marca de serviço de origem**: selecione **Internet**. 
   - **Intervalos de portas de destino**: digite *80*.
   - **Protocolo**: selecione **TCP**. 
   - **Ação**: selecione **Permitir**.  
   - **Prioridade**: digite *100*. 
   - **Nome**: digite *MyHTTPRule*. 
   - **Descrição**: digite *Permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra do NSG](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você deve inspecionar o pool de back-end do balanceador de carga e configurar uma investigação de integridade do balanceador de carga e regras de tráfego.

### <a name="view-the-back-end-address-pool"></a>Exibir o pool de endereços de back-end

Para distribuir o tráfego para as VMs, o balanceador de carga usa um pool de endereços de back-end, que contém os endereços IP dos NICs (adaptadores de rede virtuais) conectados ao balanceador de carga. 

Você criou seu pool de back-end do balanceador de carga e adicionou VMs a ele quando criou as VMs. Você também pode criar pools de back-end e adicionar ou remover VMs da página **Pools de back-end** do balanceador de carga. 

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Pools de back-end**.
   
1. Na página de **Pools de back-end**, expanda **MyBackendPool** e verifique se as opções **VM1** e **VM2** estão listadas.

1. Selecione **MyBackendPool**. 
   
   Na página **MyBackendPool**, em **MÁQUINA VIRTUAL** e **ENDEREÇO IP**, você pode remover ou adicionar VMs disponíveis ao pool.

Você pode criar novos pools de back-end selecionando **Adicionar** na página **Pools de back-end**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status da VM, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. 

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar investigação de integridade**, digite ou selecione os seguintes valores:
   
   - **Nome**: insira *MyHealthProbe*.
   - **Protocolo**: abra a lista suspensa e selecione **HTTP**. 
   - **Porta**: digite *80*. 
   - **Caminho**: aceite */* como o URI padrão. Você pode substituir esse valor por qualquer outro URI. 
   - **Intervalo**: digite *15*. O intervalo é o número de segundos entre as tentativas de investigação.
   - **Limite não íntegro**: digite *2*. Esse valor é a quantidade de falhas de investigação consecutivas que ocorrem antes que uma VM seja considerada não íntegra.
   
1. Selecione **OK**.
   
   ![Adicionar uma investigação](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balanceador de carga define como o tráfego é distribuído para as VMs. A regra define a configuração de IP de front-end para o tráfego de entrada, o pool de IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de balanceador de carga chamada **MyLoadBalancerRule** escuta a porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede ao pool de endereços de back-end **MyBackendPool**, também na porta 80. 

1. Selecione **Todos os recursos** no menu à esquerda e **myLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
   
1. Na página **Adicionar regra de balanceamento de carga**, insira ou selecione os seguintes valores:
   
   - **Nome**: digite *MyLoadBalancerRule*.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: digite *80*.
   - **Porta de back-end**: digite *80*.
   - **Pool de back-end**: selecione **MyBackendPool**.
   - **Investigação de integridade**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra do balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Criar uma regra de encaminhamento de porta NAT de entrada

Crie uma regra NAT (conversão de endereços de rede) de entrada do balanceador de carga para encaminhar o tráfego de uma porta específica do endereço IP de front-end para uma porta específica de uma VM de back-end.

1. Selecione **Todos os recursos** no menu à esquerda e **MyLoadBalancer** na lista de recursos.
   
1. Em **Configurações**, selecione **Regras de NAT de entrada** e, em seguida, **Adicionar**. 
   
1. Na página **Adicionar regra NAT de entrada**, digite ou selecione os seguintes valores:
   
   - **Nome**: digite *MyNATRuleVM1*.
   - **Porta**: digite *4221*.
   - **Máquina virtual de destino**: selecione **MyVM1** na lista suspensa.
   - **Mapeamento de porta**: selecione **Personalizado**.
   - **Porta de destino**: digite *3389*.
   
1. Selecione **OK**.
   
1. Repita as etapas para adicionar uma regra NAT de entrada chamada *MyNATRuleVM2* usando a **Porta**: *4222* e a **Máquina virtual de destino**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta seção, você instalará os IIS (Serviços de Informações da Internet) em servidores de back-end e personalizará a página da Web padrão para mostrar o nome do computador. Você usará o endereço IP público do balanceador de carga para testar o balanceador de carga. 

Cada VM de back-end serve uma versão diferente da página da web padrão do IIS, ou seja, você pode ver o balanceador de carga distribuir solicitações entre as duas VMs.

### <a name="connect-to-the-vms-with-rdp"></a>Conectar-se às VMs com RDP

Conecte-se a cada VM com a RDP (Área de Trabalho Remota). 

1. No portal, selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB**.
   
1. Na página **Visão geral**, selecione **Conectar** e selecione **Baixar arquivo RDP**. 
   
1. Abra o arquivo RDP baixado e selecione **Conectar**.
   
1. Na tela Segurança do Windows, selecione **Mais opções** e depois **Usar uma conta diferente**. 
   
   Insira o nome de usuário *azureuser* e a senha *Azure1234567* e selecione **OK**.
   
1. Responda **Sim** a qualquer solicitação de certificado. 
   
   A área de trabalho da VM é aberta em uma nova janela. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instale os IIS e substitua a página da Web padrão dos IIS 

Use o PowerShell para instalar os IIS e substitua a página da Web dos IIS padrão por uma página que exibe o nome da VM.

1. Em MyVM1 e MyVM2, inicie o **Windows PowerShell** no menu **Iniciar**. 

2. Execute os seguintes comandos para instalar o IIS e substituir a página da Web do IIS padrão:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Feche as conexões RDP com MyVM1 e MyVM2 selecionando **Desconectar**. Não desligue as VMs.

### <a name="test-load-balancing"></a>Testar o balanceamento de carga

1. No portal, na página **Visão geral** do **MyLoadBalancer**, copie o endereço IP público em **Endereço IP público**. Passe o mouse sobre o endereço e selecione o ícone **Copiar** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Cole ou digite o endereço IP público do balanceador de carga (*40.67.218.235*) na barra de endereços do navegador da Internet. 
   
   A página padrão do servidor Web do IIS personalizada é exibida no navegador. A mensagem lê **Olá, Mundo da MyVM1** ou **Olá, Mundo da MyVM2**.
   
   ![Nova página padrão do IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Atualize o navegador para ver o balanceador de carga distribuir tráfego entre VMs. Às vezes, a página de **MyVM1** é exibida e, outras vezes, é a página de **MyVM2**, já que o balanceador de carga distribui as solicitações entre as VM de back-end.
   
   >[!NOTE]
   >Você talvez precise limpar o cache do navegador ou abrir uma nova janela de navegador entre as tentativas.

## <a name="test-port-forwarding"></a>Testar o encaminhamento de porta

Com o encaminhamento de porta, você pode usar a área de trabalho remota em uma VM de back-end usando o endereço IP do balanceador de carga e o valor de porta de front-end definido na regra NAT. 

1. No portal, na página **Visão geral** do **MyLoadBalancer**, copie o endereço IP público. Passe o mouse sobre o endereço e selecione o ícone **Copiar** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Abra um prompt de comando e use o seguinte comando para criar uma sessão da área de trabalho remota com MyVM2, usando o endereço IP público do balanceador de carga e a porta de front-end que você definiu na regra NAT da VM. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Abra o arquivo RDP baixado e selecione **Conectar**.
   
1. Na tela Segurança do Windows, selecione **Mais opções** e depois **Usar uma conta diferente**. 
   
   Insira o nome de usuário *azureuser* e a senha *Azure1234567* e selecione **OK**.
   
1. Responda **Sim** a qualquer solicitação de certificado. 
   
   A área de trabalho de MyVM2 é aberta em uma nova janela. 

A conexão RDP é feita com êxito porque a regra NAT de entrada **MyNATRuleVM2** direciona o tráfego da porta de front-end do balanceador de carga 4222 à porta do MyVM2 3389 (a porta RDP).

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir o balanceador de carga e todos os recursos relacionados quando não precisar mais deles, abra o grupo de recursos **MyResourceGroupLB** e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um balanceador de carga público Standard. Você criou e configurou recursos de rede, servidores de back-end, uma investigação de integridade e regras para o balanceador de carga. Você instalou os IIS nas VMs de back-end e usou o endereço IP público do balanceador de carga para testar o balanceador de carga. Você configurou e testou o encaminhamento de porta de uma determinada porta no balanceador de carga para uma porta em uma VM de back-end. 

Para saber mais sobre o Azure Load Balancer, continue para ver mais tutoriais sobre o balanceador de carga.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
