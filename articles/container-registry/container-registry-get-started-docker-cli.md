---
title: Enviar a imagem do Docker por push para o registro privado do Azure | Microsoft Docs
description: "Envie e obtenha imagens do Docker para um registro de contêiner privado no Azure usando a CLI do Docker"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10f01e4e8c86bbbfa17cf2559caca645ff13bdcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Envie sua primeira imagem para um registro de contêiner privado do Docker usando a CLI do Docker
Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do [Docker](http://hub.docker.com), de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens públicas do Docker. Você usa a [Interface de Linha de Comando do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para [logon](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/) e outras operações no registro de contêiner.

Para obter mais informações e conceitos, consulte [visão geral](container-registry-intro.md)



## <a name="prerequisites"></a>Pré-requisitos
* **Registro de Contêiner do Azure** - crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI do Docker** - para configurar o computador local como um host Docker e acessar os comandos da CLI do Docker, instale o [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Fazer logon em um registro
Execute `docker login` para fazer logon em seu registro de contêiner as [credenciais de registro](container-registry-authentication.md).

O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Especifique o nome totalmente qualificado do registro (todo em minúsculas). Neste exemplo, é `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Etapas para enviar e receber uma imagem
O exemplo a seguir baixa a imagem de Nginx do registro do Hub do Docker público, marca-a para o registro de contêiner do Azure privado, envia-a por push ao registro e a extrai novamente.

**1. Obtenha a imagem oficial do Docker para Nginx**

Primeiro, extraia a imagem Nginx pública para seu computador local.

```
docker pull nginx
```
**2. Iniciar o contêiner Nginx**

O comando a seguir inicia o contêiner Nginx local de forma interativa na porta 8080, permitindo que você veja a saída de Nginx. Remove o contêiner em execução, uma vez interrompido.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue até [http://localhost:8080/](http://localhost:8080) para exibir o contêiner em execução. Você verá uma tela semelhante ao exemplo a seguir.

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Para interromper o contêiner em execução, pressione [CTRL]+[C].

**3. Criar um alias da imagem no registro**

O comando a seguir cria um alias da imagem, com um caminho totalmente qualificado para o registro. Este exemplo especifica o namespace `samples` para evitar confusão na raiz do registro.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. Envie a imagem para o registro**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. Obtenha a imagem do registro**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. Iniciar o contêiner Nginx no registro**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue até [http://localhost:8080/](http://localhost:8080) para exibir o contêiner em execução.

Para interromper o contêiner em execução, pressione [CTRL]+[C].

**7. (Opcional) Remover a imagem**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Limites simultâneos
Em alguns cenários, a execução simultânea de chamadas pode resultar em erros. A tabela abaixo contém os limites de chamadas simultâneas com operações "Push" e "Pull" no registro do contêiner do Azure:

| Operação  | Limite                                  |
| ---------- | -------------------------------------- |
| PULL       | Até 10 pulls simultâneos por registro |
| PUSH       | Até 5 pushes simultâneos por registro |

## <a name="next-steps"></a>Próximas etapas
Agora que conhece os fundamentos, você está pronto para começar a usar o registro! Por exemplo, inicie a implantação de imagens de contêiner para um cluster de [Serviço de Contêiner do Azure](https://azure.microsoft.com/documentation/services/container-service/).
