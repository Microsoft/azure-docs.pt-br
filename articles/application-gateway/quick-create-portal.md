---
title: 'Início Rápido: Direcionar o tráfego da Web usando o portal'
titleSuffix: Azure Application Gateway
description: Neste guia de início rápido, você aprenderá como usar o portal do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 92e966f5a9c4a21a6a55862ad582f0238802720f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538578"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure

Neste início rápido, você usará o portal do Azure para criar um gateway de aplicativo. Em seguida, você o testará para verificar se ele funciona corretamente. 

O gateway de aplicativo direciona o tráfego da Web do aplicativo para recursos específicos em um pool de back-end. Você atribuirá ouvintes a portas, criará regras e adicionará recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um só site no gateway de aplicativo, uma regra de roteamento de solicitação básica e duas máquinas virtuais usadas no pool de back-end.

Conclua também este início rápido usando o [Azure PowerShell](quick-create-powershell.md) ou a [CLI do Azure](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Você criará o gateway de aplicativo usando as guias na página **Criar um gateway de aplicativo**.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. A janela **Novo** é exibida.

2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista **em destaque**.

### <a name="basics-tab"></a>Guia Básico

1. Na página **Básico**, insira estes valores para as seguintes configurações de gateway de aplicativo:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **Criar novo** para criá-lo.
   - **Nome do gateway de aplicativo**: Insira *myAppGateway* para o nome do gateway de aplicativo.

     ![Criar gateway de aplicativo: Noções básicas](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você criará uma rede virtual ao mesmo tempo em que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

    > [!NOTE]
    > [As políticas de ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) atualmente não têm suporte em uma sub-rede do Gateway de Aplicativo.

    Em **Configurar rede virtual**, crie uma rede virtual selecionando **Criar nova**. Na janela **Criar rede virtual** que é aberta, insira os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Name**: Insira *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (sub-rede do Gateway de Aplicativo): A grade **Sub-redes** mostrará uma sub-rede chamada *Padrão*. Altere o nome dessa sub-rede para *myAGSubnet*.<br>A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.

    - **Nome da sub-rede** (sub-rede do servidor de back-end): Na segunda linha da grade **Sub-redes**, insira *myBackendSubnet* na coluna **Nome da sub-rede**.

    - **Intervalo de endereços** (sub-rede do servidor de back-end): Na segunda linha da grade **Sub-redes**, insira um intervalo de endereços que não se sobreponha ao intervalo de endereços de *myAGSubnet*. Por exemplo, se o intervalo de endereços de *myAGSubnet* for 10.0.0.0/24, digite *10.0.1.0/24* para o intervalo de endereços de *myBackendSubnet*.

    Selecione **OK** para fechar a janela **Criar rede virtual** e salvar as configurações de rede virtual.

     ![Criar gateway de aplicativo: rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na guia **Básico**, aceite os valores padrão para as outras configurações e selecione **Avançar: Front-ends**.

### <a name="frontends-tab"></a>Guia Front-ends

1. Na guia **Front-ends**, verifique se **Tipo de endereço IP do front-end** está definido como **Público**. <br>É possível configurar o IP de front-end como Público ou Privado, de acordo com o caso de uso. Neste exemplo, você escolherá um IP público de front-end.
   > [!NOTE]
   > Para a SKU do Gateway de Aplicativo v2, deve haver uma configuração de IP de front-end **Pública**. Você ainda pode ter uma configuração de IP de front-end Pública e Privada, mas a configuração de IP de front-end somente Privada (somente modo ILB) não está habilitada no momento para a SKU v2. 

2. Selecione **Adicionar novo** para o **Endereço IP público**, insira *myAGPublicIPAddress* para o nome do endereço IP público e clique em **OK**. 

     ![Criar gateway de aplicativo: front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecione **Avançar: Back-ends**.

### <a name="backends-tab"></a>Guia Back-ends

O pool de back-end é usado para encaminhar solicitações aos servidores back-end que atendem à solicitação. Os pools de back-end pode ser formado por NICs, conjuntos de dimensionamento de máquinas virtuais, endereços IP públicos, endereços IP internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. Neste exemplo, você criará um pool de back-end vazio com o gateway de aplicativo e, em seguida, adicionará destinos de back-end ao pool de back-end.

1. Na guia **Back-ends**, selecione **Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Name**: Insira *myBackendPool* para o nome do pool de back-end.
    - **Adicionar pool de back-end sem destinos**: Selecione **Sim** para criar um pool de back-end sem destinos. Você adicionará destinos de back-end depois de criar o gateway de aplicativo.

3. Na janela **Adicionar um pool de back-end**, selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **Back-ends**.

     ![Criar gateway de aplicativo: back-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na guia **Back-ends**, selecione **Avançar: Configuração**.

### <a name="configuration-tab"></a>Guia Configuração

Na guia **Configuração**, você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Selecione **Adicionar uma regra de roteamento** na coluna **Regra de roteamento**.

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *myRoutingRule* para o **Nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **Ouvinte** na janela **Adicionar uma regra de roteamento**, insira os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: insira *myListener* para o nome do ouvinte.
    - **IP de front-end**: selecione **Público** para escolher o IP público que você criou para o front-end.
  
      Aceite os valores padrão para as outras configurações na guia **Ouvinte** e, em seguida, selecione a guia **Destinos de back-end** para configurar o restante da regra de roteamento.

   ![Criar gateway de aplicativo: ouvinte](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Na guia **Destinos de back-end**, selecione **myBackendPool** para o **Destino de back-end**.

5. Na **Configuração HTTP**, selecione **Adicionar novo** para adicionar uma nova configuração HTTP. A configuração HTTP determinará o comportamento da regra de roteamento. Na janela **Adicionar uma configuração de HTTP** que é aberta, insira *myHTTPSetting* para o **Nome da configuração de HTTP** e *80* para a **Porta de back-end**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de HTTP** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento**. 

     ![Criar gateway de aplicativo: Configuração de HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Na janela **Adicionar uma regra de roteamento**, selecione **Adicionar** para salvar a regra de roteamento e retornar para a guia **Configuração**.

     ![Criar gateway de aplicativo: regra de roteamento](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecione **Avançar: Marcas** e, em seguida, **Avançar: Revisar + criar**.

### <a name="review--create-tab"></a>Guia Examinar + criar

Examine as configurações na guia **Examinar + criar** e selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-targets"></a>Adicionar destinos de back-end

Neste exemplo, você usará máquinas virtuais como o back-end de destino. Você pode criar máquinas virtuais ou usar as existentes. Você criará duas máquinas virtuais como servidores de back-end para o gateway de aplicativo.

Para fazer isso, você precisará:

1. Criar duas novas VMS, *myVM* e *myVM2*, para serem usadas como servidores back-end.
2. Instale IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. A janela **Novo** é exibida.
2. Selecione **Windows Server 2016 Datacenter** na lista **Popular**. A página **Criar uma máquina virtual** é exibida.<br>O Gateway de Aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada no pool de back-end. Neste exemplo, você usa uma máquina virtual do Windows Server 2016 Datacenter.
3. Insira esses valores na guia **Noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Insira *myVM* para o nome da máquina virtual.
    - **Região**: Selecione a mesma região em que você criou o gateway de aplicativo.
    - **Nome de usuário**: digite um nome para o nome de usuário do administrador.
    - **Senha**: Digite uma senha.
    - **Porta de entrada públicas**: Nenhum.
4. Aceite os outros padrões e selecione **Próximo: Discos**.  
5. Aceite os padrões na guia **Discos** e selecione **Próximo: Rede**.
6. Na guia **Rede**, verifique se **myVNet** está selecionado para a **Rede virtual** e se a **Sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.<br>O Gateway de Aplicativo pode comunicar-se com instâncias fora da rede virtual em que está, mas é necessário garantir que há conectividade IP.
7. Na guia **Gerenciamento**, defina **Diagnóstico de inicialização** como **Desabilitar**. Aceite os outros padrões e selecione **Revisar + criar**.
8. Na guia **Revisar + criar**, examine as configurações, corrija os erros de validação e selecione **Criar**.
9. Aguarde a criação da máquina virtual concluir antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar IIS para teste

Neste exemplo, você instala o IIS nas máquinas virtuais para verificar se o Azure criou o gateway de aplicativo com êxito.

1. Abra o PowerShell do Azure.

   Selecione **Cloud Shell** na barra de navegação superior do portal do Azure e selecione **PowerShell** na lista suspensa. 

    ![Instalar a extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual. Altere o parâmetro *Local*, se necessário: 

    ```azurepowershell
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *myVM2* para o nome da máquina virtual e a configuração **VMName** do cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores back-end ao pool de back-end

1. No menu portal do Azure, selecione **Todos os recursos** ou pesquise e selecione *Todos os recursos*. Em seguida, selecione **myAppGateway**.

2. Selecione **Pools de back-end** no menu esquerdo.

3. Selecione **myBackendPool**.

4. Em **Destinos de Back-end**, **Tipo de destino**, selecione **Máquina virtual** na lista suspensa.

5. Em **Destino**, selecione as máquinas virtuais **myVM** e **myVM2** e suas interfaces de rede associadas nas listas suspensas.

   > [!div class="mx-imgBorder"]
   > ![Adicionar servidores de back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Clique em **Salvar**.

7. Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Embora o IIS não seja exigido para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. 

Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público do gateway de aplicativo na página **Visão geral**. ![Registre o endereço IP público do gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) ou selecione **Todos os recursos**, insira *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **Visão geral**.
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador para procurar esse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

   ![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Atualize o navegador várias vezes e você deverá ver conexões com myVM e myVM2.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, exclua o grupo de recursos. Ao excluir o grupo de recursos, você também remove o gateway de aplicativo e todos os recursos relacionados.

Para excluir o grupo de recursos:

1. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos*.
2. Na página **Grupos de recursos**, pesquise por **myResourceGroupAG** na lista e, em seguida, selecione.
3. Na página **Grupo de recursos**, selecione **Excluir grupo de recursos**.
4. Insira *myResourceGroupAG* em **DIGITAR O NOME DO GRUPO DE RECURSOS** e selecione **Excluir**

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Configurar um gateway de aplicativo com o encerramento de TLS usando o portal do Azure](create-ssl-portal.md)
