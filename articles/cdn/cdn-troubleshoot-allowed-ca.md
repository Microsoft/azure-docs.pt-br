---
title: Autoridade de certificação permitida para habilitar o HTTPS personalizado
titleSuffix: Azure Content Delivery Network
description: Se você estiver usando seu próprio certificado para habilitar o HTTPS em um domínio personalizado, deverá usar uma autoridade de certificação (CA) permitida para criá-lo.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573391"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Autoridades de certificação permitidas para habilitar o HTTPS personalizado

Requisitos de certificado específicos são necessários quando você [habilita o recurso https usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) para um domínio personalizado da CDN do Azure (rede de distribuição de conteúdo). 

* O perfil **Azure CDN Standard da Microsoft** requer um certificado de uma das autoridades de certificação aprovadas na lista a seguir. Se um certificado de uma AC não aprovada ou se um certificado autoassinado for usado, a solicitação será rejeitada. 

* Os perfis **da CDN standard do Azure da Verizon** e do **Azure CDN Premium da Verizon** aceitam qualquer certificado válido de qualquer AC válida. Os perfis Verizon não dão suporte a certificados autoassinados.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar o recurso HTTPS de domínio personalizado *não* está disponível para os perfis **da CDN standard do Azure da Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

