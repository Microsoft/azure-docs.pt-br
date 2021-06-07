---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado na porta frontal do Azure
description: Se você estiver usando seu próprio certificado para habilitar o HTTPS em um domínio personalizado da porta de recepção do Azure, você deverá usar uma autoridade de certificação (CA) permitida para criá-lo.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91613672"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificação permitidas para habilitar o HTTPS personalizado na porta frontal do Azure

Quando você [habilita o recurso https usando seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) para um domínio personalizado da porta de recepção do Azure. Você precisa de uma autoridade de certificação (CA) permitida para criar seu certificado TLS/SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
