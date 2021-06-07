---
title: Configurar um ponto de extremidade do ILB (balanceador de carga interno)
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como configurar o gateway de aplicativo com um endereço IP de front-end privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711647"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurar um gateway de aplicativo com um ponto de extremidade de ILB (balanceador de carga interno)

Aplicativo Azure gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno que não está exposto à Internet. Um ponto de extremidade interno usa um endereço IP privado para o front-end, que também é conhecido como um *ponto de extremidade ILB (balanceador de carga interno)*.

Configurar o gateway usando um endereço IP privado de front-end é útil para aplicativos de linha de negócios internos que não são expostos à Internet. Ele também é útil para serviços e camadas em um aplicativo de várias camadas que está em um limite de segurança que não é exposto à Internet, mas:

- ainda requer a distribuição de carga Round Robin
- adesão de sessão
- ou rescisão de TLS (segurança de camada de transporte) (anteriormente conhecido como protocolo SSL (SSL)).

Este artigo orienta você pelas etapas para configurar um gateway de aplicativo com um endereço IP privado de front-end usando o portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Microsoft Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Crie uma nova rede virtual ou use uma existente. 

Neste exemplo, você cria uma nova rede virtual. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Há duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

1. Expanda o menu do portal e selecione **criar um recurso**.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Para **região**, selecione **EUA Central**.
5. Para **camada**, selecione **padrão**.
6. Em **Configurar rede virtual** , selecione **criar novo** e, em seguida, insira esses valores para a rede virtual:
   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
   - *myBackendSubnet* – para o nome da sub-rede de back-end.
   - *10.0.1.0/24* -para o espaço de endereço de sub-rede de back-end.

    ![Criar rede virtual](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecione **OK** para criar a rede virtual e sub-redes.
7. Selecione **Avançar: front-ends**.
8. Para **tipo de endereço IP de front-end**, selecione **privado**.

   Por padrão, é uma atribuição de endereço IP dinâmico. O primeiro endereço disponível da sub-rede configurada é atribuído como o endereço IP de front-end.
   > [!NOTE]
   > Uma vez alocado, o tipo de endereço IP (estático ou dinâmico) não pode ser alterado posteriormente.
9. Selecione **Avançar: back-ends**.
10. Selecione **Adicionar um pool de back-end**.
11. Para **nome**, digite *appGatewayBackendPool*.
12. Para **Adicionar pool de back-end sem destinos**, selecione **Sim**. Você adicionará os destinos mais tarde.
13. Selecione **Adicionar**.
14. Selecione **Avançar: configuração**.
15. Em **regras de roteamento**, selecione **Adicionar uma regra de roteamento**.
16. Para **nome da regra**, digite *Rrule-01*.
17. Para **nome do ouvinte**, digite *Listener-01*.
18. Para **IP de front-end**, selecione **privado**.
19. Aceite os padrões restantes e selecione a guia **destinos de back-end** .
20. Para **tipo de destino**, selecione **pool de back-end** e, em seguida, selecione **appGatewayBackendPool**.
21. Para **configuração de http**, selecione **Adicionar novo**.
22. Para **nome da configuração http**, digite *http-Setting-01*.
23. Para **protocolo de back-end**, selecione **http**.
24. Para **porta de back-end**, digite *80*.
25. Aceite os padrões restantes e selecione **Adicionar**.
26. Na página **Adicionar uma regra de roteamento** , selecione **Adicionar**.
27. Selecione **Avançar: Marcas**.
28. Selecione **Avançar: Revisar + criar**.
29. Examine as configurações na página Resumo e, em seguida, selecione **criar** para criar os recursos de rede e o gateway de aplicativo. Pode levar vários minutos para criar o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-pool"></a>Adicionar pool de back-end

O pool de back-end é usado para encaminhar solicitações aos servidores back-end que atendem à solicitação. O back-end pode ser composto por NICs, conjuntos de dimensionamento de máquinas virtuais, endereços IP públicos, endereços IP internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você usa máquinas virtuais como o back-end de destino. Você pode criar máquinas virtuais ou usar as existentes. Neste exemplo, você cria duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo.

Para fazer isto:

1. Crie duas novas máquinas virtuais, *myVM* e *myVM2*, usadas como servidores de back-end.
2. Instale IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual


1. Selecione **Criar um recurso**.
2. Selecione **Computação** e **Máquina virtual**.
4. Insira esses valores para a máquina virtual:
   - Selecione sua assinatura.
   - Selecione *myResourceGroupAG* para o **grupo de recursos**.
   - Digite *myVM* para o **nome da máquina virtual**.
   - Selecione **Windows Server 2019 datacenter** para **imagem**.
   - Digite um **nome de usuário** válido.
   - Digite uma **senha** válida.
1. Aceite os padrões restantes e selecione **Avançar: discos**.
1. Aceite os padrões e selecione **Avançar: rede**.
1. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**.
1. Aceite os padrões restantes e selecione **Avançar: gerenciamento**.
1. Selecione **Desabilitar** para desabilitar o diagnóstico de inicialização.
1. Selecione **Examinar + criar**.
1. Examine as configurações na página de resumo e, em seguida, selecione **Criar**. Pode levar vários minutos para criar a VM. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o Cloud Shell e verifique se ele está definido como **PowerShell**.
    ![Captura de tela mostra uma janela aberta Azure Cloud Shell console que usa o PowerShell.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Execute o comando a seguir para instalar o IIS na máquina virtual:

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você acabou de concluir. Use myVM2 para o nome da máquina virtual e para o `VMName` no `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores back-end ao pool de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.
2. Selecione **pools de back-end** e, em seguida, selecione **appGatewayBackendPool**.
3. Em **tipo de destino** , selecione **máquina virtual**  e, em **destino**, selecione o vNIC associado a myVM.
4. Repita para adicionar MyVM2.
   ![Editar o painel do pool de back-end com tipos de destino e destinos realçados.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Selecione **Salvar**.

## <a name="create-a-client-virtual-machine"></a>Criar uma máquina virtual do cliente

A máquina virtual do cliente é usada para se conectar ao pool de back-end do gateway de aplicativo.

- Crie uma terceira máquina virtual usando as etapas anteriores. Use myVM3 para o nome da máquina virtual.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Na página myAppGateway, selecione **configurações de IP de front-end** para observar o endereço IP privado de front-end.
    ![Painel de configurações de IP de front-end com o tipo privado realçado.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copie o endereço IP privado e cole-o na barra de endereços do navegador no myVM3 para acessar o pool de back-end do gateway de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você quiser monitorar a integridade do pool de back-end, consulte [logs de diagnóstico e de integridade de back-end para o gateway de aplicativo](application-gateway-diagnostics.md).
