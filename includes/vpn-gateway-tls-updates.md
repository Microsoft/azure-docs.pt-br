---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171746"
---
>[!NOTE]
>Começando em 1 de julho de 2018, o suporte está sendo removido para TLS 1.0 e 1.1 do Gateway de VPN do Azure. O Gateway de VPN oferecerá suporte somente ao protocolo TLS 1.2. Para manter o suporte, consulte as [ atualizações para habilitar o suporte para TLS1.2](#tls1).

Além disso, os seguintes algoritmos herdados também serão preteridos para TLS em 1 de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de criptografia de dados)
* 3DES (Algoritmo triplo de criptografia de dados)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Como habilitar o suporte para o TLS 1.2 no Windows 7 e no Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
