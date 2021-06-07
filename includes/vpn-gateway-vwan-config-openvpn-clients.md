---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80986797"
---
## <a name="windows-clients"></a><a name="windows"></a>Clientes do Windows

1. Baixe e instale o cliente do OpenVPN (versão 2,4 ou superior) no site oficial do [OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure ou com "New-AzVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Em seguida, abra o arquivo de configuração *vpnconfig.ovpn* da pasta OpenVPN usando o Bloco de Notas.
4. Exporte o certificado de cliente ponto a site criado e carregado para a configuração do P2S no gateway. Use os seguintes links de artigo:

   * Instruções do [Gateway de VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Instruções de [Wan virtual](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extraia a chave privada e a impressão digital base64 do *.pfx*. Há várias maneiras de realizar isso. Usar o OpenSSL no computador é uma maneira. O arquivo *profileinfo.txt* contém a chave privada e a impressão digital da CA e do certificado do Cliente. Certifique-se de usar a impressão digital do certificado do cliente.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Abra *profileinfo.txt* no Bloco de Notas. Para obter a impressão digital do certificado cliente (filho), selecione o texto (incluindo e entre) "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" do certificado filho e copie-o. Você pode identificar o certificado filho observando o assunto =/linha.
7. Alterne para o arquivo *vpnconfig.ovpn* que você abriu no Bloco de Notas na etapa 3. Localize a seção mostrada abaixo e substitua tudo entre "cert" e "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra *profileinfo.txt* no Bloco de Notas. Para obter a chave privada, selecione o texto (incluindo e entre) "-----iniciar a chave privada-----" e "-----END PRIVATE KEY-----" e copie-o.
9. Retorne para o arquivo vpnconfig.ovpn no Bloco de Notas e localize esta seção. Cole a chave privada substituindo tudo entre e "key" e "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
11. Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
12. Clique com botão direito no ícone OpenVPN na bandeja do sistema e clique em conectar.

## <a name="mac-clients"></a><a name="mac"></a>Clientes Mac

1. Baixe e instale um cliente OpenVPN, como [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure ou usando "New-AzVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Abra o arquivo de configuração vpnconfig. ovpn da pasta OpenVPN em um editor de texto.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Use os links de artigo a seguir para obter informações sobre como exportar um certificado para obter a chave pública codificada:

   * Instruções do [Gateway de VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instruções de [Wan virtual](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Consulte [exportar sua chave privada](https://openvpn.net/community-resources/how-to/#pki) no site do OpenVPN para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7. Clique duas vezes no arquivo de perfil para criar o perfil em Tunnelblick.
8. Inicie o Tunnelblick na pasta aplicativos.
9. Clique no ícone de Tunnelblick na bandeja do sistema e selecione conectar.

> [!IMPORTANT]
>Somente iOS 11.0 e posteriores e MacOS 10.13 e posteriores são compatíveis com o protocolo OpenVPN.
>
## <a name="ios-clients"></a><a name="iOS"></a>clientes iOS

1. Instale o cliente OpenVPN (versão 2,4 ou superior) na loja de aplicativos.
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure ou usando "New-AzVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Abra o arquivo de configuração vpnconfig. ovpn da pasta OpenVPN em um editor de texto.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Use os links de artigo a seguir para obter informações sobre como exportar um certificado para obter a chave pública codificada:

   * Instruções do [Gateway de VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instruções de [Wan virtual](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Consulte [exportar sua chave privada](https://openvpn.net/community-resources/how-to/#pki) no site do OpenVPN para obter informações sobre como extrair uma chave privada.
6. Não altere os outros campos.
7. Envie por email o arquivo de perfil (. ovpn) para sua conta de email configurada no aplicativo de email em seu iPhone. 
8. Abra o email no aplicativo de email no iPhone e toque no arquivo anexado

    ![Abrir email](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Toque em **mais** se você não vir **a opção Copiar para OpenVPN**

    ![Mais](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Toque em **copiar para OpenVPN** 

    ![Copiar para OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Toque em **Adicionar** na página **importar perfil**

    ![Adicionar](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Toque em **Adicionar** na página de **perfil importado**

    ![Toque em Adicionar](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Inicie o aplicativo OpenVPN e deslize a opção na página **perfil** diretamente para conectar

    ![Conectar](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de Terminal. Você pode abrir uma nova sessão pressionando simultaneamente "Ctrl + Alt + t".
2. Insira o comando a seguir para instalar os componentes necessários:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure.
4. Exporte o certificado de cliente P2S que você criou e carregou para sua configuração P2S no gateway. Use os seguintes links de artigo:

   * Instruções do [Gateway de VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Instruções de [Wan virtual](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extraia a chave privada e a impressão digital base64 do .pfx. Há várias maneiras de realizar isso. Usar OpenSSL no computador é uma maneira.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   O arquivo *profileinfo.txt* conterá a chave privada e a impressão digital da CA e do certificado do Cliente. Certifique-se de usar a impressão digital do certificado do cliente.

6. Abra *profileinfo.txt* em um editor de texto. Para obter a impressão digital do certificado cliente (filho), selecione o texto incluindo e entre "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" do certificado filho e copie-o. Você pode identificar o certificado filho observando o assunto =/linha.

7. Abra o arquivo *vpnconfig.ovpn* localize a seção mostrada abaixo. Substitua tudo entre o e "cert" e "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Abra o profileinfo.txt em um editor de texto. Para obter a chave privada, selecione o texto que inclui e entre "-----BEGIN PRIVATE KEY-----" e "-----END PRIVATE KEY-----" e copie-o.

9. Abra o arquivo vpnconfig.ovpn em um editor de texto e localize esta seção. Cole a chave privada substituindo tudo entre e "key" e "/key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
11. Para conectar usando a linha de comando, digite o seguinte comando:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Para se conectar usando o GUI, acesse as configurações do sistema.
13. Clique **+** para adicionar uma nova conexão VPN.
14. Em **Adicionar VPN**, escolha **Importar do arquivo...**
15. Navegue até o arquivo de perfil e clique duas vezes ou escolha **Abrir**.
16. Clique em **Adicionar** na janela **Adicionar VPN**.
  
    ![Importar do arquivo](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Você pode se conectar **LIGANDO** a VPN na página **Configurações de Rede**, ou no ícone de rede na bandeja do sistema.
