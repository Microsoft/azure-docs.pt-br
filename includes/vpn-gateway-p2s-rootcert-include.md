---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041556"
---
Obtenha o arquivo .cer do certificado raiz. Você pode usar um certificado raiz que foi gerado com uma solução corporativa (recomendado) ou gerar um certificado autoassinado. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base64. Você carrega esse arquivo mais tarde no Azure.

* **Certificado corporativo:** Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Adquira o arquivo .cer do certificado raiz que você deseja usar.
* **Certificado raiz autoassinado:** Se você não estiver usando uma solução de certificado empresarial, precisará criar um certificado raiz autoassinado. Caso contrário, os certificados criados não serão compatíveis com suas conexões P2S e os clientes receberão um erro de conexão quando tentarem se conectar. Você pode usar o Azure PowerShell, MakeCert ou OpenSSL. As etapas nos artigos a seguir descrevem como gerar um certificado raiz autoassinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): essas instruções exigem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções de MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. Embora o MakeCert tenha sido preterido, você ainda pode usá-lo para gerar certificados. Os certificados de cliente gerados usando o certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções do Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).