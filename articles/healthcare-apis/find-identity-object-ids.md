---
title: Localizar IDs de objeto de identidade para autenticação-API do Azure para FHIR
description: Este artigo explica como localizar as IDs de objeto de identidade necessárias para configurar a autenticação para a API do Azure para FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033618"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Localizar IDs de objeto de identidade para configuração de autenticação

Neste artigo, você aprenderá a encontrar IDs de objeto de identidade necessárias ao configurar a API do Azure para FHIR para [usar um locatário de Active Directory externo ou secundário](configure-local-rbac.md) para o plano de dados.

## <a name="find-user-object-id"></a>Localizar ID de objeto de usuário

Se você tiver um usuário com nome de usuário `myuser@contoso.com` , poderá localizar os usuários `ObjectId` usando o seguinte comando do PowerShell:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

ou você pode usar o CLI do Azure:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Localizar ID de objeto da entidade de serviço

Suponha que você tenha registrado um [aplicativo cliente de serviço](register-service-azure-ad-client-app.md) e queira permitir que este cliente de serviço acesse a API do Azure para FHIR, você pode encontrar a ID de objeto para a entidade de serviço do cliente com o seguinte comando do PowerShell:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

em que `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` é a ID do aplicativo do cliente de serviço. Como alternativa, você pode usar o `DisplayName` do cliente de serviço:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Se você estiver usando o CLI do Azure, poderá usar:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Localizar uma ID de objeto de grupo de segurança

Se você quiser localizar a ID de objeto de um grupo de segurança, poderá usar o seguinte comando do PowerShell:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Em que `mygroup` é o nome do grupo no qual você está interessado.

Se você estiver usando o CLI do Azure, poderá usar:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como encontrar IDs de objeto de identidade necessárias para configurar a API do Azure para FHIR para usar um locatário de Azure Active Directory externo ou secundário. Em seguida, leia sobre como usar as IDs de objeto para definir as configurações do RBAC local:
 
>[!div class="nextstepaction"]
>[Definir configurações de RBAC local](configure-local-rbac.md)
