---
title: Gerar um certificado autoassinado com uma AC raiz personalizada
titleSuffix: Azure Application Gateway
description: Saiba como gerar um certificado autoassinado do Gateway de Aplicativo do Azure com uma AC raiz personalizada.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93396933"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Gerar um certificado autoassinado do Gateway de Aplicativo do Azure com uma AC raiz personalizada

A SKU do Gateway de Aplicativo v2 introduz o uso de certificados raiz confiáveis para permitir o acesso de servidores back-end. Isso remove os certificados de autenticação necessários na SKU da v1. O *certificado raiz* tem um formato de codificação Base 64 X.509(.CER) do servidor de certificados back-end. Ele identifica a AC (autoridade de certificação) raiz que emitiu o certificado do servidor, e esse certificado é usado para a comunicação TLS/SSL.

O Gateway de Aplicativo confiará no certificado do site por padrão se ele estiver assinado por uma AC conhecida (por exemplo, GoDaddy ou DigiCert). Neste caso, você não precisa carregar explicitamente o certificado raiz. Para saber mais, confira [Visão geral da terminação de TLS e TLS de ponta a ponta com um Gateway de Aplicativo](ssl-overview.md). No entanto, se você tiver um ambiente de desenvolvimento/teste e não quiser comprar um certificado assinado por AC, poderá criar sua própria AC personalizada e criar um certificado autoassinado com ela. 

> [!NOTE]
> Por padrão, os certificados autoassinados não são confiáveis e podem ser difíceis de manter. Além disso, eles podem usar códigos hash e conjuntos de criptografia desatualizados que podem não ser fortes. Para obter mais segurança, adquira um certificado assinado por uma autoridade de certificação conhecida.

Neste artigo, você aprenderá a:

- Criar sua própria autoridade de certificação personalizada
- Criar um certificado autoassinado assinado por sua AC personalizada
- Carregar um certificado raiz autoassinado em um Gateway de Aplicativo para autenticar o servidor back-end

## <a name="prerequisites"></a>Pré-requisitos

