---
title: 'Gateway de VPN do Azure: criar & instalar arquivos de configuração de cliente VPN-conexões RADIUS P2S'
description: Crie arquivos de configuração de cliente VPN Windows, OS X e Linux para conexões que usam autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440061"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar arquivos de configuração de cliente VPN para autenticação RADIUS P2S

Para conectar-se a uma rede virtual via P2S (ponto a site), é necessário configurar o dispositivo do cliente ao qual será conectado. Você pode criar conexões VPN P2S de dispositivos cliente Windows, OS X e Linux. 

Ao utilizar a autenticação RADIUS, há várias opções de autenticação: autenticação de nome de usuário/senha, autenticação de certificado e outros tipos de autenticação. A configuração do cliente de VPN é diferente para cada tipo de autenticação. Para configurar o cliente de VPN, use arquivos de configuração do cliente que contêm as configurações necessárias. Este artigo ajuda você a criar e instalar a configuração do cliente de VPN para o tipo de autenticação de RADIUS que você deseja usar.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

O fluxo de trabalho de configuração para a autenticação de RADIUS P2S é o seguinte:

1. [Configure o gateway de VPN do Azure para conectividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configure o servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obter a configuração de cliente de VPN para a opção de autenticação de sua preferência e usá-la para configurar o cliente de VPN** (este artigo).
4. [Conclua a configuração de P2S e conecte-se](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se houver alguma alteração na configuração VPN ponto a site após gerar o perfil de configuração do cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, será necessário gerar e instalar uma nova configuração de cliente VPN nos dispositivos dos usuários.
>
>

Para usar as seções neste artigo, primeiro decida qual tipo de autenticação você deseja usar: nome de usuário e senha, certificado ou outros tipos de autenticação. Cada seção tem etapas para Windows, OS X e Linux (etapas limitadas disponíveis no momento).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Autenticação de nome de usuário e senha

É possível configurar a autenticação de nome de usuário/senha para utilizar o Active Directory ou não utilizar o Active Directory. Em cada cenário, todos os usuários que se conectam devem ter credenciais de nome de usuário e senha que podem ser autenticadas por meio do RADIUS.

Quando configura a autenticação com nome de usuário e senha, você só pode criar uma configuração para o protocolo de autenticação EAP-MSCHAPv2 de nome de usuário e senha. Nos comandos, `-AuthenticationMethod` é `EapMSChapv2`.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. gerar arquivos de configuração de cliente VPN

Você pode gerar os arquivos de configuração de cliente VPN usando o portal do Azure ou usando Azure PowerShell.

#### <a name="azure-portal"></a>Portal do Azure

1. Navegue até o gateway de rede virtual.
2. Clique em **configuração ponto a site**.
3. Clique em **baixar cliente VPN**.
4. Selecione o cliente e preencha todas as informações solicitadas.
5. Clique em **baixar** para gerar o arquivo. zip.
6. O arquivo. zip será baixado, normalmente para a pasta downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Gere arquivos de configuração de cliente VPN para usar com a autenticação de nome de usuário e senha. É possível gerar os arquivos de configuração do cliente VPN, utilizando o comando a seguir:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Executar o comando retorna um link. Copie e cole o link em um navegador da Web para baixar **VpnClientConfiguration.zip**. Descompacte o arquivo para ver as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86**: essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* **Genérica**: essa pasta contém informações gerais utilizadas para criar sua própria configuração do cliente VPN. Essa pasta não é necessária para as configurações de autenticação de nome de usuário/senha.
* **Mac**: se IKEv2 foi configurado ao criar o gateway de rede virtual, você verá uma pasta nomeada **Mac** que contém um arquivo **mobileconfig**. Esse arquivo é utilizado para configurar clientes Mac.

Se você já criou arquivos de configuração do cliente, poderá recuperá-los utilizando o cmdlet`Get-AzVpnClientConfiguration`. Porém, se você fizer alterações na configuração VPN do P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet `New-AzVpnClientConfiguration` para criar uma nova configuração de fazer o download.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. configurar clientes VPN

É possível configurar os seguintes clientes de VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux usando strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Configuração de cliente VPN do Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista dos sistemas operacionais clientes com suporte, consulte as [Perguntas Frequentes](vpn-gateway-vpn-faq.md#P2S).

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador do **VpnClientSetupAmd64** . Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador do **VpnClientSetupX86** . 
2. Clique duas vezes no pacote para instalá-lo. Se você vir um pop-up do SmartScreen, selecione **mais informações**  >  **executar mesmo assim**.
3. No computador cliente, navegue para **Configurações de Rede** e selecione **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Configuração de cliente de VPN do Mac (OSX)

1. Selecione o arquivo **VpnClientSetup mobileconfig** e envie-o para cada um dos usuários. Você pode usar o email ou outro método.

2. Localize o arquivo **mobileconfig** no Mac.

   ![Local do arquivo mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Etapa opcional – se você quiser especificar um DNS personalizado, adicione as seguintes linhas para o arquivo **mobileconfig**:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Clique duas vezes no perfil para instalá-lo, e selecione **Continuar**. O nome do perfil é o mesmo que o nome da sua rede virtual.

   ![Mensagem de instalação](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecione **Continuar** para confiar no remetente do perfil e continuar a instalação.

   ![Mensagem de confirmação](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Durante a instalação do perfil, você terá a opção de especificar o nome de usuário e senha para autenticação de VPN. Não é obrigatório inserir essas informações. Se você fizer isso, as informações serão salvas e utilizadas automaticamente ao iniciar uma conexão.Selecione **Instalar** para continuar.

   ![Caixas de nome de usuário e senha para VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Insira um nome de usuário e senha para os privilégios necessários para instalar o perfil no computador. Selecione **OK**.

   ![Caixas de nome de usuário e senha para instalação do perfil](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Após a instalação do perfil, ele estará visível na caixa de diálogo **Perfis**. Também é possível abrir essa caixa de diálogo posteriormente a partir das **Preferências do Sistema**.

   ![Caixa de diálogo "Perfis"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Para acessar a conexão VPN, abra a caixa de diálogo **Rede** em **Preferências do Sistema**.

   ![Ícones nas Preferências do Sistema](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A conexão VPN aparece como **IkeV2-VPN**. O nome pode ser alterado, atualizando o arquivo **mobileconfig**.

    ![Detalhes da conexão VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecione **configurações de autenticação**. Selecione **Nome de Usuário** na lista e insira suas credenciais. Se você inseriu as credenciais anteriormente, o **nome de usuário** será escolhido automaticamente na lista, e o nome de usuário e a senha serão preenchidos previamente. Selecione **OK** para salvar as configurações.

    ![Captura de tela que mostra a lista suspensa "configurações de autenticação" com "nome de usuário" selecionado.](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Retorne para a caixa de diálogo **Rede** e selecione **Aplicar** para salvar as alterações. Para iniciar a conexão, selecione **Conectar**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Configuração do cliente VPN do Linux por meio do strongSwan

As instruções a seguir foram criadas por meio do forteSwan 5.5.1 no Ubuntu 17.0.4. As telas reais podem ser diferentes, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **Terminal** para instalar o **strongSwan** e seu Gerenciador de Rede executando o comando no exemplo. Se você receber um erro relacionado a `libcharon-extra-plugins`, substitua-o por `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o ícone **Gerenciador de rede** (seta para cima/seta para baixo) e selecione **Editar Conexões**.

   ![Seleção "Editar Conexões" no Gerenciador de Rede](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Clique no botão **Adicionar** para criar uma nova conexão.

   ![Botão "Adicionar" para uma conexão](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **IPsec/IKEv2 (strongswan)** no menu suspenso e, em seguida, selecione **Criar**. Você pode renomear sua conexão nesta etapa.

   ![Selecionar o tipo de conexão](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o arquivo **VpnSettings.xml** na pasta **Genérico** dos arquivos de configuração do cliente baixado. Localize a marca chamada `VpnServer` e copie o nome, começando com `azuregateway` e terminando com `.cloudapp.net`.

   ![Conteúdo do arquivo VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Cole esse nome no campo **Endereço** da sua nova conexão VPN na seção **Gateway**. Em seguida, selecione o ícone da pasta no final do campo **Certificado**, navegue até a pasta **Genérico** e selecione o arquivo **VpnServerRoot**.
7. Na seção **Cliente** da conexão, selecione **EAP** para **Autenticação** e insira seu nome de usuário e senha. É possível ter que selecionar o ícone de bloqueio direito para salvar essas informações. Em seguida, selecione **Salvar**.

   ![Editar configurações de conexão](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecione o ícone do **Gerenciador de Rede** (seta para cima/seta para baixo) e passe o mouse sobre **Conexões VPN**. Você visualiza a conexão VPN que foi criada. Para iniciar a conexão, selecione-a.

   ![Conexão "VPN com Radius" no Gerenciador de Rede](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Autenticação de certificado
 
Você pode criar arquivos de configuração de cliente VPN para autenticação de certificado RADIUS que usa o protocolo EAP-TLS. Normalmente, um certificado emitido pela empresa é utilizado para autenticar um usuário para VPN. Certifique-se de que todos os usuários conectados tenham um certificado instalado em seus dispositivos e que o servidor RADIUS possa validar o certificado.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Nos comandos, `-AuthenticationMethod` é `EapTls`. Durante a autenticação do certificado, o cliente valida o servidor RADIUS, validando seu certificado. `-RadiusRootCert` é o arquivo .cer que contém o certificado raiz utilizado para validar o servidor RADIUS.

Cada dispositivo cliente de VPN requer um certificado do cliente instalado. Às vezes, um dispositivo Windows possui vários certificados de cliente. Durante a autenticação, isso pode resultar em uma caixa de diálogo de pop-up que lista todos os certificados. O usuário deve, em seguida, escolha o certificado a ser usado. O certificado correto pode ser filtrado, especificando o certificado raiz ao qual o certificado do cliente deve estar encadeado. 

`-ClientRootCert` é o arquivo .cer que contém o certificado raiz. É um parâmetro opcional. Se o dispositivo com o qual você quer conectar-se tiver apenas um certificado do cliente, não será necessário especificar esse parâmetro.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. gerar arquivos de configuração de cliente VPN

Gere arquivos de configuração de cliente VPN para usar com a autenticação de certificado. É possível gerar os arquivos de configuração do cliente VPN, utilizando o comando a seguir:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Executar o comando retorna um link. Copie e cole o link em um navegador da Web para baixar o VpnClientConfiguration.zip. Descompacte o arquivo para ver as seguintes pastas:

* **WindowsAmd64** e **WindowsX86**: essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* **GenericDevice** : essa pasta contém informações gerais utilizadas para criar sua própria configuração de cliente VPN.

Se você já criou arquivos de configuração do cliente, poderá recuperá-los utilizando o cmdlet`Get-AzVpnClientConfiguration`. Porém, se você fizer alterações na configuração VPN do P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet `New-AzVpnClientConfiguration` para criar uma nova configuração de fazer o download.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. configurar clientes VPN

É possível configurar os seguintes clientes de VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (com suporte, nenhuma etapa no artigo ainda)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Configuração de cliente VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador do **VpnClientSetupAmd64** . Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador do **VpnClientSetupX86** . Se você vir um pop-up do SmartScreen, selecione **mais informações**  >  **executar mesmo assim**. Você também pode salvar o pacote de instalação em outros computadores clientes.
2. Cada cliente requer um certificado do cliente para autenticação. Instale o certificado do cliente. Para obter informações sobre certificados de cliente, consulte [certificados de cliente para ponto a site](vpn-gateway-certificates-point-to-site.md). Para instalar um certificado que foi gerado, consulte [Instalar um certificado em clientes Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue para **Configurações de Rede** e selecione **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Configuração de cliente de VPN do Mac (OSX)

É necessário criar um perfil separado para cada dispositivo Mac que conecta-se à rede virtual do Azure. Isso ocorre porque esses dispositivos exigem o certificado de autenticação do usuário a ser especificado no perfil. A pasta **Genérico** possui todas as informações necessárias para criar um perfil:

* **VpnSettings.xml** contém configurações importantes, como o tipo de túnel e o endereço do servidor.
* **VpnServerRoot.cer** contém o certificado raiz necessário para validar o gateway de VPN durante a instalação de conexão P2S.
* **VpnServerRoot.cer** contém o certificado raiz necessário para validar o servidor RADIUS durante a autenticação.

Use as etapas a seguir para configurar o cliente VPN nativo em um Mac para autenticação de certificado:

1. Importe os certificados raiz **VpnServerRoot** e **RadiusServerRoot** para o Mac. Copie cada arquivo para o Mac, clique duas vezes nele, em seguida, selecione **Adicionar**.

   ![Adicionar o certificado VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Adicionar o certificado RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente requer um certificado do cliente para autenticação. Instale o certificado do cliente no dispositivo cliente.
3. Abra a caixa de diálogo **Rede** em **Preferências de Rede**. Selecione **+** para criar um novo perfil de conexão de cliente VPN para uma conexão P2S com a rede virtual do Azure.

   O valor de **Interface** é **VPN** e o valor do **Tipo de VPN** é **IKEv2**. Especifique um nome para o perfil na caixa **Nome do serviço** e, em seguida, selecione **Criar** para criar o perfil de conexão do cliente VPN.

   ![Informações sobre nome do serviço e interface](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nas caixas **Endereço do Servidor** e **ID Remota** do perfil. Deixe a caixa **ID Local** em branco.

   ![Informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecione **Configurações de Autenticação** e selecione **Certificado**. 

   ![Configurações de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique em **Selecionar** para escolher o certificado que deseja utilizar para autenticação.

   ![Selecionar um certificado para autenticação](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e selecione **Continuar**.

   ![Lista "Escolher uma Identidade"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Na caixa **ID local**, especifique o nome do certificado (da Etapa 6). Neste exemplo, é **ikev2Client.com**. Em seguida, selecione o botão **Aplicar** para salvar as alterações.

   ![Caixa "ID Local"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na caixa de diálogo **Rede**, selecione **Aplicar** para salvar as alterações. Em seguida, selecione **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Trabalho com outros tipos de autenticação ou protocolos

Para usar um tipo de autenticação diferente (por exemplo, OTP) ou usar um protocolo de autenticação diferente (como PEAP-MSCHAPv2 em vez de EAP-MSCHAPv2), é necessário criar seu próprio perfil de configuração do cliente VPN. Para criar o perfil, você precisa de informações como o endereço IP do gateway de rede virtual, tipo de túnel e rotas de túnel dividido. É possível obter essas informações usando as etapas a seguir:

1. Use o cmdlet `Get-AzVpnClientConfiguration` para gerar a configuração do cliente VPN para EapMSChapv2.

2. Descompacte o arquivo VpnClientConfiguration.zip e procure a pasta **GenericDevice**. Ignore as pastas quem contêm os instaladores do Windows para arquiteturas de 64 bits e de 32 bits.
 
3. A pasta **GenericDevice** contém um arquivo XML chamado **VpnSettings**. Este arquivo contém todas as informações necessárias:

   * **VpnServer**: FQDN do gateway de VPN do Azure. Este é o endereço ao qual o cliente se conecta.
   * **VpnType**: tipo de túnel utilizado conectar-se.
   * **Rotas**: rotas que você precisa configurar no perfil para que somente o tráfego associado à rede virtual do Azure seja enviado via túnel P2S.
   
   A pasta **GenericDevice** também contém um arquivo .cer chamado **VpnServerRoot**. Este arquivo contém o certificado raiz necessário para validar o gateway de VPN do Azure durante a configuração da conexão P2S. Instale o certificado em todos os dispositivos que irão conectar-se à rede virtual do Azure.

## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](point-to-site-how-to-radius-ps.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
