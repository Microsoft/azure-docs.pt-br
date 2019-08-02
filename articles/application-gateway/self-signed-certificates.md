---
title: Gerar um certificado autoassinado de Aplicativo Azure gateway com uma autoridade de certificação raiz Personalizada
description: Saiba como gerar um certificado autoassinado de Aplicativo Azure gateway com uma autoridade de certificação raiz Personalizada
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0b97f2f6df87255e10faaf58c40ea9136354bff6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386298"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Gerar um certificado autoassinado de Aplicativo Azure gateway com uma autoridade de certificação raiz Personalizada

O SKU do gateway de aplicativo v2 apresenta o uso de certificados raiz confiáveis para permitir servidores de back-end. Isso remove os certificados de autenticação que foram necessários na SKU v1. O *certificado raiz* é um X. 509 codificado em Base-64 (. CER) formato de certificado raiz do servidor de certificado de back-end. Ele identifica a AC (autoridade de certificação) raiz que emitiu o certificado do servidor e o certificado do servidor é usado para a comunicação SSL.

O gateway de aplicativo confia no certificado do site por padrão se ele é assinado por uma autoridade de certificação conhecida (por exemplo, GoDaddy ou DigiCert). Você não precisa carregar explicitamente o certificado raiz nesse caso. Para obter mais informações, consulte [visão geral da terminação SSL e SSL de ponta a ponta com o gateway de aplicativo](ssl-overview.md). No entanto, se você tiver um ambiente de desenvolvimento/teste e não quiser comprar um certificado assinado por uma autoridade de certificação, poderá criar sua própria AC personalizada e criar um certificado autoassinado com ele. 

> [!NOTE]
> Os certificados autoassinados não são confiáveis por padrão e podem ser difíceis de manter. Além disso, eles podem usar hash desatualizado e conjuntos de codificação que podem não ser fortes. Para obter mais segurança, adquira um certificado assinado por uma autoridade de certificação conhecida.

Neste artigo, você aprenderá a:

- Criar sua própria autoridade de certificação personalizada
- Criar um certificado autoassinado assinado por sua autoridade de certificação personalizada
- Carregar um certificado raiz autoassinado para um gateway de aplicativo para autenticar o servidor de back-end

## <a name="prerequisites"></a>Pré-requisitos

