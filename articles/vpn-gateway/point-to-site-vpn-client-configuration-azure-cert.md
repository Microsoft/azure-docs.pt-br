---
title: 'Criar & instalar arquivos de configuração de cliente VPN P2S: autenticação de certificado'
titleSuffix: Azure VPN Gateway
description: Crie e instale arquivos de configuração de cliente de VPN do Windows, Linux, Linux (strongSwan) e macOS X para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556045"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar arquivos de configuração de cliente VPN para configurações P2S da autenticação de certificado nativa do Azure

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para que os clientes nativos do Windows, Mac IKEv2 VPN ou Linux se conectem a uma rede virtual em conexões ponto a site que usam a autenticação de certificado nativa do Azure.

Os arquivos de configuração do cliente são específicos para a configuração de VPN para a rede virtual. Se houver alterações na configuração de VPN Ponto a Site depois de gerar os arquivos de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, gere e instale novos arquivos de configuração de cliente VPN nos dispositivos do usuário.

* Para saber mais sobre conexões Ponto a Site, confira [Sobre VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções sobre o OpenVPN, confira [Configurar o OpenVPN para P2S](vpn-gateway-howto-openvpn.md) e [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Gerar os arquivos de configuração de cliente VPN

Antes de começar, verifique se todos os usuários conectados têm um certificado válido instalado no dispositivo do usuário. Para saber mais sobre como instalar um certificado de cliente, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

É possível gerar arquivos de configuração do cliente usando o PowerShell ou usando o Portal do Azure. O método retorna o mesmo arquivo zip. Descompacte o arquivo para ver as seguintes pastas:

* **WindowsAmd64** e **WindowsX86** , que contêm os pacotes dos instaladores do Windows de 32 bits e 64 bits, respectivamente. O pacote do instalador de **WindowsAmd64** serve para todos os clientes do Windows de 64 bits, não apenas ao Amd.
* **Generic** , que contém informações gerais, usadas para criar sua própria configuração de cliente VPN. A pasta Genérico será fornecida se IKEv2 ou SSTP+IKEv2 tiver sido configurado no gateway. Se apenas o SSTP estiver configurado, a pasta Genérico não estará presente.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Gerenciar arquivos usando o Portal do Azure

1. No Portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
1. Na página gateway de rede virtual, selecione **configuração ponto a site**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Baixar o cliente VPN":::
1. Na parte superior da página configuração ponto a site, selecione **baixar cliente VPN**. Levará alguns minutos para o pacote de configuração do cliente ser gerado.
1. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele terá o mesmo nome do seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Gerar arquivos usando o PowerShell

1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente de VPN usando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copie a URL para o seu navegador para baixar o arquivo zip. Em seguida, descompacte o arquivo para exibir as pastas.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Você precisa configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que se conecta ao Azure. O Azure não oferece arquivo mobileconfig para autenticação de certificado do Azure nativa. A **Generic** contém todas as informações que você precisa para configuração. Caso não veja a pasta Genérico em seu download, talvez o IKEv2 não tenha sido selecionado como um tipo de túnel. Observe que o SKU básico do gateway de VPN não oferece suporte a IKEv2. Depois que o IKEv2 for selecionado, gere o arquivo zip novamente para recuperar a pasta Genérico.<br>A pasta Genérico contém os seguintes arquivos:

* **VpnSettings.xml** , que contém configurações importantes, como o endereço do servidor e o tipo de túnel. 
* **VpnServerRoot. cer** , que contém o certificado raiz necessário para validar o gateway de VPN do Azure durante a configuração de conexão do P2S.

Use as seguintes etapas para configurar o cliente VPN nativo do Mac para autenticação de certificado. Você precisa concluir estas etapas em cada Mac que se conecta ao Azure:

1. Importe o certificado raiz **VpnServerRoot** para seu Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele. Selecione **Adicionar** para importar.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Captura de tela mostra a página certificados":::
  
    >[!NOTE]
    >Ao clicar duas vezes na caixa de diálogo **Adicionar** , o certificado poderá não ser exibido, mas será instalado no repositório correto. Você pode verificar o certificado no conjunto de chaves de logon, na categoria Certificados.
    >
  
1. Verifique se você instalou um certificado do cliente emitido pelo certificado raiz que você carregou no Azure quando definiu as configurações de P2S. Ele é diferente do VPNServerRoot que você instalou na etapa anterior. O certificado do cliente é usado para autenticação e é necessário. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
1. Abra a caixa de diálogo **rede** em **preferências de rede** e selecione **' + '** para criar um novo perfil de conexão de cliente VPN para uma conexão P2S com a rede virtual do Azure.

   O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **nome do serviço** e, em seguida, selecione **criar** para criar o perfil de conexão de cliente VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="Captura de tela mostra a janela de rede com a opção de selecionar uma interface, selecionar tipo de VPN e inserir um nome de serviço":::
1. Na pasta **Genérico** , no arquivo **VpnSettings.xml** , copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="Captura de tela mostra informações do servidor.":::
1. Selecione **configurações de autenticação** e selecione **certificado**. Para o **Catalina** , selecione **nenhum** e, em seguida, **certificado**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="Captura de tela mostra as configurações de autenticação.":::

   Para o Catalina, selecione **nenhum** e, em seguida, **certificado**. **Selecione** o certificado correto:
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Captura de tela mostra a janela de rede sem nenhum selecionado para configurações de autenticação e certificado selecionado.":::

1. Clique em **selecionar...** para escolher o certificado que deseja usar para autenticação. Trata-se do certificado que você instalou na etapa 2.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Captura de tela mostra a janela de rede com configurações de autenticação, onde você pode selecionar um certificado.":::
1. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, selecione **continuar**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Captura de tela mostra a caixa de diálogo escolher uma identidade, na qual você pode selecionar o certificado apropriado.":::

1. No campo **ID local** , especifique o nome do certificado (da Etapa 6). Neste exemplo, é `ikev2Client.com`. Em seguida, selecione **aplicar** para salvar as alterações.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="A captura de tela mostra aplicar.":::
1. Na caixa de diálogo **rede** , selecione **aplicar** para salvar todas as alterações. Em seguida, selecione **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (GUI strongSwan)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalar e configurar

As instruções a seguir foram criadas no Ubuntu 18.0.4. O Ubuntu 16.0.10 não dá suporte para GUI do StrongSwan. Se você quiser usar o Ubuntu 16.0.10, precisará usar a linha de [comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder às telas que você vê, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **Terminal** para instalar o **strongSwan** e seu Gerenciador de Rede executando o comando no exemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Selecione **configurações** e, em seguida, selecione **rede**. Selecione o **+** botão para criar uma nova conexão.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Captura de tela mostra a página conexões de rede.":::

1. Selecione **IPSec/IKEv2 (strongSwan)** no menu e clique duas vezes em.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Captura de tela mostra a página Adicionar VPN.":::

1. Na página **Adicionar VPN** , adicione um nome para sua conexão VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="A captura de tela mostra escolher um tipo de conexão.":::
1. Abra o arquivo **VpnSettings.xml** da pasta **Genérico** contida nos arquivos de configuração do cliente baixados. Localize a marca chamada **VpnServer** e copie o nome, começando com ' azuregateway ' e terminando com '. cloudapp.net '.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="A captura de tela mostra copiar dados.":::
1. Cole o nome no campo **endereço** da nova conexão VPN na seção **Gateway** . Em seguida, selecione o ícone da pasta no final do campo **Certificado** , navegue até a pasta **Genérico** e selecione o arquivo **VpnServerRoot**.
1. Na seção **Cliente** da conexão, da **Autenticação** , selecione **Certificado/chave privada**. Para **Certificado** e **Chave privada** , escolha o certificado e a chave privada que foram criados anteriormente. Em **Opções** , selecione **Solicitar um endereço IP interno**. Em seguida, selecione **Adicionar**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="A captura de tela mostra a solicitação de um endereço IP interno.":::

1. **Ative a conexão.**

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="A captura de tela mostra a cópia.":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (CLI do strongSwan)

### <a name="install-strongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar e configurar

1. Baixe o pacote de VPNClient do portal do Azure.
1. Extraia o arquivo.
1. Na pasta **genérica** , copie ou mova o **VpnServerRoot. cer** para **/etc/IPSec.d/cacerts**.
1. Copie ou mova **CP Client. p12** para **/etc/IPSec.d/Private/**. Esse arquivo é o certificado do cliente para o gateway de VPN.
1. Abra o arquivo **VpnSettings.xml** e copie o `<VpnServer>` valor. Você usará esse valor na próxima etapa.
1. Ajuste os valores no exemplo abaixo e, em seguida, adicione o exemplo à configuração **/etc/IPSec.conf** .
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Adicione os valores a seguir a **/etc/IPSec.Secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Execute os seguintes comandos:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Próximas etapas

Retorne ao artigo original do qual você estava trabalhando e conclua a configuração do P2S.

* [Etapas de configuração do PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Portal do Azure etapas de configuração](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
