---
title: (PRETERIDO) Gerenciar o cluster do Azure Swarm com a API do Docker
description: Implantar contêineres em um cluster do Docker Swarm no Serviço de Contêiner do Azure
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 04cc9048271d653bd77fd7f2707c8f510ea8c29f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456540"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>(PRETERIDO) Gerenciamento de contêiner com Docker Swarm

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O Docker Swarm fornece um ambiente para implantação de cargas de trabalho em contêineres de em um conjunto de pool de hosts do Docker. Docker Swarm usa a API nativa do Docker. O fluxo de trabalho para gerenciar contêineres em um Docker Swarm é quase idêntico ao que seria usado em um host de contêiner único. Este documento fornece exemplos simples de implantação de cargas de trabalho em contêineres em uma instância do Serviço de Contêiner do Azure do Docker Swarm. Para obter documentação mais detalhada sobre o Docker Swarm, confira [Docker Swarm em Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Pré-requisitos para os exercícios deste documento:

[Criar um Cluster Swarm no Serviço de Contêiner do Azure](container-service-deployment.md)

[Conectar-se ao cluster Swarm no Serviço de Contêiner do Azure](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Implantar um novo contêiner
Para criar um novo contêiner no Docker Swarm, use o comando `docker run` (verificando que você abriu um túnel SSH para os mestres de acordo com os pré-requisitos acima). Este exemplo cria um contêiner da imagem `yeasy/simple-web` :

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Depois que o contêiner tiver sido criado, use `docker ps` para retornar informações sobre o contêiner. Observe aqui que o agente de Swarm que hospeda o contêiner é listado:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Agora você pode acessar o aplicativo em execução nesse contêiner por meio do nome DNS público do balanceador de carga do agente de Swarm. Você pode encontrar essas informações no portal do Azure:  

![Resultados da visita real](./media/container-service-docker-swarm/real-visit.jpg)  

Por padrão, o Balanceador de Carga tem as portas 80, 8080 e 443 abertas. Se você quiser conectar outra porta, precisará abrir a porta no Azure Load Balancer para o Pool de Agentes.

## <a name="deploy-multiple-containers"></a>Implantar vários contêineres
Como diversos contêineres são iniciados, executando 'docker run' várias vezes, você pode usar o comando `docker ps` para ver qual hospeda os contêineres nos quais estão em execução. No exemplo abaixo, três contêineres são espalhados igualmente em três agentes Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Implantar contêineres usando Docker Compose
O recurso Docker Compose pode ser usado para automatizar a implantação e a configuração de vários contêineres. Para tanto, verifique se um túnel Secure Shell (SSH) foi criado e se a variável DOCKER_HOST foi definida (consulte os pré-requisitos acima).

Crie um arquivo docker-compose.yml em seu sistema local. Para fazer isso, use este [exemplo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Execute `docker-compose up -d` para iniciar as implantações do contêiner:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por fim, a lista de contêineres de execução será retornada. Essa lista mostra os contêineres que foram implantados usando o Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalmente, você pode usar `docker-compose ps` para examinar apenas os contêineres definidos em seu arquivo `compose.yml`.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Docker Swarm.](https://docs.docker.com/swarm/)

