---
author: baanders
description: incluir um arquivo a fim de configurar uma autenticação local para DefaultAzureCredential em exemplos de Gêmeos Digitais do Azure – com introdução
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473634"
---
### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

Quando você o executa em seu computador local, este exemplo usa o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte da biblioteca `Azure.Identity`) para autenticar usuários com uma instância dos Gêmeos Digitais do Azure. Para obter mais informações sobre diferentes maneiras que um aplicativo cliente pode se autenticar com os Gêmeos Digitais do Azure, confira [*Instruções: como gravar um código de autenticação do aplicativo*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]