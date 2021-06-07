---
title: Referência de atividades de auditoria do Azure AD (Azure AD) | Microsoft Docs
description: Obtenha uma visão geral das atividades de auditoria que podem ser registradas em log nos seus logs de auditoria do Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f9976faf2a803e747bce8b851a3555e6d746b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89231139"
---
# <a name="azure-ad-audit-activity-reference"></a>Referência das atividades de auditoria do Azure AD

Com os relatórios do Azure AD (Azure Active Directory), é possível obter as informações necessárias para determinar o desempenho do ambiente.

A arquitetura de relatório no Azure AD consiste nos seguintes componentes:

- **Relatórios de atividades** 
    - [Entradas](concept-sign-ins.md) – Fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário
    - [Logs de auditoria](concept-audit-logs.md) – fornece rastreamento por meio de logs para todas as alterações feitas por vários recursos no Azure AD. 
    
- **Relatórios de segurança** 
    - [Entradas arriscadas](../identity-protection/overview-identity-protection.md) -uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário. 
    - [Usuários sinalizados para risco](../identity-protection/overview-identity-protection.md) – um usuário arriscado é um indicador para uma conta de usuário que pode ter sido comprometida. 

Este artigo lista as atividades de auditoria que podem ser registradas nos logs de auditoria.

## <a name="access-reviews"></a>Análises de acesso

|Auditar categoria|Atividade|
|---|---|
|Revisões de acesso|Revisão de acesso encerrada|
|Revisões de acesso|Adicionar aprovador para a aprovação de solicitação|
|Revisões de acesso|Adicionar revisor para a revisão de acesso|
|Revisões de acesso|Aplicar revisão de acesso|
|Revisões de acesso|Criar a revisão de acesso|
|Revisões de acesso|Criar programa|
|Revisões de acesso|Criar solicitação de aprovação|
|Revisões de acesso|Excluir revisão de acesso|
|Revisões de acesso|Excluir programa|
|Revisões de acesso|Vincular controle do programa|
|Revisões de acesso|Integrar com as Revisões de Acesso do Azure AD|
|Revisões de acesso|Remover revisor da revisão de acesso|
|Revisões de acesso|Solicitar interrupção da análise|
|Revisões de acesso|Solicitar aplicação dos resultados da análise|
|Revisões de acesso|Examinar associação de função RBAC|
|Revisões de acesso|Revisar atribuição do aplicativo|
|Revisões de acesso|Examinar a associação de grupo|
|Revisões de acesso|Revisar solicitação de aprovação de solicitação|
|Revisões de acesso|Desvincular controle do programa|
|Revisões de acesso|Atualizar revisão de acesso|
|Revisões de acesso|Atualizar o status de Integração das Revisões de Acesso do Azure AD|
|Revisões de acesso|Atualizar configurações de notificação de email da revisão de acesso|
|Revisões de acesso|Atualizar a configuração de contagem de recorrências da revisão de acesso|
|Revisões de acesso|Atualizar a duração de recorrência da revisão de acesso na configuração de dias|
|Revisões de acesso|Atualizar a configuração do tipo final de recorrência da revisão de acesso|
|Revisões de acesso|Atualizar a configuração do tipo de recorrência da revisão de acesso|
|Revisões de acesso|Atualizar configurações de lembrete de revisão de acesso|
|Revisões de acesso|Atualizar programa|
|Revisões de acesso|Atualizar aprovação de solicitação|
|Revisões de acesso|Usuário desabilitado|

## <a name="account-provisioning"></a>Provisionamento de conta

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de aplicativos|Recuperar concessões de permissões de aplicativo V2|
|Gerenciamento de aplicativos|Recuperar entidades de serviço de aplicativo V2 no locatário atual|
|Gerenciamento de aplicativos|Atualizar aplicativo V1|
|Gerenciamento de aplicativos|Atualizar aplicativo V2|
|Gerenciamento de aplicativos|Atualizar concessão de permissão de aplicativo V2|
|Gerenciamento de aplicativos|Adicionar Oauth2Permissiongrant|
|Gerenciamento de aplicativos|Adicionar atribuição de função de aplicativo à entidade de serviço|

