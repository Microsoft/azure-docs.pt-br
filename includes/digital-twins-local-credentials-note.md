---
author: baanders
description: arquivo de inclusão para DefaultAzureCredential no Azure digital gêmeos Samples-Note
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494469"
---
>[!NOTE]
> Este exemplo usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da `Azure.Identity` biblioteca) para autenticar usuários com a instância do gêmeos digital do Azure quando você o executa em seu computador local. Com esse tipo de autenticação, o exemplo pesquisará as credenciais do Azure no seu ambiente local, como um logon de um [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local ou no Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre `DefaultAzureCredential` como usar o e outras opções de autenticação, consulte [*como escrever o código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).