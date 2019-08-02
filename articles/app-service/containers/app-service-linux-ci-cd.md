---
title: Implantação Contínua com o Aplicativo Web para Contêineres - Serviço de Aplicativo do Azure | Microsoft Docs
description: Como configurar a implantação contínua no Aplicativo Web para Contêineres.
keywords: serviço de aplicativo do azure, linux, docker, across
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: be3768aadb3e256aa7b0ba7fe3d72cf4169e9bb2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705712"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implantação contínua com o Aplicativo Web para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Ativar a implantação contínua com o ACR

![Captura de tela de ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de aplicativo do Azure** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **Configurações do Contêiner**, selecione **Contêiner Único**
5. Selecione **Registro de Contêiner do Azure**
6. Selecione **Implantação contínua > em**
7. Selecione **Salvar** para ativar a implantação contínua.

## <a name="use-the-acr-webhook"></a>Usar o webhook ACR

Depois que a Implantação Contínua tiver sido habilitada, você poderá exibir o webhook recém-criado na página de webholes do Registro de Contêiner do Azure.

![Captura de tela de ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

No seu Registro de Contêiner, clique em Webhooks para visualizar os webhooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implantação contínua com o Docker Hub (opcional)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de aplicativo do Azure** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **Configurações do Contêiner**, selecione **Contêiner Único**
5. Selecione **Hub do Docker**
6. Selecione **Implantação contínua > em**
7. Selecione **Salvar** para ativar a implantação contínua.

![Captura de tela da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie a URL do Webhook. Para adicionar um webhook para o Hub do Docker, execute <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks para o Hub do Docker</a>.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Serviço de Aplicativo do Azure no Linux](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md)
* [Criar um aplicativo Web Ruby no Serviço de Aplicativo no Linux](quickstart-ruby.md)
* [Implantar um aplicativo Web Docker/Go no Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Serviço de Aplicativo do Azure nas Perguntas Frequentes do Linux](./app-service-linux-faq.md)
* [Gerenciar Aplicativo Web para Contêineres usando a CLI do Azure](./app-service-linux-cli.md)
