---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91822829"
---
Para abrir uma sessão SSH direta com seu contêiner, seu aplicativo deve estar em execução.

Cole a seguinte URL no seu navegador e substitua `<app-name>` pelo nome do aplicativo:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se você ainda não estiver autenticado, será necessário autenticar com sua assinatura do Azure para se conectar. Uma vez autenticado, consulte um shell no navegador, onde você pode executar comandos dentro de seu contêiner.

![Conexão SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
