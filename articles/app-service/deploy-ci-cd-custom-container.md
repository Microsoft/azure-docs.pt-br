---
title: CI/CD para contêineres personalizados do Linux
description: Saiba como configurar a implantação contínua em um contêiner personalizado do Linux no serviço Azure App. A implantação contínua tem suporte para o Hub e ACR do Docker.
keywords: serviço de aplicativo do azure, linux, docker, across
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e879d8370821ea465147e344d0fe95836c843ff4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008003"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implantação contínua com o Aplicativo Web para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Ativar a implantação contínua com o ACR

![Captura de tela de ACR webhook](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de aplicativo do Azure** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **Configurações do Contêiner**, selecione **Contêiner Único**
5. Selecione **Registro de Contêiner do Azure**
6. Selecione **Implantação contínua > em**
7. Selecione **Salvar** para ativar a implantação contínua.

## <a name="use-the-acr-webhook"></a>Usar o webhook ACR

Depois que a Implantação Contínua tiver sido habilitada, você poderá exibir o webhook recém-criado na página de webholes do Registro de Contêiner do Azure.

![Captura de tela que mostra onde você pode exibir o webhook recém-criado em sua página WebHooks do registro de contêiner do Azure.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

No seu Registro de Contêiner, clique em Webhooks para visualizar os webhooks atuais.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Ativar a implantação contínua com o Docker Hub (opcional)

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a opção **Serviço de aplicativo do Azure** no lado esquerdo da página.
3. Selecione o nome do aplicativo para o qual você deseja configurar a implantação contínua.
4. Na página **Configurações do Contêiner**, selecione **Contêiner Único**
5. Selecione **Hub do Docker**
6. Selecione **Implantação contínua > em**
7. Selecione **Salvar** para ativar a implantação contínua.

![Captura de tela da configuração de aplicativo](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Copie a URL do Webhook. Para adicionar um webhook para o Hub do Docker, execute <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks para o Hub do Docker</a>.

## <a name="automate-with-cli"></a>Automatização com a CLI

Para configurar o CI/CD usando o CLI do Azure, execute o comando [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) para gerar a URL do webhook. A URL pode ser usada para configurar seu registro de contêiner do DockerHub ou do Azure.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Próximas etapas

* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Criar um aplicativo Web Ruby no Serviço de Aplicativo no Linux](quickstart-ruby.md)
* [Início Rápido: Executar um contêiner personalizado no Serviço de Aplicativo](quickstart-custom-container.md?pivots=container-linux)
* [Serviço de Aplicativo nas Perguntas Frequentes do Linux](faq-app-service-linux.md)
* [Configurar contêineres personalizados do Linux](configure-custom-container.md)
