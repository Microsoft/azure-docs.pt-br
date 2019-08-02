---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320520"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| Nome | Tipo de dados | DESCRIÇÃO |
|--|--|--|
|HTTP_PROXY|cadeia de caracteres|O proxy a ser usado, por exemplo, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|cadeia de caracteres|Quaisquer credenciais necessárias para autenticação no proxy, por exemplo, nomedeusuario:senha.|
|`<proxy-user>`|cadeia de caracteres|O usuário para o proxy.|
|`<proxy-password>`|cadeia de caracteres|A senha associada ao `<proxy-user>` para o proxy.|
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
