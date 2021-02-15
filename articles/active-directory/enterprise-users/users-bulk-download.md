---
title: Baixar uma lista de usuários no portal de Azure Active Directory | Microsoft Docs
description: Baixe registros de usuário em massa no centro de administração do Azure em Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861592"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Baixar uma lista de usuários no portal Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de importação (criação) de usuário em massa.

## <a name="required-permissions"></a>Permissões necessárias

Para baixar a lista de usuários do centro de administração do Azure AD, você deve estar conectado com um usuário atribuído a uma ou mais funções de administrador no nível da organização no Azure AD (o Administrador de Usuários é a função mínima exigida). O emissor de convites independente e o desenvolvedor de aplicativos não são considerados funções de administrador.

## <a name="to-download-a-list-of-users"></a>Para baixar uma lista de usuários

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta de administrador de usuário na organização.
2. Navegue para Azure Active Directory > Usuários. Em seguida, selecione os usuários que deseja incluir no download, marcando a caixa na coluna à esquerda ao lado de cada usuário. Observação: neste momento, não há como selecionar todos os usuários para exportação. Cada um deve ser selecionado individualmente.
3. No Azure AD, selecione **usuários**  >  **baixar usuários**.
4. Na página **baixar usuários** , selecione **Iniciar** para receber um arquivo CSV listando propriedades de perfil de usuário. Se houver erros, você poderá baixar e exibir o arquivo de resultados na página Resultados da operação em massa. O arquivo contém o motivo de cada erro.

   ![Selecione onde você deseja que a lista os usuários que deseja baixar](./media/users-bulk-download/bulk-download.png)

   O arquivo de download conterá a lista filtrada de usuários.

   Os seguintes atributos de usuários são incluídos:

   - userPrincipalName
   - displayName
   - sobrenome
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - Serviço Móvel
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Verificar o status

Você pode ver o status de suas solicitações em massa pendentes na página **resultados da operação em massa** .

[![Verifique o status na página resultados de operações em massa.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites do serviço de download em massa

Cada atividade em massa para criar uma lista de usuários pode ser executada por até uma hora. Isso permite a criação e o download de uma lista de pelo menos 500.000 usuários.

## <a name="next-steps"></a>Próximas etapas

- [Adição de Usuários em Massa](users-bulk-add.md)
- [Exclusão em massa de usuários](users-bulk-delete.md)
- [Restauração de usuários em massa](users-bulk-restore.md)
