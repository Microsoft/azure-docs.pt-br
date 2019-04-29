---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319960"
---
Cada computador cliente que você conectar a uma VNet com uma conexão Ponto a Site deve ter um certificado de cliente instalado. Você pode gerá-lo do certificado raiz e instalá-lo em cada computador cliente. Se você não instalar um certificado de cliente válido, a autenticação falhará quando o cliente tenta se conectar à VNet.

Você pode gerar um certificado exclusivo para cada cliente ou pode usar o mesmo certificado para vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado. Caso contrário, se vários clientes usarem o mesmo certificado de cliente para autenticar e você o revogar, você precisará gerar e instalar novos certificados para cada cliente que usa esse certificado.

Você pode gerar certificados de cliente usando os seguintes métodos:

- **Certificado corporativo:**

  - Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato do valor de nome comum *nome\@seudomínio.com*. Use esse formato, em vez do formato *nome do domínio\nomedeusuário*.
  - Verifique se o certificado do cliente é baseado em um modelo de certificado de usuário que tenha *Autenticação de Cliente* listada como o primeiro item na lista de usuários. Verifique o certificado clicando duas vezes nele e exibindo **Uso Avançado de Chave** na guia **Detalhes**.

- **Certificado raiz autoassinado:** Siga as etapas em um dos seguintes artigos de certificado de P2S para que os certificados de cliente que você cria sejam compatíveis com as conexões P2S. As etapas nesses artigos geram um certificado de cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Essas instruções exigem que o Windows 10 e o PowerShell gerem certificados. Os certificados gerados podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. Embora o MakeCert tenha sido preterido, você ainda pode usá-lo para gerar certificados. Você pode instalar os certificados gerados em qualquer cliente de P2S com suporte.
  * [Instruções do Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Ao gerar um certificado do cliente de um certificado raiz autoassinado, ele é instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, exporte-o como arquivo .pfx e junto com toda a cadeia de certificados. Essa ação criará um arquivo .pfx que contém as informações do certificado raiz necessárias para o cliente autenticar. 

**Para exportar o certificado**

Para obter as etapas exportar um certificado, confira [Gerar e exportar certificados para Ponto a Site usando PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
