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
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997982"
---
De volta na janela do terminal local, adicione um remoto do Azure ao repositório Git local. Substitua *\<deploymentLocalGitUrl-from-create-step>* pela URL do repositório remoto Git que você salvou em [Criar um aplicativo Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando solicitado a fornecer credenciais pelo Gerenciador de Credenciais do Git, insira as credenciais criadas em **Configurar um usuário de implantação**, não as credenciais usadas para entrar no portal do Azure.

```bash
git push azure master
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:
