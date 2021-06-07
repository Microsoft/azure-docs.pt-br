---
title: Autoridades de certificação permitidas para o Azure front door Standard/Premium (visualização)
description: Este artigo lista todas as autoridades de certificação permitidas quando você cria seu próprio certificado.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434859"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autoridades de certificação permitidas para o Azure front door Standard/Premium (visualização)

Quando você habilita o recurso HTTPS usando seu próprio certificado para um domínio personalizado Standard/Premium da porta de front-end do Azure. Você precisa de uma autoridade de certificação (CA) permitida para criar seu certificado TLS/SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
