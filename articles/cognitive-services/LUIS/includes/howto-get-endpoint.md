---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91544872"
---
Na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e cole em uma nova guia do navegador.

A URL do ponto de extremidade é semelhante ao formato a seguir, com seu próprio subdomínio personalizado, ID do aplicativo e chave do ponto de extremidade substituindo ID_APP e ID_CHAVE:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```