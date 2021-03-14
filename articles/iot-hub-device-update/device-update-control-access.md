---
title: Entender a atualização de dispositivo para autenticação e autorização do Hub IoT | Microsoft Docs
description: Entenda como a atualização de dispositivo para o Hub IoT usa o RBAC do Azure para fornecer autenticação e autorização para usuários e APIs de serviço.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661716"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>RBAC (controle de acesso baseado em função) do Azure e atualização do dispositivo

A atualização de dispositivo usa o RBAC do Azure para fornecer autenticação e autorização para usuários e APIs de serviço.

## <a name="configure-access-control-roles"></a>Configurar funções de controle de acesso

Para que outros usuários e aplicativos tenham acesso à atualização do dispositivo, os usuários ou aplicativos devem ter acesso concedido a esse recurso. Aqui estão as funções com suporte da atualização de dispositivo

|   Nome da função   | Descrição  |
| :--------- | :---- |
|  Administrador de atualização de dispositivo | Tem acesso a todos os recursos de atualização de dispositivo  |
|  Leitor de atualização de dispositivo| Pode exibir todas as atualizações e implantações |
|  Administrador de conteúdo de atualização de dispositivo | Pode exibir, importar e excluir atualizações  |
|  Leitor de conteúdo de atualização de dispositivo | Pode exibir atualizações  |
|  Administrador de implantações de atualização de dispositivo | Pode gerenciar a implantação de atualizações em dispositivos|
|  Leitor de implantações de atualização de dispositivo| Pode exibir as implantações de atualizações em dispositivos |

Uma combinação de funções pode ser usada para fornecer o nível certo de acesso. Por exemplo, um desenvolvedor pode importar e gerenciar atualizações usando a função de administrador de conteúdo de atualização de dispositivo, mas precisa de uma função de leitor de implantações de atualização de dispositivo para exibir o progresso de uma atualização. Por outro lado, um operador de solução com a função leitor de atualização de dispositivo pode exibir todas as atualizações, mas precisa usar a função de administrador implantações de atualização de dispositivo para implantar uma atualização específica nos dispositivos.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Autenticar em APIs REST de atualização de dispositivo para publicação e gerenciamento

A atualização de dispositivo também usa o Azure AD para autenticação para publicar e gerenciar conteúdo por meio de APIs de serviço. Para começar, você precisa criar e configurar um aplicativo cliente.

### <a name="create-client-azure-ad-app"></a>Criar Aplicativo Azure AD de cliente

Para integrar um aplicativo ou serviço com o Azure AD, [primeiro registre](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) um aplicativo com o Azure AD. A instalação do aplicativo cliente varia dependendo do fluxo de autorização usado.  A configuração abaixo é para obter diretrizes ao usar as APIs REST de atualização de dispositivo.

* Definir a autenticação de cliente: ' redirecionar URIs para o cliente Web ou nativo '.
* Definir permissões de API-atualização de dispositivo para o Hub IoT expõe:
  * Permissões delegadas: ' user_impersonation '
  * **Opcional**, conceder consentimento de administrador

[Próximas etapas: criar recursos de atualização de dispositivo e configurar funções de controle de acesso](./create-device-update-account.md)
