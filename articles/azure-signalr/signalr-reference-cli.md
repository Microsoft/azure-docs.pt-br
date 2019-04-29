---
title: Exemplos de CLI do Azure - Serviço Azure SignalR
description: Exemplos de CLI do Azure - Serviço Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071069"
---
# <a name="azure-cli-reference"></a>Referência da CLI do Azure

A tabela a seguir inclui links para scripts bash para o Serviço do Azure SignalR do Microsoft Azure usando a CLI do Azure.

| | |
|-|-|
|**Criar**||
| [Criar um novo Serviço SignalR e grupo de recurso](scripts/signalr-cli-create-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório.  |
|**Integração**||
| [Criar um novo SignalR Service e o aplicativo Web configurado para usar o SignalR](scripts/signalr-cli-create-with-app-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. Também adiciona um novo plano de serviço de aplicativo e o aplicativo Web para hospedar um aplicativo Web do ASP.NET Core que usa o SignalR Service. O aplicativo web é configurado com uma Configuração de Aplicativo para se conectar ao novo recurso de serviço SignalR. |
| [Criar um novo SignalR Service e aplicativo Web configurado para usar o SignalR e o GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. Também adiciona um novo aplicativo Web do Azure e o plano para hospedar um aplicativo Web do ASP.NET Core que usa o SignalR Service de hospedagem. O aplicativo web está configurado com configurações de aplicativo para a cadeia de caracteres de conexão para o novo recurso de serviço SignalR e os segredos de cliente para oferecer suporte a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/) conforme demonstrado no [tutorial de autenticação](signalr-concept-authenticate-oauth.md). O aplicativo Web também é configurado para usar uma fonte de implantação do repositório git local. |
| | |
