---
title: APIs do Microsoft Graph para PIM (versão prévia) – Azure AD | Microsoft Docs
description: Fornece informações sobre como usar as APIs do Microsoft Graph para Azure AD PIM (Privileged Identity Management) (Versão Prévia).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb102c23d80095b8eb79fc25d1c1fd9d7f374fce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91529687"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>APIs do Microsoft Graph para Privileged Identity Management (versão prévia)

Você pode executar tarefas do Privileged Identity Management usando as [APIs do Microsoft Graph](/graph/overview) para o Azure Active Directory. Este artigo descreve conceitos importantes para uso das APIs do Microsoft Graph para Privileged Identity Management.

Para obter detalhes sobre as APIs do Microsoft Graph, confira a [Referência de API do Azure AD Privileged Identity Management](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta).

> [!IMPORTANT]
> APIs na versão /beta no Microsoft Graph estão em versão prévia e estão sujeitas a alterações. Não há suporte para o uso dessas APIs em aplicativos de produção.

## <a name="required-permissions"></a>Permissões necessárias

Para chamar as APIs do Microsoft Graph para Privileged Identity Management, você precisará ter **uma ou mais** das seguintes permissões:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para os aplicativos que chamam as APIs do Microsoft Graph para o Privileged Identity Management, eles precisam ter as permissões necessárias. A forma mais fácil de especificar as permissões necessárias é usando a [estrutura de consentimento do Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões no Graph Explorer

Caso esteja usando o Graph Explorer para testar suas chamadas, você pode especificar as permissões na ferramenta.

1. Entre no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) como um Administrador Global.

1. Clique em **modificar permissões**.

    ![Captura de tela que mostra a página "Gerenciador de gráficos" com a ação "Modificar permissões" selecionada.](./media/pim-apis/graph-explorer.png)

1. Marque as caixas de seleção ao lado das permissões que deseja incluir. `PrivilegedAccess.ReadWrite.AzureAD` ainda não está disponível no Graph Explorer.

    ![Graph Explorer – modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique em **Modificar Permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Próximas etapas

- [Referência de API do Azure AD Privileged Identity Management](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)