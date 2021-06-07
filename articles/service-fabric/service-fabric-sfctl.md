---
title: CLI do Azure Service Fabric-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos e subgrupos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257046"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar clusters e entidades do Service Fabric. Esta versão é compatível com o tempo de execução do Service Fabric 7,0.

Os comandos seguem o padrão de substantivo-verbo. Confira subgrupos para saber mais.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descrição|
| --- | --- |
| [aplicativo](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativo. |
| [chaos](service-fabric-sfctl-chaos.md) | Iniciar, parar e emitir relatórios sobre o serviço de teste de caos. |
| [em](service-fabric-sfctl-cluster.md) | Selecionar, gerenciar e operar clusters do Service Fabric. |
| [Compose](service-fabric-sfctl-compose.md) | Criar, excluir e gerenciar aplicativos do Docker Compose. |
| [contêiner](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [events](service-fabric-sfctl-events.md) | Recupere eventos do repositório de eventos (se o serviço EventStore já estiver instalado). |
| [for](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [malha](service-fabric-sfctl-mesh.md) | Excluir e gerenciar aplicativos de Malha do Service Fabric. |
| [nó](service-fabric-sfctl-node.md) | Gerenciar os nós que formam um cluster. |
| [particion](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [property](service-fabric-sfctl-property.md) | Propriedades de armazenamento e a consulta em nomes do Service Fabric. |
| [Replica](service-fabric-sfctl-replica.md) | Gerenciar as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço de gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerencie clusters autônomos do Service Fabric. |
| [serviço](service-fabric-sfctl-service.md) | Crie, exclua e gerencie o serviço, os tipos de serviço e os pacotes de serviço. |
| [configurações](service-fabric-sfctl-settings.md) | Defina as configurações locais para essa instância do sfctl. |
| [loja](service-fabric-sfctl-store.md) | Execute operações de arquivo de nível básico no repositório de imagens do cluster. |

## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](./scripts/sfctl-upgrade-application.md).