## <a name="application-proxy"></a>Proxy de aplicativo

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de aplicativos|Adicionar aplicativo|
|Gerenciamento de aplicativos|Adicionar proprietário ao aplicativo|
|Gerenciamento de aplicativos|Adicionar proprietário à entidade de serviço|
|Gerenciamento de aplicativos|Adicionar política à entidade de serviço|
|Gerenciamento de diretórios|Adicionar entidade de serviço|
|Gerenciamento de diretórios|Adicionar credenciais de entidade de serviço|
|Gerenciamento de diretórios|Autorizar aplicativo|
|Gerenciamento de diretórios|Excluir aplicativo|
|Gerenciamento de diretórios|Excluir aplicativo irreversivelmente|
|Gerenciamento de diretórios|Remover OAuth2PermissionGrant|
|Gerenciamento de diretórios|Remover a atribuição de função de aplicativo da entidade de serviço|
|Gerenciamento de diretórios|Remover proprietário do aplicativo|
|Recurso|Remover proprietário da entidade de serviço|
|Recurso|Remover política da entidade de serviço|
|Recurso|Remover entidade de serviço|


## <a name="automated-password-rollover"></a>Substituição de senha automática

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de aplicativos|Remover credenciais de entidade de serviço|


## <a name="b2c"></a>B2C

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de aplicativos|Restaurar aplicativo|
|Gerenciamento de aplicativos|Revogar autorização|
|Gerenciamento de aplicativos|Atualizar aplicativo|
|Gerenciamento de aplicativos|Atualizar segredos externos|
|Gerenciamento de aplicativos|Atualizar entidade de serviço|
|Gerenciamento de aplicativos|Emitir um token de acesso para o aplicativo|
|Gerenciamento de aplicativos|Emitir um código de autorização para o aplicativo|
|Gerenciamento de aplicativos|Emitir um id_token para o aplicativo|
|Gerenciamento de aplicativos|Validar credenciais de conta local|
|Gerenciamento de aplicativos|Validar autenticação de usuário|
|Gerenciamento de aplicativos|Adicionar permissões da aplicativo V2|
|Gerenciamento de aplicativos|Adicionar uma chave baseada em segredo ASCII a um contêiner de chave CPIM|
|Gerenciamento de aplicativos|Adicionar uma chave a um contêiner de chave CPIM|
|Gerenciamento de aplicativos|AdminPolicyDatas-SetResources|
|Gerenciamento de aplicativos|AdminUserJourneys-GetResources|
|Gerenciamento de aplicativos|AdminUserJourneys-RemoveResources|
|Autenticação|AdminUserJourneys-SetResources|
|Autenticação|Criar IdentityProvider|
|Autenticação|Criar aplicativo V1|
|Autenticação|Criar aplicativo V2|
|Autenticação|Criar um domínio personalizado no locatário|
|Autorização|Criar um novo AdminUserJourney|
|Autorização|Criar json de recurso localizado|
|Autorização|Criar novo IDP personalizado|
|Autorização|Criar novo IDP|
|Autorização|Criar ou atualizar um recurso de diretório do B2C|
|Autorização|Criar política|
|Autorização|Criar política trustFramework|
|Autorização|Criar política trustFramework com prefixo configurável|
|Autorização|Criar atributo de usuário|
|Autorização|CreateTrustFrameworkPolicy|
|Autorização|Cria ou atualiza um novo AdminUserJourney|
|Autorização|Excluir IDP|
|Autorização|Excluir IdentityProvider|
|Autorização|Excluir aplicativo V1|
|Autorização|Excluir aplicativo V2|
|Autorização|Excluir concessão de permissão de aplicativo V2|
|Autorização|Excluir um recurso do diretório do B2C|
|Autorização|Excluir um contêiner de chave CPIM|
|Autorização|Excluir política trustFramework|
|Autorização|Excluir atributo de usuário|
|Autorização|Habilitar recurso do B2C|
|Autorização|Obter os recursos de diretório do B2C em uma assinatura|
|Autorização|Obter IDP personalizado|
|Autorização|Obter IDP|
|Autorização|Obter aplicativos V1 e V2|
|Autorização|Obter aplicativo V1|
|Autorização|Obter aplicativos V1|
|Autorização|Obter aplicativo V2|
|Autorização|Obter aplicativos V2|
|Autorização|Obter recurso do Active Directory B2C|
|Autorização|Obter uma lista de domínios personalizados no locatário|
|Autorização|Obter um Percurso do Usuário|
|Autorização|Obter declarações do aplicativo permitidas para o Percurso do Usuário|
|Autorização|Obter declarações autodeclaradas permitidas para o Percurso do Usuário|
|Autorização|Obter declarações autodeclaradas permitidas de política|
|Autorização|Obter lista de declarações de saída disponíveis|
|Autorização|Obter definições de conteúdo para o Percurso do Usuário|
|Autorização|Obter IDPs para um fluxo específico do administrador|
|Autorização|Obter metadados de chave ativa do contêiner de chave em JWK|
|Autorização|Obter lista de todos os fluxos de administrador|
|Autorização|Obter lista de marcas de todos os fluxos de administrador para todos os usuários|
|Autorização|Obter lista de locatários de um usuário|
|Autorização|Obter declarações autodeclaradas das contas locais|
|Autorização|Obter json de recurso localizado|
|Autorização|Obter operações do provedor de recursos Microsoft.AzureActiveDirectory|
|Autorização|Obter políticas|
|Autorização|Obter política|
|Autorização|Obter propriedades de recurso de um locatário|
|Autorização|Obter lista de IDP com suporte|
|Autorização|Obter lista de IDP com suporte do Percurso do Usuário|
|Autorização|Obter informações do locatário|
|Autorização|Obter recursos permitidos para o locatário|
|Autorização|Obter lista de IDPs personalizadas definida pelo locatário|
|Autorização|Obter lista de IDPs definida pelo locatário|
|Autorização|Obter lista de IDPs locais definida pelo locatário|
|Autorização|Obter detalhes do locatário de um usuário para a criação de recursos|
|Autorização|Obter lista de locatários|
|Autorização|Obter tenantDomains|
|Autorização|Obter a cultura com suporte padrão para CPIM|
|Autorização|Obter os detalhes de um fluxo de administrador|
|Autorização|Obter a lista de UserJourneys para o locatário|
|Autorização|Obter o conjunto de culturas com suporte disponíveis para CPIM|
|Autorização|Obter política trustFramework|
|Autorização|Obter política trustFramework como xml|
|Autorização|Editar atributo de usuário|
|Autorização|Obter atributos de usuário|
|Autorização|Obter lista de Percurso do Usuário|
|Autorização|GetIEFPolicies|
|Autorização|GetIdentityProviders|
|Autorização|GetTrustFrameworkPolicy|
|Autorização|Obtém um contêiner de chave CPIM em formato jwk|
|Autorização|Obtém uma lista de contêineres de chave no locatário|
|Autorização|Obtém o tipo de locatário|
|Autorização|MigrateTenantMetadata|
|Autorização|Fazer patch de IdentityProvider|
|Autorização|PutTrustFrameworkPolicy|
|Autorização|PutTrustFrameworkpolicy|
|Autorização|Remover um Percurso do Usuário|
|Autorização|Restaurar um backup de contêiner de chave CPIM|
|Autorização|Recuperar concessões de permissões de aplicativo V2|
|Autorização|Recuperar entidades de serviço de aplicativo V2 no locatário atual|
|Autorização|Atualizar IDP personalizado|
|Autorização|Atualizar IDP|
|Autorização|Atualizar IDP local|
|Autorização|Atualizar aplicativo V1|
|Autorização|Atualizar aplicativo V2|
|Autorização|Atualizar concessão de permissão de aplicativo V2|
|Autorização|Atualizar política|
|Autorização|Atualizar atributo de usuário|
|Autorização|Carregar uma chave CPIM criptografada|
|Autorização|Autorização do usuário: a API está desabilitada para o conjunto de recursos do locatário|
|Autorização|Autorização do usuário: acesso concedido ao usuário como ‘Administrador do locatário’|
|Autorização|Autorização do usuário: direitos de acesso concedidos ao usuário como 'Usuários autenticados'|
|Autorização|Verificar se o recurso B2C está habilitado|
|Autorização|Verificar se o recurso está habilitado|
|Autorização|Criar programa|
|Autorização|Excluir programa|
|Autorização|Vincular controle do programa|
|Autorização|Integrar com as Revisões de Acesso do Azure AD|
|Autorização|Desvincular controle do programa|
|Autorização|Atualizar programa|
|Autorização|Desabilitar SSO da Área de Trabalho|
|Autorização|Desabilitar SSO da Área de Trabalho para um domínio específico|
|Autorização|Desabilitar proxy de aplicativo|
|Autorização|Desabilitar autenticação de passagem|
|Autorização|Habilitar SSO da Área de Trabalho|
|Gerenciamento de diretórios|Habilitar SSO da Área de Trabalho para um domínio específico|
|Gerenciamento de diretórios|Habilitar proxy de aplicativo|
|Gerenciamento de diretórios|Habilitar autenticação de passagem|
|Gerenciamento de diretórios|Criar um domínio personalizado no locatário|
|Gerenciamento de diretórios|Habilitar recurso do B2C|
|Gerenciamento de diretórios|Obter uma lista de domínios personalizados no locatário|
|Gerenciamento de diretórios|Obter propriedades de recurso de um locatário|
|Gerenciamento de diretórios|Obter informações do locatário|
|Gerenciamento de diretórios|Obter recursos permitidos para o locatário|
|Gerenciamento de diretórios|Obter tenantDomains|
|Chave|Obtém o tipo de locatário|
|Chave|Verificar se o recurso B2C está habilitado|
|Chave|Verificar se o recurso está habilitado|
|Chave|Adicionar um parceiro à empresa|
|Chave|Adicionar domínio não verificado|
|Chave|Adicionar domínio verificado|
|Chave|Criar empresa|
|Chave|Criar configurações da empresa|
|Chave|Excluir configurações da empresa|
|Chave|Rebaixar parceiro|
|Chave|Diretório excluído|
|Outro|Diretório excluído permanentemente|
|Outro|Diretório agendado para exclusão|
|Recurso|Promover empresa à condição de parceira|
|Recurso|Limpar propriedades de gerenciamento de direitos|
|Recurso|Remover parceiro da empresa|
|Recurso|Remover domínio não verificado|
|Recurso|Remover domínio verificado|
|Recurso|Definir informações da empresa|
|Recurso|Definir recurso DirSync|
|Recurso|Definir sinalizador DirSyncEnabled|
|Recurso|Definir parceria|
|Recurso|Definir limite de exclusão acidental|
|Recurso|Definir local de dados permitido da empresa|
|Recurso|Definir recurso multinacional de empresa como habilitado|
|Recurso|Definir recurso de diretório no locatário|
|Recurso|Definir a autenticação de domínio|
|Recurso|Definir configurações de federação no domínio|
|Recurso|Definir política de senha|
|Recurso|Definir propriedades de gerenciamento de direitos|
|Recurso|Atualizar empresa|
|Recurso|Atualizar configurações da empresa|
|Recurso|Domínio de atualização|
|Recurso|Verificar domínio|
|Recurso|Verificar domínio por email|
|Recurso|Integração|
|Recurso|Atualizar configurações de alerta|
|Recurso|Atualizar configurações de resumo semanal|
|Recurso|Desabilitar write-back de senha para o diretório|
|Recurso|Habilitar write-back de senha para o diretório|
|Recurso|Adicionar atribuição de função de aplicativo ao grupo|
|Recurso|Adicionar grupo|
|Recurso|Adicionar membro ao grupo|
|Recurso|Adicionar proprietário ao grupo|
|Recurso|Criar configurações de grupo|
|Recurso|Excluir grupo|
|Recurso|Excluir configurações de grupo|
|Recurso|Concluir a aplicação de licença com base em grupo aos usuários|
|Recurso|Excluir grupo irreversivelmente|
|Recurso|Remover atribuição de função de aplicativo do grupo|
|Recurso|Remover membro do grupo|
|Recurso|Remover proprietário do grupo|
|Recurso|Restaurar grupo|
|Recurso|Definir licença de grupo|
|Recurso|Definir grupo a ser gerenciado pelo usuário|
|Recurso|Iniciar a aplicação de licença com base em grupo aos usuários|
|Recurso|Disparar recálculo de licença do grupo|
|Recurso|Atualizar grupo|
|Recurso|Atualizar configurações de grupo|
|Recurso|Adicionar membro|
|Recurso|Criar Grupo|
|Recurso|Excluir grupo|
|Recurso|Remover membro|
|Recurso|Atualizar grupo|
|Recurso|Aprovar uma solicitação pendente para ingressar em um grupo|
|Recurso|Cancelar uma solicitação pendente para ingressar em um grupo|
|Recurso|Criar política de gerenciamento de ciclo de vida|
|Recurso|Excluir uma solicitação pendente para ingressar em um grupo|
|Recurso|Rejeitar uma solicitação pendente para ingressar em um grupo|
|Recurso|Renovar grupo|
|Recurso|Solicitar ingresso em um grupo|
|Recurso|Definir propriedades de grupo dinâmico|
|Recurso|Atualizar política de gerenciamento de ciclo de vida|
|Recurso|Adicionar uma chave baseada em segredo ASCII a um contêiner de chave CPIM|
|Recurso|Adicionar uma chave a um contêiner de chave CPIM|
|Recurso|Excluir um contêiner de chave CPIM|
|Recurso|Excluir contêiner de chave|
|Recurso|Obter metadados de chave ativa do contêiner de chave em JWK|
|Recurso|Obter metadados do contêiner de chave|
|Recurso|Obtém um contêiner de chave CPIM em formato jwk|
|Recurso|Obtém uma lista de contêineres de chave no locatário|
|Recurso|Restaurar um backup de contêiner de chave CPIM|
|Recurso|Salvar contêiner de chave|
|Recurso|Carregar uma chave CPIM criptografada|
|Recurso|Emitir um código de autorização para o aplicativo|
|Recurso|Emitir um id_token para o aplicativo|


