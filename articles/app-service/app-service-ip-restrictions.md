---
title: "Restrições de IP do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Como usar as restrições de IP do Serviço de Aplicativo do Azure"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.openlocfilehash: 54c0c5050c812c2a59631541d94c553974acd2f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrições de IP estático do Serviço de Aplicativo do Azure #

As restrições de IP permitem definir uma lista de endereços IP cujo acesso ao aplicativo é permitido. A lista de permissões pode incluir endereços IP individuais ou um intervalo de endereços IP definido por uma máscara de sub-rede.

Quando um cliente gera uma solicitação para o aplicativo, seu endereço IP é buscado na lista de permissões. Se o endereço IP não estiver na lista, o aplicativo responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

As restrições de IP são avaliadas nas mesmas instâncias do Plano do Serviço de Aplicativo atribuídas ao seu aplicativo.

Para adicionar uma regra de restrição de IP ao aplicativo, use o menu para abrir **Rede**>**Restrições de IP** e clique em **Configurar Restrições de IP**

![IP restrictions] (media/app-service-ip-restrictions/ip-restrictions.png)

A partir deste ponto, é possível analisar a lista de regras de restrição de IP definida para o aplicativo.

![lista de restrições de IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Clique em **[+] Adicionar** para adicionar uma nova regra de restrição de IP.

![adicionar restrições de IP](media/app-service-ip-restrictions/add-ip-restrictions.png)
