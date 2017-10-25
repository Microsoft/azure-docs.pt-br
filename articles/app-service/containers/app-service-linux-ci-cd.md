---
title: "Implantação Contínua com os Aplicativos Web para Contêineres do Azure | Microsoft Docs"
description: "Como configurar a implantação contínua nos os Aplicativos Web do Azure para Contêineres."
keywords: "serviço de aplicativo do azure, linux, oss, acr"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 27a2c95c09197b3439d3fac7c74d253df2b32b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-with-azure-web-app-for-containers"></a>Implantação contínua com o Aplicativo Web do Azure para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/en-us/services/container-registry/) Gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Etapa 1– entrar no Azure

Entrar no Portal do Azure em http://portal.azure.com

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Etapa 2 – Habilitar recurso de implantação contínua do contêiner

Você pode habilitar o recurso de implantação contínua usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

No  **[portal do Azure](https://portal.azure.com/)**, clique a opção **Serviço de Aplicativo** à esquerda da página.

Clique no nome do aplicativo para o qual você deseja configurar a implantação contínua do Hub do Docker.

Nas **Configurações do aplicativo**, adicione uma configuração de aplicativo chamada `DOCKER_ENABLE_CI` com o valor `true`.

![inserir imagem da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Etapa 3 – preparar a URL do Webhook

Você pode obter a URL do Webhook usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
``` 

Para a URL do Webhook, você precisa ter o seguinte ponto de extremidade: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Etapa 4 – adicionar um webhook

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Na sua folha de portal de registro, clique em **Webhooks** e crie um novo webhook clicando em **Adicionar**. Na folha **Criar webhook**, nomeie o webhook. Para o URI do Webhook, você precisa fornecer a URL obtida na **Etapa 3**

Verifique se você definiu o escopo como o repositório que contém a imagem de contêiner.

![inserir imagem de webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Quando a imagem é atualizada, o aplicativo Web é atualizado automaticamente com a nova imagem.

### <a name="docker-hub"></a>Hub do Docker

Na página do Hub do Docker, clique em **Webhooks** e, em seguida, em **CRIAR UM WEBHOOK**.

![inserir imagem da adição do webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para a URL do Webhook, você precisa fornecer a URL obtida na **Etapa 3**

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Quando a imagem é atualizada, o aplicativo Web é atualizado automaticamente com a nova imagem.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Aplicativo Web do Azure para Contêineres?](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/en-us/services/container-registry/)
* [Usando o .NET Core no aplicativo Web para Contêineres do Azure](quickstart-dotnetcore.md)
* [Usando o Ruby no aplicativo Web para Contêineres do Azure](quickstart-ruby.md)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure](quickstart-custom-docker-image.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](./app-service-linux-faq.md) 
* [Gerenciar aplicativo Web para Contêineres usando a CLI do Azure 2.0](./app-service-linux-cli.md)
