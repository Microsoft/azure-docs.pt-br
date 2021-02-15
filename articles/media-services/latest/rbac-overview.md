---
title: Controle de acesso baseado em função para contas de serviços de mídia
description: Este artigo discute o controle de acesso baseado em função do Azure (RBAC do Azure) para contas dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955149"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Controle de acesso baseado em função do Azure (RBAC do Azure) para contas de serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Atualmente, os serviços de mídia do Azure não definem nenhuma função personalizada específica para o serviço. Para obter acesso completo à conta dos serviços de mídia, os clientes podem usar as funções internas de **proprietário** ou **colaborador**. A principal diferença entre essas funções é: o **proprietário** pode controlar quem tem acesso a um recurso e o **colaborador** não pode. A função **leitor** interna também pode ser usada, mas o usuário ou aplicativo terá acesso de leitura somente para as APIs dos serviços de mídia. 

## <a name="design-principles"></a>Princípios de design

Um dos princípios de design de chave da API v3 é tornar a API mais segura. as APIs v3 não retornam segredos ou credenciais em operações de **obtenção** ou de **lista** . As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separado para obter segredos ou credenciais. A função **leitor** não pode chamar operações como Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Ter ações separadas permite definir permissões de segurança de RBAC do Azure mais granulares em uma função personalizada, se desejado.

Para listar as operações que os serviços de mídia dão suporte, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [definições de função internas](../../role-based-access-control/built-in-roles.md) informa exatamente o que a função concede. 

Confira os artigos a seguir para saber mais:

- [Funções de administrador da assinatura clássica, funções do Azure e funções do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md)
- [Adicionar ou remover atribuições de função do Azure usando a API REST](../../role-based-access-control/role-assignments-rest.md)
- [Operações do provedor de recursos dos serviços de mídia](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Próximas etapas

- [Desenvolvendo com APIs dos serviços de mídia v3](media-services-apis-overview.md)
- [Obter a política de chave de conteúdo usando o .NET dos serviços de mídia](get-content-key-policy-dotnet-howto.md)
