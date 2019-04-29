---
title: 'Gerar e exportar certificados para ponto a site: PowerShell: Azure | Microsoft Docs'
description: Crie um certificado raiz autoassinado, exportar a chave pública e gerar os certificados do cliente usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60763725"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para Ponto a Site usando o PowerShell

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente utilizando o PowerShell no Windows 10 ou no Windows Server 2016. Se você estiver procurando por instruções de certificado diferentes, confira [Certificados – Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Execute as etapas deste artigo em um computador que executa o Windows 10 ou no Windows Server 2016. Os cmdlets do PowerShell que você utiliza para gerar certificados são parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 é necessário apenas para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte. 

Se você não tiver acesso a um computador com Windows 10 ou Windows Server 2016 poderá utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que você gerar utilizando qualquer um dos métodos podem ser instalados em qualquer sistema operacional cliente [com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

## <a name="rootcert"></a>1. Criar um certificado raiz autoassinado

Utilize o cmdlet New-SelfSignedCertificate para criar um certificado raiz autoassinado. Para obter informações adicionais sobre os parâmetros, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Em um computador com Windows 10 ou Windows Server 2016, abra um console do Windows PowerShell com privilégios elevados. Esses exemplos não funcionam no modo "Experimentar" do Azure Cloud Shell. Você deve executar esses exemplos localmente.
2. Use o exemplo a seguir para criar o certificado raiz autoassinado. O exemplo a seguir cria um certificado raiz autoassinado chamado 'P2SRootCert' que é instalado automaticamente em 'Certificates-Current User\Personal\Certificates'. Exiba o certificado abrindo *certmgr.msc* ou *Gerenciar Certificados de Usuário*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Gerar um certificado de cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.

Os exemplos utilizam o cmdlet New-SelfSignedCertificate para gerar um certificado do cliente que expira em um ano. Para informações de parâmetros adicionais, como definir um valor de expiração diferente para o certificado do cliente, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exemplo 1

Use este exemplo se você não tiver fechado o console do PowerShell depois de criar o certificado raiz autoassinado. Este exemplo continua da seção anterior e usa a variável declarada '$cert'. Se você fechou o console do PowerShell após criar o certificado raiz autoassinado ou está criando certificados do cliente adicionais em uma nova sessão de console do PowerShell, use as etapas no [Examplo 2](#ex2).

Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'.  Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemplo 2

Se você estiver criando certificados do cliente adicionais ou não estiver usando a mesma sessão do PowerShell usada para criar o certificado raiz autoassinado, use as seguintes etapas:

1. Identifique o certificado raiz autoassinado instalado no computador. Esse cmdlet retorna uma lista de certificados instalados no seu computador.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Localize o nome da entidade da lista retornada e copie a impressão digital localizada ao lado dela para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado raiz autoassinado do qual você deseja gerar um certificado filho. Nesse caso, 'P2SRootCert'.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declare uma variável para o certificado raiz usando a impressão digital da etapa anterior. Substitua THUMBPRINT pela impressão digital do certificado raiz do qual você deseja gerar um certificado filho.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, usando a impressão digital para P2SRootCert na etapa anterior, a variável teria esta aparência:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'. Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Exportar a chave pública de certificado raiz (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado raiz autoassinado e a chave privada para armazená-lo (opcional)

Convém exportar o certificado raiz autoassinado e armazená-lo com segurança como backup. Se necessário, você poderá instalá-lo posteriormente em outro computador e gerar mais certificados do cliente. Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="clientexport"></a>4. Exportar o certificado do cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalar um certificado do cliente exportado

Cada cliente que conecta-se à VNet através de uma conexão P2S requer um certificado do cliente para ser instalado localmente.

Para instalar um certificado de cliente, confira [Instalar um certificado de cliente para conexões Ponto a Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Continuar com as etapas de configuração P2S

Continue com a configuração de Ponto a Site.

* Para conferir as etapas do modelo de implantação do **Gerenciador de Recursos**, confira [Configurar P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
