---
title: Gerar e exportar certificados para conexões VPN de usuário | WAN virtual do Azure
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente para conexões VPN de usuário usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328031"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Gerar e exportar certificados para conexões VPN de usuário

As conexões VPN de usuário (ponto a site) usam certificados para autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente utilizando o PowerShell no Windows 10 ou no Windows Server 2016.

Execute as etapas deste artigo em um computador que executa o Windows 10 ou no Windows Server 2016. Os cmdlets do PowerShell que você utiliza para gerar certificados são parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 é necessário apenas para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com as [etapas de WAN virtual para a conexão VPN do usuário](virtual-wan-about.md)
