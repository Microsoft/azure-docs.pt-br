---
title: Redirecionamento de HTTP para HTTPS no portal-Aplicativo Azure gateway
description: Saiba como criar um gateway de aplicativo com o tráfego redirecionado de HTTP para HTTPS usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397273"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Criar um gateway de aplicativo com HTTP para redirecionamento HTTPS usando o portal do Azure

Você pode usar o portal do Azure para criar um [Gateway de aplicativo](overview.md) com um certificado para terminação TLS. Uma regra de roteamento é usada para redirecionar o tráfego HTTP para a porta HTTPS no gateway do seu aplicativo. Neste exemplo, você também pode criar um [conjunto de escala de máquina virtual](../virtual-machine-scale-sets/overview.md) para pool de back-end do gateway do aplicativo que contém duas instâncias de máquina virtual.

Neste artigo, você aprenderá como:

* Criará um certificado autoassinado
* Configurar uma rede
* Criar um gateway de aplicativo com o certificado
* Adicionar um ouvinte e uma regra de direcionamento
* Criar um conjunto de dimensionamento de máquinas virtuais com o pool de back-end padrão

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior para criar um certificado e instalar o IIS. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Para executar os comandos neste tutorial, você também precisa executar `Login-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-self-signed-certificate"></a>Criará um certificado autoassinado

Para uso em produção, você deve importar um certificado válido assinado por um fornecedor confiável. Para este tutorial, você cria um certificado autoassinado usando o [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Você pode usar o [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) com a impressão digital que foi retornada para exportar um arquivo pfx do certificado.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Você deverá ver algo parecido com este resultado:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Use a impressão digital para criar o arquivo pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a comunicação entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
3. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
4. Insira esses valores para o gateway de aplicativo:

   - *myAppGateway* - para o nome do gateway de aplicativo.
   - *myResourceGroupAG* - para o novo grupo de recursos.

     ![Criar novo gateway de aplicativo](./media/create-url-route-portal/application-gateway-create.png)

5. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
6. Clique em **Escolher uma rede virtual** , clique em **Criar novo** e insira esses valores para a rede virtual:

   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar rede virtual](./media/create-url-route-portal/application-gateway-vnet.png)

7. Clique em **OK** para criar a rede virtual e a sub-rede.
8. Em **Configuração de IP de Frontend** , selecione as opções **Tipo de endereço IP****Público** e **Criar novo**. Insira *myAGPublicIPAddress* como o nome. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
9. Em **Configuração do ouvinte** , selecione **HTTPS** ,escolha **Selecionar um arquivo** , navegue até o arquivo *c:\appgwcert.pfx* e selecione **Abrir**.
10. Insira *appgwcert* como o nome do certificado e *Azure123456!* para a senha.
11. Deixe o firewall do aplicativo Web desabilitado e selecione **OK**.
12. Examine as configurações na página de resumo e selecione **OK** para criar os recursos de rede e o gateway de aplicativo. A criação do gateway de aplicativo pode levar vários minutos, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **Todos os recursos** no menu esquerdo e selecione **myVNet** na lista de recursos.
2. Selecione **Sub-redes** e clique em **Sub-rede**.

    ![Criar sub-rede](./media/create-url-route-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* como o nome da sub-rede.
4. Insira *10.0.2.0/24* como o intervalo de endereços e selecione **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Adicionar um ouvinte e uma regra de direcionamento

### <a name="add-the-listener"></a>Adicionar o ouvinte

Primeiro, adicione o ouvinte denominado *myListener* à porta 80.

1. Abra o grupo de recursos **myResourceGroupAG** e selecione **myAppGateway**.
2. Selecione **Ouvintes** e selecione **+ Básico**.
3. Insira *MyListener* como o nome.
4. Insira *httpPort* como o novo nome de porta de front-end e *80* como a porta.
5. Verifique se o protocolo está definido como **HTTP** e selecione **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Adicionar uma regra de roteamento com uma configuração de redirecionamento

1. Em **myAppGateway** , selecione **regras** e, em seguida, selecione **+ regra de roteamento de solicitação**.
2. Para o **nome da regra** , digite *Rule2*.
3. Verifique se **MyListener** está selecionado como o ouvinte.
4. Clique na guia **destinos de back-end** e selecione **tipo de destino** como *redirecionamento*.
5. Em **Tipo de redirecionamento** , selecione **Permanente**.
6. Em **Destino de redirecionamento** , selecione **Ouvinte**.
7. Verifique se o **Ouvinte de destino** está definido como **appGatewayHttpListener**.
8. Para a **cadeia de caracteres de consulta include** e **incluir o caminho** , selecione *Sim*.
9. Selecione **Adicionar**.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais configurado para fornecer servidores para o pool de back-end no gateway de aplicativo.

1. No canto superior esquerdo do portal, selecione **+ Criar um recurso**.
2. Selecione **Computação**.
3. Na caixa de pesquisa, digite *conjunto de dimensionamento* e pressione Enter.
4. Selecione **Conjunto de dimensionamento de máquinas virtuais** e selecione **Criar**.
5. Em **Nome do conjunto de dimensionamento de máquinas virtuais** , digite *myvmss*.
6. Como imagem de disco do sistema operacional, ** verifique se **Windows Server 2016 Datacenter** está selecionado.
7. Em **Grupo de recursos** , selecione **myResourceGroupAG**.
8. Em **Nome de usuário** , digite *azureuser*.
9. Em **Senha** , digite *Azure123456!* e confirme a senha.
10. Em **Contagem de instâncias** , verifique se o valor é **2**.
11. Em **Tamanho da instância** , selecione **D2s_v3**.
12. Em **Redes** , defina **Escolher opções de balanceamento de carga** como **Gateway de Aplicativo**.
13. Verifique se **Gateway de Aplicativo** está definido como **myAppGateway**.
14. Verifique se **Sub-rede** está definido como **myBackendSubnet**.
15. Selecione **Criar**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associar o conjunto de dimensionamento ao pool de back-end apropriado

A interface do usuário do portal do conjunto de dimensionamento de máquinas virtuais cria um novo pool de back-end para o conjunto de dimensionamento, mas é melhor associá-la ao appGatewayBackendPool existente.

1. Abra o grupo de recursos **myResourceGroupAg**.
2. Selecione **myAppGateway**.
3. Selecione **Pools de back-end**.
4. Selecione **myAppGatewaymyvmss**.
5. Selecione **Remover todos os destinos do pool de back-end**.
6. Selecione **Salvar**.
7. Depois que esse processo for concluído, selecione o pool de back-end **myAppGatewaymyvmss** , selecione **Excluir** e **OK** para confirmar.
8. Selecione **appGatewayBackendPool**.
9. Em **Destinos** , selecione **VMSS**.
10. Em **VMSS** , selecione **myvmss**.
11. Em **Configurações do Adaptador de Rede** , selecione **myvmssNic**.
12. Selecione **Salvar**.

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de dimensionamento

Por fim, você deve atualizar o conjunto de dimensionamento com essas alterações.

1. Selecione o conjunto de dimensionamento **myvmss**.
2. Em **Configurações** , selecione **Instâncias**.
3. Selecione ambas as instâncias e selecione **Atualizar**.
4. Clique em **Sim** para confirmar.
5. Depois que isso for concluído, volte para **myAppGateway** e selecione **Pools de back-end**. Agora você deve ver que o **appGatewayBackendPool** tem dois destinos, e **myAppGatewaymyvmss** não tem destinos.
6. Selecione **myAppGatewaymyvmss** e selecione **Excluir**.
7. Selecione **OK** para confirmar.

### <a name="install-iis"></a>Instalar o IIS

Uma maneira fácil de instalar o IIS no conjunto de dimensionamento é usando o PowerShell. No portal, clique no ícone do Cloud Shell e selecione **PowerShell**.

Cole o comando a seguir na janela do PowerShell e pressione Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de dimensionamento

Depois de alterar as instâncias com o IIS, você deve atualizar novamente o conjunto de dimensionamento com essa alteração.

1. Selecione o conjunto de dimensionamento **myvmss**.
2. Em **Configurações** , selecione **Instâncias**.
3. Selecione ambas as instâncias e selecione **Atualizar**.
4. Clique em **Sim** para confirmar.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Você pode obter o endereço IP público do aplicativo na página Visão Geral do gateway de aplicativo.

1. Selecione **myAppGateway**.
2. Na página **Visão Geral** , anote o endereço IP em **Endereço IP público de front-end**.

3. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Por exemplo, http://52.170.203.149

   ![Aviso de segurança](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Para aceitar o aviso de segurança se você usou um certificado autoassinado, selecione **detalhes** e **vá para a página da Web**. Seu site de IIS protegido é exibido, como no exemplo a seguir:

   ![Testar a URL de base no gateway de aplicativo](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Criar um gateway de aplicativo com redirecionamento interno](redirect-internal-site-powershell.md).