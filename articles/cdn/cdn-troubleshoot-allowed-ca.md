---
title: Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure | Microsoft Docs
description: Se você estiver usando seu próprio certificado para habilitar HTTPS em um domínio personalizado, deverá usar uma AC (autoridade de certificação) permitida para criá-lo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 754941163ddce9512870f0b76a96207472e5b2aa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593353"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure

Para um domínio personalizado da CDN (Rede de Distribuição de Conteúdo) do Azure em um ponto de extremidade **CDN do Azure Standard da Microsoft**, quando [habilitar o recurso HTTPS usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), deverá usar uma AC (autoridade de certificação) permitida para criar o certificado SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar HTTPS personalizado está disponível apenas com perfis **CDN do Azure Standard da Microsoft**. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

