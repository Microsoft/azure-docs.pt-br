---
title: "Repositórios de Registro de contêiner do Azure | Microsoft Docs"
description: "Como usar os repositórios de Registro de contêiner do Azure para imagens do Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: d737df98fee955b57166cb44f45f61dff906514e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Repositórios de Registro de contêiner do Azure

O Registro de contêiner do Azure permite armazenar imagens de contêiner em repositórios. Armazenando imagens em repositórios, você poderá ter grupos de imagens (ou versão de imagens) em ambientes isolados. Você pode especificar esses repositórios quando você envia imagens por push para o Registro.


## <a name="prerequisites"></a>Pré-requisitos
* **Registro de Contêiner do Azure** - crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI do Docker** - para configurar o computador local como um host Docker e acessar os comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).
* **Efetuar pull de uma imagem** – efetuar pull de uma imagem do Registro do Hub do Docker público, marcá-lo e enviá-lo por push ao seu Registro. Para diretrizes sobre como enviar imagens por push e efetuar pull dessas imagens, consulte [Enviar imagem do Docker por push para Registro privado do Azure](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Exibindo repositórios no Portal

Depois que você tiver enviado imagens por push para o Registro de contêiner, você poderá ver uma lista dos repositórios hospedando as imagens no Portal do Azure.

Se você seguiu as etapas do artigo [Enviar imagem do Docker por push para Registro privado do Azure](container-registry-get-started-docker-cli.md), agora você deve ter uma imagem de Nginx no Registro do contêiner. Como parte das instruções, você deve ter especificado um namespace para a imagem. No exemplo abaixo, o comando envia a imagem NGinx para o repositório "samples":

```
docker push myregistry.azurecr.io/samples/nginx
```
 O Registro de Contêiner do Azure dá suporte a namespaces do repositório de vários níveis. Esse recurso permite que você agrupe coleções de imagens relacionadas a um aplicativo específico ou uma coleção de aplicativos para equipes de desenvolvimento ou operacionais específicas. Para saber mais sobre repositórios em registros de contêiner, consulte [Registros de contêiner do Docker privado no Azure](container-registry-intro.md).

Para exibir os repositórios de Registro de contêiner do Azure:

1. Faça logon no Portal do Azure
2. Na folha **Registro de Contêiner do Azure**, selecione o Registro que deseja inspecionar
3. Na folha do Registro, clique em **Repositórios** para ver uma lista de todos os repositórios e suas imagens
4. (Opcional) Selecione uma imagem específica para ver as marcas

![Repositórios no portal](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Próximas etapas
Agora que conhece os fundamentos, você está pronto para começar a usar o registro! Por exemplo, inicie a implantação de imagens de contêiner para um cluster de [Serviço de Contêiner do Azure](https://azure.microsoft.com/documentation/services/container-service/).
