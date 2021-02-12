---
title: Configurar o controle de acesso baseado em função local (RBAC local) para a API do Azure para FHIR
description: Este artigo descreve como configurar a API do Azure para FHIR para usar um locatário externo do Azure AD para o plano de dados
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524191"
---
# <a name="configure-local-rbac-for-fhir"></a>Configurar o RBAC local para FHIR 

Este artigo explica como configurar a API do Azure para FHIR para usar um locatário de Azure Active Directory secundário externo para gerenciar o acesso ao plano de dados. Use esse modo somente se não for possível usar o locatário Azure Active Directory associado à sua assinatura.

> [!NOTE]
> Se o plano de dados do serviço FHIR estiver configurado para usar seu locatário de Azure Active Directory primário associado à sua assinatura, [use o RBAC do Azure para atribuir funções de plano de dados](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Adicionar entidade de serviço

O RBAC local permite que você use um locatário de Azure Active Directory externo com seu servidor FHIR. Para permitir que o sistema RBAC local Verifique as associações de grupo nesse locatário, a API do Azure para FHIR deve ter uma entidade de serviço no locatário. Essa entidade de serviço será criada automaticamente em locatários vinculados a assinaturas que implantaram a API do Azure para FHIR, mas, caso seu locatário não tenha nenhuma assinatura vinculada a ela, um administrador de locatários precisará criar essa entidade de serviço com um dos seguintes comandos:

Usando o `Az` módulo do PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

ou você pode usar o `AzureAd` módulo do PowerShell:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

ou você pode usar CLI do Azure:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Configurar RBAC local

Você pode configurar a API do Azure para FHIR para usar um locatário de Azure Active Directory externo ou secundário na folha de **autenticação** :

![Atribuições de RBAC locais](media/rbac/local-rbac-guids.png).

Na caixa autoridade, insira um locatário de Azure Active Directory válido. Depois que o locatário for validado, a caixa **IDs de objeto permitidas** deverá ser ativada e você poderá inserir uma lista de IDs de objeto de identidade. Essas IDs podem ser as IDs de objeto de identidade de:

* Um usuário Azure Active Directory.
* Uma entidade de serviço do Azure Active Directory.
* Um grupo de segurança do Azure Active Directory.

Você pode ler o artigo sobre como [encontrar IDs de objeto de identidade](find-identity-object-ids.md) para obter mais detalhes.

Depois de inserir as IDs de objeto necessárias, clique em **salvar** e aguarde até que as alterações sejam salvas antes de tentar acessar o plano de dados usando os usuários atribuídos, as entidades de serviço ou os grupos.

## <a name="caching-behavior"></a>Comportamento de caching

A API do Azure para FHIR armazenará decisões em cache por até 5 minutos. Se você conceder a um usuário acesso ao servidor FHIR adicionando-os à lista de IDs de objeto permitidos ou removê-los da lista, deverá esperar que ele demore até cinco minutos para que as alterações nas permissões sejam propagadas.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a atribuir o acesso ao plano de dados FHIR usando um locatário de Azure Active Directory externo (secundário). Em seguida, saiba mais sobre as configurações adicionais para a API do Azure para FHIR:
 
>[!div class="nextstepaction"]
>[Configurações adicionais API do Azure para FHIR](azure-api-for-fhir-additional-settings.md)
