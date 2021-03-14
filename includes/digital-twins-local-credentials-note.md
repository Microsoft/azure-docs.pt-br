---
author: baanders
description: arquivo de inclusão para DefaultAzureCredential no Azure digital gêmeos Samples-Note
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473682"
---
>[!NOTE]
> Quando você o executa em seu computador local, este exemplo usa o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte da biblioteca `Azure.Identity`) para autenticar usuários com uma instância dos Gêmeos Digitais do Azure. Com esse tipo de autenticação, o exemplo pesquisará as credenciais do Azure no seu ambiente local, como um logon de um [CLI do Azure](/cli/azure/install-azure-cli) local ou no Visual Studio/Visual Studio Code.
>
> Para obter mais informações sobre `DefaultAzureCredential` como usar o e outras opções de autenticação, consulte [*como escrever o código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).