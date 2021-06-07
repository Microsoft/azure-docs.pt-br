---
title: 'Gerar e exportar certificados para ponto a site: Linux: CLI'
description: Criar um certificado raiz autoassinado, exportar a chave pública e gerar certificados do cliente usando a CLI do Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393999"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente usando a CLI do Linux e o strongSwan. Se você estiver procurando por instruções de certificado diferentes, confira os artigos [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Para obter mais informações sobre como instalar o strongSwan usando a GUI em vez da CLI, confira as etapas no artigo [Configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="install-strongswan"></a>Instalar o strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de ponto a site para [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