## <a name="core-directory"></a>Diretório principal

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de unidades administrativas|Baixar um tipo de detecção de risco único|
|Gerenciamento de unidades administrativas|Baixar administradores e status de aceitação do resumo semanal|
|Gerenciamento de unidades administrativas|Baixar todos os tipos de detecção de riscos|
|Gerenciamento de unidades administrativas|Baixar detecções de risco de usuário gratuitas|
|Gerenciamento de unidades administrativas|Baixar usuários sinalizados por risco|
|Gerenciamento de aplicativos|Convites do Lote processados|
|Gerenciamento de aplicativos|Convites do Lote carregados|
|Gerenciamento de aplicativos|Adicionar proprietário à política|
|Gerenciamento de aplicativos|Adicionar política|
|Gerenciamento de aplicativos|Excluir política|
|Gerenciamento de aplicativos|Remover credenciais de política|
|Gerenciamento de aplicativos|Atualizar política|
|Gerenciamento de aplicativos|Definir política de registro MFA|
|Gerenciamento de aplicativos|Definir política de risco de entrada|
|Gerenciamento de aplicativos|Definir política de risco do usuário|
|Gerenciamento de aplicativos|Aceitar os Termos de Uso|
|Gerenciamento de aplicativos|Criar Termos de Uso|
|Gerenciamento de aplicativos|Recusar Termos de Uso|
|Gerenciamento de aplicativos|Excluir Termos de Uso|
|Gerenciamento de aplicativos|Editar Termos de Uso|
|Gerenciamento de aplicativos|Publicar Termos de Uso|
|Gerenciamento de aplicativos|Cancelar a publicação dos Termos de Uso|
|Gerenciamento de aplicativos|Adicionar certificado TLS/SSL do aplicativo|
|Gerenciamento de aplicativos|Excluir Associação TLS|
|Gerenciamento de aplicativos|Registrar conector|
|Gerenciamento de aplicativos|AdminPolicyDatas-RemoveResources|
|Gerenciamento de aplicativos|AdminPolicyDatas-SetResources|
|Gerenciamento de aplicativos|AdminUserJourneys-GetResources|
|Gerenciamento de diretórios|AdminUserJourneys-RemoveResources|
|Gerenciamento de diretórios|AdminUserJourneys-SetResources|
|Gerenciamento de diretórios|Criar IdentityProvider|
|Gerenciamento de diretórios|Criar um novo AdminUserJourney|
|Gerenciamento de diretórios|Criar json de recurso localizado|
|Gerenciamento de diretórios|Criar novo IDP personalizado|
|Gerenciamento de diretórios|Criar novo IDP|
|Gerenciamento de diretórios|Criar ou atualizar um recurso de diretório do B2C|
|Gerenciamento de diretórios|Criar política|
|Gerenciamento de diretórios|Criar política trustFramework|
|Gerenciamento de diretórios|Criar política trustFramework com prefixo configurável|
|Gerenciamento de diretórios|Criar atributo de usuário|
|Gerenciamento de diretórios|CreateTrustFrameworkPolicy|
|Gerenciamento de diretórios|Excluir IDP|
|Gerenciamento de diretórios|Excluir IdentityProvider|
|Gerenciamento de diretórios|Excluir um recurso do diretório do B2C|
|Gerenciamento de diretórios|Excluir política trustFramework|
|Gerenciamento de diretórios|Excluir atributo de usuário|
|Gerenciamento de diretórios|Obter recursos de diretório do B2C em um grupo de recursos|
|Gerenciamento de diretórios|Obter os recursos de diretório do B2C em uma assinatura|
|Gerenciamento de diretórios|Obter IDP personalizado|
|Gerenciamento de diretórios|Obter IDP|
|Gerenciamento de diretórios|Obter recurso do Active Directory B2C|
|Gerenciamento de diretórios|Obter um Percurso do Usuário|
|Gerenciamento de diretórios|Obter declarações do aplicativo permitidas para o Percurso do Usuário|
|Gerenciamento de diretórios|Obter declarações autodeclaradas permitidas para o Percurso do Usuário|
|Gerenciamento de diretórios|Obter declarações autodeclaradas permitidas de política|
|Gerenciamento de diretórios|Obter lista de declarações de saída disponíveis|
|Gerenciamento de diretórios|Obter definições de conteúdo para o Percurso do Usuário|
|Gerenciamento de diretórios|Obter IDPs para um fluxo específico do administrador|
|Gerenciamento de diretórios|Obter lista de todos os fluxos de administrador|
|Gerenciamento de diretórios|Obter lista de marcas de todos os fluxos de administrador para todos os usuários|
|Gerenciamento de grupos|Membros do grupo de download em massa-iniciados|
|Gerenciamento de grupos|Membros do grupo de download em massa-concluído|
|Gerenciamento de grupos|Membros do grupo de importação em massa-iniciados|
|Gerenciamento de grupos|Membros do grupo de importação em massa-concluídos|
|Gerenciamento de grupos|Remoção de membros do grupo em massa-iniciada|
|Gerenciamento de grupos|Remoção de membros do grupo em massa – concluída|
|Gerenciamento de grupos|Grupos de download em massa-iniciados|
|Gerenciamento de grupos|Grupos de download em massa-concluídos|
|Gerenciamento de grupos|Obter lista de locatários de um usuário|
|Gerenciamento de grupos|Obter declarações autodeclaradas das contas locais|
|Gerenciamento de grupos|Obter json de recurso localizado|
|Gerenciamento de grupos|Obter operações do provedor de recursos Microsoft.AzureActiveDirectory|
|Gerenciamento de grupos|Obter políticas|
|Gerenciamento de grupos|Obter política|
|Gerenciamento de grupos|Obter lista de IDP com suporte|
|Gerenciamento de grupos|Obter lista de IDP com suporte do Percurso do Usuário|
|Gerenciamento de grupos|Obter lista de IDPs personalizadas definida pelo locatário|
|Gerenciamento de grupos|Obter lista de IDPs definida pelo locatário|
|Gerenciamento de grupos|Obter lista de IDPs locais definida pelo locatário|
|Gerenciamento de grupos|Obter detalhes do locatário de um usuário para a criação de recursos|
|Gerenciamento de grupos|Obter a cultura com suporte padrão para CPIM|
|Gerenciamento de grupos|Obter os detalhes de um fluxo de administrador|
|Gerenciamento de grupos|Obter a lista de UserJourneys para o locatário|
|Gerenciamento de grupos|Obter o conjunto de culturas com suporte disponíveis para CPIM|
|Gerenciamento de grupos|Obter política trustFramework|
|Gerenciamento de grupos|Obter política trustFramework como xml|
|Gerenciamento de grupos|Editar atributo de usuário|
|Gerenciamento de política|Obter atributos de usuário|
|Gerenciamento de política|Obter lista de Percurso do Usuário|
|Gerenciamento de política|GetIEFPolicies|
|Gerenciamento de política|GetIdentityProviders|
|Gerenciamento de política|GetTrustFrameworkPolicy|
|Recurso|MigrateTenantMetadata|
|Recurso|Mover recursos|
|Recurso|Fazer patch de IdentityProvider|
|Recurso|PutTrustFrameworkPolicy|
|Recurso|PutTrustFrameworkpolicy|
|Recurso|Remover um Percurso do Usuário|
|Recurso|Atualizar IDP personalizado|
|Recurso|Atualizar IDP|
|Recurso|Atualizar IDP local|
|Recurso|Exibir um recurso de diretório do B2C|
|Recurso|Atualizar política|
|Recurso|Atualizar status da assinatura|
|Gerenciamento de funções|Atualizar atributo de usuário|
|Gerenciamento de funções|Validar movimentação de recursos|
|Gerenciamento de funções|Adicionar dispositivo|
|Gerenciamento de funções|Adicionar configuração do dispositivo|
|Gerenciamento de funções|Adicionar proprietário registrado ao dispositivo|
|Gerenciamento de funções|Adicionar usuários registrados ao dispositivo|
|Gerenciamento de funções|Excluir um dispositivo|
|Gerenciamento de funções|Excluir configuração de dispositivo|
|Gerenciamento de funções|O dispositivo não é mais compatível|
|Gerenciamento de funções|O dispositivo não é mais gerenciado|
|Gerenciamento de Usuários|AccessReview_Review|
|Gerenciamento de Usuários|AccessReview_Update|
|Gerenciamento de Usuários|ActivationAborted|
|Gerenciamento de Usuários|ActivationApproved|
|Gerenciamento de Usuários|ActivationCanceled|
|Gerenciamento de Usuários|ActivationRequested|
|Gerenciamento de Usuários|Adicionar membro qualificado à função|
|Gerenciamento de Usuários|Adicionar membro à função|
|Gerenciamento de Usuários|Adicionar atribuição de função à definição de função|
|Gerenciamento de Usuários|Adicionar função originada no modelo|
|Gerenciamento de Usuários|Adicionar membro no escopo à função|
|Gerenciamento de Usuários|Adicionado|
|Gerenciamento de Usuários|Atribuir|
|Gerenciamento de Usuários|Criação de usuários em massa-iniciada|
|Gerenciamento de Usuários|Criação de usuários em massa – concluída|
|Gerenciamento de Usuários|Exclusão de usuários em massa – iniciada|
|Gerenciamento de Usuários|Exclusão de usuários em massa concluída|
|Gerenciamento de Usuários|Usuários do download em massa-iniciados|
|Gerenciamento de Usuários|Usuários do download em massa-concluídos|
|Gerenciamento de Usuários|Restauração em massa de usuários excluídos-iniciado|
|Gerenciamento de Usuários|Restauração em massa de usuários excluídos-concluído|
|Gerenciamento de Usuários|Usuários de convite em massa-iniciados|
|Gerenciamento de Usuários|Usuários de convite em massa-concluídos|
|Gerenciamento de Usuários|Remover proprietário registrado do dispositivo|
|Gerenciamento de Usuários|Remover usuários registrados do dispositivo|
|Gerenciamento de Usuários|Remover membro qualificado da função|
|Gerenciamento de Usuários|Remover membro da função|
|Gerenciamento de Usuários|Remover atribuição de função da definição de função|
|Gerenciamento de Usuários|Remover membro no escopo da função|
|Gerenciamento de Usuários|Atualizar dispositivo|
|Gerenciamento de Usuários|Atualizar configuração do dispositivo|
|Gerenciamento de Usuários|Atualizar função|






