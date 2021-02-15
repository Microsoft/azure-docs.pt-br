---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – com introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011228"
---
### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

Quando você o executa em seu computador local, este exemplo usa o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da biblioteca `Azure.Identity`) para autenticar usuários com uma instância dos Gêmeos Digitais do Azure. Para obter mais informações sobre diferentes maneiras que um aplicativo cliente pode se autenticar com os Gêmeos Digitais do Azure, confira [*Instruções: como gravar um código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]