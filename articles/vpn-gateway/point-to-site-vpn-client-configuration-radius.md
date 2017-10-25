---
title: "Criar e instalar arquivos de configuração de cliente VPN para conexões RADIUS P2S: PowerShell: Azure | Microsoft Docs"
description: "Este artigo ajuda você a criar o arquivo de configuração de cliente VPN para conexões Ponto a Site que usam a autenticação RADIUS."
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
ms.date: 10/16/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: c56a9b06a11d25cf046a0faeb29af1d78d9a4a89
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>Criar e instalar arquivos de configuração de cliente VPN para autenticação RADIUS P2S (Versão Prévia)

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para um cliente VPN nativo do Windows ou Mac IKEv2 para se conectar a uma rede virtual Ponto a Site. O servidor RADIUS fornece várias opções de autenticação e por isso, a configuração de cliente VPN varia para cada opção.

### <a name="workflow"></a>Fluxo de trabalho

  1. Configurar o gateway de VPN do Azure para conectividade P2S.
  2. Configurar o servidor RADIUS para autenticação. 
  3. Obter a configuração de cliente VPN para a opção de autenticação de sua preferência e usá-la para configurar o cliente VPN em seu dispositivo do Windows. Isso lhe permite se conectar-se a redes virtuais do Azure através de uma conexão P2S.

>[!IMPORTANT]
>Se houver alterações na configuração de VPN Ponto a Site depois de gerar o perfil de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, você deve gerar e instalar uma nova configuração de cliente VPN em seus dispositivos do usuário.
>
>

## <a name="adeap"></a>Autenticação de nome de usuário e senha

* **Autenticação do AD:** Um cenário comum é a autenticação de domínio do AD. Nesse cenário, os usuários usam suas credenciais de domínio para se conectar às redes virtuais do Azure. Você pode criar arquivos de configuração de cliente VPN para autenticação RADIUS do AD.

* **Autenticação sem AD:** Você também pode configurar o cenário de autenticação de RADIUS do nome de usuário e senha sem AD.

Certifique-se de que todos os usuários que se conectam possuem credenciais de nome de usuário e senha que podem ser autenticadas por meio do RADIUS. Você só pode criar uma configuração para o protocolo de autenticação EAP-MSCHAPv2 de nome de usuário e senha. '-AuthenticationMethod' é especificado como 'EapMSChapv2'.

### <a name="usernamefiles"></a>Gerar os arquivos de configuração de cliente VPN

Criar a configuração de cliente VPN usando o seguinte comando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Depois de executar o comando, ele retornará um link. Copie e cole o link em um navegador da Web para baixar um arquivo compactado, chamado 'VpnClientConfiguration.zip'. Descompacte o arquivo para ver as seguintes pastas: 
 
* Pastas denominadas 'WindowsAmd64' e 'WindowsX86'. Essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* Uma pasta chamada 'GenericDevice'. Esta pasta contém informações gerais, usadas para criar sua própria configuração de cliente VPN. Ignore essa pasta.
* Se IKEv2 foi configurado quando você criou o gateway de rede virtual, você verá uma pasta chamada 'Mac' que contém um arquivo chamado 'mobileconfig'. Esse arquivo é usado para configurar os clientes Mac.

