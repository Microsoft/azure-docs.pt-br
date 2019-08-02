---
title: Solucionar problemas de gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Saiba mais sobre alguns itens que você deve verificar para ajudá-lo a solucionar problemas de gerenciamento de direitos de Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488985"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Solucionar problemas de gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> No momento, o gerenciamento de direitos do Azure AD (Azure Active Directory) está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve alguns itens que você deve verificar para ajudá-lo a solucionar problemas de gerenciamento de direitos do Azure Active Directory (AD do Azure).

## <a name="checklist-for-entitlement-management-administration"></a>Lista de verificação para administração de gerenciamento de direitos

* Se você receber uma mensagem de acesso negado ao configurar o gerenciamento de direitos e for um administrador global, verifique se o diretório tem uma [licença Azure ad Premium P2 (ou EMS E5)](entitlement-management-overview.md#license-requirements).  
* Se você receber uma mensagem de acesso negado ao criar ou exibir pacotes de acesso e for membro de um grupo de criador de catálogo, deverá criar um catálogo antes de criar seu primeiro pacote de acesso.

## <a name="checklist-for-adding-a-resource"></a>Lista de verificação para adicionar um recurso

* Para que um aplicativo seja um recurso em um pacote do Access, ele deve ter pelo menos uma função de recurso que possa ser atribuída. As funções são definidas pelo próprio aplicativo e são gerenciadas no Azure AD. Observe que o portal do Azure também pode mostrar entidades de serviço para serviços que não podem ser selecionados como aplicativos.  Em particular, o **Exchange Online** e o **SharePoint Online** são serviços, não aplicativos que têm funções de recurso no diretório, para que não possam ser incluídos em um pacote de acesso.  Em vez disso, use o licenciamento baseado em grupo para estabelecer uma licença apropriada para um usuário que precisa acessar esses serviços.

* Para que um grupo seja um recurso em um pacote do Access, ele deve ser possível modificá-lo no Azure AD.  Os grupos que se originam em um Active Directory local não podem ser atribuídos como recursos porque seus atributos de proprietário ou membro não podem ser alterados no Azure AD.  

* As bibliotecas de documentos do SharePoint Online e os documentos individuais não podem ser adicionados como recursos.  Em vez disso, crie um grupo de segurança do Azure AD, inclua esse grupo e uma função de site no pacote de acesso e, no SharePoint Online, use esse grupo para controlar o acesso à biblioteca de documentos ou ao documento.

* Se houver usuários que já foram atribuídos a um recurso que você deseja gerenciar com um pacote do Access, certifique-se de que os usuários sejam atribuídos ao pacote do Access com uma política apropriada. Por exemplo, talvez você queira incluir um grupo em um pacote de acesso que já tenha usuários no grupo. Se esses usuários no grupo exigirem acesso contínuo, eles deverão ter uma política apropriada para os pacotes de acesso para que não percam seu acesso ao grupo. Você pode atribuir o pacote de acesso solicitando que os usuários solicitem o pacote de acesso que contém esse recurso ou atribuindo-os diretamente ao pacote de acesso. Para obter mais informações, consulte [Editar e gerenciar um pacote de acesso existente](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Lista de verificação para fornecer acesso a usuários externos

* Se houver uma lista de [permissões](../b2b/allow-deny-list.md)B2B, os usuários cujos diretórios não são permitidos não poderão solicitar acesso.

* Verifique se não há [políticas de acesso condicional](../conditional-access/require-managed-devices.md) que impeçam que usuários externos solicitem acesso ou possam usar os aplicativos nos pacotes de acesso.

## <a name="checklist-for-request-issues"></a>Lista de verificação para problemas de solicitação

* Quando um usuário quiser solicitar acesso a um pacote do Access, verifique se ele está usando o **link meu portal de acesso** para o pacote de acesso. Para obter mais informações, consulte [copiar meu portal de acesso link](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Quando um usuário entrar no portal meu acesso para solicitar um pacote de acesso, certifique-se de autenticar usando sua conta institucional. A conta institucional pode ser uma conta no diretório de recursos ou em um diretório que está incluído em uma das políticas do pacote de acesso. Se a conta do usuário não for uma conta organizacional ou o diretório não estiver incluído na política, o usuário não verá o pacote de acesso. Para obter mais informações, consulte [solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um usuário estiver impedido de entrar no diretório de recursos, ele não poderá solicitar acesso no meu portal de acesso. Antes que o usuário possa solicitar acesso, você deve remover o bloco de entrada do perfil do usuário. Para remover o bloco de entrada, na portal do Azure, clique em **Azure Active Directory**, clique em **usuários**, clique no usuário e, em seguida, clique em **perfil**. Edite a seção **configurações** e altere a **entrada de bloco** para **não**. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um usuário usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Você também pode verificar se o usuário foi bloqueado devido a uma [política de proteção de identidade](../identity-protection/howto-unblock-user.md).

* No portal meu acesso, se um usuário for um solicitante e um aprovador, ele não verá a solicitação de um pacote de acesso na página aprovações  . Esse comportamento é intencional: um usuário não pode aprovar sua própria solicitação. Verifique se o pacote de acesso que eles estão solicitando tem aprovadores adicionais configurados na política. Para obter mais informações, consulte [Editar uma política existente](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Se um novo usuário externo, que não tenha sido assinado anteriormente no seu diretório, receber um pacote de acesso, incluindo um site do SharePoint Online, seu pacote de acesso será exibido como não totalmente entregue até que sua conta seja provisionada no SharePoint Online.

## <a name="next-steps"></a>Próximas etapas

- [Exibir relatórios de como os usuários têm acesso no gerenciamento de direitos](entitlement-management-reports.md)
