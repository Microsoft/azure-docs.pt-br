---
title: Exemplos de CLI do Azure - Serviço Azure SignalR
description: Siga exemplos reais para saber CLI do Azure para o serviço de Signaler do Azure. Você aprenderá a criar um serviço de Signalr com mais serviços do Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86515233"
---
# <a name="azure-cli-reference"></a>Referência da CLI do Azure

A tabela a seguir inclui links para scripts bash para o Serviço do Azure SignalR do Microsoft Azure usando a CLI do Azure.

| Script | Descrições |
|-|-|
|**Criar**||
| [Criar um novo Serviço SignalR e grupo de recurso](scripts/signalr-cli-create-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório.  |
|**Integração**||
| [Criar um novo SignalR Service e o aplicativo Web configurado para usar o SignalR](scripts/signalr-cli-create-with-app-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. Também adiciona um novo aplicativo Web e o plano do serviço de aplicativo para hospedar um ASP.NET Core aplicativo Web que usa o serviço Signalr. O aplicativo web é configurado com uma Configuração de Aplicativo para se conectar ao novo recurso de serviço SignalR. |
| [Criar um novo SignalR Service e aplicativo Web configurado para usar o SignalR e o GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. O também adiciona um novo aplicativo Web do Azure e um plano de hospedagem para hospedar um ASP.NET Core aplicativo Web que usa o serviço Signalr. O aplicativo web está configurado com configurações de aplicativo para a cadeia de caracteres de conexão para o novo recurso de serviço SignalR e os segredos de cliente para oferecer suporte a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/) conforme demonstrado no [tutorial de autenticação](signalr-concept-authenticate-oauth.md). O aplicativo Web também é configurado para usar uma fonte de implantação do repositório git local. |
| | |
