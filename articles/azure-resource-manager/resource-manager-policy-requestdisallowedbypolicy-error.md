---
title: "Erro RequestDisallowedByPolicy com a política de recurso do Azure | Microsoft Docs"
description: Descreve a causa do erro RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 07/12/2017
ms.author: genli
ms.openlocfilehash: 3b11dc6afac716ef391976093839547e8fd37a91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro RequestDisallowedByPolicy com a política de recurso do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy e também fornece a solução para esse erro.

## <a name="symptom"></a>Sintoma

Quando tentar executar uma ação durante a implantação, você poderá receber um erro **RequestDisallowedByPolicy**, que impede que a ação seja executada. O exemplo a seguir mostra o erro:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Solucionar problemas

Para recuperar detalhes sobre a política que bloqueou a implantação, use um dos seguintes métodos:

### <a name="method-1"></a>Método 1

No PowerShell, forneça o identificador de política como o parâmetro `Id` para recuperar detalhes sobre a política que bloqueou sua implantação.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Método 2 

Na CLI 2.0 do Azure, forneça o nome da definição de política: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, seu departamento de TI pode impor uma política de recurso que proíbe a criação de Endereços IP públicos, Grupos de Segurança de Rede, Rotas Definidas pelo Usuário ou tabelas de rotas. A mensagem de erro na seção **Sintomas** mostra uma política chamada **regionPolicyDefinition**. Sua política pode ter um nome diferente.
Para resolver esse problema, trabalhe com o departamento de TI para examinar as políticas de recurso e determinar como executar a ação solicitada em conformidade com essas políticas.

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral da política de recurso](resource-manager-policy.md)
- [Exibir atribuições de política por meio do portal](resource-manager-policy-portal.md#view-policy-assignments)