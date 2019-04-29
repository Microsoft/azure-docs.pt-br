---
title: Gerenciar o Aplicativo Web para Contêineres usando a CLI do Azure – Serviço de Aplicativo do Azure | Microsoft Docs
description: Gerenciar aplicativo Web para Contêineres usando a CLI do Azure.
keywords: serviço de aplicativo do azure, aplicativo web, cli, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 21f6963fbaada4524f27602454d38e7252a5e8b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850077"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gerenciar aplicativo Web para Contêineres usando a CLI do Azure

Usando os comandos deste artigo, é possível criar e gerenciar Aplicativo Web para Contêineres usando a CLI do Azure.
Você pode começar a usar a nova versão da CLI de duas maneiras:

* [Instalando a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu computador.
* Usando o [Azure Cloud Shell (versão prévia)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

Para criar um Plano de Serviço de Aplicativo do Linux, você pode usar o seguinte comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Criar um aplicativo Web de contêiner do Docker personalizado

Para criar um aplicativo Web e configurá-lo para executar um contêiner do Docker personalizado, você pode usar o seguinte comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Ativar o log de contêiner do Docker

Para ativar o log de contêiner do Docker, você pode usar o seguinte comando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Alterar o contêiner do Docker personalizado para um aplicativo existente do Aplicativo Web para Contêineres

Para alterar um aplicativo criado anteriormente, da imagem do Docker atual para uma nova imagem, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Usando imagens do Docker de um registro particular

Você pode configurar seu aplicativo para usar imagens de um registro particular. Você precisa fornecer a URL do registro, o nome de usuário e a senha. Isso pode ser feito usando o seguinte comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Habilitar implantações contínuas para imagens personalizadas do Docker

Com o comando a seguir, você pode habilitar a funcionalidade de CD e obter a URL de webhook. Essa URL pode ser usada para configurar os repositórios do DockerHub ou do Registro de Contêiner do Azure.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Criar um aplicativo do Aplicativo Web para Contêineres usando uma das estruturas de tempo de execução internas

Para criar um aplicativo PHP 5.6 do Aplicativo Web para Contêineres, use o seguinte comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Alterar a versão da estrutura de um aplicativo existente do Aplicativo Web para Contêineres

Para alterar um aplicativo criado anteriormente, da versão da estrutura atual para o Node.js 6.11, você pode usar o seguinte comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurar implantações do Git para seu aplicativo Web

Para configurar implantações do Git para seu aplicativo, você pode usar o seguinte comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Próximas etapas

* [O que é o Serviço de Aplicativo do Azure no Linux?](app-service-linux-intro.md)
* [Instalar a CLI do Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (versão prévia)](../../cloud-shell/overview.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação Contínua com o Aplicativo Web para Contêineres](app-service-linux-ci-cd.md)
