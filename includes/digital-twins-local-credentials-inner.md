---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – sem introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212800"
---
Com `DefaultAzureCredential`, o exemplo pesquisará as credenciais no ambiente local, como uma conexão do Azure em uma [CLI do Azure](/cli/azure/install-azure-cli) local ou no Visual Studio ou Visual Studio Code. Por isso, será necessário *entrar no Azure localmente* por meio de um desses mecanismos para configurar as credenciais do exemplo.

Caso esteja usando o Visual Studio ou o Visual Studio Code para executar um exemplo de código, verifique se você está conectado nesse editor com as mesmas credenciais do Azure que deseja usar para acessar a instância dos Gêmeos Digitais do Azure.

Caso contrário, você poderá [instalar a CLI do Azure local](/cli/azure/install-azure-cli), iniciar um prompt de comando no computador e executar o comando `az login` para entrar em sua conta do Azure. Depois de entrar, ao executar o exemplo de código, ele deverá conectar de modo automático.