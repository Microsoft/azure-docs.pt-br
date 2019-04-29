---
title: 'Gerar e exportar certificados para ponto a site: MakeCert : Azure | Microsoft Docs'
description: Criar um certificado raiz autoassinado, exportar a chave pública e gerar certificados do cliente usando o MakeCert.
services: vpn-gateway
documentationcenter: na
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 09/05/2018
ms.date: 10/01/2018
ms.author: v-jay
ms.openlocfilehash: 973c0aa3bd187e963f15adbe34955d6bc9fa612d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768099"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para conexões Ponto a Site usando o MakeCert

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente usando o MakeCert. Se você estiver procurando por instruções de certificado diferentes, confira [Certificados – PowerShell](vpn-gateway-certificates-point-to-site.md) ou [Certificados – Linux](vpn-gateway-certificates-point-to-site-linux.md).

Enquanto é recomendável usar o [etapas do Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) para criar seus certificados, fornecemos essas instruções MakeCert como um método opcional. Os certificados que você gerar utilizando qualquer um dos métodos podem ser instalados em [qualquer sistema operacional cliente com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). No entanto, o MakeCert tem a seguinte limitação:

* O MakeCert foi preterido. Isso significa que essa ferramenta pode ser removida a qualquer momento. Todos os certificados que você já tiver gerado usando MakeCert não serão afetados quando o MakeCert não estiver mais disponível. MakeCert só é usado para gerar os certificados, não como um mecanismo de validação.

## <a name="rootcert"></a>Criar um certificado raiz autoassinado

As etapas a seguir mostram como criar um certificado autoassinado usando o MakeCert. Essas etapas não são específicas do modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

1. Baixe e instale o [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Após a instalação, você geralmente pode encontrar o utilitário makecert.exe sob esse caminho: ' C:\Program arquivos (x86) \Windows Kits\10\bin\<arch >'. Embora, é possível que ele tenha sido instalado em outro local. Abra um prompt de comando como administrador e navegue até o local do utilitário MakeCert. Você pode usar o exemplo a seguir, ajustando para o local apropriado:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Em seguida, crie e instale um certificado no repositório de certificados Pessoal em seu computador. O exemplo a seguir cria um arquivo *.cer* correspondente que você carrega no Azure ao configurar P2S. Substitua “P2SRootCert” e “P2SRootCert.cer” pelo nome que você deseja usar para o certificado. O certificado está localizado em seus 'Certificados – Current User\Personal\Certificates'.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="cer"></a>Exportar a chave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O arquivo exported.cer deve ser carregado no Azure. Para obter instruções, consulte [Configurar uma conexão ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Para adicionar um certificado raiz confiável adicional, consulte [esta seção](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) do artigo.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado autoassinado e a chave privada para armazená-lo (opcional)

Convém exportar o certificado raiz autoassinado e armazená-lo com segurança. Se precisar, você pode instalá-lo depois em outro computador e gerar mais certificados de cliente, ou exportar outro arquivo .cer. Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Não instale o certificado autoassinado diretamente no computador cliente. Você precisa gerar um certificado de cliente do certificado autoassinado. Em seguida, você exporta e instala o certificado de cliente no computador cliente. Essas etapas não são específicas ao modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### <a name="clientcert"></a>Gerar um certificado do cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.
 
1. No mesmo computador usado para criar o certificado autoassinado, abra um prompt de comando como administrador.
2. Modifique e execute o exemplo para gerar um certificado de cliente.
   * Altere *“P2SRootCert”* para o nome da raiz autoassinada com base na qual você está gerando o certificado do cliente. Verifique se você está usando o nome do certificado raiz, que é equivalente ao valor 'CN=' que você especificou quando criou a raiz autoassinada.
   * Altere *P2SChildCert* para o nome que você deseja fornecer a um certificado do cliente gerado.

   Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado P2SChildcert em seu repositório de certificados Personal que foi gerado com base no certificado raiz P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="clientexport"></a>Exportar um certificado do cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Instalar um certificado do cliente exportado

Para instalar um certificado de cliente, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de Ponto a Site. 

* Para conferir as etapas do modelo de implantação do **Gerenciador de Recursos**, confira [Configurar P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