Você pode recuperar os arquivos de configuração do cliente que você criou. O cmdlet 'Get-AzureRmVpnClientConfiguration' retorna uma URL (link) de onde você pode baixar o arquivo VpnClientConfiguration.zip. Se você fizer alterações na sua configuração de VPN de P2S, tais como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet 'New-AzureRmVpnClientConfiguration' para recriar a configuração.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="adwincli"></a>Configurar um cliente VPN do Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais que recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes](vpn-gateway-vpn-faq.md#P2S).

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 

### <a name="admaccli"></a>Configurar um cliente VPN do Mac (OSX)

1. Selecione o arquivo **VpnClientSetup mobileconfig** e envie-o para cada um dos usuários. Você pode usar o email ou outro método para fazer isso.

2. Localize o arquivo **mobileconfig** no Mac.

  ![localize o arquivo mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Clique duas vezes no perfil para instalá-lo, clique em **Continuar**. O nome do perfil é o mesmo que o nome da sua rede virtual.

  ![instalar](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Clique em **Continuar** para confiar no remetente do perfil e continuar com a instalação.

  ![continuar](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Durante a instalação do perfil, você terá a opção de especificar o nome de usuário e a senha usados para a autenticação de VPN. Não é obrigatório inserir essas informações. Se especificadas, as informações são salvas e usadas automaticamente quando você iniciar uma conexão. Clique em **Instalar** para continuar.

  ![Configurações](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Insira um nome de usuário e senha para os privilégios necessários para instalar o perfil em seu computador. Clique em **OK**.

  ![nome de usuário e senha](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Uma vez instalado, o perfil fica visível na caixa de diálogo **Perfis**. Essa caixa de diálogo também pode ser aberta posteriormente em **Preferências do sistema**.

  ![system preferences]](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Para acessar a conexão VPN, abra a caixa de diálogo **Rede** em **Preferências do sistema**.

  ![rede](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. A conexão VPN aparece como **IkeV2-VPN**. O nome pode ser alterado, atualizando o arquivo **mobileconfig**.

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Clique em **Configurações da Autenticação**. Escolha o **Nome de usuário** na lista suspensa e insira suas credenciais. Se você inseriu as credenciais anteriormente, então o **Nome de usuário** é automaticamente selecionado na lista suspensa e o nome de usuário e senha são pré-populados. Clique em **OK** para salvar as configurações. Isso levará você de volta à caixa de diálogo de Rede.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Clique em **Aplicar** para salvar os detalhes. Para iniciar a conexão, clique em **Conectar**.

## <a name="certeap"></a>Autenticação de certificado
 
Você pode criar arquivos de configuração de cliente VPN para autenticação de certificado RADIUS que usa o protocolo EAP-TLS. Normalmente, um certificado emitido pelo Enterprise é usado para autenticar um usuário para VPN. Certifique-se de que todos os usuários que se conectam possuem um certificado instalado no dispositivo dos usuários e que o certificado pode ser validado pelo servidor RADIUS.
 
* '-AuthenticationMethod' é 'EapTls'.
* Durante a autenticação do certificado, o cliente valida o servidor RADIUS, validando seu certificado. -RadiusRootCert é o arquivo. cer que contém o certificado raiz que é usado para validar o servidor RADIUS.  
* Às vezes, um dispositivo Windows possui vários certificados de cliente. Durante a autenticação, isso pode resultar em uma caixa de diálogo pop-up que lista todos os certificados. O usuário deve, em seguida, escolha o certificado a ser usado. O certificado correto pode ser filtrado especificando o certificado raiz ao qual o certificado de cliente deve estar encadeado. '-ClientRootCert' é o arquivo. cer que contém o certificado raiz. É um parâmetro opcional. Se o dispositivo do qual você deseja se conectar tiver apenas um certificado de cliente, esse parâmetro não precisa ser especificado.

### <a name="certfiles"></a>Gerar os arquvios de configuração de cliente VPN

Criar a configuração de cliente VPN usando o seguinte comando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

O resultado do cmdlet retorna um link. Copie e cole o link em um navegador da Web para baixar um arquivo compactado, chamado 'VpnClientConfiguration.zip'. Descompacte o arquivo para ver as seguintes pastas:

* Pastas denominadas 'WindowsAmd64' e 'WindowsX86'. Essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* Uma pasta chamada 'GenericDevice'. Esta pasta contém informações gerais, usadas para criar sua própria configuração de cliente VPN.

Você pode recuperar os arquivos de configuração do cliente que você criou. O cmdlet 'Get-AzureRmVpnClientConfiguration' retorna uma URL (link) de onde você pode baixar o arquivo VpnClientConfiguration.zip. Se você fizer alterações na sua configuração de VPN de P2S, tais como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet 'New-AzureRmVpnClientConfiguration' para recriar a configuração.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="certwincli"></a>Configurar um cliente VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**. Você também pode salvar o pacote de instalação em outros computadores clientes.
2. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.

### <a name="certmaccli"></a>Configurar um cliente VPN do MAC

Deve ser criado um perfil separado para cada dispositivo do Mac que se conecta à rede virtual do Azure. Isso ocorre porque esses dispositivos exigem o certificado de autenticação do usuário a ser especificado no perfil. A pasta **Genérico** possui todas as informações necessárias para criar um perfil.

  * **VpnSettings.xml** contém configurações importantes, como o tipo de túnel e o endereço do servidor.
  * **VpnServerRoot.cer** contém o certificado raiz necessário para validar o gateway de VPN durante a instalação de conexão P2S.
  * **VpnServerRoot.cer** contém o certificado raiz necessário para validar o servidor RADIUS durante a autenticação.

Use as seguintes etapas para configurar o cliente VPN do Mac nativo para autenticação de certificado:

1. Importar os certificados raiz **VpnServerRoot** e **RadiusServerRoot** para o Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele.  
Clique em **Adicionar** para importar.

  **Adicionar VpnServerRoot**

  ![adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **Adicionar RadiusServerRoot**

  ![adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Abra a caixa de diálogo **Rede** em **Preferências de rede** e clique em **'+'** para criar um novo perfil de conexão de cliente VPN para uma conexão de P2S para a rede virtual do Azure.

  O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **Nome do serviço** e, em seguida, clique em **Criar** para criar o perfil de conexão de cliente VPN.

  ![rede](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil. Deixe o campo **ID Local** em branco.

  ![informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. Clique em **Configurações de autenticação** e selecione **Certificado**. 

  ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. Clique em **Selecionar...** para escolher o certificado que você deseja usar para autenticação.

  ![certificado](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, clique em **Continuar**.

  ![identidade](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. No campo **ID Local**, especifique o nome do certificado (da Etapa 5). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para salvar as alterações.

  ![aplicar](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. Na caixa de diálogo **Rede**, clique em **Aplicar** para salvar todas as alterações. Em seguida, clique em **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="otherauth"></a>Outros tipos de autenticação ou protocolos

Para usar um tipo de autenticação diferente (por exemplo, OTP) e não nome de usuário/senha ou certificados ou para usar um protocolo de autenticação diferentes (como PEAP-MSCHAPv2, em vez de EAP-MSCHAPv2), você deve criar seu próprio perfil de configuração de cliente VPN. Para criar o perfil, você precisa de informações como o endereço IP do gateway de rede virtual, tipo de túnel e rotas de túnel dividido. Você pode obter essas informações através das seguintes etapas:

1. Use o cmdlet 'Get-AzureRmVpnClientConfiguration' para gerar a configuração do cliente VPN para EapMSChapv2. Para obter instruções, confira [esta seção](#ccradius) do artigo.

2. Descompacte o arquivo VpnClientConfiguration.zip e procure a pasta GenenericDevice. Ignore as pastas que contendo os instaladores do Windows para arquiteturas de 64 bits e de 32 bits.
 
3. A pasta GenenericDevice contém um arquivo XML chamado VpnSettings. Este arquivo contém todas as informações necessárias.

  * VpnServer - FQDN do gateway de VPN do Azure. Este é o endereço ao qual o cliente se conecta.
  * VpnType - o tipo de túnel que você usa para se conectar.
  * Routes - As rotas que você precisa configurar no seu perfil para que somente o tráfego de saída da rede virtual do Azure seja enviada por meio do túnel de P2S.
  * A pasta GenenericDevice também contém um arquivo .cer chamado 'VpnServerRoot'. Este arquivo contém o certificado raiz necessário para validar o gateway de VPN durante a instalação de conexão de P2S. Instale o certificado em todos os dispositivos que irão se conectar à rede virtual do Azure. 
 
## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](point-to-site-how-to-radius-ps.md).

