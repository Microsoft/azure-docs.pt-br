---
title: 'Configurar a autenticação do Azure AD para a conexão VPN do usuário: WAN virtual'
description: Saiba como configurar a autenticação de Azure Active Directory para VPN de usuário.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 9cc68eb60096c4431acfc988c87ca9bf99f1f045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043391"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Configurar a autenticação de Azure Active Directory para VPN de usuário

Este artigo mostra como configurar a autenticação do Azure AD para VPN de usuário na WAN virtual para se conectar aos seus recursos no Azure por meio de uma conexão VPN OpenVPN. A Autenticação Azure Active Directory só está disponível para gateways usando o protocolo OpenVPN e clientes que executam o Windows.

Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste artigo, você aprenderá como:

* Criar uma WAN virtual
* Criar um hub virtual
* Criar uma configuração de VPN de usuário
* Baixar um perfil VPN de usuário de WAN virtual
* Aplicar a configuração de VPN do usuário a um hub virtual
* Conectar uma VNet a um hub virtual
* Baixar e aplicar a configuração do cliente VPN do usuário
* Exibir sua WAN virtual

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN virtual

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, clique em **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e clique em Enter.
2. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Criar WAN.
3. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Localização do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo:** Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
4. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
5. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Criar um hub virtual vazio

1. Em sua WAN virtual, selecione hubs e clique em **+ novo hub**.

   ![Captura de tela mostra a caixa de diálogo configuração de hubs com o novo hub selecionado.](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na página de criação do hub virtual, preencha os seguintes campos.

   **Região** – selecione a região na qual deseja implantar o hub virtual.

   **Nome** – digite o nome que deseja dar ao seu hub virtual.

   **Espaço de endereço privado do hub** – o intervalo de endereços do hub na notação CIDR.

   ![Captura de tela mostra o painel criar Hub virtual onde você pode inserir valores.](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Clique em **Revisar + Criar**.
4. Na página **validação aprovada** , clique em **criar**.

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>Criar uma nova configuração de VPN de usuário

Uma configuração de VPN de usuário define os parâmetros para conectar clientes remotos.

1. Na WAN Virtual, selecione **Configurações de VPN de usuário**.

   ![Captura de tela mostra o item de menu de configurações do usuário V N selecionado.](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. clique em **+Criar configuração de VPN de usuário**.

   ![Captura de tela mostra o link de configuração criar usuário V P N.](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Insira as informações e clique em **criar**.

   * **Nome da configuração** – Insira o nome que você deseja chamar para sua configuração de VPN de usuário.
   * **Tipo de túnel** – selecione OpenVPN.
   * **Método de autenticação** – selecione Azure Active Directory.
   * **Público** -tipo na ID do aplicativo do aplicativo empresarial da [VPN do Azure](openvpn-azure-ad-tenant.md) registrada em seu locatário do Azure AD. 
   * **Emissor** - `https://sts.windows.net/<your Directory ID>/`
   * **Locatário do AAD** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![Captura de tela mostra o painel de configuração criar novo usuário V N, em que você pode inserir os valores.](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Editar atribuição de hub

1. Navegue até a folha **hubs** na WAN virtual.
2. Selecione o hub ao qual deseja associar a configuração do servidor de VPN e clique nas reticências (...).

   ![A captura de tela mostra editar Hub virtual selecionado no menu.](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Clique em **Editar hub virtual**.
4. Marque a caixa de seleção **Incluir gateway ponto a site** e selecione a **Unidade de escala do gateway** desejada.

   ![Captura de tela mostra a caixa de diálogo Editar Hub virtual, onde você pode selecionar a unidade de escala do gateway.](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Insira o **Pool de endereços** do qual os endereços IP serão atribuídos aos clientes VPN.
6. Clique em **Confirmar**.
7. Essa operação pode demorar até 30 minutos para ser concluída.

## <a name="download-user-vpn-profile"></a><a name="device"></a>Baixar perfil VPN do usuário

Use o perfil VPN para configurar seus clientes.

1. Na página de sua WAN virtual, clique em **configurações de VPN de usuário**.
2. Na parte superior da página, clique em **Baixar configurações de VPN do usuário**.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

## <a name="configure-user-vpn-clients"></a>Configurar clientes VPN do usuário

Para se conectar, você precisará baixar o Cliente VPN do Azure e importar o perfil do cliente VPN baixado nas etapas anteriores em cada computador que deseja se conectar à VNet.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para &reg; conexões de protocolo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para baixar o Cliente VPN do Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![A captura de tela mostra a importação selecionada no menu mais.](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![Captura de tela mostra uma caixa de diálogo aberta onde você pode selecionar um arquivo.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![Captura de tela mostra o nome da conexão adicionado e o botão salvar selecionado.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![Captura de tela mostra o botão conectar para a conexão que você acabou de criar.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![Captura de tela mostra a conexão em um status conectado com a opção de desconectar.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências (...) ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![Captura de tela mostra remover selecionado no menu.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![Captura de tela mostra uma caixa de diálogo de confirmação com a opção de remover ou cancelar.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione as reticências (...) ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![Captura de tela mostra o diagnóstico selecionado no menu.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![Captura de tela mostra o botão Executar diagnóstico para uma conexão.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![Captura de tela mostra a caixa de diálogo entrar para esta ação.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![Captura de tela mostra os resultados do diagnóstico.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página de visão geral, cada ponto do mapa representa um hub.
3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando esses recursos não forem mais necessários, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
