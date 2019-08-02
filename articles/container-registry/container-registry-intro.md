---
title: Registros de contêiner do Docker privado no Azure – visão geral
description: Introdução ao serviço de Registro de Contêiner do Azure, fornecendo registros Docker privados gerenciados baseados em nuvem.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 8b20c54d2151afae227a085dd66a0d31f2b85305
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310663"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registros de contêiner do Docker privado no Azure

O Registro de Contêiner do Azure é um serviço gerenciado do Registro do Docker privado com base no Docker Registry 2.0 open-source. Criar e manter registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de contêiner Docker.

Use registros de contêiner do Azure com seus pipelines existentes de desenvolvimento e implantação de contêiner ou use Tarefas do Registro de Contêiner do Azure para criar imagens de contêiner no Azure. Compile sob demanda ou automatize completamente os builds com gatilho como commits de código-fonte e atualizações de imagem de base.

Para obter mais informações sobre os conceitos de Registro e do Docker, confira [Visão geral do Docker](https://docs.docker.com/engine/docker-overview/) e [Sobre Registros, repositórios e imagens](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de uso

Obtenha imagens de um registro de contêiner do Azure para vários destinos de implantação:

* **Sistemas de orquestração escalonáveis** que gerenciam aplicativos em contêineres em clusters de hosts, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que dão suporte à criação e execução de aplicativos em grande escala, incluindo [Serviço de Kubernetes do Azure (AKS)](../aks/index.yml), [Serviço de Aplicativo](../app-service/index.yml), [Lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e outros.

Os desenvolvedores também podem enviar um registro de contêiner como parte de um fluxo de trabalho de desenvolvimento do contêiner. Por exemplo, ter como destino um registro de contêiner de uma ferramenta de entrega e integração contínua, como o [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) ou o [Jenkins](https://jenkins.io/).

Configure Tarefas do ACR para recriar imagens do aplicativo automaticamente quando suas imagens base forem atualizadas ou para automatizar builds de imagem quando sua equipe confirmar o código em um repositório GIT. Crie tarefas de várias etapas para automatizar a criação, o teste e a aplicação de patch de várias imagens de contêiner em paralelo na nuvem.

O Azure fornece ferramentas, incluindo a interface de linha de comando do Azure, o portal do Azure e o suporte de API para gerenciar seus registros de contêiner do Azure. Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.

## <a name="key-features"></a>Principais recursos

* **SKUs de Registro** – crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um dando suporte à integração de webhook, autenticação de registro com o Azure Active Directory e funcionalidade de exclusão. Aproveite o armazenamento local e de rede fechada de suas imagens de contêiner criando um registro no mesmo local do Azure de suas implantações. Use o recurso [replicação geográfica](container-registry-geo-replication.md) de registros Premium para cenários avançados de replicação e distribuição de imagens de contêiner. 

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma identidade do Azure, uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) com suporte do Azure Active Directory ou uma conta do administrador fornecida. Faça logon no registro usando a CLI do Azure ou o comando `docker login` padrão.

* **Artefatos e imagens compatíveis** – agrupadas em um repositório, cada imagem é um instantâneo somente leitura de um contêiner compatível com o Docker. Registros de contêiner do Azure podem incluir imagens do Windows e Linux. Você controla os nomes de imagem para todas as implantações de contêiner. Use [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para envio de imagens em um repositório ou extraia uma imagem de um repositório. Além de imagens de contêiner do Docker, o Registro de Contêiner do Azure armazena [formatos de conteúdo relacionados](container-registry-image-formats.md), como [Gráficos do Helm](container-registry-helm-repos.md) e imagens criadas conforme a [Especificação de Formato de Imagem da OCI (Iniciativa de Contêiner Aberto)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Tarefas do Registro de Contêiner do Azure** – use as [Tarefas do ACR](container-registry-tasks-overview.md) (Tarefas do Registro de Contêiner do Azure) para simplificar o build, o teste, o envio por push e a implantação de imagens no Azure. Por exemplo, use as Tarefas do ACR para estender o loop interno de desenvolvimento para a nuvem descarregando operações `docker build` no Azure. Configurar tarefas de build para automatizar seu pipeline de patch de sistema operacional e estrutura do contêiner e compilar imagens automaticamente quando sua equipe confirma o código para controle do código-fonte.

  As [tarefas de várias etapas](container-registry-tasks-overview.md#multi-step-tasks) fornecem uma definição de tarefas com base em etapa e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas da tarefa definem o build de imagem de contêiner individual e operações de push. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

## <a name="next-steps"></a>Próximas etapas

* [Criar um registro de contêiner usando o portal do Azure](container-registry-get-started-portal.md)
* [Criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md)
* [Automatizar builds e manutenção de contêiner com Tarefas do ACR](container-registry-tasks-overview.md)
