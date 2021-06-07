---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629959"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|string|O proxy a ser usado, por exemplo, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Todas as credenciais necessárias para a autenticação no proxy, por exemplo, `username:password` . Esse valor **deve estar em letras** minúsculas. |
|`<proxy-user>`|string|O usuário para o proxy.|
|`<proxy-password>`|string|A senha associada ao `<proxy-user>` para o proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