## <a name="identity-protection"></a>Proteção de identidade

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de diretórios|Elevate|
|Gerenciamento de diretórios|Removido|
|Gerenciamento de diretórios|Alterações na configuração de função|
|Outro|ScanAlertsNow|
|Outro|Inscrever-se|
|Outro|Unelevate|
|Outro|UpdateAlertSettings|
|Outro|UpdateCurrentState|
|Gerenciamento de política|Revisão de acesso encerrada|
|Gerenciamento de política|Adicionar aprovador para a aprovação de solicitação|
|Gerenciamento de política|Adicionar revisor para a revisão de acesso|
|Gerenciamento de Usuários|Aplicar revisão de acesso|
|Gerenciamento de Usuários|Criar a revisão de acesso|


## <a name="invited-users"></a>Usuários convidados

|Auditar categoria|Atividade|
|---|---|
|Outro|Criar solicitação de aprovação|
|Outro|Excluir revisão de acesso|
|Gerenciamento de Usuários|Remover revisor da revisão de acesso|
|Gerenciamento de Usuários|Solicitar aplicação dos resultados da análise|
|Gerenciamento de Usuários|Solicitar interrupção da análise|
|Gerenciamento de Usuários|Revisar atribuição do aplicativo|
|Gerenciamento de Usuários|Examinar a associação de grupo|
|Gerenciamento de Usuários|Examinar associação de função RBAC|


