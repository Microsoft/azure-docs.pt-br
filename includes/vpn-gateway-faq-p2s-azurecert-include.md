---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171742"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso usar minha própria CA de raiz de PKI interna para a conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quais ferramentas posso usar para criar certificados?

Você pode usar sua solução de Enterprise PKI (sua PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Há instruções para configurações e parâmetros de certificado?

* **Solução PKI/Enterprise PKI interna:** Consulte as etapas a serem [gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Consulte a [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artigo para obter as etapas.

* **MakeCert:** Consulte a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) artigo para obter as etapas.

* **OpenSSL:** 

    * Ao exportar certificados, verifique se converteu o certificado raiz em Base64.

    * Para o certificado do cliente:

      * Ao criar a chave privada, especifique o período como 4096.
      * Ao criar o certificado, para o parâmetro *-extensions*, especifique *usr_cert*.