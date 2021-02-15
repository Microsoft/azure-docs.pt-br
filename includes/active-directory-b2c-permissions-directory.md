---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: f8c972bdb9195008c2983d3993e8d9369749b284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200179"
---
#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/) 

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **APIs da Microsoft** e, em seguida, selecione **Microsoft Graph**.
1. Selecione **Permissões de aplicativo**.
1. Expanda o grupo de permissões apropriado e marque a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo:
    * **AuditLog**  >  **AuditLog. Read. All**: para ler os logs de auditoria do diretório.
    * **Do diretório**  >  **Directory. ReadWrite. All**: para cenários de migração de usuário ou de gerenciamento de usuário.
    * **Política**  >  do **Policy. ReadWrite. TrustFramework**: para cenários de CI/CD (integração contínua/entrega contínua). Por exemplo, implantação de política personalizada com Azure Pipelines.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Se você não estiver conectado no momento com a conta de administrador global, entre com uma conta em seu locatário Azure AD B2C que tenha sido atribuído pelo menos à função *administrador de aplicativos de nuvem* e, em seguida, selecione **conceder consentimento de administrador para (seu nome de locatário)**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status**. Pode levar alguns minutos para que as permissões sejam propagadas.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Na página Visão geral do **aplicativo registrado** , selecione **configurações**.
1. Em **acesso à API**, selecione **permissões necessárias**.
1. Selecione **Microsoft Graph**.
1. Em **permissões do aplicativo**, marque a caixa de seleção da permissão para conceder ao seu aplicativo de gerenciamento. Por exemplo:
    * **Ler todos os dados do log de auditoria**: Selecione essa permissão para ler os logs de auditoria do diretório.
    * **Ler e gravar dados do diretório**: Selecione essa permissão para cenários de migração de usuário ou gerenciamento de usuários.
    * **Ler e gravar as políticas de estrutura de confiança de sua organização**: Selecione essa permissão para cenários de CI/CD (integração contínua/entrega contínua). Por exemplo, implantação de política personalizada com Azure Pipelines.
1. Clique em **Salvar**.
1. Selecione **Conceder permissões** e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem totalmente.
