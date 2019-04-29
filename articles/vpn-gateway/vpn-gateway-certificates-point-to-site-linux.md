---
title: 'Gerar e exportar certificados para ponto a site: Linux: CLI: Azure | Microsoft Docs'
description: Criar um certificado raiz autoassinado, exportar a chave pública e gerar certificados do cliente usando a CLI do Linux (strongSwan).
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 01/31/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766158"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente usando a CLI do Linux e o strongSwan. Se você estiver procurando por instruções de certificado diferentes, confira os artigos [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Para obter mais informações sobre como instalar o strongSwan usando a GUI em vez da CLI, confira as etapas no artigo [Configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-and-export"></a>Gerar e exportar
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de ponto a site para [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).