---
author: baanders
description: incluir arquivo para o pré-requisito de permissão na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92205465"
---
## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ter uma [função em sua assinatura](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) que tenha as seguintes permissões:
* Criar e gerenciar recursos do Azure
* Gerenciar o acesso do usuário aos recursos do Azure (incluindo concessão e delegação de permissões)

Funções comuns que atendem a esse requisito são *proprietário*, *administrador da conta* ou a combinação de administrador e *colaborador* de *acesso do usuário* . Para obter uma explicação completa sobre funções e permissões, incluindo quais permissões estão incluídas com outras funções, visite [*funções de administrador de assinatura clássicas, funções do Azure e funções do Azure ad*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do RBAC do Azure.

Para exibir sua função em sua assinatura, visite a [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure (você pode usar este link ou procurar *assinaturas* com a barra de pesquisa do Portal). Procure o nome da assinatura que você está usando e exiba sua função na coluna *minha função* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Exibição da página assinaturas no portal do Azure, mostrando o usuário como proprietário" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Se você descobrir que o valor é *colaborador* ou outra função que não tem as permissões necessárias descritas acima, você pode entrar em contato com o usuário em sua assinatura *que tem essas* permissões (como um proprietário de assinatura ou administrador de conta) e proceder de uma das seguintes maneiras:
* Solicite que eles concluam as etapas neste artigo em seu nome
* Solicite que eles elevem sua função na assinatura para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.