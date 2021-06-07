---
title: Permissões de consentimento do aplicativo para funções personalizadas no Azure Active Directory | Microsoft Docs
description: Veja uma versão prévia das permissões de consentimento do aplicativo para as funções personalizadas do Azure AD no portal do Azure, no PowerShell ou na API do Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740101"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de consentimento do aplicativo para funções personalizadas no Azure Active Directory

Este artigo contém as permissões de consentimento do aplicativo disponíveis atualmente para definições de função personalizada no Azure AD (Azure Active Directory). Neste artigo, você encontrará as permissões necessárias para alguns cenários comuns relacionados ao consentimento e às permissões do aplicativo.

## <a name="required-license-plan"></a>Plano de licença necessária

O uso desse recurso requer uma licença do Azure AD Premium P1 para sua organização do Azure AD. Para localizar a licença correta para os requisitos, consulte [Comparar recursos geralmente disponíveis nas edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Permissões de consentimento do aplicativo

Use as permissões listadas neste artigo para gerenciar as políticas de consentimento do aplicativo, bem como a permissão para conceder consentimento aos aplicativos.

> [!NOTE]
> O portal de administração do Azure AD ainda não dá suporte à adição das permissões listadas neste artigo a uma definição de função do diretório personalizada. Você precisa [usar o PowerShell do Azure AD para criar uma função do diretório personalizada](custom-create.md#create-a-role-using-powershell) com as permissões listadas neste artigo.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Conceder permissões delegadas a aplicativos em nome próprio (consentimento do usuário)

Para permitir que os usuários concedam consentimento a aplicativos em nome de si mesmos (consentimento do usuário), sujeito a uma política de consentimento do aplicativo.

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

Em que `{id}` é substituído pela ID de uma [política de consentimento do aplicativo](../manage-apps/manage-app-consent-policies.md) que definirá as condições que precisam ser atendidas para que essa permissão esteja ativa.

Por exemplo, para permitir que os usuários deem consentimento em nome deles, sujeito à política de consentimento do aplicativo interno com a ID `microsoft-user-default-low`, você usaria a permissão `...managePermissionGrantsForSelf.microsoft-user-default-low`.

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Conceder permissões a aplicativos em nome de todos (consentimento do administrador)

Para delegar consentimento do administrador em todo o locatário a aplicativos, para permissões delegadas e permissões de aplicativo (funções de aplicativo):

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

Em que `{id}` é substituído pela ID de uma [política de consentimento do aplicativo](../manage-apps/manage-app-consent-policies.md) que definirá as condições que precisam ser atendidas para que essa permissão seja passível de ser usada.

Por exemplo, para permitir que as atribuições de função deem consentimento do administrador em todo o locatário a aplicativos sujeitos a uma [política de consentimento do aplicativo](../manage-apps/manage-app-consent-policies.md) personalizada com a ID `low-risk-any-app`, você usaria a permissão `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app`.

### <a name="managing-app-consent-policies"></a>Gerenciar as políticas de consentimento do aplicativo

Para delegar a criação, a atualização e a exclusão de [políticas de consentimento do aplicativo](../manage-apps/manage-app-consent-policies.md).

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>Lista completa de permissões

Permissão | Descrição
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | Concede a capacidade de dar consentimento a aplicativos no próprio nome (consentimento do usuário), sujeito à política de consentimento do aplicativo `{id}`.
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | Concede a permissão para dar consentimento a aplicativos em nome de todos (consentimento do administrador em todo o locatário), sujeito à política de consentimento do aplicativo `{id}`.
microsoft.directory/permissionGrantPolicies/standard/read | Concede a capacidade de ler as políticas de consentimento do aplicativo.
microsoft.directory/permissionGrantPolicies/basic/update | Concede a capacidade de atualizar propriedades básicas em políticas de consentimento do aplicativo existentes.
microsoft.directory/permissionGrantPolicies/create | Concede a capacidade de criar políticas de consentimento do aplicativo.
microsoft.directory/permissionGrantPolicies/delete | Concede a capacidade de excluir as políticas de consentimento do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Criar funções personalizadas usando o portal do Azure, o Azure AD PowerShell e a API do Graph](custom-create.md)
- [Exibir as atribuições de uma função personalizada](../roles/view-assignments.md)
