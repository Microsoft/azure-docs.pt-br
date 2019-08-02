---
title: Entenda o controle de acesso baseado em função dos Gêmeos Digitais do Azure | Microsoft Docs
description: Aprenda a autenticação em gêmeos digitais com controle de acesso baseado em função.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyhughes
ms.openlocfilehash: 33e09ad52722665e6162b18159012d69ec1463bd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849269"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controle de acesso baseado em função em Gêmeos Digitais do Azure

O Azure Digital Twins permite o controle preciso do acesso a dados, recursos e ações específicos em seu gráfico espacial. Isso é feito por meio de um gerenciamento granular de funções e permissões chamado RBAC (controle de acesso baseado em função). O RBAC consiste em _funções_ e _atribuições de funções_. Funções de identificam o nível de permissões. As atribuições de função associam uma função com um usuário ou dispositivo.

Usando o RBAC, pode ser concedida permissão para:

- Um usuário.
- Um dispositivo.
- Uma entidade de serviço.
- Uma função definida pelo usuário.
- Todos os usuários que pertencem a um domínio.
- Um locatário.

O grau de acesso também pode ser ajustado.

O RBAC é único, pois as permissões são herdadas no gráfico espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor pode usar o RBAC para:

- Conceda ao usuário a capacidade de gerenciar dispositivos para um prédio inteiro ou apenas para uma sala ou andar específico.
- Conceda a um administrador acesso global a todos os nós de gráfico espacial para um gráfico inteiro ou apenas para uma seção do gráfico.
- Conceda um acesso de leitura de especialista em suporte ao gráfico, exceto para as chaves de acesso.
- Conceda a todos os membros de um domínio de acesso de leitura a todos os objetos de gráfico.

## <a name="rbac-best-practices"></a>Práticas recomendadas de RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma definição de função é uma coleção de permissões e outros atributos que constituem uma função. Uma definição de função lista as operações permitidas, que incluem *CRIAR*, *LER*, *ATUALIZAR* e *EXCLUIR* que qualquer objeto com essa função pode executar. Também especifica para quais tipos de objetos as permissões são aplicáveis.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Para recuperar as definições completas para funções anteriores, consulte as sistema/funções de API.
> Saiba mais, lendo [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Tipos de identificador de objeto

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Saiba como conceder permissões à entidade de serviço, lendo [Criar e gerenciar atribuição de função](./security-create-manage-role-assignments.md#grant).

Os seguintes artigos de documentação de referência descrevem:

- Como [Consultar a ID de objeto de um usuário](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Como [Obter a ID de objeto para uma entidade de serviço](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Como [Recuperar a ID do objeto de um locatário do Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Atribuições de função

Uma atribuição de função do Azure Digital Twins associa um objeto, como um usuário ou um locatário dos Gêmeos Digitais do Azure, a uma função e um espaço. As permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui todo o gráfico espacial abaixo dele.

Por exemplo, um usuário recebe uma atribuição de função com a função `DeviceInstaller` para o nó raiz de um gráfico espacial, que representa um edifício. O usuário pode então ler e atualizar dispositivos para esse nó e todos os outros espaços filhos no edifício.

Para conceder permissões a um destinatário, crie uma atribuição de função. Para revogar permissões, remova a atribuição de função.

>[!IMPORTANT]
> Saiba mais sobre atribuições de funções, lendo [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como criar e gerenciar atribuições de função dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
