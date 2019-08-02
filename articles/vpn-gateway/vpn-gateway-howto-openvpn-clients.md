---
title: Como configurar clientes do OpenVPN para o Gateway de VPN do Azure | Microsoft Docs
description: Etapas para configurar clientes do OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: b8f1626da730178d2cd9c2f31c4f9876102b3d46
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477837"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes de OpenVPN para o Gateway de VPN do Azure

Este artigo ajuda você a configurar **OpenVPN® protocolo** clientes.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verifique se você concluiu as etapas para configurar o OpenVPN para seu gateway de VPN. Para obter detalhes, confira [Configurar o OpenVPN para Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Clientes do Windows

1. Baixe e instale o cliente OpenVPN (versão 2.4 ou superior) do oficial [OpenVPN site](https://openvpn.net/index.php/open-source/downloads.html).
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure ou com "New-AzVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Em seguida, abra o arquivo de configuração *vpnconfig.ovpn* da pasta OpenVPN usando o Bloco de Notas.
4. [Exporte](vpn-gateway-certificates-point-to-site.md#clientexport) o certificado de cliente P2S que você criou e carregou para sua configuração P2S no gateway.
5. Extraia a chave privada e a impressão digital base64 do *.pfx*. Há várias maneiras de fazer isso. Usar o OpenSSL no computador é uma maneira. O arquivo *profileinfo.txt* contém a chave privada e a impressão digital da CA e do certificado do Cliente. Certifique-se de usar a impressão digital do certificado do cliente.

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
8. Abra *profileinfo.txt* no Bloco de Notas. Para obter a chave privada, selecione o texto (incluindo e entre) "---BEGIN PRIVATE KEY---" e "---END PRIVATE KEY---" e copie-o.
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

## <a name="mac"></a>Clientes Mac

1. Baixe e instale um cliente OpenVPN, tais como [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure ou usando "New-AzVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no Bloco de notas.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Confira [Exportar a chave pública](vpn-gateway-certificates-point-to-site.md#cer) para obter informações sobre como exportar um certificado e obter a chave pública codificada.
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Consulte [Exportar a chave privada](https://openvpn.net/community-resources/how-to/#pki) para obter mais informações sobre como extrair uma chave privada.
6. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7. Clique duas vezes o arquivo de perfil para criar o perfil no Tunnelblick.
8. Inicie o Tunnelblick da pasta de aplicativos.
9. Clique no ícone na bandeja do sistema Tunnelblick e conecte-se de escolher.

> [!IMPORTANT]
>Somente iOS 11.0 e posteriores e MacOS 10.13 e posteriores são compatíveis com o protocolo OpenVPN.
>

## <a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de Terminal. Você pode abrir uma nova sessão pressionando simultaneamente "Ctrl + Alt + t".
2. Insira o comando a seguir para instalar os componentes necessários:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configuração ponto a site no portal do Azure.
4. [Exporte](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) o certificado de cliente P2S que você criou e carregou para sua configuração P2S no gateway. 
5. Extraia a chave privada e a impressão digital base64 do .pfx. Há várias maneiras de fazer isso. Usar OpenSSL no computador é uma maneira.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
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
8. Abra o profileinfo.txt em um editor de texto. Para obter a chave privada, selecione o texto, inclusive e entre "---BEGIN PRIVATE KEY---" e "---END PRIVATE KEY---" e copie-o.

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
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Para se conectar usando o GUI, acesse as configurações do sistema.
13. Clique em **+** para adicionar uma nova conexão VPN.
14. Em **Adicionar VPN**, escolha **Importar do arquivo...**
15. Navegue até o arquivo de perfil e clique duas vezes ou escolha **Abrir**.
16. Clique em **Adicionar** na janela **Adicionar VPN**.
  
    ![Importar do arquivo](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Você pode se conectar **LIGANDO** a VPN na página **Configurações de Rede**, ou no ícone de rede na bandeja do sistema.

## <a name="next-steps"></a>Próximas etapas

Se você quiser que os clientes VPN para ser capaz de acessar recursos em outra rede virtual, em seguida, siga as instruções na [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artigo para configurar uma conexão de rede virtual para rede virtual. Certifique-se de habilitar o BGP nos gateways e nas conexões, caso contrário, o tráfego não fluirá.

**"OpenVPN" é uma marca registrada da Inc OpenVPN.**
