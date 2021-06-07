---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026505"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Criar um certificado raiz autoassinado

Utilize o cmdlet New-SelfSignedCertificate para criar um certificado raiz autoassinado. Para obter informações adicionais sobre os parâmetros, consulte [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

1. Em um computador com Windows 10 ou Windows Server 2016, abra um console do Windows PowerShell com privilégios elevados. Esses exemplos não funcionam no modo "Experimentar" do Azure Cloud Shell. Você deve executar esses exemplos localmente.
1. Use o exemplo a seguir para criar o certificado raiz autoassinado. O exemplo a seguir cria um certificado raiz autoassinado chamado 'P2SRootCert' que é instalado automaticamente em 'Certificates-Current User\Personal\Certificates'. Exiba o certificado abrindo *certmgr.msc* ou *Gerenciar Certificados de Usuário*.

   Entre usando o `Connect-AzAccount` cmdlet. Em seguida, execute o exemplo a seguir com as modificações necessárias.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Deixe o console do PowerShell aberto e prossiga com as próximas etapas para gerar certificados de cliente.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Gerar um certificado do cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.

Os exemplos utilizam o cmdlet New-SelfSignedCertificate para gerar um certificado do cliente que expira em um ano. Para informações de parâmetros adicionais, como definir um valor de expiração diferente para o certificado do cliente, consulte [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Exemplo 1-a sessão do console do PowerShell ainda está aberta

Use este exemplo se você não tiver fechado o console do PowerShell depois de criar o certificado raiz autoassinado. Este exemplo continua da seção anterior e usa a variável declarada '$cert'. Se você fechou o console do PowerShell depois de criar o certificado raiz autoassinado ou estiver criando certificados de cliente adicionais em uma nova sessão de console do PowerShell, use as etapas no [exemplo 2](#ex2).

Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'.  Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Exemplo 2 – nova sessão de console do PowerShell

Se você estiver criando certificados do cliente adicionais ou não estiver usando a mesma sessão do PowerShell usada para criar o certificado raiz autoassinado, use as seguintes etapas:

1. Identifique o certificado raiz autoassinado instalado no computador. Esse cmdlet retorna uma lista de certificados instalados no seu computador.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Localize o nome da entidade da lista retornada e copie a impressão digital localizada ao lado dela para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado raiz autoassinado do qual você deseja gerar um certificado filho. Nesse caso, 'P2SRootCert'.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Declare uma variável para o certificado raiz usando a impressão digital da etapa anterior. Substitua THUMBPRINT pela impressão digital do certificado raiz do qual você deseja gerar um certificado filho.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, usando a impressão digital para P2SRootCert na etapa anterior, a variável teria esta aparência:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'. Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportar a chave pública de certificado raiz (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado raiz autoassinado e a chave privada para armazená-lo (opcional)

Convém exportar o certificado raiz autoassinado e armazená-lo com segurança como backup. Se necessário, você poderá instalá-lo posteriormente em outro computador e gerar mais certificados do cliente. Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Exportar o certificado do cliente

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]