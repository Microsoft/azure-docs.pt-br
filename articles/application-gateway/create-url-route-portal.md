---
title: 'Tutorial: Regras de roteamento com base no caminho da URL usando o portal – Gateway de Aplicativo do Azure'
description: Neste tutorial, você aprende como criar regras de roteamento baseadas em caminhos de URL para um gateway de aplicativo e conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708944"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar um gateway de aplicativo com regras de roteamentos com base no caminho usando o portal do Azure

Você pode usar o portal do Azure para configurar [regras de roteamento baseadas em caminhos de URL](./url-route-overview.md) quando você cria um [gateway de aplicativo](./overview.md). Neste tutorial, você criará pools de back-end usando máquinas virtuais. Em seguida, você criará regras de roteamento para certificar-se de que o tráfego da Web chega aos servidores apropriados nos pools.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar um Gateway de Aplicativo
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar um ouvinte de back-end
> * Criar uma regra de roteamento baseada em caminho

![Exemplo de roteamento de URL](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, você criará três máquinas virtuais a serem usadas como servidores de back-end para o gateway de aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o gateway de aplicativo funciona conforme esperado.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Windows Server 2016 Datacenter** na lista Popular.
3. Insira esses valores para a máquina virtual:

    - **Assinatura** – selecione sua assinatura.
    - **Grupo de recursos**, selecione **Criar novo** e, em seguida, digite *myResourceGroupAG*.
    - **Nome da máquina virtual**: *myVM1*
    - **Região**: *EUA (Leste dos EUA)*
    - **Nome de usuário**: digite um nome de usuário
    - **Senha**: digite uma senha


4. Selecione **Avançar:Discos**.
5. Selecione **Avançar:Rede**
6. Em **Rede virtual**, selecione **Criar novo** e insira estes valores para a rede virtual:

   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myBackendSubnet* – para o nome da primeira sub-rede
   - *10.0.1.0/24* - para o espaço de endereço da sub-rede.
   - *myAGSubnet* – para o nome da segunda sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
7. Selecione **OK**.

8. Verifique se, em **Sub-rede**, **myBackendSubnet** está selecionado para a sub-rede e selecione **Avançar: Gerenciamento**.
9. Selecione **Desabilitar** para desabilitar o diagnóstico de inicialização.
10. Selecione **Examinar + Criar**, examine as configurações na página de resumo e selecione **Criar**.
11. Crie mais duas máquinas virtuais, *myVM2* e *myVM3*, e coloque-as na rede virtual *MyVNet* e na sub-rede *myBackendSubnet*.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual: 

    ```azurepowershell
         $publicSettings = @{ "fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Instale o IIS nas outras máquinas virtuais usando as etapas que você acabou de concluir. Use *myVM2* e *myVM3* para os valores de VMName em Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

1. Selecione **Criar um recurso** no menu esquerdo do portal do Azure. A janela **Novo** é exibida.

2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista **em destaque**.

### <a name="basics-tab"></a>Guia Básico

1. Na página **Básico**, insira estes valores para as seguintes configurações de gateway de aplicativo:

   - **Assinatura**: Selecione sua assinatura.
   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos.
   - **Nome do gateway de aplicativo**: digite *myAppGateway* para o nome do gateway de aplicativo.
   - **Região** – selecione **EUA (Leste dos EUA)** .

        ![Criar gateway de aplicativo: Noções básicas](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Em **Configurar rede virtual**, selecione **myVNet** como o nome da rede virtual.
3. Selecione **myAGSubnet** como a sub-rede.
3. Aceite os valores padrão para as outras configurações e selecione **Avançar: Front-ends**.

### <a name="frontends-tab"></a>Guia Front-ends

1. Na guia **Front-ends**, verifique se **Tipo de endereço IP do front-end** está definido como **Público**.

   > [!NOTE]
   > Para a SKU do Gateway de Aplicativo v2, você só pode escolher a configuração de IP de front-end **Pública**. A configuração de IP de front-end privado não está habilitada para esta SKU v2.

2. Selecione **Adicionar novo** para o **Endereço IP público**, insira *myAGPublicIPAddress* para o nome do endereço IP público e clique em **OK**. 
3. Selecione **Avançar: Back-ends**.

### <a name="backends-tab"></a>Guia Back-ends

O pool de back-end é usado para encaminhar solicitações aos servidores back-end que atendem à solicitação. Os pools de back-end podem ser formados por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure.

1. Na guia **Back-ends**, selecione **Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Name**: Insira *myBackendPool* para o nome do pool de back-end.
3. Em **Tipo de destino**, selecione **Máquina virtual** na lista suspensa.

5. Em **Destino**, selecione o adaptador de rede para **myVM1**.
6. Selecione **Adicionar**.
7. Repita para adicionar um pool de back-end de *Imagens* com *myVM2* como destino e um pool de back-end de *Vídeo* com *myVM3* como destino.
8. Selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **Back-ends**.

4. Na guia **Back-ends**, selecione **Avançar: Configuração**.

### <a name="configuration-tab"></a>Guia Configuração

Na guia **Configuração**, você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Selecione **Adicionar uma regra de roteamento** na coluna **Regra de roteamento**.

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *myRoutingRule* para o **Nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **Ouvinte** na janela **Adicionar uma regra de roteamento**, digite os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: insira *myListener* para o nome do ouvinte.
    - **IP de front-end**: selecione **Público** para escolher o IP público que você criou para o front-end.
    - **Porta**: digite *8080*
  
        Aceite os valores padrão para as outras configurações na guia **Ouvinte** e, em seguida, selecione a guia **Destinos de back-end** para configurar o restante da regra de roteamento.

4. Na guia **Destinos de back-end**, selecione **myBackendPool** para o **Destino de back-end**.

5. Para a **Configuração de HTTP**, selecione **Adicionar novo** para criar uma configuração de HTTP. A configuração HTTP determinará o comportamento da regra de roteamento. 

6. Na janela **Adicionar uma configuração de HTTP** que é aberta, insira *myHTTPSetting* para o **Nome da configuração de HTTP**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de HTTP** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento**.
7. Em **Roteamento com base em caminho**, selecione **Adicionar vários destinos para criar uma regra baseada em caminho**.
8. Para **Caminhos**, digite */images/* \*.
9. Para **Nome de destino**, digite *Imagens*.
10. Para **Configuração de HTTP**, selecione **myHTTPSetting**
11. Para **Destino de back-end**, selecione **Imagens**.
12. Selecione **Adicione** para salvar a regra de caminho e volte para a guia **Adicionar uma regra de roteamento**.
13. Repita para adicionar outra regra para Vídeo.
14. Selecione **Adicionar** para adicionar a regra de roteamento e volte para a guia **Configuração**.
15. Selecione **Avançar: Marcas** e, em seguida, **Avançar: Revisar + criar**.

> [!NOTE]
> Você não precisa adicionar uma regra de caminho */* * personalizada para lidar com casos padrão. Isso é manipulado automaticamente pelo pool de back-end padrão.

### <a name="review--create-tab"></a>Guia Examinar + criar

Examine as configurações na guia **Examinar + criar** e selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.


## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

    ![Registrar o endereço IP público do gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Como http:\//52.188.72.175:8080.

    ![Testar a URL de base no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   O ouvinte na porta 8080 encaminha essa solicitação para o pool de back-end padrão.

3. Altere a URL para *http://&lt;ip-address&gt;:8080/images/test.htm*, substituindo &lt;ip-address&gt; pelo seu endereço IP e você verá algo parecido com o exemplo a seguir:

    ![Testar a URL de imagens no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   O ouvinte na porta 8080 encaminha esta solicitação para o pool de back-end de *Imagens*.

4. Altere a URL para *http://&lt;ip-address&gt;:8080/video/test.htm*, substituindo &lt;ip-address&gt; pelo seu endereço IP e você verá algo parecido com o exemplo a seguir:

    ![Testar a URL de vídeo no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   O ouvinte na porta 8080 encaminha esta solicitação para o pool de back-end de *Vídeos*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos e selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar o TLS de ponta a ponta no Gateway de Aplicativo do Azure](./ssl-overview.md)