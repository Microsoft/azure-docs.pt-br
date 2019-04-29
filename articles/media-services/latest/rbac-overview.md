---
title: Controle de acesso baseado em função para contas de serviços de mídia - Azure | Microsoft Docs
description: Este artigo discute o controle de acesso baseado em função (RBAC) para contas de serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930193"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controle de acesso baseado em função (RBAC) para contas de serviços de mídia

Atualmente, os serviços de mídia do Azure não define as funções personalizadas específicas para o serviço. Para obter acesso total à conta de serviços de mídia, os clientes podem usar as funções internas do **proprietário** ou **Colaborador**. A principal diferença entre essas funções é: o **proprietário** possível controlar quem tem acesso a um recurso e o **Colaborador** não é possível. Interna **leitor** função também pode ser usada, mas o usuário ou aplicativo só terá acesso de leitura para as APIs de serviços de mídia. 

## <a name="design-principles"></a>Princípios de design

Um dos princípios de design de chave da API v3 é tornar a API mais segura. APIs v3 não retornar segredos ou as credenciais na **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separada para obter as credenciais ou segredos. O **leitor** função não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite que você defina permissões mais granulares de segurança RBAC em uma função personalizada se desejado.

Para listar as operações dos serviços de mídia dá suporte a, execute:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O [definições de função interna](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artigo lhe mostra exatamente o que a função concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções de administrador da assinatura clássica, funções do Azure RBAC e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é o RBAC para recursos do Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Use o RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>Próximas etapas

- [Desenvolver com os serviços de mídia APIs v3](media-services-apis-overview.md)
- [Obter política de chave de conteúdo usando o .NET de serviços de mídia](get-content-key-policy-dotnet-howto.md)
