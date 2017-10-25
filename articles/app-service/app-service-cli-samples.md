---
title: "Exemplos de CLI do Azure - Serviço de aplicativo | Microsoft Docs"
description: "Exemplos de CLI do Azure - Serviço de aplicativo"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6718694af487929d193dae54ecb2d85ece64887a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples"></a>Exemplos de CLI do Azure

A tabela a seguir inclui links para bash scripts criados usando a CLI do Azure.

| | |
|-|-|
|**Como criar o aplicativo**||
| [Criar um aplicativo web e implantar o código do GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web e implanta o código de um repositório do GitHub público. |
| [Como criar um aplicativo Web com a implantação contínua do GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web com a publicação contínua de um repositório do GitHub que você possui. |
| [Como criar um aplicativo Web e implantar o código de um repositório Git local](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure e configura o push de código de um repositório Git local. |
| [Como criar um aplicativo Web e implantar o código em um ambiente de preparo](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure com um slot de implantação para alterações de código de preparo. |
| [Criar um aplicativo web do ASP.NET Core em um contêiner do Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web do Azure no Linux e carrega uma imagem do Docker do Docker Hub. |
|**Como configurar o aplicativo**||
| [Como mapear um domínio personalizado para um aplicativo Web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web do Azure e mapeia um nome de domínio personalizado para ele. |
| [Associar um certificado SSL personalizado a um aplicativo Web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web do Azure e associa o certificado SSL de um nome de domínio personalizado a ele. |
|**Como escalar um aplicativo**||
| [Como escalar manualmente um aplicativo Web](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure e pode ser dimensionado em 2 instâncias. |
| [Como escalar um aplicativo Web em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Cria dois aplicativos Web do Azure em duas regiões geográficas diferentes e os disponibiliza por meio de um único ponto de extremidade usando o Gerenciador de tráfego do Azure. |
|**Como conectar o aplicativo aos recursos**||
| [Como conectar um aplicativo Web a um Banco de Dados SQL](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web do Azure e um Banco de Dados SQL e, em seguida, adiciona a cadeia de conexão do banco de dados para as configurações do aplicativo. |
| [Como conectar um aplicativo Web a uma conta de armazenamento](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Cria um aplicativo Web do Azure e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
| [Conectar um aplicativo Web a um cache redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure e um cache redis e, em seguida, adiciona detalhes da conexão redis às configurações do aplicativo.) |
| [Conectar um aplicativo Web ao BD Cosmos](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure e um BD Cosmos e, em seguida, adiciona os detalhes da conexão do BD Cosmos às configurações do aplicativo. |
|**Como monitorar o aplicativo**||
| [Como monitorar um aplicativo Web com logs do servidor Web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um aplicativo Web do Azure, habilita o registro em log para ele e baixa os logs em sua máquina local. |
| | |