## <a name="microsoft-identity-manager-mim"></a>MIM (Microsoft Identity Manager)

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de grupos|Revisar solicitação de aprovação de solicitação|
|Gerenciamento de grupos|Atualizar revisão de acesso|
|Gerenciamento de grupos|Atualizar configurações de notificação de email da revisão de acesso|
|Gerenciamento de grupos|Atualizar a configuração de contagem de recorrências da revisão de acesso|
|Gerenciamento de grupos|Atualizar a duração de recorrência da revisão de acesso na configuração de dias|
|Gerenciamento de Usuários|Atualizar a configuração do tipo final de recorrência da revisão de acesso|
|Gerenciamento de Usuários|Atualizar a configuração do tipo de recorrência da revisão de acesso|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Auditar categoria|Atividade|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Adicionado|
|PIM|AddedOutsidePIM|
|PIM|Atribuir|
|PIM|DismissAlert|
|PIM|Elevate|
|PIM|ReactivateAlert|
|PIM|Removido|
|PIM|RemovedOutsidePIM|
|PIM|Solicitar interrupção da análise|
|PIM|Alterações na configuração de função|
|PIM|ScanAlertsNow|
|PIM|Inscrever-se|
|PIM|Unassign|
|PIM|Unelevate|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Gerenciamento de grupo de autoatendimento

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de grupos|Redefinir senha de usuário|
|Gerenciamento de grupos|Restaurar usuário|
|Gerenciamento de grupos|Definir alteração forçada de senha de usuário|
|Gerenciamento de grupos|Definir gerenciador de usuários|
|Gerenciamento de grupos|Definir metadados de token OAuth de usuários como habilitados|
|Gerenciamento de grupos|Atualizar carimbo de data/hora StsRefreshTokenValidFrom|
|Gerenciamento de grupos|Atualizar segredos externos|
|Gerenciamento de grupos|Atualizar usuário|
|Gerenciamento de grupos|O administrador gera uma senha temporária|


