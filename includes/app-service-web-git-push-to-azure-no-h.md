---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102205964"
---
De volta na janela do terminal local, adicione um remoto do Azure ao repositório Git local. Substitua *\<deploymentLocalGitUrl-from-create-step>* pela URL do repositório remoto Git que você salvou em [Criar um aplicativo Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando solicitado a fornecer credenciais pelo Gerenciador de Credenciais do Git, insira as credenciais criadas em **Configurar um usuário de implantação**, não as credenciais usadas para entrar no portal do Azure.

```bash
git push azure main
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:
