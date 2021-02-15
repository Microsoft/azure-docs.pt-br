---
title: 'Gerar e exportar certificados para P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente para P2S usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306677"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para Ponto a Site usando o PowerShell

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente utilizando o PowerShell no Windows 10 ou no Windows Server 2016. Se você estiver procurando por instruções de certificado diferentes, confira [Certificados – Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Execute as etapas deste artigo em um computador que executa o Windows 10 ou no Windows Server 2016. Os cmdlets do PowerShell que você utiliza para gerar certificados são parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 é necessário apenas para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte.

Se você não tiver acesso a um computador com Windows 10 ou Windows Server 2016 poderá utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que você gera usando qualquer um dos métodos podem ser instalados em qualquer sistema operacional cliente [com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) .

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalar um certificado do cliente exportado

Cada cliente que conecta-se à VNet através de uma conexão P2S requer um certificado do cliente para ser instalado localmente.

Para instalar um certificado de cliente, confira [Instalar um certificado de cliente para conexões Ponto a Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de Ponto a Site.

* Para conferir as etapas do modelo de implantação do **Gerenciador de Recursos**, confira [Configurar P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).