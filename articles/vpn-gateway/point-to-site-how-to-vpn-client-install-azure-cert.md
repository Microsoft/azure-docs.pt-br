---
title: 'Instale um certificado de cliente Point-to-Site: Azure | Microsoft Docs'
description: Instalar um certificado de cliente para a autenticação de certificado P2S – Windows, Mac e Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: c278c1c85961fbeb0779cad98f8ac16d4961ba75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679923"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalar certificados de cliente para conexões de autenticação de certificado P2S

Todos os clientes que se conectam a uma rede virtual usando a autenticação de certificado do Azure Ponto a Site exigem um certificado de cliente. Este artigo ajuda você a instalar um certificado de cliente que é usado para autenticação ao se conectar a uma VNet usando P2S.

## <a name="generate"></a>Adquirir um certificado de cliente

Não importa de qual sistema operacional cliente você deseja se conectar, você deve sempre ter um certificado do cliente. Você pode gerar um certificado de cliente de um certificado raiz que foi gerado usando uma solução de AC corporativa, ou um certificado raiz autoassinado. Veja as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md), do [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) ou do [Linux](vpn-gateway-certificates-point-to-site-linux.md) para obter as etapas para gerar um certificado do cliente. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Os clientes de VPN de MAC têm suporte somente para o modelo de implantação do Gerenciador de Recursos. Não há suporte para eles no modelo de implantação clássico.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

O certificado do cliente do Linux é instalado no cliente como parte da sua configuração. Veja [Configuração de cliente – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) para obter instruções.

## <a name="next-steps"></a>Próximas etapas

Continue com as etapas de configuração de ponto a site para [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).