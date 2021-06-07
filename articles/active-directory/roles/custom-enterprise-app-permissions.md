---
title: Permissões de aplicativo para funções personalizadas no Azure Active Directory | Microsoft Docs
description: Veja uma versão prévia das permissões de aplicativo empresarial para as funções personalizadas do Azure AD no portal do Azure, no PowerShell ou na API do Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466786"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de aplicativo empresarial para funções personalizadas no Azure Active Directory

Este artigo contém as permissões de aplicativo empresarial disponíveis atualmente para definições de função personalizadas no Azure AD (Azure Active Directory). Neste artigo, você encontrará as listas de permissões para alguns cenários comuns e a lista completa de permissões de aplicativo empresarial. Atualmente, as permissões de Proxy de Aplicativo não estão implantadas nesta versão.

## <a name="required-license-plan"></a>Plano de licença necessária

O uso desse recurso requer uma licença do Azure AD Premium P1 para sua organização do Azure AD. Para localizar a licença correta para os requisitos, consulte [Comparar recursos geralmente disponíveis nas edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Permissões de aplicativo empresarial

Para obter mais informações sobre como usar essas permissões, confira [Atribuir funções personalizadas para gerenciar aplicativos empresariais](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Atribuir usuários ou grupos a um aplicativo

Para delegar a atribuição de usuário e grupos que podem acessar aplicativos de logon único baseados em SAML. Permissões necessárias

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>Criar aplicativos da galeria

Para delegar a criação de aplicativos da Galeria do Azure AD, como ServiceNow, F5, Salesforce, entre outros. Permissões necessárias:

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>Configurar URLs SAML básicas

Para delegar a atualização e a leitura de Configurações de SAML básicas para aplicativos de logon único baseados em SAML. Permissões necessárias:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>Implantar ou criar certificados de autenticação

Para delegar o gerenciamento dos certificados de autenticação para aplicativos de logon único baseados em SAML. Permissões necessárias.

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Atualizar o endereço de email de notificação de certificado de entrada expirando

Para delegar a atualização de endereços de email de notificação de certificados de entrada expirados para aplicativos de logon único baseados em SAML. Permissões necessárias:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Gerenciar assinatura de token SAML e o algoritmo de entrada

Para delegar a atualização da assinatura do token SAML e do algoritmo de entrada para aplicativos de logon único baseados em SAML. Permissões necessárias:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>Gerenciar declarações e atributos de usuário

Para delegar a criação, a exclusão e a atualização de atributos e declarações de usuário para aplicativos de logon único baseados em SAML. Permissões necessárias:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>Permissões de provisionamento de aplicativo

A execução de qualquer operação de gravação, como gerenciar o trabalho, o esquema ou as credenciais por meio da interface do usuário, também exigirá as permissões de leitura para exibir a página de provisionamento.

Definir o escopo para todos os usuários e grupos ou para os usuários e grupos atribuídos atualmente requer as permissões synchronizationJob e synchronizationCredentials.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Ativar ou reiniciar trabalhos de provisionamento

Para delegar a capacidade de ativar, desativar e reiniciar trabalhos de provisionamento. Permissões necessárias:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>Configurar o esquema de provisionamento  

Para delegar atualizações para o mapeamento de atributos. Permissões necessárias:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Ler as configurações de provisionamento associadas ao objeto de aplicativo

Para delegar a capacidade de ler as configurações de provisionamento associadas ao objeto. Permissões necessárias:

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Ler as configurações de provisionamento associadas à entidade de serviço

Para delegar a capacidade de ler as configurações de provisionamento associadas à entidade de serviço. Permissões necessárias:

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>Autorizar o acesso ao aplicativo para provisionamento  

Para delegar a capacidade de autorizar o acesso ao aplicativo para provisionamento. Exemplo do token de portador OAuth de entrada. Permissões necessárias:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>Lista completa de permissões

Permissão | Descrição
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | Ler todas as propriedades nas políticas de aplicativo.
microsoft.directory/applicationPolicies/allProperties/update | Atualizar todas as propriedades nas políticas de aplicativo.
microsoft.directory/applicationPolicies/basic/update | Atualizar as propriedades padrão das políticas do aplicativo.
microsoft.directory/applicationPolicies/create | Criar políticas de aplicativo.
microsoft.directory/applicationPolicies/createAsOwner | Criar políticas de aplicativo. O criador é adicionado como o primeiro proprietário.
microsoft.directory/applicationPolicies/delete | Excluir as políticas de aplicativo.
microsoft.directory/applicationPolicies/owners/read | Ler os proprietários nas políticas de aplicativo.
microsoft.directory/applicationPolicies/owners/update | Atualizar a propriedade de proprietário das políticas de aplicativo.
microsoft.directory/applicationPolicies/policyAppliedTo/read | Ler as políticas de aplicativo aplicadas à lista de objetos.
microsoft.directory/applicationPolicies/standard/read | Ler as propriedades padrão das políticas de aplicativo.
microsoft.directory/servicePrincipals/allProperties/allTasks | Criar e excluir servicePrincipals e ler e atualizar todas as propriedades no Azure Active Directory.
microsoft.directory/servicePrincipals/allProperties/read | Ler todas as propriedades dos servicePrincipals.
microsoft.directory/servicePrincipals/allProperties/update | Atualizar todas as propriedades dos servicePrincipals.
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Ler as atribuições de função da entidade de serviço.
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Atualizar as atribuições de função da entidade de serviço.
microsoft.directory/servicePrincipals/appRoleAssignments/read | Ler as atribuições de função atribuídas a entidades de serviço.
microsoft.directory/servicePrincipals/audience/update | Atualizar as propriedades de público-alvo nas entidades de serviço.
microsoft.directory/servicePrincipals/authentication/update | Atualizar as propriedades de autenticação nas entidades de serviço.
microsoft.directory/servicePrincipals/basic/update | Atualizar as propriedades básicas nas entidades de serviço.
microsoft.directory/servicePrincipals/create | Criar entidades de serviço.
microsoft.directory/servicePrincipals/createAsOwner | Criar entidades de serviço. O criador é adicionado como o primeiro proprietário.
microsoft.directory/servicePrincipals/credentials/update | Atualizar as propriedades de credenciais nas entidades de serviço.
microsoft.directory/servicePrincipals/delete | Excluir as entidades de serviço.
microsoft.directory/servicePrincipals/disable | Desabilitar as entidades de serviço.
microsoft.directory/servicePrincipals/enable | Habilitar as entidades de serviço.
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Ler as credenciais de logon único de senha nas entidades de serviço.
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Gerenciar as credenciais de logon único de senha em entidades de serviço.
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Ler as concessões de permissão delegada nas entidades de serviço.
microsoft.directory/servicePrincipals/owners/read | Ler os proprietários nas entidades de serviço.
microsoft.directory/servicePrincipals/owners/update | Atualizar os proprietários nas entidades de serviço.
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | Ler as políticas nas entidades de serviço.
microsoft.directory/servicePrincipals/policies/update | Atualizar as políticas nas entidades de serviço.
microsoft.directory/servicePrincipals/standard/read | Ler as propriedades padrão das entidades de serviço.
microsoft.directory/servicePrincipals/synchronization/standard/read | Ler as configurações de provisionamento associadas à entidade de serviço.
microsoft.directory/servicePrincipals/tag/update | Atualizar a propriedade de marcas nas entidades de serviço.
microsoft.directory/applicationTemplates/instantiate | Criar uma instância de aplicativos de galeria por meio de modelos de aplicativo.
microsoft.directory/auditLogs/allProperties/read | Ler logs de auditoria.
microsoft.directory/signInReports/allProperties/read | Ler os relatórios de entrada.
microsoft.directory/applications/synchronization/standard/read | Ler as configurações de provisionamento associadas ao objeto de aplicativo.
microsoft.directory/servicePrincipals/synchronizationJobs/manage | Gerenciar todos os aspectos da sincronização de trabalhos para recursos de entidade de serviço
microsoft.directory/servicePrincipals/synchronization/standard/read | Ler as configurações de provisionamento associadas às entidades de serviço
microsoft.directory/servicePrincipals/synchronizationSchema/manage | Gerenciar todos os aspectos da sincronização de esquemas para recursos de entidade de serviço
microsoft.directory/provisioningLogs/allProperties/read | Ler todas as propriedades de logs de provisionamento

## <a name="next-steps"></a>Próximas etapas

- [Criar funções personalizadas usando o portal do Azure, o Azure AD PowerShell e a API do Graph](custom-create.md)
- [Listar atribuições de função](view-assignments.md)