## <a name="self-service-password-management"></a>Gerenciamento de senhas de autoatendimento

|Auditar categoria|Atividade|
|---|---|
|Gerenciamento de diretórios|Os administradores exigem que o usuário redefina a senha|
|Gerenciamento de diretórios|Atribuir usuário externo ao aplicativo|
|Gerenciamento de Usuários|Email não enviado; o usuário cancelou a assinatura|
|Gerenciamento de Usuários|Convidar usuário externo|
|Gerenciamento de Usuários|Resgatar convite para usuário externo|
|Gerenciamento de Usuários|Criação de locatário viral|
|Gerenciamento de Usuários|Criação de usuário viral|
|Gerenciamento de Usuários|Registro de senha do usuário|
|Gerenciamento de Usuários|Redefinição de senha do usuário|
|Gerenciamento de Usuários|Impedido de executar a redefinição de senha de autoatendimento|


## <a name="terms-of-use"></a>Termos de uso

|Auditar categoria|Atividade|
|---|---|
|Termos de Uso|Aceitar os Termos de Uso|
|Termos de Uso|Criar Termos de Uso|
|Termos de Uso|Recusar Termos de Uso|
|Termos de Uso|Excluir Consentimento|
|Termos de Uso|Excluir Termos de Uso|
|Termos de Uso|Editar Termos de Uso|
|Termos de Uso|Expirar Termos de Uso|
|Termos de Uso|Exclusão irreversível dos Termos de Uso|
|Termos de Uso|Publicar Termos de Uso|
|Termos de Uso|Cancelar a publicação dos Termos de Uso|


## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos relatórios do Azure AD](overview-reports.md).
- [Relatório de logs de auditoria](concept-audit-logs.md). 
- [Acesso programático aos relatórios do Microsoft Azure Active Directory](concept-reporting-api.md)