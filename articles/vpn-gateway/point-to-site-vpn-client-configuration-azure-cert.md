---
title: "Criar e instalar arquivos de configuração de cliente VPN P2S para autenticação de certificado do Azure: PowerShell: Azure | Microsoft Docs"
description: "Este artigo ajuda você a criar e instalar o arquivo de configuração de cliente VPN para conexões Ponto a Site que usam a autenticação de certificado."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2017
ms.author: cherylmc
ms.openlocfilehash: 4abfdcc0a50c229555088dff0ac2c00c15f49218
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar arquivos de configuração de cliente VPN para configurações P2S da autenticação de certificado nativa do Azure

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para um cliente VPN nativo do Windows ou Mac IKEv2 para se conectar a uma rede virtual Ponto a Site que usa a autenticação de certificado nativa do Azure.

>[!NOTE]
>Atualmente, o IKEv2 para P2S está em Versão Prévia.
>

### <a name="workflow"></a>Fluxo de trabalho de P2S

  1. Configure o gateway de VPN do Azure para uma conexão P2S.
  2. Gere o certificado raiz e os certificados do cliente. Carregue a chave pública do certificado raiz no Azure e instale os certificados do cliente nos dispositivos cliente. Os certificados são usados para autenticar usuários em conexão.
  3. Obter a configuração de cliente VPN para a opção de autenticação de sua preferência e usá-la para configurar o cliente VPN em seu dispositivo com Windows e Mac. Isso lhe permite se conectar-se a redes virtuais do Azure através de uma conexão Ponto a Site.

>[!NOTE]
>Os arquivos de configuração do cliente são específicos para a configuração de VPN da VNet. Se houver alterações na configuração de VPN Ponto a Site depois de gerar os arquivos de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, gere e instale novos arquivos de configuração de cliente VPN nos dispositivos do usuário.
>
>

## <a name="generate"></a>Gerar os arquivos de configuração de cliente VPN

Antes de começar, verifique se todos os usuários conectados têm um certificado válido instalado no dispositivo do usuário. Para saber mais sobre como instalar um certificado de cliente, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

É possível gerar arquivos de configuração do cliente usando o PowerShell ou usando o Portal do Azure. O método retorna o mesmo arquivo zip. Descompacte o arquivo para ver as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes dos instaladores do Windows de 32 bits e 64 bits, respectivamente. O pacote do instalador de **WindowsAmd64** serve para todos os clientes do Windows de 64 bits, não apenas ao Amd.
  * **Generic**, que contém informações gerais, usadas para criar sua própria configuração de cliente VPN. Ignore essa pasta. A pasta Genérico será fornecida se IKEv2 ou SSTP+IKEv2 tiver sido configurado no gateway. Se apenas o SSTP estiver configurado, a pasta Genérico não estará presente.

### <a name="zipportal"></a>Gerenciar arquivos usando o Portal do Azure

1. No Portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
2. Na página de gateway de rede virtual, clique em **Configuração Ponto a Site**.
3. Na parte superior da página da configuração ponto a site, clique em **Baixar cliente VPN**. Levará alguns minutos para o pacote de configuração do cliente ser gerado.
4. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele terá o mesmo nome do seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="zipps"></a>Gerar arquivos usando o PowerShell

1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente de VPN usando o seguinte comando:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Copie a URL para o seu navegador para baixar o arquivo zip. Em seguida, descompacte o arquivo para exibir as pastas.

## <a name="installwin"></a>Instalar o pacote de configuração de cliente VPN do Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais quer recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#P2S).

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 

## <a name="installmac"></a>Instalar a configuração de cliente de VPN do Mac (OSX)

É necessário criar uma configuração de cliente de VPN separada para cada dispositivo Mac que se conecta à VNet do Azure. Não é possível reutilizar os mesmos arquivos de configuração para vários dispositivos Mac. Isso ocorre porque, para esses dispositivos, você deve especificar o certificado de usuário nos arquivos de configuração de cliente VPN. A pasta **Genérico** possui todas as informações necessárias para criar uma configuração de cliente de VPN. Caso não veja a pasta Genérico em seu download, talvez o IKEv2 não tenha sido selecionado como um tipo de túnel. Depois que o IKEv2 for selecionado, gere o arquivo zip novamente para recuperar a pasta Genérico. A pasta Genérico contém os seguintes arquivos:

* **VpnSettings.xml**, que contém configurações importantes, como o tipo de túnel e o endereço do servidor. 
* **VpnServerRoot.cer**, que contém o certificado raiz necessário para validar o Gateway de VPN do Azure durante a instalação de conexão P2S.

Use as seguintes etapas para configurar o cliente VPN do Mac nativo para autenticação de certificado:

1. Importe o certificado raiz **VpnServerRoot** para seu Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele.  
Clique em **Adicionar** para importar.

  ![adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. Abra a caixa de diálogo **Rede** em **Preferências de rede** e clique em **'+'** para criar um novo perfil de conexão de cliente VPN para uma conexão de P2S para a rede virtual do Azure.

  O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **Nome do serviço** e, em seguida, clique em **Criar** para criar o perfil de conexão de cliente VPN.

  ![rede](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil.

  ![informações do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. Clique em **Configurações de autenticação** e selecione **Certificado**. 

  ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. Clique em **Selecionar...** para escolher o certificado que você deseja usar para autenticação.

  ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, clique em **Continuar**.

  ![identidade](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. No campo **ID local**, especifique o nome do certificado (da Etapa 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para salvar as alterações.

  ![aplicar](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. Na caixa de diálogo **Rede**, clique em **Aplicar** para salvar todas as alterações. Em seguida, clique em **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](vpn-gateway-howto-point-to-site-rm-ps.md).
