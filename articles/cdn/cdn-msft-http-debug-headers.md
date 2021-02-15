---
title: Depurar cabeçalhos HTTP para a CDN do Azure da Microsoft | Microsoft Docs
description: Cabeçalhos de solicitação de cache de depuração fornece informações adicionais sobre a política de cache aplicada ao ativo solicitado. Esses cabeçalhos são específicos para a CDN do Azure da Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81260404"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Depurar o cabeçalho HTTP para a CDN do Azure da Microsoft
O cabeçalho de resposta de depuração, `X-Cache` , fornece detalhes sobre a camada da pilha CDN da qual o conteúdo foi servido. Esse cabeçalho é específico para a CDN do Azure da Microsoft.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

Cabeçalho | Descrição
-------|------------
X-cache: TCP_HIT | Esse cabeçalho é retornado quando o conteúdo é servido do cache de borda da CDN. 
X-cache: TCP_REMOTE_HIT | Esse cabeçalho é retornado quando o conteúdo é servido do cache regional da CDN (camada de blindagem de origem)
X-cache: TCP_MISS | Esse cabeçalho é retornado quando há um erro de cache e o conteúdo é servido da origem. 


