---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041585"
---
Cada computador cliente que você conectar a uma VNet com uma conexão Ponto a Site deve ter um certificado de cliente instalado. Você pode gerá-lo do certificado raiz e instalá-lo em cada computador cliente. Se você não instalar um certificado de cliente válido, a autenticação falhará quando o cliente tenta se conectar à VNet.

Você pode gerar um certificado exclusivo para cada cliente ou pode usar o mesmo certificado para vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado. Caso contrário, se vários clientes usarem o mesmo certificado de cliente para autenticar e você o revogar, você precisará gerar e instalar novos certificados para cada cliente que usa esse certificado.

Você pode gerar certificados de cliente usando os seguintes métodos:

* **Certificado corporativo:**

  * Se você estiver usando uma solução de certificado empresarial, gere um certificado de cliente com o formato comum do valor de nome *name\@yourdomain.com*. Use esse formato, em vez do formato *nome do domínio\nomedeusuário*.

  * Verifique se o certificado do cliente é baseado em um modelo de certificado de usuário que tenha *Autenticação de Cliente* listada como o primeiro item na lista de usuários. Verifique o certificado clicando duas vezes nele e exibindo **Uso Avançado de Chave** na guia **Detalhes**.

* **Certificado raiz autoassinado:** Siga as etapas em um dos seguintes artigos de certificado de P2S para que os certificados de cliente que você cria sejam compatíveis com as conexões P2S.

  Ao gerar um certificado do cliente de um certificado raiz autoassinado, ele é instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, exporte-o como arquivo .pfx e junto com toda a cadeia de certificados. Essa ação criará um arquivo .pfx que contém as informações do certificado raiz necessárias para o cliente autenticar.

  As etapas desses artigos geram um certificado de cliente compatível, que você poderá exportar e distribuir.

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): essas instruções exigem o Windows 10 e o PowerShell para gerar certificados. Os certificados gerados podem ser instalados em qualquer cliente de P2S com suporte.

  * [Instruções MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. Embora o MakeCert tenha sido preterido, você ainda pode usá-lo para gerar certificados. Você pode instalar os certificados gerados em qualquer cliente de P2S com suporte.

  * [Instruções do Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).