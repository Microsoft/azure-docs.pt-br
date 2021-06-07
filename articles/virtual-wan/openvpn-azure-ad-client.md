---
title: 'Gateway de VPN: cliente VPN para conexões de protocolo P2S OpenVPN: autenticação do Azure AD'
description: Saiba como usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3dbdfb461bc2ae79439f39df3efbc2c564782110
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548386"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Configurar um cliente VPN para conexões de protocolo P2S OpenVPN: Autenticação do Azure AD

Este artigo ajuda você a configurar o cliente VPN para se conectar a uma rede virtual usando a VPN ponto a site e a autenticação do Azure Active Directory. Para se conectar e autenticar usando o Azure AD, primeiro configure seu locatário do Azure AD. Para obter mais informações, confira [Configurar um locatário do Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Trabalhando com perfis de cliente

Para se conectar, você precisará baixar o Cliente VPN do Azure e configurar um perfil de cliente VPN em cada computador que deseja se conectar à VNet. Você pode criar um perfil de cliente em um computador, exportá-lo e, depois, importá-lo em computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o Cliente VPN do Azure

Use este [link](https://go.microsoft.com/fwlink/?linkid=2117554) para baixar o Cliente VPN do Azure. Verifique se o cliente VPN do Azure tem permissão para ser executado em segundo plano. Para verificar/habilitar a permissão, siga as etapas abaixo:

1. Acesse Iniciar e selecione Configurações > Privacidade > Aplicativos em segundo plano.
2. Em Aplicativos em segundo plano, verifique se a opção **Permitir que os aplicativos sejam executados em segundo plano** está ativada.
3. Em Escolher quais aplicativos podem ser executados em segundo plano, mude as configurações do Cliente VPN do Azure para **Ativado**.

  ![permissão](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Para criar um perfil de cliente baseado em certificado

Ao trabalhar com um perfil baseado em certificado, verifique se os certificados apropriados estão instalados no computador cliente. Para obter mais informações sobre certificados, confira [Instalar certificados de cliente](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![raio](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> O segredo do servidor pode ser exportado no perfil de cliente da VPN P2S.  Instruções sobre como exportar um perfil de cliente podem ser encontradas [aqui](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Para exportar e distribuir um perfil de cliente

Quando tiver um perfil de trabalho e precisar distribuí-lo para outros usuários, você poderá exportá-lo executando as seguintes etapas:

1. Realce o perfil de cliente VPN que você deseja exportar, selecione **...** e depois **Exportar**.

    ![Captura de tela mostra a opção Exportar selecionada no menu.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione a localização em que você deseja salvar esse perfil, deixe o nome do arquivo como está e selecione **Salvar** para salvar o arquivo XML.

    ![Captura de tela mostra a caixa de diálogo Salvar como na qual é possível inserir um nome de arquivo.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **Importar**.

    ![Captura de tela mostra a opção Importar selecionada no menu mais.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **Abrir**.

    ![Captura de tela mostra a caixa de diálogo Abrir na qual você pode selecionar um arquivo.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **Salvar**.

    ![Captura de tela mostra o Nome da conexão adicionado e o botão Salvar selecionado.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **Conectar** para se conectar à VPN.

    ![Captura de tela mostra o botão Conectar da conexão que você acabou de criar.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e mostrará o texto **Conectado**.

    ![Captura de tela mostra a conexão com status Conectado e a opção para desconectar.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **Remover**.

    ![Captura de tela mostra a opção Remover selecionada no menu.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **Remover** para excluir.

    ![Captura de tela mostra uma caixa de diálogo de confirmação com a opção Remover ou Cancelar.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Criar uma conexão

1. Na página, selecione **+** e depois **+ Adicionar**.

    ![Captura de tela mostra a opção Adicionar selecionada no menu mais.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de conexão. Se você não tiver certeza dos valores, entre em contato com o administrador. Após preencher os valores, selecione **Salvar**.

    ![Captura de tela mostra o painel em que você pode inserir os valores necessários.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **Conectar** para se conectar à VPN.

    ![Captura de tela mostra o botão Conectar da conexão.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais apropriadas e depois escolha **Continuar**.

    ![Captura de tela mostra a caixa de diálogo Entrar.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez conectado com sucesso, o ícone ficará verde e vai mostrar o texto **Conectado**.

    ![Captura de tela mostra a conexão com status Conectado.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Para se conectar automaticamente

Essas etapas ajudam a configurar sua conexão para que se reestabeleça automaticamente com o Always On.

1. Na home page do cliente VPN, selecione **Configurações de VPN**.

    ![Captura de tela mostra as conexões VPN nas quais é possível selecionar as Configurações de VPN.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo Alternar aplicativos.

    ![Captura de tela mostra uma mensagem de verificação sobre como alternar aplicativos.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verifique se a conexão que você deseja definir ainda não está conectada e, depois, realce o perfil e marque a caixa de seleção **Conectar automaticamente**.

    ![Captura de tela mostra a caixa de diálogo Configurações, na qual você pode marcar a opção Conectar automaticamente.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **Conectar** para iniciar a conexão VPN.

    ![Captura de tela mostra o botão Conectar.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta **Diagnosticar**. Selecione **...** ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **Diagnosticar**.

    ![Captura de tela mostra a opção Diagnosticar selecionada no menu.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página **Propriedades de Conexão**, selecione **Executar Diagnóstico**.

    ![Captura de tela mostra o botão Executar diagnóstico para uma conexão.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![Captura de tela mostra a caixa de diálogo Entrar para esta ação.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![Captura de tela mostra os resultados do diagnóstico.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Como fazer para adicionar sufixos DNS ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcações **\<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>**

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Como fazer para adicionar servidores DNS personalizados ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcações **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>**

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
> O cliente OpenVPN do Azure AD utiliza as entradas de NRPT DNS (Tabela de Políticas de Resolução de Nomes DNS), o que significa que os servidores DNS não serão listados na saída de `ipconfig /all`. Para confirmar suas configurações de DNS em uso, confira [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) no PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Como fazer para adicionar rotas personalizadas ao cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcações **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>**

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Como fazer para direcionar todo o tráfego para o túnel VPN (túnel de força)?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcações **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>**

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Como fazer para bloquear (excluir) as rotas do cliente VPN?

Você pode modificar o arquivo XML do perfil baixado e adicionar as marcações **\<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>**

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
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Eu posso importar o perfil por meio de um prompt da linha de comando?

Você pode importar o perfil por meio de um prompt da linha de comando colocando o arquivo **azurevpnconfig.xml** baixado na pasta **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** e executando o seguinte comando:

```
azurevpn -i azurevpnconfig.xml 
```
para forçar a importação, use a opção **-f** também


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira [Criar um locatário de Azure Active Directory para conexões VPN abertas P2S que usam a autenticação do Azure AD](openvpn-azure-ad-tenant.md).