- **[OpenSSL](https://www.openssl.org/) em um computador executando Windows ou Linux** 

   Embora possa haver outras ferramentas disponíveis para o gerenciamento de certificados, este tutorial usa OpenSSL. Você encontra o OpenSSL agrupado com várias distribuições do Linux, como o Ubuntu.
- **Um servidor Web**

   Por exemplo, Apache, IIS ou NGINX para testar os certificados.

- **SKU do Gateway de Aplicativo v2**
   
  Se você não tiver um gateway de aplicativo, confira [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Criar um certificado de AC raiz

Crie seu certificado de autoridade de certificação (AC) raiz usando o OpenSSL.

### <a name="create-the-root-key"></a>Criar a chave raiz

1. Conecte-se ao computador em que o OpenSSL está instalado e execute o comando a seguir. Isso cria uma chave protegida por senha.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Quando solicitado, digite uma senha forte. Por exemplo, pelo menos nove caracteres, usando letras maiúsculas, letras minúsculas, números e símbolos.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Criar um certificado raiz e assiná-lo automaticamente

1. Use os comandos a seguir para gerar o csr e o certificado.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Os comandos anteriores criam o certificado raiz. Você o usará para assinar o certificado do servidor.

1. Quando solicitado, digite a senha da chave raiz e as informações organizacionais da AC personalizada, como país/região, estado, org, UO e o nome de domínio totalmente qualificado (é o domínio do emissor).

   ![criar certificado raiz](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Criar um certificado do servidor

Em seguida, você criará um certificado de servidor usando o OpenSSL.

### <a name="create-the-certificates-key"></a>Criar a chave do certificado

Use o comando a seguir para gerar a chave do certificado do servidor.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Criar a CSR (solicitação de assinatura de certificado)

A CSR é uma chave pública fornecida a uma AC na solicitação de um certificado. A AC emite o certificado para essa solicitação específica.

> [!NOTE]
> O CN (nome comum) do certificado do servidor deve ser diferente do domínio do emissor. Por exemplo, neste caso, o CN do emissor é `www.contoso.com` e o CN do certificado do servidor é `www.fabrikam.com`.


1. Use o comando a seguir para gerar a CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando solicitado, digite a senha da chave raiz e as informações organizacionais da AC personalizada: país/região, estado, org, UO e o nome de domínio totalmente qualificado. É o domínio do site e deve ser diferente do emissor.

   ![Certificado do servidor](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Gerar o certificado com a CSR e a chave e assiná-lo com a chave raiz da AC

1. Use o comando a seguir para criar o certificado:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verificar o certificado recém-criado

1. Use o comando a seguir para imprimir a saída do arquivo CRT e verificar seu conteúdo:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verificação do certificado](media/self-signed-certificates/verify-cert.png)

1. No seu diretório, verifique se você tem os seguintes arquivos:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Configurar o certificado nas configurações de TLS do servidor Web

No servidor Web, configure o TLS usando os arquivos fabrikam.crt e fabrikam.key. Se o servidor Web não puder usar dois arquivos, combine-os em um único arquivo .pem ou. pfx usando comandos do OpenSSL.

### <a name="iis"></a>IIS

Para obter instruções sobre como importar o certificado e carregá-lo como certificado do servidor no IIS, confira [COMO: Instalar certificados importados em um servidor Web no Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Para obter instruções de vinculação de TLS, confira [Como configurar SSL no IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

A configuração a seguir é um exemplo de [host virtual configurado para SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) no Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

A configuração a seguir é um exemplo de [bloqueios de servidor NGINX](https://nginx.org/docs/http/configuring_https_servers.html) com a configuração de TLS:

![NGINX com TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Acessar o servidor para verificar a configuração

1. Adicione o certificado raiz ao repositório de raiz confiável do computador. Ao acessar o site, verifique se toda a cadeia de certificados é vista no navegador.

   ![Certificados raiz confiáveis](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Supõe-se que o DNS foi configurado para apontar o nome do servidor Web (neste exemplo, www.fabrikam.com) para o endereço IP do seu servidor Web. Caso contrário, edite o [arquivo de host](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) para resolver o nome.
1. Navegue até seu site e clique no ícone de cadeado, na caixa de endereço do navegador, para verificar as informações do site e do certificado.

## <a name="verify-the-configuration-with-openssl"></a>Verificar a configuração com OpenSSL

Você também pode usar OpenSSL para verificar o certificado.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificação de certificado OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Carregar o certificado raiz nas configurações de HTTP do Gateway de Aplicativo

Para carregar o certificado no Gateway de Aplicativo, você deve exportar o certificado .crt para um formato .cer de codificação Base 64. Como o .cert já contém a chave pública no formato de codificação Base 64, basta renomear a extensão de arquivo de .crt para .cer. 

### <a name="azure-portal"></a>Portal do Azure

Para carregar o certificado raiz confiável do portal, selecione **Configurações de HTTP** e escolha o protocolo **HTTPS**.

![Adicionar um certificado secundário usando o portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Você também pode usar o CLI do Azure ou o Azure PowerShell para carregar o certificado raiz. O código a seguir é um exemplo do Azure PowerShell.

> [!NOTE]
> O exemplo a seguir adiciona um certificado raiz confiável ao Gateway de Aplicativo, cria uma nova configuração de HTTP e adiciona uma nova regra, supondo que o pool do back-end e o ouvinte já existam.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Verificar a integridade do back-end do Gateway de Aplicativo

1. Clique na exibição **Integridade de back-end** do seu gateway de aplicativo para verificar se a investigação está íntegra.
1. O Status da investigação HTTPS deve ser **Healthy**.

![Investigação HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre SSL\TLS no Gateway de Aplicativo, confira [Visão geral do término de TLS e TLS de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md).