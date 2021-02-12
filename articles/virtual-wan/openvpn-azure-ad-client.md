---
title: 'Gateway de VPN: cliente VPN para conexões P2S do protocolo OpenVPN: autenticação do Azure AD'
description: Saiba como usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8e97a2f077efd4d00eec4a91645dc1b65057ebd9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565000"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configurar um cliente VPN para conexões de protocolo P2S OpenVPN: Autenticação do Azure AD

Este artigo ajuda você a configurar um cliente VPN para se conectar a uma rede virtual usando VPN ponto a site e autenticação Azure Active Directory. Antes de poder se conectar e autenticar usando o Azure AD, você deve primeiro configurar seu locatário do Azure AD. Para obter mais informações, consulte [configurar um locatário do Azure ad](openvpn-azure-ad-tenant.md).

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Trabalhando com perfis de cliente

Para se conectar, você precisa baixar o cliente VPN do Azure e configurar um perfil de cliente VPN em todos os computadores que queiram se conectar à VNet. Você pode criar um perfil de cliente em um computador, exportá-lo e, em seguida, importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o Cliente VPN do Azure. Verifique se o cliente VPN do Azure tem permissão para ser executado em segundo plano. Para verificar/habilitar a permissão, siga as etapas abaixo:

1. Vá para iniciar e, em seguida, selecione configurações > privacidade > aplicativos em segundo plano.
2. Em aplicativos em segundo plano, certifique-se de **que a execução de aplicativos em segundo plano** esteja ativada.
3. Em escolher quais aplicativos podem ser executados em segundo plano, ative as configurações do cliente VPN do Azure para **ativado**.

  ![permissão](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Para criar um perfil de cliente baseado em certificado

Ao trabalhar com um perfil baseado em certificado, verifique se os certificados apropriados estão instalados no computador cliente. Para obter mais informações sobre certificados, consulte [instalar certificados de cliente](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O segredo do servidor pode ser exportado no perfil do cliente VPN P2S.  As instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Para exportar e distribuir um perfil de cliente

Quando tiver um perfil de trabalho e precisar distribuí-lo para outros usuários, você poderá exportá-lo usando as seguintes etapas:

1. Realce o perfil do cliente VPN que você deseja exportar, selecione **...** e, em seguida, selecione **Exportar**.

    ![A captura de tela mostra exportar selecionado no menu.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione o local no qual você deseja salvar esse perfil, deixe o nome do arquivo como está e, em seguida, selecione **salvar** para salvar o arquivo XML.

    ![Captura de tela mostra uma caixa de diálogo Salvar como onde é possível inserir um nome de arquivo.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![A captura de tela mostra a importação selecionada no menu mais.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![Captura de tela mostra uma caixa de diálogo aberta onde você pode selecionar um arquivo.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![Captura de tela mostra o nome da conexão adicionado e o botão salvar selecionado.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![Captura de tela mostra o botão conectar para a conexão que você acabou de criar.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![Captura de tela mostra a conexão em um status conectado com a opção de desconectar.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![Captura de tela mostra remover selecionado no menu.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![Captura de tela mostra uma caixa de diálogo de confirmação com a opção de remover ou cancelar.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Criar uma conexão

1. Na página, selecione **+** e **+ Adicionar**.

    ![Captura de tela mostra adicionar selecionado no menu mais.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de conexão. Se você não tiver certeza dos valores, contate o administrador. Depois de preencher os valores, selecione **salvar**.

    ![Captura de tela mostra o painel onde você pode inserir os valores necessários.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Conectar** para se conectar à VPN.

    ![Captura de tela mostra o botão conectar para sua conexão.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais apropriadas e, em seguida, selecione **continuar**.

    ![Captura de tela mostra a caixa de diálogo entrar.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez conectado com êxito, o ícone ficará verde e informará **conectado**.

    ![Captura de tela mostra a conexão em um status conectado.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Para se conectar automaticamente

Essas etapas ajudam a configurar sua conexão para se conectar automaticamente com o Always on.

1. Na home page do cliente VPN, selecione configurações de **VPN**.

    ![A captura de tela mostra as conexões V P N nas quais é possível selecionar as configurações V P N.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo Alternar aplicativos.

    ![Captura de tela mostra uma mensagem de verificação sobre como alternar aplicativos.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verifique se a conexão que você deseja definir ainda não está conectada e, em seguida, realce o perfil e marque a caixa de seleção **conectar automaticamente** .

    ![Captura de tela mostra uma caixa de diálogo Configurações, na qual você pode selecionar conectar automaticamente.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **conectar** para iniciar a conexão VPN.

    ![Captura de tela mostra o botão conectar.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione o **...** ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![Captura de tela mostra o diagnóstico selecionado no menu.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão** , selecione **Executar Diagnóstico**.

    ![Captura de tela mostra o botão Executar diagnóstico para uma conexão.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![Captura de tela mostra a caixa de diálogo entrar para esta ação.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![Captura de tela mostra os resultados do diagnóstico.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como fazer adicionar sufixos DNS ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** as marcas

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Como fazer adicionar servidores DNS personalizados ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** as marcas

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> O cliente OpenVPN do Azure AD utiliza as entradas do DNS Tabela de Políticas de Resolução de Nomes (NRPT), o que significa que os servidores DNS não serão listados na saída de `ipconfig /all` . Para confirmar suas configurações de DNS em uso, consulte [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) no PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como fazer adicionar rotas personalizadas ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** as marcas

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Como fazer direcionar todo o tráfego para o túnel VPN (túnel forçado)?

Você pode modificar o arquivo XML do perfil baixado e adicionar **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** as marcas

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Como fazer bloquear (excluir) as rotas do cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** as marcas

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Posso importar o perfil de um prompt de linha de comando?

Você pode importar o perfil de um prompt de linha de comando colocando o arquivo **azurevpnconfig.xml** baixado na pasta **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** e executando o seguinte comando:

```
azurevpn -i azurevpnconfig.xml 
```
para forçar a importação, use a opção **-f** também


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [criar um locatário de Azure Active Directory para conexões VPN abertas do P2S que usam a autenticação do Azure ad](openvpn-azure-ad-tenant.md).