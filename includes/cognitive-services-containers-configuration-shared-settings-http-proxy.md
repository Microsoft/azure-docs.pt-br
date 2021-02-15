---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001129"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| Name | Tipo de dados | Descrição |
|--|--|--|
|HTTPS_PROXY|string|O proxy a ser usado, por exemplo, `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|string|Quaisquer credenciais necessárias para autenticação no proxy, por exemplo, nomedeusuario:senha.|
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
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
