---
title: "Tutorial de instâncias de Contêiner do Azure - Preparar seu aplicativo | Azure Docs"
description: "Preparar um aplicativo para implantação nas Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ca4cd00b3e9e58fd1137b896e7aac96549bf6d05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Criar contêiner para implantação nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure permitem a implantação de contêineres do Docker na infraestrutura do Azure sem o provisionamento de máquinas virtuais ou a adoção de serviço de nível superior. Neste tutorial, você criará um aplicativo Web simples no Node.js e o empacotará em um contêiner que pode ser executado usando as Instâncias de Contêiner do Azure. Abordaremos:

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando imagens de contêiner da fonte do aplicativo
> * Testando as imagens em um ambiente do Docker local

Nos tutoriais subsequentes, você carregará sua imagem em um Registro de Contêiner do Azure e os implantará nas Instâncias de Contêiner do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial assume uma compreensão básica dos conceitos fundamentais do Docker como contêineres, imagens de contêiner e comandos básicos do docker. Se necessário, consulte [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para conhecer os conceitos básicos de contêiner. 

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Portanto, é recomendável usar um ambiente de desenvolvimento completo do Docker.

## <a name="get-application-code"></a>Obter o código de aplicativo

O exemplo neste tutorial inclui um aplicativo Web simples criado no [Node.js](http://nodejs.org). O aplicativo serve a uma página HTML estática e tem esta aparência:

![Aplicativo de tutorial mostrado no navegador][aci-tutorial-app]

Use o git para baixar o exemplo:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Criar a imagem de contêiner

O Dockerfile fornecido no repositório de exemplo mostra como o contêiner é criado. Ele inicia de uma [imagem oficial do Node.js][dockerhub-nodeimage] com base no [Alpine Linux](https://alpinelinux.org/), uma distribuição pequena que é adequada para usar com contêineres. Em seguida, ele copia os arquivos do aplicativo para o contêiner, instala dependências usando o Gerenciador de Pacotes do Node e inicia o aplicativo.

```
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Use o comando `docker build` para criar a imagem de contêiner, marcando-o como *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Use o `docker images` para ver a imagem interna:

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Antes de tentar implantar o contêiner nas Instâncias de Contêiner do Azure, execute-o localmente para confirmar se ele funciona. A opção `-d` permite que o contêiner seja executado em segundo plano, enquanto `-p` permite mapear uma porta aleatória em seu computador para a porta 80 no contêiner.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Abra o navegador http://localhost:8080 para confirmar que o contêiner está sendo executado.

![Executando o aplicativo localmente no navegador][aci-tutorial-app-local]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de contêiner que pode ser implantada nas Instâncias de Contêiner do Azure. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando imagens de contêiner da fonte do aplicativo
> * Testando o contêiner localmente

Avance para o próximo tutorial para saber mais sobre como armazenar imagens de contêiner em um Registro de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Enviar imagens por push ao Registro de Contêiner do Azure](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png