- **[OpenSSL](https://www.openssl.org/) em um computador executando Windows ou Linux** 

   Embora possa haver outras ferramentas disponíveis para o gerenciamento de certificados, este tutorial usa OpenSSL. Você pode encontrar o OpenSSL agrupado com muitas distribuições do Linux, como o Ubuntu.
- **Um servidor Web**

   Por exemplo, Apache, IIS ou NGINX para testar os certificados.

- **Um SKU do gateway de aplicativo v2**
   
  Se você não tiver um gateway de aplicativo existente, [consulte início rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Criar um certificado de autoridade de certificação raiz

Crie seu certificado de autoridade de certificação raiz usando OpenSSL.

### <a name="create-the-root-key"></a>Criar a chave raiz

1. Entre no computador em que o OpenSSL está instalado e execute o comando a seguir. Isso cria uma chave protegida por senha.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. No prompt, digite uma senha forte. Por exemplo, pelo menos nove caracteres, usando letras maiúsculas, letras minúsculas, números e símbolos.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Criar um certificado raiz e autosigná-lo

1. Use os comandos a seguir para gerar o CSR e o certificado.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Os comandos anteriores criam o certificado raiz. Você usará isso para assinar o certificado do servidor.

1. Quando solicitado, digite a senha para a chave raiz e as informações organizacionais para a autoridade de certificação personalizada, como país, estado, org, UO e o nome de domínio totalmente qualificado (esse é o domínio do emissor).

   ![criar certificado raiz](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Criar um certificado do servidor

Em seguida, você criará um certificado de servidor usando o OpenSSL.

### <a name="create-the-certificates-key"></a>Criar a chave do certificado

Use o comando a seguir para gerar a chave para o certificado do servidor.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Criar o CSR (solicitação de assinatura de certificado)

O CSR é uma chave pública que é dada a uma CA ao solicitar um certificado. A CA emite o certificado para essa solicitação específica.

> [!NOTE]
> O CN (nome comum) para o certificado do servidor deve ser diferente do domínio do emissor. Por exemplo, nesse caso, o CN para o emissor é www.contoso.com e o CN do certificado do servidor é www.fabrikam.com


1. Use o seguinte comando para gerar o CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando solicitado, digite a senha para a chave raiz e as informações organizacionais para a autoridade de certificação personalizada: País, estado, org, UO e o nome de domínio totalmente qualificado. Esse é o domínio do site e deve ser diferente do emissor.

   ![Certificado do servidor](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Gere o certificado com o CSR e a chave e assine-o com a chave raiz da autoridade de certificação

1. Use o seguinte comando para criar o certificado:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verificar o certificado recém-criado

1. Use o comando a seguir para imprimir a saída do arquivo CRT e verificar seu conteúdo:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verificação de certificado](media/self-signed-certificates/verify-cert.png)

1. Verifique os arquivos em seu diretório e verifique se você tem os seguintes arquivos:

   - contoso. CRT
   - contoso. Key
   - fabrikam. CRT
   - fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Configurar o certificado nas configurações de SSL do seu servidor Web

No servidor Web, configure o SSL usando os arquivos fabrikam. CRT e fabrikam. Key. Se o servidor Web não puder usar dois arquivos, você poderá combiná-los em um único arquivo. PEM ou. pfx usando comandos do OpenSSL.

### <a name="iis"></a>IIS

Para obter instruções sobre como importar o certificado e carregá-los como certificado do servidor no [IIS, consulte Como: Instalar certificados importados em um servidor Web no Windows](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)Server 2003.

Para obter instruções de associação SSL, consulte [como configurar o SSL no IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

A configuração a seguir é um [host virtual de exemplo configurado para SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) no Apache:

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

A configuração a seguir é um [bloco de servidor Nginx](http://nginx.org/docs/http/configuring_https_servers.html) de exemplo com configuração SSL:

![NGINX com SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Acessar o servidor para verificar a configuração

1. Adicione o certificado raiz ao repositório de raiz confiável do computador. Ao acessar o site, certifique-se de que toda a cadeia de certificados seja vista no navegador.

   ![Certificados raiz confiáveis](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Supõe-se que o DNS foi configurado para apontar o nome do servidor Web (neste exemplo, www.fabrikam.com) para o endereço IP do seu servidor Web. Caso contrário, você pode editar o [arquivo](https://answers.microsoft.com/windows/forum/windows_10-other_settings-winpc/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) de hosts para resolver o nome.
1. Navegue até seu site e clique no ícone de cadeado na caixa de endereço do navegador para verificar as informações do site e do certificado.

## <a name="verify-the-configuration-with-openssl"></a>Verificar a configuração com OpenSSL

Ou, você pode usar o OpenSSL para verificar o certificado.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificação de certificado OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Carregar o certificado raiz nas configurações de HTTP do gateway de aplicativo

Para carregar o certificado no gateway de aplicativo, você deve exportar o certificado. CRT para um formato. cer base-64 codificado. Como. o CRT já contém a chave pública no formato codificado base-64, basta renomear a extensão de arquivo de. CRT para. cer. 

### <a name="azure-portal"></a>Portal do Azure

Para carregar o certificado raiz confiável do portal, selecione as **configurações de http** e escolha o protocolo **https** .

![Adicionar um certificado usando o portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Ou, você pode usar CLI do Azure ou Azure PowerShell para carregar o certificado raiz. O código a seguir é um exemplo de Azure PowerShell.

> [!NOTE]
> O exemplo a seguir adiciona um certificado raiz confiável ao gateway de aplicativo, cria uma nova configuração de HTTP e adiciona uma nova regra, supondo que o pool de back-end e o ouvinte já existam.

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

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
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
### <a name="verify-the-application-gateway-backend-health"></a>Verificar a integridade do back-end do gateway de aplicativo

1. Clique na exibição **integridade de back-end** do seu gateway de aplicativo para verificar se a investigação está íntegra.
1.  Você deve ver que o status é **íntegro** para a investigação de HTTPS.

    ![Investigação de HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SSL\TLS no gateway de aplicativo, consulte [visão geral da terminação SSL e SSL de ponta a ponta com o gateway de aplicativo](ssl-overview.md).

