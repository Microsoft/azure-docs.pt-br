---
title: "Operações do provedor do Azure Resource Manager | Microsoft Docs"
description: "Fornece detalhes sobre as operações disponíveis nos provedores de recursos do Microsoft Azure Resource Manager"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 95c6a1e9cfadeb19a0ec5a53fffb15ef220bfec0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos do Azure Resource Manager

Este documento lista as operações disponíveis para cada provedor de recursos do Microsoft Azure Resource Manager. Elas podem ser usadas em funções personalizadas para fornecer permissões de RBAC (Controle de acesso baseado em função) completas para recursos no Azure. Observe que esta não é uma lista abrangente e outras operações podem ser adicionadas ou removidas conforme a atualização de cada provedor. Cadeias de caracteres de operação seguem o formato de `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Para obter uma lista abrangente e atual, use `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (na CLI do Azure) para listar as operações dos provedores de recursos do Azure.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operação | Descrição |
|---|---|
|/configuration/action|Atualizar a configuração do locatário.|
|/services/action|Atualizar uma instância de serviço no locatário.|
|/configuration/write|Criar uma configuração de locatário.|
|/configuration/read|Ler a configuração de locatário.|
|/services/write|Criar uma instância de serviço no locatário.|
|/services/read|Ler as instâncias de serviço no locatário.|
|/services/delete|Excluir uma instância de serviço no locatário.|
|/services/servicemembers/action|Criar uma instância de membro de serviço no serviço.|
|/services/servicemembers/read|Ler a instância do membro de serviço no serviço.|
|/services/servicemembers/delete|Excluir uma instância de membro de serviço no serviço.|
|/services/servicemembers/alerts/read|Ler os alertas de um membro de serviço.|
|/services/alerts/read|Ler os alertas de um serviço.|
|/services/alerts/read|Ler os alertas de um serviço.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operação | Descrição |
|---|---|
|/generateRecommendations/action|Gera recomendações|
|/suppressions/action|Criar/atualizar supressões|
|/register/action|Registra a assinatura do Microsoft Advisor|
|/generateRecommendations/read|Obter o status de gerar recomendações|
|/recommendations/read|Ler recomendações|
|/suppressions/read|Obter supressões|
|/suppressions/delete|Excluir supressão|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operação | Descrição |
|---|---|
|/servers/read|Recupera as informações do Analysis Server especificado.|
|/servers/write|Criar ou atualizar o Analysis Server especificado.|
|/servers/delete|Excluir o Analysis Server.|
|/servers/suspend/action|Suspende o Analysis Server.|
|/servers/resume/action|Retoma o Analysis Server.|
|/servers/checkNameAvailability<br>/action|Verificar se determinado nome do Analysis Server é válido e não está em uso.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operação | Descrição |
|---|---|
|/checkNameAvailability/action|Verificar se o nome do serviço fornecido está disponível|
|/register/action|Registrar a assinatura para o provedor de recursos Microsoft.ApiManagement|
|/unregister/action|Cancelar o registro da assinatura para o provedor de recursos Microsoft.ApiManagement|
|/service/write|Criar uma nova instância do Serviço de Gerenciamento de API|
|/service/read|Ler metadados de uma instância do Serviço de Gerenciamento de API|
|/service/delete|Excluir uma instância do Serviço de Gerenciamento de API|
|/service/updatehostname/action|Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API|
|/service/uploadcertificate/action|Carregar certificado SSL para um Serviço de Gerenciamento de API|
|/service/backup/action|Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário|
|/service/restore/action|Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário|
|/service/managedeployments/action|Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API|
|/service/getssotoken/action|Obter token de SSO que pode ser usado para fazer logon no portal de herdado do Serviço de Gerenciamento de API como administrador|
|/service/applynetworkconfigurationupdates/action|Atualiza os recursos Microsoft.ApiManagement em execução na rede virtual para obter configurações de rede atualizadas.|
|/service/operationresults/read|Obter o status atual da operação de longa duração|
|/service/networkStatus/read|Obter o status de acesso de rede dos recursos.|
|/service/loggers/read|Obter lista de agentes ou obter detalhes do agente|
|/service/loggers/write|Adicionar novo agente ou atualizar detalhes do agente existente|
|/service/loggers/delete|Remover agente existente|
|/service/users/read|Obter uma lista de usuários registrados ou obter detalhes da conta de um usuário|
|/service/users/write|Registrar um novo usuário ou atualizar detalhes da conta de um usuário existente|
|/service/users/delete|Remover conta de usuário|
|/service/users/generateSsoUrl/action|Gerar a URL do SSO. A URL pode ser usada para acessar o portal de administração|
|/service/users/subscriptions/read|Obter lista de assinaturas do usuário|
|/service/users/keys/read|Obter lista de chaves do usuário|
|/service/users/groups/read|Obter lista de grupos do usuário|
|/service/tenant/operationResults/read|Obter lista de resultados da operação ou obter o resultado de uma operação específica|
|/service/tenant/policy/read|Obter configuração de política do locatário|
|/service/tenant/policy/write|Configuração de política do locatário|
|/service/tenant/policy/delete|Remover configuração de política do locatário|
|/service/tenant/configuration/save/action|Criar confirmação com instantâneo de configuração para uma ramificação específica no repositório|
|/service/tenant/configuration/deploy/action|Executar uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados.|
|/service/tenant/configuration/validate/action|Valida as alterações da ramificação git especificada|
|/service/tenant/configuration/operationResults/read|Obter lista de resultados da operação ou obter o resultado de uma operação específica|
|/service/tenant/configuration/syncState/read|Obter status da última sincronização git|
|/service/tenant/access/read|Obter informações detalhadas de acesso do locatário|
|/service/tenant/access/write|Atualizar os detalhes de informações de acesso do locatário|
|/service/tenant/access/regeneratePrimaryKey/action|Regenerar a chave de acesso primária|
|/service/tenant/access/regenerateSecondaryKey/action|Regenerar a chave de acesso secundária|
|/service/identityProviders/read|Obter lista de provedores de identidade ou obter detalhes do provedor de identidade|
|/service/identityProviders/write|Criar um novo provedor de identidade atualizar detalhes de um provedor de identidade existente|
|/service/identityProviders/delete|Remover o provedor de identidade existente|
|/service/subscriptions/read|Obter uma lista de assinaturas de produtos ou obter os detalhes de assinatura do produto|
|/service/subscriptions/write|Inscrever um usuário existente para um produto existente ou atualizar os detalhes de uma assinatura existente. Essa operação pode ser usada para renovar a assinatura|
|/service/subscriptions/delete|Excluir assinatura. Essa operação pode ser usada para excluir a assinatura|
|/service/subscriptions/regeneratePrimaryKey/action|Regenerar a chave primária da assinatura|
|/service/subscriptions/regenerateSecondaryKey/action|Regenerar a chave secundária da assinatura|
|/service/backends/read|Obter lista de back-ends ou obter os detalhes de back-end|
|/service/backends/write|Adicionar um novo back-end ou atualizar os detalhes do back-end existente|
|/service/backends/delete|Remover o back-end existente|
|/service/apis/read|Obter lista de todas as APIs registradas ou obter detalhes da API|
|/service/apis/write|Criar nova API ou atualizar os detalhes da API existente|
|/service/apis/delete|Remover a API existente|
|/service/apis/policy/read|Obter detalhes de configuração de política para API|
|/service/apis/policy/write|Definir detalhes de configuração de política para API|
|/service/apis/policy/delete|Remover configuração de política da API|
|/service/apis/operations/read|Obter lista de operações de API existentes ou obter os detalhes de operação da API|
|/service/apis/operations/write|Criar nova operação de API ou atualizar operação de API existente|
|/service/apis/operations/delete|Remover a operação da API existente|
|/service/apis/operations/policy/read|Obter detalhes de configuração de política para a operação|
|/service/apis/operations/policy/write|Definir detalhes de configuração de política para a operação|
|/service/apis/operations/policy/delete|Remover configuração de política da operação|
|/service/products/read|Obter lista de produtos ou obter detalhes do produto|
|/service/products/write|Criar novo produto ou atualizar detalhes do produto existente|
|/service/products/delete|Remover produto existente|
|/service/products/subscriptions/read|Obter lista de assinaturas de produto|
|/service/products/apis/read|Obter lista de APIs adicionadas ao produto existente|
|/service/products/apis/write|Adicionar API existente a um produto existente|
|/service/products/apis/delete|Remover API existente do produto existente|
|/service/products/policy/read|Obter a configuração de política do produto existente|
|/service/products/policy/write|Definir a configuração de política para o produto existente|
|/service/products/policy/delete|Remover configuração de política de produto existente|
|/service/products/groups/read|Obter lista de grupos de desenvolvedores associados ao produto|
|/service/products/groups/write|Associar o grupo do desenvolvedor existente ao produto existente|
|/service/products/groups/delete|Excluir a associação de grupo do desenvolvedor existente ao produto existente|
|/service/openidConnectProviders/read|Obter lista de provedores OpenID Connect ou obter detalhes do provedor OpenID Connect|
|/service/openidConnectProviders/write|Criar um novo provedor OpenID Connect ou atualizar detalhes de um provedor OpenID Connect existente|
|/service/openidConnectProviders/delete|Remover o provedor OpenID Connect existente|
|/service/certificates/read|Obter lista de certificados ou obter os detalhes do certificado|
|/service/certificates/write|Adicionar novo certificado|
|/service/certificates/delete|Remover certificado existente|
|/service/properties/read|Obter a lista de todas as propriedades ou obtém detalhes de uma propriedade especificada|
|/service/properties/write|Criar uma nova propriedade ou atualizar o valor da propriedade especificada|
|/service/properties/delete|Remover uma propriedade existente|
|/service/groups/read|Obter lista de grupos ou obter detalhes de um grupo|
|/service/groups/write|Criar novo grupo ou atualizar detalhes do grupo existente|
|/service/groups/delete|Remover grupo existente|
|/service/groups/users/read|Obter lista de usuários do grupo|
|/service/groups/users/write|Adicionar usuário existente a grupo existente|
|/service/groups/users/delete|Remover usuário existente do grupo existente|
|/service/authorizationServers/read|Obter lista de servidores de autorização ou obter detalhes do servidor de autorização|
|/service/authorizationServers/write|Criar um novo servidor de autorização ou atualizar detalhes de um servidor de autorização existente|
|/service/authorizationServers/delete|Remover servidor de autorização existente|
|/service/reports/bySubscription/read|Obter relatórios agregados por assinatura.|
|/service/reports/byRequest/read|Obter solicitações de dados de relatório|
|/service/reports/byOperation/read|Obter relatórios agregados por operações|
|/service/reports/byGeo/read|Obter relatórios agregados por região geográfica|
|/service/reports/byUser/read|Obter relatórios agregados por desenvolvedores.|
|/service/reports/byTime/read|Obter relatórios agregados por intervalos de tempo|
|/service/reports/byApi/read|Obter relatórios agregados por APIs|
|/service/reports/byProduct/read|Obter relatórios agregados por produtos.|

## <a name="microsoftappservice"></a>Microsoft.AppService

| Operação | Descrição |
|---|---|
|/appidentities/Read|Retornar o recurso (site da Web) registrado no Gateway.|
|/appidentities/Write|Criar uma nova identidade de aplicativo.|
|/appidentities/Delete|Excluir uma identidade de aplicativo existente.|
|/deploymenttemplates/listMetadata/Action|Listar os metadados de interface do usuário associados ao pacote do aplicativo de API.|
|/deploymenttemplates/generate/Action|Retornar um modelo de implantação para provisionar instâncias de aplicativo de API.|
|/gateways/Read|Retornar a instância do Gateway.|
|/gateways/Write|Criar um novo Gateway ou atualizar um existente.|
|/gateways/Delete|Excluir uma instância do Gateway existente.|
|/gateways/listLoginUris/Action|Preenche o repositório de token e retorna URIs de login OAuth.|
|/gateways/listKeys/Action|Retornar os segredos de gateway.|
|/gateways/tokens/Write|Criar um novo Token Zumo com o nome fornecido.|
|/gateways/registrations/Read|Retornar o recurso (site da Web) registrado no Gateway.|
|/gateways/registrations/Write|Registra um recurso (site da Web) com o Gateway.|
|/gateways/registrations/Delete|Cancela o registro de um recurso (site da Web) do Gateway.|
|/apiapps/Read|Retornar a instância do aplicativo de API.|
|/apiapps/Write|Criar um novo aplicativo de API ou atualizar um existente.|
|/apiapps/Delete|Excluir uma instância existente do aplicativo de API.|
|/apiapps/listStatus/Action|Retornar o status do aplicativo de API.|
|/apiapps/listKeys/Action|Retornar segredos do aplicativo de API.|
|/apiapps/apidefinitions/Read|Retornar a definição de API do aplicativo de API.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operação | Descrição |
|---|---|
|/elevateAccess/action|Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário|
|/classicAdministrators/read|Ler os administradores para obter a assinatura.|
|/classicAdministrators/write|Adicionar ou modificar o administrador de uma assinatura.|
|/classicAdministrators/delete|Remover o administrador da assinatura.|
|/locks/read|Obter bloqueios no escopo especificado.|
|bloqueios/gravação|Adicionar bloqueios no escopo especificado.|
|/locks/delete|Excluir bloqueios no escopo especificado.|
|/policyAssignments/read|Obter informações sobre uma atribuição de política.|
|/policyAssignments/write|Criar uma atribuição de política no escopo especificado.|
|/policyAssignments/delete|Excluir uma atribuição de política no escopo especificado.|
|/permissions/read|Listar todas as permissões que o chamador tem em determinado escopo.|
|/roleDefinitions/read|Obter informações sobre uma definição de função.|
|/roleDefinitions/write|Criar ou atualizar uma definição de função personalizada com permissões especificadas e escopos atribuíveis.|
|/roleDefinitions/delete|Excluir a definição de função personalizada especificada.|
|/providerOperations/read|Obter operações para todos os provedores de recursos que podem ser usados em definições de função.|
|/policyDefinitions/read|Obter informações sobre uma definição de política.|
|/policyDefinitions/write|Criar uma definição de política personalizada.|
|/policyDefinitions/delete|Excluir uma definição de política.|
|/roleAssignments/read|Obter informações sobre uma atribuição de função.|
|/roleAssignments/write|Criar uma atribuição de função no escopo especificado.|
|/roleAssignments/delete|Exclua uma atribuição de função no escopo especificado.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operação | Descrição |
|---|---|
|/automationAccounts/read|Obter uma conta da Automação do Azure|
|/automationAccounts/write|Criar ou atualizar uma conta da Automação do Azure|
|/automationAccounts/delete|Excluir uma conta da Automação do Azure|
|/automationAccounts/configurations/readContent/action|Obter o conteúdo de DSC de Automação do Azure|
|/automationAccounts/hybridRunbookWorkerGroups/read|Ler recursos do Hybrid Runbook Worker|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Excluir recursos do Hybrid Runbook Worker|
|/automationAccounts/jobSchedules/read|Obter uma agenda de trabalho da Automação do Azure|
|/automationAccounts/jobSchedules/write|Criar uma agenda de trabalho da Automação do Azure|
|/automationAccounts/jobSchedules/delete|Excluir uma agenda de trabalho da Automação do Azure|
|/automationAccounts/connectionTypes/read|Obter um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/connectionTypes/Write|Criar um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/connectionTypes/delete|Excluir um ativo de tipo de conexão da Automação do Azure|
|/automationAccounts/modules/read|Obter um módulo da Automação do Azure|
|/automationAccounts/modules/write|Criar ou atualizar um módulo da Automação do Azure|
|/automationAccounts/modules/delete|Excluir um módulo da Automação do Azure|
|/automationAccounts/credentials/read|Obter um ativo de credencial da Automação do Azure|
|/automationAccounts/credentials/write|Criar ou atualizar um ativo de credencial da Automação do Azure|
|/automationAccounts/credentials/delete|Excluir um ativo de credencial da Automação do Azure|
|/automationAccounts/certificates/read|Obter um ativo de certificado da Automação do Azure|
|/automationAccounts/certificates/write|Criar ou atualizar um ativo de certificado da Automação do Azure|
|/automationAccounts/certificates/delete|Excluir um ativo de certificado da Automação do Azure|
|/automationAccounts/schedules/read|Obter um ativo de agendamento da Automação do Azure|
|/automationAccounts/schedules/write|Criar ou atualizar um ativo de agendamento da Automação do Azure|
|/automationAccounts/schedules/delete|Excluir um ativo de agendamento da Automação do Azure|
|/automationAccounts/jobs/read|Obter um trabalho da Automação do Azure|
|/automationAccounts/jobs/write|Criar um trabalho da Automação do Azure|
|/automationAccounts/jobs/stop/action|Interrompe um trabalho da Automação do Azure|
|/automationAccounts/jobs/suspend/action|Suspende um trabalho da Automação do Azure|
|/automationAccounts/jobs/resume/action|Reinicia um trabalho da Automação do Azure|
|/automationAccounts/jobs/runbookContent/action|Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho|
|/automationAccounts/jobs/output/action|Obter a saída de um trabalho|
|/automationAccounts/jobs/read|Obter um trabalho da Automação do Azure|
|/automationAccounts/jobs/write|Criar um trabalho da Automação do Azure|
|/automationAccounts/jobs/stop/action|Interrompe um trabalho da Automação do Azure|
|/automationAccounts/jobs/suspend/action|Suspende um trabalho da Automação do Azure|
|/automationAccounts/jobs/resume/action|Reinicia um trabalho da Automação do Azure|
|/automationAccounts/jobs/streams/read|Obter um fluxo de trabalho da Automação do Azure|
|/automationAccounts/connections/read|Obter um ativo de conexão da Automação do Azure|
|/automationAccounts/connections/write|Criar ou atualizar um ativo de conexão da Automação do Azure|
|/automationAccounts/connections/delete|Excluir um ativo de conexão da Automação do Azure|
|/automationAccounts/variables/read|Ler um ativo de variável da Automação do Azure|
|/automationAccounts/variables/write|Criar ou atualizar um ativo de variável da Automação do Azure|
|/automationAccounts/variables/delete|Excluir um ativo de variável da Automação do Azure|
|/automationAccounts/runbooks/readContent/action|Obter o conteúdo de um runbook da Automação do Azure|
|/automationAccounts/runbooks/read|Obter um runbook da Automação do Azure|
|/automationAccounts/runbooks/write|Criar ou atualizar um runbook da Automação do Azure|
|/automationAccounts/runbooks/delete|Excluir um runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/readContent/action|Obter o conteúdo de um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/writeContent/action|Criar o conteúdo de um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/read|Obter um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/publish/action|Publica um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/undoEdit/action|Desfazer edições em um rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/read|Obter um trabalho de teste de rascunho do runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/write|Criar um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/stop/action|Interrompe um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspende um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/runbooks/draft/testJob/resume/action|Retoma um trabalho de teste de rascunho de runbook da Automação do Azure|
|/automationAccounts/webhooks/read|Ler um webhook da Automação do Azure|
|/automationAccounts/webhooks/write|Criar ou atualizar um webhook da Automação do Azure|
|/automationAccounts/webhooks/delete|Excluir um webhook da Automação do Azure |
|/automationAccounts/webhooks/generateUri/action|Gera um URI para um webhook da Automação do Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Esse provedor não é um provedor ARM completo e não fornece nenhuma operação de ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos de lote e permite a criação de contas do Lote|
|/batchAccounts/write|Criar uma nova conta do Lote ou atualizar uma conta do Lote existente|
|/batchAccounts/read|Listar contas do Lote ou obtém as propriedades de uma conta do Lote|
|/batchAccounts/delete|Excluir uma conta do Lote|
|/batchAccounts/listkeys/action|Listar chaves de acesso para uma conta do Lote|
|/batchAccounts/regeneratekeys/action|Regenera chaves de acesso para uma conta do Lote|
|/batchAccounts/syncAutoStorageKeys/action|Sincroniza as chaves de acesso da conta de armazenamento automática configurada para uma conta do Lote|
|/batchAccounts/applications/read|Listar aplicativos ou obtém as propriedades de um aplicativo|
|/batchAccounts/applications/write|Criar um novo aplicativo ou atualizar um aplicativo existente|
|/batchAccounts/applications/delete|Excluir um aplicativo|
|/batchAccounts/applications/versions/read|Obter as propriedades de um pacote de aplicativos|
|/batchAccounts/applications/versions/write|Criar um novo pacote de aplicativos ou atualizar um pacote de aplicativos existente|
|/batchAccounts/applications/versions/activate/action|Ativa um pacote de aplicativos|
|/batchAccounts/applications/versions/delete|Excluir um pacote de aplicativos|
|/locations/quotas/read|Obter cotas do Lote da assinatura especificada na região do Azure especificada|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operação | Descrição |
|---|---|
|/invoices/read|Listar faturas disponíveis|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operação | Descrição |
|---|---|
|/mapApis/Read|Operação de Leitura|
|/mapApis/Write|Operação de Gravação|
|/mapApis/Delete|Operação de Exclusão|
|/mapApis/regenerateKey/action|Regenera a chave|
|/mapApis/listSecrets/action|Listar os segredos|
|/mapApis/listSingleSignOnToken/action|Ler o token de autorização de logon único para o recurso|
|/Operations/read|Descrição da operação.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operação | Descrição |
|---|---|
|/checknameavailability/action|Verificar se um nome está disponível para uso com um novo Cache Redis|
|/register/action|Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura|
|/unregister/action|Cancela o registro do provedor de recursos 'Microsoft.Cache' com uma assinatura|
|/redis/write|Modificar as configurações do Cache Redis e a configuração no portal de gerenciamento|
|/redis/read|Exibir as configurações do Cache Redis e a configuração no portal de gerenciamento|
|/redis/delete|Excluir todo o Cache Redis|
|/redis/listKeys/action|Exibir o valor das chaves de acesso do Cache Redis no portal de gerenciamento|
|/redis/regenerateKey/action|Alterar o valor das chaves de acesso do Cache Redis no portal de gerenciamento|
|/redis/import/action|Importar dados em um formato especificado de vários blobs para o Redis|
|/redis/export/action|Exportar dados do Redis para blobs de armazenamento prefixados no formato especificado|
|/redis/forceReboot/action|Forçar a reinicialização de uma instância de cache, possivelmente com perda de dados.|
|/redis/stop/action|Interromper uma instância de cache.|
|/redis/start/action|Iniciar uma instância de cache.|
|/redis/metricDefinitions/read|Obter a métrica disponível para um Cache Redis|
|/redis/firewallRules/read|Obter as regras de firewall de IP de um Cache Redis|
|/redis/firewallRules/write|Editar as regras de firewall de IP de um Cache Redis|
|/redis/firewallRules/delete|Excluir regras de firewall de IP de um Cache Redis|
|/redis/listUpgradeNotifications/read|Listar as notificações de atualização mais recentes do locatário de cache.|
|/redis/linkedservers/read|Obter servidores vinculados associados a um Cache Redis.|
|/redis/linkedservers/write|Adicionar um servidor vinculado a um Cache Redis|
|/redis/linkedservers/delete|Excluir servidor vinculado de um Cache Redis|
|/redis/patchSchedules/read|Obter o agendamento de aplicação de patch de um Cache Redis|
|/redis/patchSchedules/write|Modificar o agendamento de aplicação de patch de um Cache Redis|
|/redis/patchSchedules/delete|Excluir a agenda de patch de um Cache Redis|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operação | Descrição |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Provisionar entidade de serviço para entidade de serviço de aplicativo|
|/validateCertificateRegistrationInformation/Action|Validar objeto de compra de certificado sem enviá-lo|
|/register/action|Registrar o provedor de recursos do Microsoft Certificates da assinatura|
|/certificateOrders/Write|Adicionar uma nova certificateOrder ou atualizar uma existente|
|/certificateOrders/Delete|Excluir um AppServiceCertificate existente|
|/certificateOrders/Read|Obter a lista de CertificateOrders|
|/certificateOrders/reissue/Action|Emitir novamente um certificateorder existente|
|/certificateOrders/renew/Action|Renovar um certificateorder existente|
|/certificateOrders/retrieveCertificateActions/Action|Recuperar a lista de ações de certificado|
|/certificateOrders/retrieveEmailHistory/Action|Recuperar o histórico de email do certificado|
|/certificateOrders/resendEmail/Action|Reenviar email de certificado|
|/certificateOrders/verifyDomainOwnership/Action|Verificar a propriedade de domínio|
|/certificateOrders/resendRequestEmails/Action|Reenviar emails de solicitação para outro endereço de email|
|/certificateOrders/resendRequestEmails/Action|Recuperar o selo de site para um certificado do Serviço de Aplicativo emitido|
|/certificateOrders/certificates/Write|Adicionar um novo certificado ou atualizar um existente|
|/certificateOrders/certificates/Delete|Excluir um certificado existente|
|/certificateOrders/certificates/Read|Obter a lista de certificados|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operação | Descrição |
|---|---|
|/register/action|Registrar para computação clássica|
|/checkDomainNameAvailability/action|Verificar a disponibilidade de um nome de domínio específico.|
|/moveSubscriptionResources/action|Mover todos os recursos clássicos para uma assinatura diferente.|
|/validateSubscriptionMoveAvailability/action|Valide a disponibilidade da assinatura para a operação de movimentação clássica.|
|/operatingSystemFamilies/read|Listar as famílias de sistema operacional convidado disponíveis no Microsoft Azure e também lista as versões de sistema operacional disponíveis para cada f
|/capabilities/read|Mostra as funcionalidades|
|/operatingSystems/read|Listar as versões do sistema operacional convidado disponíveis atualmente no Microsoft Azure.|
|/resourceTypes/skus/read|Obter a lista de SKU para tipos de recursos com suporte.|
|/domainNames/read|Retornar os nomes de domínio dos recursos.|
|/domainNames/write|Adicionar ou modificar os nomes de domínio dos recursos.|
|/domainNames/delete|Remover os nomes de domínio dos recursos.|
|/domainNames/swap/action|Alterna o slot de preparo para o slot de produção.|
|/domainNames/serviceCertificates/read|Retornar todos os certificados de serviço usados.|
|/domainNames/serviceCertificates/write|Adicionar ou modificar os certificados de serviço usados.|
|/domainNames/serviceCertificates/delete|Excluir os certificados de serviço usados.|
|/domainNames/serviceCertificates/operationStatuses/read|Ler o status da operação para os certificados de serviço dos nomes de domínio.|
|/domainNames/capabilities/read|Mostra os recursos de nome de domínio|
|/domainNames/extensions/read|Retornar extensões de nome de domínio.|
|/domainNames/extensions/write|Adicionar as extensões de nome de domínio.|
|/domainNames/extensions/delete|Remover as extensões de nome de domínio.|
|/domainNames/extensions/operationStatuses/read|Ler o status da operação das extensões de nomes de domínio.|
|/domainNames/active/write|Define o nome de domínio ativo.|
|/domainNames/slots/read|Mostra os slots de implantação.|
|/domainNames/slots/write|Criar ou atualizar a implantação.|
|/domainNames/slots/delete|Excluir determinado slot de implantação.|
|/domainNames/slots/start/action|Inicia um slot de implantação.|
|/domainNames/slots/stop/action|Suspende o slot de implantação.|
|/domainNames/slots/operationStatuses/read|Ler o status da operação dos slots de nomes de domínio.|
|/domainNames/slots/roles/read|Obter a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/read|Retornar a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/write|Adicionar ou modificar a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/delete|Remover a referência de extensão para a função do slot de implantação.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Ler o status da operação de referências de extensão das funções de slot dos nomes de domínio.|
|/domainNames/slots/roles/roleInstances/read|Obter a instância de função.|
|/domainNames/slots/roles/roleInstances/restart/action|Reinicia as instâncias de função.|
|/domainNames/slots/roles/roleInstances/reimage/action|Refaz a imagem da instância de função.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Ler o status da operação de instâncias de função das funções de slot dos nomes de domínio.|
|/domainNames/slots/state/start/write|Altera o estado do slot de implantação para interrompido.|
|/domainNames/slots/state/stop/write|Altera o estado do slot de implantação para iniciado.|
|/domainNames/slots/upgradeDomain/write|Atualizar o domínio.|
|/domainNames/internalLoadBalancers/read|Obter os balanceadores de carga internos.|
|/domainNames/internalLoadBalancers/write|Criar um novo balanceamento de carga interno.|
|/domainNames/internalLoadBalancers/delete|Remover um novo balanceamento de carga interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Ler o status da operação para os balanceadores de carga internos dos nomes de domínio.|
|/domainNames/loadBalancedEndpointSets/read|Mostra os conjuntos de ponto de extremidade com balanceamento de carga|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Ler o status da operação para os conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio.|
|/domainNames/availabilitySets/read|Mostra a conjunto de disponibilidade para o recurso.|
|/quotas/read|Obter a cota para a assinatura.|
|/virtualMachines/read|Retorna a lista de máquinas virtuais.|
|/virtualMachines/write|Adicionar ou modificar máquinas virtuais.|
|/virtualMachines/delete|Remover as máquinas virtuais.|
|/virtualMachines/start/action|Iniciar a máquina virtual.|
|/virtualMachines/redeploy/action|Reimplanta a máquina virtual.|
|/virtualMachines/restart/action|Reinicia as máquinas virtuais.|
|/virtualMachines/stop/action|Interrompe a máquina virtual.|
|/virtualMachines/shutdown/action|Desliga a máquina virtual.|
|/virtualMachines/attachDisk/action|Anexa um disco de dados a uma máquina virtual.|
|/virtualMachines/detachDisk/action|Desanexa um disco de dados da máquina virtual.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Baixa o arquivo RDP na máquina virtual.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|Obter um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|Excluir um grupo de segurança de rede associado ao adaptador de rede.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obter as definições de métrica.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obter as configurações de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Adicionar ou modificar configurações de diagnóstico.|
|/virtualMachines/metrics/read|Obter a métrica.|
|/virtualMachines/operationStatuses/read|Ler o status da operação das máquinas virtuais.|
|/virtualMachines/extensions/read|Obter a extensão da máquina virtual.|
|/virtualMachines/extensions/write|Coloca a extensão da máquina virtual.|
|/virtualMachines/extensions/operationStatuses/read|Ler o status da operação das extensões das máquinas virtuais.|
|/virtualMachines/asyncOperations/read|Obter as operações assíncronas possíveis|
|/virtualMachines/disks/read|Recupera a lista de discos de dados|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obter o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Excluir o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Ler que o status de operação das máquinas virtuais associadas a grupos de segurança de rede.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operação | Descrição |
|---|---|
|/register/action|Registrar-se na rede clássica|
|/gatewaySupportedDevices/Read|Recupera a lista de dispositivos com suporte.|
|/reservedIps/read|Obter os IPs reservados|
|/reservedIps/write|Adicionar um novo IP reservado|
|/reservedIps/delete|Excluir um IP reservado.|
|/reservedIps/link/action|Vincular um IP reservado|
|/reservedIps/join/action|Ingressar em um IP reservado|
|/reservedIps/operationStatuses/read|Ler o status da operação dos IPs reservados.|
|/virtualNetworks/read|Obter a rede virtual.|
|/virtualNetworks/write|Adicionar uma nova rede virtual.|
|/virtualNetworks/delete|Excluir a rede virtual.|
|/virtualNetworks/peer/action|Coloca uma rede virtual com outra rede virtual de mesmo nível.|
|/virtualNetworks/join/action|Ingressar na rede virtual.|
|/virtualNetworks/checkIPAddressAvailability/action|Verificar a disponibilidade de determinado endereço IP em uma rede virtual.|
|/virtualNetworks/capabilities/read|Mostra as funcionalidades|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|Obter um grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|Adiciona um grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|Excluir o grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|Ler o status da operação do grupo de segurança associado à sub-rede da rede virtual.|
|/virtualNetworks/operationStatuses/read|Ler o status da operação das redes virtuais.|
|/virtualNetworks/gateways/read|Obter os gateways de rede virtual.|
|/virtualNetworks/gateways/write|Adiciona um gateway de rede virtual.|
|/virtualNetworks/gateways/delete|Excluir o gateway de rede virtual.|
|/virtualNetworks/gateways/startDiagnostics/action|Inicia o diagnóstico do gateway de rede virtual.|
|/virtualNetworks/gateways/stopDiagnostics/action|Interrompe o diagnóstico do gateway de rede virtual.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Baixa o diagnóstico de gateway.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera a chave de serviço do circuito.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Baixa o script de configuração do dispositivo.|
|/virtualNetworks/gateways/listPackage/action|Listar o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/operationStatuses/read|Ler o status da operação dos gateways de redes virtuais.|
|/virtualNetworks/gateways/packages/read|Obter o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/connections/read|Recupera a lista de conexões.|
|/virtualNetworks/gateways/connections/connect/action|Conecta uma conexão de gateway site a site.|
|/virtualNetworks/gateways/connections/disconnect/action|Desconecta uma conexão de gateway site a site.|
|/virtualNetworks/gateways/connections/test/action|Testa uma conexão de gateway site a site.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Ler os certificados de cliente revogados.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoga um certificado de cliente.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Cancela a revogação de um certificado de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/read|Localizar os certificados raiz do cliente.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carrega um novo certificado raiz do cliente.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Excluir o certificado de cliente do gateway de rede virtual.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Baixa o certificado por impressão digital.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Listar o pacote de certificado do gateway de rede virtual.|
|/networkSecurityGroups/read|Obter o grupo de segurança de rede.|
|/networkSecurityGroups/write|Adiciona um novo grupo de segurança de rede.|
|/networkSecurityGroups/delete|Excluir o grupo de segurança de rede.|
|/networkSecurityGroups/operationStatuses/read|Ler o status da operação do grupo de segurança de rede.|
|/networkSecurityGroups/securityRules/read|Obter a regra de segurança.|
|/networkSecurityGroups/securityRules/write|Adiciona ou atualiza uma regra de segurança.|
|/networkSecurityGroups/securityRules/delete|Excluir a regra de segurança.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Ler o status da operação das regras de segurança do grupo de segurança de rede.|
|/quotas/read|Obter a cota para a assinatura.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operação | Descrição |
|---|---|
|/register/action|Registrar para armazenamento clássico|
|/checkStorageAccountAvailability/action|Verificar a disponibilidade de uma conta de armazenamento.|
|/capabilities/read|Mostra as funcionalidades|
|/publicImages/read|Obter a imagem pública da máquina virtual.|
|/images/read|Retornar a imagem.|
|/storageAccounts/read|Retornar a conta de armazenamento com a conta fornecida.|
|/storageAccounts/write|Adiciona uma nova conta de armazenamento.|
|/storageAccounts/delete|Excluir a conta de armazenamento.|
|/storageAccounts/listKeys/action|Listar as chaves de acesso das contas de armazenamento.|
|/storageAccounts/regenerateKey/action|Regenera as chaves de acesso existentes da conta de armazenamento.|
|/storageAccounts/operationStatuses/read|Ler o status da operação do recurso.|
|/storageAccounts/images/read|Retornar a imagem da conta de armazenamento.|
|/storageAccounts/images/delete|Excluir uma imagem da conta de armazenamento específica.|
|/storageAccounts/disks/read|Retornar o disco da conta de armazenamento.|
|/storageAccounts/disks/write|Adiciona um disco de conta de armazenamento.|
|/storageAccounts/disks/delete|Excluir um disco de conta de armazenamento específico.|
|/storageAccounts/disks/operationStatuses/read|Ler o status da operação do recurso.|
|/storageAccounts/osImages/read|Retornar a imagem do sistema operacional da conta de armazenamento.|
|/storageAccounts/osImages/delete|Excluir uma imagem do sistema operacional da conta de armazenamento específica.|
|/storageAccounts/services/read|Obter os serviços disponíveis.|
|/storageAccounts/services/metricDefinitions/read|Obter as definições de métrica.|
|/storageAccounts/services/metrics/read|Obter a métrica.|
|/storageAccounts/services/diagnosticSettings/read|Obter as configurações de diagnóstico.|
|/storageAccounts/services/diagnosticSettings/write|Adicionar ou modificar configurações de diagnóstico.|
|/disks/read|Retornar o disco da conta de armazenamento.|
|/osImages/read|Retornar a imagem do sistema operacional.|
|/quotas/read|Obter a cota para a assinatura.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operação | Descrição |
|---|---|
|/accounts/read|Ler as contas de API.|
|/accounts/write|Grava as contas de API.|
|/accounts/delete|Excluir contas de API|
|/accounts/listKeys/action|Listar chaves|
|/accounts/regenerateKey/action|Regenerar chave|
|/accounts/skus/read|Ler SKUs disponíveis para um recurso existente.|
|/accounts/usages/read|Obter a utilização de cota de um recurso existente.|
|/Operations/read|Descrição da operação.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operação | Descrição |
|---|---|
|/RateCard/read|Retornar dados da oferta, metadados de recurso/medidor e tarifas da assinatura especificada.|
|/UsageAggregates/read|Recupera o consumo do Microsoft Azure para uma assinatura. O resultado contém dados totais de uso, assinatura e informações relativas a recursos em determinado intervalo de tempo.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura com o provedor de recursos Microsoft.Compute|
|/restorePointCollections/read|Obter as propriedades de uma coleção de pontos de restauração|
|/restorePointCollections/write|Criar uma nova coleção de pontos de restauração ou atualizar uma existente|
|/restorePointCollections/delete|Excluir a coleção de pontos de restauração e os pontos contidos nela|
|/restorePointCollections/restorePoints/read|Obter as propriedades de um ponto de restauração|
|/restorePointCollections/restorePoints/write|Criar um novo ponto de restauração|
|/restorePointCollections/restorePoints/delete|Excluir o ponto de restauração|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obter as propriedades de um ponto de restauração com URIs de SAS do blob|
|/virtualMachineScaleSets/read|Obter as propriedades de um conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/write|Criar um novo conjunto de dimensionamento de máquinas virtuais ou atualizar um existente|
|/virtualMachineScaleSets/delete|Excluir o conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/start/action|Iniciar a instância do conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/powerOff/action|Desligar a instância do conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/restart/action|Reiniciar a instância do conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/deallocate/action|Desligar e liberar os recursos de computação para as instâncias do conjunto de dimensionamento de máquinas virtuais |
|/virtualMachineScaleSets/manualUpgrade/action|Atualizar manualmente as instâncias para o modelo mais recente do conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/scale/action|Reduzir ou escalar horizontalmente a contagem de instâncias de um conjunto de dimensionamento de máquinas virtuais existente|
|/virtualMachineScaleSets/instanceView/read|Obter a exibição de instância do conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/skus/read|Listar as SKUs válidas para um conjunto de dimensionamento de máquinas virtuais existente|
|/virtualMachineScaleSets/virtualMachines/read|Recuperar as propriedades de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais|
|/virtualMachineScaleSets/virtualMachines/delete|Excluir uma máquina virtual específica em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/start/action|Iniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Desligar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Reiniciar uma instância de máquina virtual em um conjunto de dimensionamento de VM.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Desligar e liberar os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recuperar a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais.|
|/images/read|Obter as propriedades da imagem|
|/images/write|Criar uma nova imagem ou atualizar uma existente|
|/images/delete|Excluir a imagem|
|/operations/read|Listar as operações disponíveis no provedor de recursos Microsoft.Compute|
|/disks/read|Obter as propriedades de um disco|
|/disks/write|Criar um novo disco ou atualizar um existente|
|/disks/delete|Excluir o disco|
|/disks/beginGetAccess/action|Obter o URI de SAS do disco para acesso de blob|
|/disks/endGetAccess/action|Revogar o URI de SAS do disco|
|/snapshots/read|Obter as propriedades de um instantâneo|
|/snapshots/write|Criar um novo instantâneo ou atualizar um existente|
|/snapshots/delete|Excluir um instantâneo|
|/availabilitySets/read|Obter as propriedades de um conjunto de disponibilidade|
|/availabilitySets/write|Criar um novo conjunto de disponibilidade ou atualizar um existente|
|/availabilitySets/delete|Excluir o conjunto de disponibilidade|
|/availabilitySets/vmSizes/read|Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade|
|/virtualMachines/read|Obter as propriedades de uma máquina virtual|
|/virtualMachines/write|Criar uma nova máquina virtual ou atualizar uma máquina virtual existente|
|/virtualMachines/delete|Excluir a máquina virtual|
|/virtualMachines/start/action|Inicia a máquina virtual|
|/virtualMachines/powerOff/action|Desligar a máquina virtual. Observe que a máquina virtual continuará a ser cobrada.|
|/virtualMachines/redeploy/action|Reimplantar a máquina virtual|
|/virtualMachines/restart/action|Reinicia a máquina virtual|
|/virtualMachines/deallocate/action|Desligar a máquina virtual e liberar os recursos de computação|
|/virtualMachines/generalize/action|Definir o estado da máquina virtual como Generalizada e preparar a máquina virtual para captura|
|/virtualMachines/capture/action|Capturar a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes|
|/virtualMachines/convertToManagedDisks/action|Converter os discos baseados em blob da máquina virtual em discos gerenciados|
|/virtualMachines/vmSizes/read|Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada|
|/virtualMachines/instanceView/read|Obter o status de tempo de execução detalhado da máquina virtual e seus recursos|
|/virtualMachines/extensions/read|Obter as propriedades de uma extensão da máquina virtual|
|/virtualMachines/extensions/write|Criar uma nova extensão da máquina virtual ou atualizar uma existente|
|/virtualMachines/extensions/delete|Excluir a extensão da máquina virtual|
|/locations/vmSizes/read|Listar os tamanhos de máquina virtual disponível em um local|
|/locations/usages/read|Obter as quantidades de uso atual e limites de serviço para os recursos de computação da assinatura em determinado local|
|/locations/operations/read|Obter o status de uma operação assíncrona|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos de registro de contêiner e permite a criação de registros de contêiner.|
|/checknameavailability/read|Verificar se esse nome de registro é válido e não está em uso.|
|/registries/read|Retornar a lista de registros de contêiner ou obter as propriedades do registro de contêiner especificado.|
|/registries/write|Criar um registro de contêiner com os parâmetros especificados ou atualizar as propriedades ou marcas do registro de contêiner especificado.|
|/registries/delete|Excluir um registro de contêiner existente.|
|/registries/listCredentials/action|Listar as credenciais de logon para o registro de contêiner especificado.|
|/registries/regenerateCredential/action|Regenerar as credenciais de logon para o registro de contêiner especificado.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operação | Descrição |
|---|---|
|/containerServices/subscriptions/read|Obter os Serviços de Contêiner especificados com base na assinatura|
|/containerServices/resourceGroups/read|Obter os Serviços de Contêiner especificado com base no grupo de recursos|
|/containerServices/resourceGroups/ContainerServiceName/read|Obter o Serviço de Contêiner especificado|
|/containerServices/resourceGroups/ContainerServiceName/write|Inserir ou atualizar o Serviço de Contêiner especificado|
|/containerServices/resourceGroups/ContainerServiceName/delete|Excluir o serviço de contêiner especificado|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operação | Descrição |
|---|---|
|/updateCommunicationPreference/action|Atualizar as preferências de comunicação|
|/listCommunicationPreference/action|Listar preferência de comunicação|
|/applications/read|Operação de Leitura|
|/applications/write|Operação de Gravação|
|/applications/write|Operação de Gravação|
|/applications/delete|Operação de Exclusão|
|/applications/listSecrets/action|Listar segredos|
|/applications/listSingleSignOnToken/action|Ler tokens de logon único|
|/operations/read|operações de leitura|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operação | Descrição |
|---|---|
|/hubs/read|Ler Hub de informações do Customer Insights|
|/hubs/write|Criar ou atualizar um Hub de informações do Customer Insights|
|/hubs/delete|Excluir Hub de informações do Customer Insights|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Obter a métrica disponível para o recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para o recurso|
|/hubs/authorizationPolicies/read|Ler política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/write|Criar ou atualizar política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Excluir política de assinatura de acesso compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Regenerar a chave primária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Regenerar a chave secundária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights|
|/hubs/profiles/read|Ler perfil do Azure Customer Insights|
|/hubs/profiles/write|Gravar qualquer perfil do Azure Customer Insights|
|/hubs/kpi/read|Ler indicador principal de desempenho do Azure Customer Insights|
|/hubs/kpi/write|Criar ou atualizar indicador principal de desempenho do Azure Customer Insights|
|/hubs/kpi/delete|Excluir indicador principal de desempenho do Azure Customer Insights|
|/hubs/views/read|Ler modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/views/write|Criar ou atualizar modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/views/delete|Excluir modo de exibição de aplicativo do Azure Customer Insights|
|/hubs/interactions/read|Ler interação do Azure Customer Insights|
|/hubs/interactions/write|Criar ou atualizar interação do Azure Customer Insights|
|/hubs/roleAssignments/read|Ler atribuição RBAC do Azure Customer Insights|
|/hubs/roleAssignments/write|Criar ou atualizar atribuição RBAC do Azure Customer Insights|
|/hubs/roleAssignments/delete|Excluir atribuição RBAC do Azure Customer Insights|
|/hubs/connectors/read|Ler conector do Azure Customer Insights|
|/hubs/connectors/write|Criar ou atualizar conector do Azure Customer Insights|
|/hubs/connectors/delete|Excluir conector do Azure Customer Insights|
|/hubs/connectors/mappings/read|Ler mapeamento do conector do Azure Customer Insights|
|/hubs/connectors/mappings/write|Criar ou atualizar mapeamento de conector do Azure Customer Insights|
|/hubs/connectors/mappings/delete|Excluir mapeamento do conector do Azure Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operação | Descrição |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do nome do catálogo para o locatário.|
|/catalogs/read|Obter propriedades dos catálogos na assinatura ou no grupo de recursos.|
|/catalogs/write|Criar catálogo ou atualizar as marcações e propriedades do catálogo.|
|/catalogs/delete|Excluir o catálogo.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operação | Descrição |
|---|---|
|/datafactories/read|Ler Data Factory.|
|/datafactories/write|Criar ou atualizar data factory|
|/datafactories/delete|Excluir o data factory.|
|/datafactories/datapipelines/read|Ler pipeline.|
|/datafactories/datapipelines/delete|Excluir o pipeline.|
|/datafactories/datapipelines/pause/action|Pausar pipeline.|
|/datafactories/datapipelines/resume/action|Retomar pipeline.|
|/datafactories/datapipelines/update/action|Atualizar pipeline.|
|/datafactories/datapipelines/write|Criar ou atualizar pipeline|
|/datafactories/linkedServices/read|Ler serviço vinculado.|
|/datafactories/linkedServices/delete|Excluir o serviço vinculado.|
|/datafactories/linkedServices/write|Criar ou atualizar serviço vinculado|
|/datafactories/tables/read|Ler tabela.|
|/datafactories/tables/delete|Excluir a tabela.|
|/datafactories/tables/write|Criar ou atualizar tabela|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operação | Descrição |
|---|---|
|/accounts/read|Obter informações sobre a conta DataLakeAnalytics.|
|/accounts/write|Criar ou atualizar a conta DataLakeAnalytics.|
|/accounts/delete|Excluir a conta DataLakeAnalytics.|
|/accounts/firewallRules/read|Obter informações sobre uma regra de firewall.|
|/accounts/firewallRules/write|Criar ou atualizar uma regra de firewall.|
|/accounts/firewallRules/delete|Excluir uma regra de firewall.|
|/accounts/storageAccounts/read|Obter a conta de armazenamento vinculado para a conta do DataLakeAnalytics.|
|/accounts/storageAccounts/write|Vincular uma conta de armazenamento à conta do DataLakeAnalytics.|
|/accounts/storageAccounts/delete|Desvincular uma conta de armazenamento da conta do DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/read|Obter contêineres na conta de armazenamento|
|/accounts/storageAccounts/Containers/listSasTokens/action|Listar de tokens SAS para o contêiner de armazenamento|
|/accounts/dataLakeStoreAccounts/read|Obter conta vinculada do DataLakeStore para a conta de DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/write|Vincular uma conta do DataLakeStore à conta do DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/delete|Desvincular uma conta do DataLakeStore da conta do DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operação | Descrição |
|---|---|
|/accounts/read|Obter informações sobre uma conta DataLakeStore existente.|
|/accounts/write|Criar uma nova conta de DataLakeStore ou atualizar uma conta DataLakeStore existente.|
|/accounts/delete|Excluir uma conta existente do DataLakeStore.|
|/accounts/firewallRules/read|Obter informações sobre uma regra de firewall.|
|/accounts/firewallRules/write|Criar ou atualizar uma regra de firewall.|
|/accounts/firewallRules/delete|Excluir uma regra de firewall.|
|/accounts/trustedIdProviders/read|Obter informações sobre um provedor de identidade confiável.|
|/accounts/trustedIdProviders/write|Criar ou atualizar um provedor de identidade confiável.|
|/accounts/trustedIdProviders/delete|Excluir um provedor de identidade confiável.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operação | Descrição |
|---|---|
|/register/action|Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub|
|/checkNameAvailability/Action|Verificar se o nome do IotHub está disponível|
|/usages/Read|Obter detalhes de uso de assinatura para esse provedor.|
|/operations/Read|Obter todas as operações de ResourceProvider|
|/iotHubs/Read|Obter os recursos IotHub|
|/iotHubs/Write|Criar ou atualizar recursos IotHub|
|/iotHubs/Delete|Excluir recurso IotHub|
|/iotHubs/listkeys/Action|Obter todas as chaves IotHub|
|/iotHubs/exportDevices/Action|Exportar dispositivos|
|/iotHubs/importDevices/Action|Importar dispositivos|
|/IotHubs/metricDefinitions/read|Obter as métricas disponíveis para o serviço IotHub|
|/iotHubs/iotHubKeys/listkeys/Action|Obter a chave IotHub para o nome fornecido|
|/iotHubs/iotHubStats/Read|Obter estatísticas de IotHub|
|/iotHubs/quotaMetrics/Read|Obter métrica de cota|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Criar grupo de consumidores EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Obter grupo de consumidores EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Excluir grupo de consumidores EventHub|
|/iotHubs/routing/routes/$testall/Action|Testar uma mensagem com todas as rotas existentes|
|/iotHubs/routing/routes/$testnew/Action|Testar uma mensagem em uma rota de teste fornecida|
|/IotHubs/diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/IotHubs/diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/iotHubs/skus/Read|Obter SKUs IotHub válidos|
|/iotHubs/jobs/Read|Obter detalhes de trabalhos enviados em determinado IotHub|
|/iotHubs/routingEndpointsHealth/Read|Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operação | Descrição |
|---|---|
|/Subscription/register/action|Registra a assinatura|
|/labs/delete|Excluir laboratórios.|
|/labs/read|Ler laboratórios.|
|/labs/write|Adicionar ou modificar os laboratórios.|
|/labs/ListVhds/action|Listar imagens de disco disponíveis para a criação de imagens personalizadas.|
|/labs/GenerateUploadUri/action|Gerar um URI para carregar imagens de disco personalizadas para um laboratório.|
|/labs/CreateEnvironment/action|Criar máquinas virtuais em um laboratório.|
|/labs/ClaimAnyVm/action|Declarar uma máquina virtual aleatória declarável no laboratório.|
|/labs/ExportResourceUsage/action|Exportar o uso de recursos de laboratório para uma conta de armazenamento|
|/labs/users/delete|Excluir perfis de usuário.|
|/labs/users/read|Ler perfis de usuário.|
|/labs/users/write|Adicionar ou modificar perfis de usuário.|
|/labs/users/secrets/delete|Excluir segredos.|
|/labs/users/secrets/read|Ler segredos.|
|/labs/users/secrets/write|Adicionar ou modificar segredos.|
|/labs/users/environments/delete|Excluir ambientes.|
|/labs/users/environments/read|Ler ambientes.|
|/labs/users/environments/write|Adicionar ou modificar ambientes.|
|/labs/users/disks/delete|Excluir discos.|
|/labs/users/disks/read|Ler discos.|
|/labs/users/disks/write|Adicionar ou modificar discos.|
|/labs/users/disks/Attach/action|Anexar e criar a concessão do disco para a máquina virtual.|
|/labs/users/disks/Detach/action|Desanexar e interromper a concessão do disco anexado à máquina virtual.|
|/labs/customImages/delete|Excluir imagens personalizadas.|
|/labs/customImages/read|Ler imagens personalizadas.|
|/labs/customImages/write|Adicionar ou modificar imagens personalizadas.|
|/labs/serviceRunners/delete|Excluir executores de serviço.|
|/labs/serviceRunners/read|Ler os executores de serviço.|
|/labs/serviceRunners/write|Adicionar ou modificar os executores de serviço.|
|/labs/artifactSources/delete|Excluir fontes de artefato.|
|/labs/artifactSources/read|Ler fontes de artefato.|
|/labs/artifactSources/write|Adicionar ou modificar fontes de artefato.|
|/labs/artifactSources/artifacts/read|Ler artefatos.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Gera um modelo do ARM para o artefato determinado, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado.|
|/labs/artifactSources/armTemplates/read|Ler modelos do Azure Resource Manager.|
|/labs/costs/read|Ler custos.|
|/labs/costs/write|Adicionar ou modificar custos.|
|/labs/virtualNetworks/delete|Excluir redes virtuais.|
|/labs/virtualNetworks/read|Ler redes virtuais.|
|/labs/virtualNetworks/write|Adicionar ou modificar redes virtuais.|
|/labs/formulas/delete|Excluir fórmulas.|
|/labs/formulas/read|Ler fórmulas.|
|/labs/formulas/write|Adicionar ou modificar fórmulas.|
|/labs/schedules/delete|Excluir agendas.|
|/labs/schedules/read|Ler agendas.|
|/labs/schedules/write|Adicionar ou modificar agendamentos.|
|/labs/schedules/Execute/action|Executar uma agenda.|
|/labs/schedules/ListApplicable/action|Listar todas as agendas aplicáveis|
|/labs/galleryImages/read|Ler imagens da galeria.|
|/labs/policySets/EvaluatePolicies/action|Avaliar a política de laboratório.|
|/labs/policySets/policies/delete|Excluir políticas.|
|/labs/policySets/policies/read|Ler políticas.|
|/labs/policySets/policies/write|Adicionar ou modificar políticas.|
|/labs/virtualMachines/delete|Excluir as máquinas virtuais.|
|/labs/virtualMachines/read|Ler máquinas virtuais.|
|/labs/virtualMachines/write|Adicionar ou modificar máquinas virtuais.|
|/labs/virtualMachines/Start/action|Iniciar uma máquina virtual.|
|/labs/virtualMachines/Stop/action|Parar uma máquina virtual|
|/labs/virtualMachines/ApplyArtifacts/action|Aplicar artefatos à máquina virtual.|
|/labs/virtualMachines/AddDataDisk/action|Anexar um disco de dados novo ou existente à máquina virtual.|
|/labs/virtualMachines/DetachDataDisk/action|Desanexar o disco especificado da máquina virtual.|
|/labs/virtualMachines/Claim/action|Assumir o controle de uma máquina virtual existente|
|/labs/virtualMachines/ListApplicableSchedules/action|Listar todas as agendas aplicáveis|
|/labs/virtualMachines/schedules/delete|Excluir agendas.|
|/labs/virtualMachines/schedules/read|Ler agendas.|
|/labs/virtualMachines/schedules/write|Adicionar ou modificar agendamentos.|
|/labs/virtualMachines/schedules/Execute/action|Executar uma agenda.|
|/labs/notificationChannels/delete|Excluir notificationchannels.|
|/labs/notificationChannels/read|Ler notificationchannels.|
|/labs/notificationChannels/write|Adicionar ou modificar notificationchannels.|
|/labs/notificationChannels/Notify/action|Enviar notificação ao canal fornecido.|
|/schedules/delete|Excluir agendas.|
|/schedules/read|Ler agendas.|
|/schedules/write|Adicionar ou modificar agendamentos.|
|/schedules/Execute/action|Executar uma agenda.|
|/schedules/Retarget/action|Atualizar ID de recurso de destino de uma agenda.|
|/locations/operations/read|Operações de leitura.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operação | Descrição |
|---|---|
|/databaseAccountNames/read|Verificar a disponibilidade do nome.|
|/databaseAccounts/read|Ler uma conta de banco de dados.|
|/databaseAccounts/write|Atualizar uma conta de banco de dados.|
|/databaseAccounts/listKeys/action|Listar chaves de uma conta de banco de dados|
|/databaseAccounts/regenerateKey/action|Girar chaves de uma conta de banco de dados|
|/databaseAccounts/listConnectionStrings/action|Obter as cadeias de caracteres de conexão para uma conta de banco de dados|
|/databaseAccounts/changeResourceGroup/action|Alterar o grupo de recursos de uma conta de banco de dados|
|/databaseAccounts/failoverPriorityChange/action|Alterar as prioridades de failover das regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual|
|/databaseAccounts/delete|Excluir as contas de banco de dados.|
|/databaseAccounts/metricDefinitions/read|Ler o banco de dados de definições de métrica da conta.|
|/databaseAccounts/metrics/read|Ler a métrica da conta do banco de dados.|
|/databaseAccounts/usages/read|Ler os usos da conta do banco de dados.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Ler a coleção de definições de métrica.|
|/databaseAccounts/databases/collections/metrics/read|Ler a métrica da coleção.|
|/databaseAccounts/databases/collections/usages/read|Ler os usos da coleção.|
|/databaseAccounts/databases/metricDefinitions/read|Ler as definições de métrica do banco de dados|
|/databaseAccounts/databases/metrics/read|Ler a métrica do banco de dados.|
|/databaseAccounts/databases/usages/read|Ler os usos do banco de dados.|
|/databaseAccounts/readonlykeys/read|Ler as chaves somente leitura da conta do banco de dados.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operação | Descrição |
|---|---|
|/generateSsoRequest/Action|Gerar uma solicitação para entrar no centro de controle de domínio.|
|/validateDomainRegistrationInformation/Action|Validar o objeto de compra de domínio sem enviá-lo|
|/checkDomainAvailability/Action|Verificar se um domínio está disponível para compra|
|/listDomainRecommendations/Action|Recuperar as recomendações de domínio da lista com base em palavras-chave|
|/register/action|Registrar o provedor de recursos Microsoft Domains para a assinatura|
|/domains/Read|Obter a lista de domínios|
|/domains/Write|Adicionar um novo domínio ou atualizar um existente|
|/domains/Delete|Excluir um modo de exibição existente.|
|/domains/operationresults/Read|Obter uma operação de domínio|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operação | Descrição |
|---|---|
|/lcsprojects/read|Exibir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário|
|/lcsprojects/write|Criar e atualizar projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário. Somente as propriedades de nome e a descrição podem ser atualizadas. A assinatura e o local associados ao projeto não podem ser atualizados após a criação|
|/lcsprojects/delete|Excluir projetos dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem ao usuário|
|/lcsprojects/clouddeployments/read|Exibir as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário|
|/lcsprojects/clouddeployments/write|Criar as implantações de avaliação do Microsoft Dynamics AX 2012 R3 em um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics que pertencem a um usuário. As implantações podem ser gerenciadas no portal de gerenciamento do Azure|
|/lcsprojects/connectors/read|Ler os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics|
|/lcsprojects/connectors/write|Criar e atualizar os conectores que pertencem a um projeto dos Serviços de Ciclo de Vida do Microsoft Dynamics|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operação | Descrição |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/register/action|Registrar a assinatura do provedor de recursos EventHub e permitir a criação de recursos EventHub|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e status do namespace são as propriedades que podem ser atualizadas.|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/eventhubs/write|Criar ou atualizar propriedades EventHub.|
|/namespaces/eventhubs/read|Obter lista de descrições de recursos de EventHub|
|/namespaces/eventhubs/Delete|Operação para excluir o recurso EventHub|
|/namespaces/eventHubs/consumergroups/write|Criar ou atualizar propriedades ConsumerGroup.|
|/namespaces/eventHubs/consumergroups/read|Obter lista de descrições de recursos ConsumerGroup|
|/namespaces/eventHubs/consumergroups/Delete|Operação para excluir o recurso ConsumerGroup|
|/namespaces/eventhubs/authorizationRules/read| Obter a lista de regras de autorização EventHub|
|/namespaces/eventhubs/authorizationRules/write|Criar regras de autorização EventHub e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/eventhubs/authorizationRules/delete|Operação para excluir regras de autorização EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obter a cadeia de conexão para EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/diagnosticSettings/read|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/diagnosticSettings/write|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/logDefinitions/read|Obter lista de descrições do recurso de log do namespace|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operação | Descrição |
|---|---|
|/providers/features/read|Obter o recurso de uma assinatura em determinado provedor de recursos.|
|/providers/features/register/action|Registrar o recurso de uma assinatura em determinado provedor de recursos.|
|/features/read|Obter os recursos de uma assinatura.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operação | Descrição |
|---|---|
|/clusters/write|Criar ou atualizar o Cluster HDInsight|
|/clusters/read|Obter detalhes sobre o Cluster HDInsight|
|/clusters/delete|Excluir um cluster HDInsight|
|/clusters/changerdpsetting/action|Alterar configuração de RDP para Cluster HDInsight|
|/clusters/configurations/action|Atualizar a configuração do Cluster HDInsight|
|/clusters/configurations/read|Obter configurações de Cluster HDInsight|
|/clusters/roles/resize/action|Redimensionar um cluster HDInsight|
|/locations/capabilities/read|Obter recursos de assinatura|
|/locations/checkNameAvailability/read|Verificar disponibilidade do nome|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos importar/exportar e habilita a criação de trabalhos de importação/exportação.|
|/jobs/write|Criar um trabalho com os parâmetros especificados ou atualizar as propriedades ou marcações do trabalho especificado.|
|/jobs/read|Obter as propriedades para o trabalho especificado ou retornar a lista de trabalhos.|
|/jobs/listBitLockerKeys/action|Obter as chaves do BitLocker para o trabalho especificado.|
|/jobs/delete|Excluir um trabalho existente.|
|/locations/read|Obter as propriedades do local especificado ou retornar a lista de locais.|

## <a name="microsoftinsights"></a>Microsoft.insights

| Operação | Descrição |
|---|---|
|/Register/Action|Registrar o provedor do Microsoft Insights|
|/AlertRules/Write|Gravar em uma configuração de regra de alerta|
|/AlertRules/Delete|Excluir uma configuração de regra de alerta|
|/AlertRules/Read|Ler uma configuração de regra de alerta|
|/AlertRules/Activated/Action|Regra de alerta ativada|
|/AlertRules/Resolved/Action|Regra de alerta resolvida|
|/AlertRules/Throttled/Action|A regra de alerta está limitada|
|/AlertRules/Incidents/Read|Ler uma configuração incidente da regra de alerta|
|/MetricDefinitions/Read|Ler definições de métrica|
|/eventtypes/values/Read|Ler valores do tipo de evento de gerenciamento|
|/eventtypes/digestevents/Read|Ler resumo do tipo de evento de gerenciamento|
|/Metrics/Read|Ler métrica|
|/LogProfiles/Write|Gravar em uma configuração de perfil de log|
|/LogProfiles/Delete|Excluir configuração de perfis de log|
|/LogProfiles/Read|Ler perfis de log|
|/AutoscaleSettings/Write|Gravar em uma configuração de dimensionamento automático|
|/AutoscaleSettings/Delete|Excluir uma configuração de dimensionamento automático|
|/AutoscaleSettings/Read|Ler uma configuração de dimensionamento automático|
|/AutoscaleSettings/Scaleup/Action|Dimensionar operação de escalada vertical automaticamente|
|/AutoscaleSettings/Scaledown/Action|Dimensionar operação de redução vertical automaticamente|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Ler definições de métrica|
|/ActivityLogAlerts/Activated/Action|Disparou o alerta de log de atividade|
|/DiagnosticSettings/Write|Gravar na configuração de diagnóstico|
|/DiagnosticSettings/Delete|Excluir configuração de diagnóstico|
|/DiagnosticSettings/Read|Ler uma configuração de diagnóstico|
|/LogDefinitions/Read|Ler definições de log|
|/ExtendedDiagnosticSettings/Write|Gravar em uma configuração de diagnóstico estendido|
|/ExtendedDiagnosticSettings/Delete|Excluir configuração de diagnóstico estendido|
|/ExtendedDiagnosticSettings/Read|Ler uma configuração de diagnóstico estendido|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operação | Descrição |
|---|---|
|/register/action|Registrar uma assinatura|
|/checkNameAvailability/read|Verificar se um nome de chave do cofre é válido e não está em uso|
|/vaults/read|Exibir as propriedades de um cofre de chaves|
|/vaults/write|Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente|
|/vaults/delete|Excluir um cofre de chaves|
|/vaults/deploy/action|Permite acesso aos segredos em um cofre de chaves durante a implantação de recursos do Azure|
|/vaults/secrets/read|Exibir as propriedades de um segredo, mas não seu valor|
|/vaults/secrets/write|Criar um novo segredo ou atualizar o valor de um segredo existente|
|/vaults/accessPolicies/write|Atualizar uma política de acesso existente por mesclagem ou substituição, ou adicionar uma nova política de acesso a um cofre.|
|/deletedVaults/read|Exibir as propriedades de cofres de chaves com exclusão reversível|
|/locations/operationResults/read|Verificar o resultado de uma operação de longo prazo|
|/locations/deletedVaults/read|Exibir as propriedades de um cofre de chaves com exclusão reversível|
|/locations/deletedVaults/purge/action|Limpar um cofre de chaves com exclusão reversível|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operação | Descrição |
|---|---|
|/workflows/read|Ler o fluxo de trabalho.|
|/workflows/write|Criar ou atualizar o fluxo de trabalho.|
|/workflows/delete|Excluir o fluxo de trabalho.|
|/workflows/run/action|Iniciar uma execução do fluxo de trabalho.|
|/workflows/disable/action|Desabilitar o fluxo de trabalho.|
|/workflows/enable/action|Permitir o fluxo de trabalho.|
|/workflows/validate/action|Valida o fluxo de trabalho.|
|/workflows/move/action|Mover o fluxo de trabalho de sua id de assinatura, grupo de recursos e nome existente para uma id de assinatura, grupo de recursos e/ou nome diferente.|
|/workflows/listSwagger/action|Obter as definições do Swagger para o fluxo de trabalho.|
|/workflows/regenerateAccessKey/action|Regenera os segredos da chave de acesso.|
|/workflows/listCallbackUrl/action|Obter a URL de retorno de chamada para o fluxo de trabalho.|
|/workflows/versions/read|Ler a versão do fluxo de trabalho.|
|/workflows/versions/triggers/listCallbackUrl/action|Obter a URL de retorno de chamada do gatilho.|
|/workflows/runs/read|Ler a execução do fluxo de trabalho.|
|/workflows/runs/cancel/action|Cancelar a execução de um fluxo de trabalho.|
|/workflows/runs/actions/read|Ler a ação de execução do fluxo de trabalho.|
|/workflows/runs/operations/read|Ler o status da operação de execução do fluxo de trabalho.|
|/workflows/triggers/read|Ler o gatilho.|
|/workflows/triggers/run/action|Executar o gatilho.|
|/workflows/triggers/listCallbackUrl/action|Obter a URL de retorno de chamada do gatilho.|
|/workflows/triggers/histories/read|Ler os históricos de gatilho.|
|/workflows/triggers/histories/resubmit/action|Reenviar o gatilho do fluxo de trabalho.|
|/workflows/accessKeys/read|Ler a chave de acesso.|
|/workflows/accessKeys/write|Criar ou atualizar a chave de acesso.|
|/workflows/accessKeys/delete|Excluir a chave de acesso.|
|/workflows/accessKeys/list/action|Listar os segredos de chave de acesso.|
|/workflows/accessKeys/regenerate/action|Regenera os segredos da chave de acesso.|
|/locations/workflows/validate/action|Valida o fluxo de trabalho.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos do serviço Web do Machine Learning e permite a criação de serviços Web.|
|/webServices/action|Criar propriedades do serviço Web regionais para regiões com suporte|
|/commitmentPlans/read|Ler um plano de compromisso de Machine Learning|
|/commitmentPlans/write|Criar ou atualizar plano de compromisso de Machine Learning|
|/commitmentPlans/delete|Excluir plano de compromisso de Machine Learning|
|/commitmentPlans/join/action|Ingressar em um plano de compromisso de Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Ler associações de plano de compromisso de Machine Learning|
|/commitmentPlans/commitmentAssociations/move/action|Mover associações de plano de compromisso de Machine Learning|
|/Workspaces/read|Ler um Espaço de Trabalho do Machine Learning|
|/Workspaces/write|Criar ou atualizar Espaço de Trabalho do Machine Learning|
|/Workspaces/delete|Criar um Espaço de Trabalho do Machine Learning|
|/Workspaces/listworkspacekeys/action|Listar chaves para um Espaço de Trabalho do Machine Learning|
|/Workspaces/resyncstoragekeys/action|Ressincronizar as chaves da conta de armazenamento configurada para um Espaço de Trabalho do Machine Learning|
|/webServices/read|Ler um serviço Web do Machine Learning|
|/webServices/write|Criar ou atualizar serviço Web do Machine Learning|
|/webServices/delete|Excluir um serviço Web do Machine Learning|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operação | Descrição |
|---|---|
|/agreements/offers/plans/read|Retornar um contrato para um item específico do marketplace|
|/agreements/offers/plans/sign/action|Assinar um contrato para um item específico do marketplace|
|/agreements/offers/plans/cancel/action|Cancelar um contrato para um item específico do marketplace|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operação | Descrição |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura|
|/unregister/action|Cancelar o registro da assinatura|
|/checkTrafficManagerNameAvailability/action|Verificar a disponibilidade de um nome DNS relativo do Gerenciador de Tráfego.|
|/dnszones/read|Obter a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera conjuntos de registros contidos na zona.|
|/dnszones/write|Criar ou atualizar uma zona DNS em um grupo de recursos.  Usado para atualizar as marcações em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros na zona.|
|/dnszones/delete|Excluir a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets.|
|/dnszones/MX/read|Obter o conjunto de registros do tipo 'MX' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/MX/write|Criar ou atualizar um conjunto de registros do tipo 'MX' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/MX/delete|Remover o conjunto de registros de determinado nome e tipo ‘MX’ de uma zona DNS.|
|/dnszones/NS/read|Obter o conjunto de registros DNS do tipo NS|
|/dnszones/NS/write|Criar ou atualizar o conjunto de registros DNS do tipo NS|
|/dnszones/NS/delete|Excluir o conjunto de registros DNS do tipo NS|
|/dnszones/AAAA/read|Obter o conjunto de registros do tipo 'AAAA' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/AAAA/write|Criar ou atualizar um conjunto de registros do tipo 'AAAA' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/AAAA/delete|Remover o conjunto de registros de determinado nome e digite ‘AAAA’ de uma zona DNS.|
|/dnszones/CNAME/read|Obter o conjunto de registros do tipo 'CNAME' no formato JSON. O conjunto de registros contém TTL, marcações e etag.|
|/dnszones/CNAME/write|Criar ou atualizar um conjunto de registros do tipo 'CNAME' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/CNAME/delete|Remover o conjunto de registros de determinado nome e tipo ‘CNAME’ de uma zona DNS.|
|/dnszones/SOA/read|Obter o conjunto de registros DNS do tipo SOA|
|/dnszones/SOA/write|Criar ou atualizar o conjunto de registros DNS do tipo SOA|
|/dnszones/SRV/read|Obter o conjunto de registros do tipo 'SRV' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/SRV/write|Criar ou atualizar o conjunto de registros do tipo SRV|
|/dnszones/SRV/delete|Remover o conjunto de registros de determinado nome e tipo ‘SRV’ de uma zona DNS.|
|/dnszones/PTR/read|Obter o conjunto de registros do tipo 'PTR' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/PTR/write|Criar ou atualizar um conjunto de registros do tipo 'PTR' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/PTR/delete|Remover o conjunto de registros de determinado nome e tipo ‘PTR’ de uma zona DNS.|
|/dnszones/A/read|Obter o conjunto de registros do tipo 'A', no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/A/write|Criar ou atualizar um conjunto de registros do tipo 'A' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/A/delete|Remover o conjunto de registros de determinado nome e tipo ‘A’ de uma zona DNS.|
|/dnszones/TXT/read|Obter o conjunto de registros do tipo 'TXT' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags.|
|/dnszones/TXT/write|Criar ou atualizar um conjunto de registros do tipo 'TXT' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros.|
|/dnszones/TXT/delete|Remover o conjunto de registros de determinado nome e tipo ‘TXT’ de uma zona DNS.|
|/dnszones/recordsets/read|Obter os conjuntos de registros DNS em todos os tipos|
|/networkInterfaces/read|Obter uma definição de adaptador de rede. |
|/networkInterfaces/write|Criar uma interface de rede ou atualizar uma interface de rede existente. |
|/networkInterfaces/join/action|Adicionar uma Máquina Virtual a um adaptador de rede|
|/networkInterfaces/delete|Excluir um adaptador de rede|
|/networkInterfaces/effectiveRouteTable/action|Obter tabela de rota configurada no adaptador de rede da máquina virtual|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obter grupos de segurança de configurados no adaptador de rede da máquina virtual|
|/networkInterfaces/loadBalancers/read|Obter todos os balanceadores de carga dos quais o adaptador de rede faz parte|
|/networkInterfaces/ipconfigurations/read|Obter uma definição de configuração de IP do adaptador de rede. |
|/publicIPAddresses/read|Obter uma definição de endereço IP público.|
|/publicIPAddresses/write|Criar um endereço IP público ou atualizar um endereço IP público existente. |
|/publicIPAddresses/delete|Excluir um endereço IP público.|
|/publicIPAddresses/join/action|Ingressar em um endereço IP público|
|/routeFilters/read|Obter uma definição de filtro de rota|
|/routeFilters/join/action|Ingressar em um filtro de rota|
|/routeFilters/delete|Excluir uma definição de filtro de rota|
|/routeFilters/write|Criar um filtro de rota ou atualizar um filtro de rota existente|
|/routeFilters/rules/read|Obter uma definição de regra de filtro de rota|
|/routeFilters/rules/write|Criar uma regra de filtro de rota ou atualizar uma regra de filtro de rota existente|
|/routeFilters/rules/delete|Excluir uma definição de regra de filtro de rota|
|/networkWatchers/read|Obter a definição de observador de rede|
|/networkWatchers/write|Criar um observador de rede ou atualizar um observador de rede existente|
|/networkWatchers/delete|Excluir um observador de rede|
|/networkWatchers/configureFlowLog/action|Configurar o registro de fluxo em log para um recurso de destino.|
|/networkWatchers/ipFlowVerify/action|Retornar se o pacote é permitido ou negado em relação a um destino específico.|
|/networkWatchers/nextHop/action|Para um destino especificado e endereço IP de destino especificados, retornar o tipo do próximo salto e próximo endereço IP esperado.|
|/networkWatchers/queryFlowLogStatus/action|Obter o status do registro do fluxo em log em um recurso.|
|/networkWatchers/queryTroubleshootResult/action|Obter o resultado da solução de problemas da operação de solução de problemas executada ou atualmente em execução.|
|/networkWatchers/securityGroupView/action|Exibir as regras de grupo de segurança de rede configuradas e em vigor aplicadas a uma máquina virtual.|
|/networkWatchers/topology/action|Obter uma exibição dos recursos em nível de rede e de suas relações em um grupo de recursos.|
|/networkWatchers/troubleshoot/action|Iniciar a solução de problemas em um recurso de rede no Azure.|
|/networkWatchers/packetCaptures/queryStatus/action|Obter informações sobre propriedades e status de um recurso de captura de pacote.|
|/networkWatchers/packetCaptures/stop/action|Interromper a sessão de captura de pacote em execução.|
|/networkWatchers/packetCaptures/read|Obter a definição de captura de pacote|
|/networkWatchers/packetCaptures/write|Criar uma captura de pacote|
|/networkWatchers/packetCaptures/delete|Excluir uma captura de pacote|
|/loadBalancers/read|Obter uma definição de balanceador de carga|
|/loadBalancers/write|Criar um balanceador de carga ou atualizar um balanceador de carga existente|
|/loadBalancers/delete|Excluir um balanceador de carga|
|/loadBalancers/networkInterfaces/read|Obter as referências a todos os adaptadores de rede em um balanceador de carga|
|/loadBalancers/loadBalancingRules/read|Obter uma definição regra de balanceamento de carga do balanceador de carga|
|/loadBalancers/backendAddressPools/read|Obter uma definição de pool de endereços de back-end do balanceador de carga|
|/loadBalancers/backendAddressPools/join/action|Ingressar em um pool de endereços de back-end do balanceador de carga|
|/loadBalancers/inboundNatPools/read|Obter uma definição de pool NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatPools/join/action|Ingressar em um pool NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/read|Obter uma definição de regra NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/write|Criar uma regra NAT de entrada do balanceador de carga ou atualizar uma regra NAT de entrada do balanceador de carga existente|
|/loadBalancers/inboundNatRules/delete|Excluir uma regra NAT de entrada do balanceador de carga|
|/loadBalancers/inboundNatRules/join/action|Adicionar uma regra NAT de entrada do balanceador de carga|
|/loadBalancers/outboundNatRules/read|Obter uma definição de regra NAT de saída do balanceador de carga|
|/loadBalancers/probes/read|Obter uma investigação do balanceador de carga|
|/loadBalancers/virtualMachines/read|Obter as referências a todas as máquinas virtuais em um balanceador de carga|
|/loadBalancers/frontendIPConfigurations/read|Obter uma definição de configuração de IP de front-end do balanceador de carga|
|/trafficManagerGeographicHierarchies/read|Obter a hierarquia de geográfica do Gerenciador de Tráfego contendo regiões que podem ser usadas com o método de roteamento de tráfego geográfico|
|/bgpServiceCommunities/read|Obter comunidades do serviço BGP|
|/applicationGatewayAvailableWafRuleSets/read|Obter conjuntos de regras WAF disponíveis para o Gateway de Aplicativo|
|/virtualNetworks/read|Obter a definição de rede virtual|
|/virtualNetworks/write|Criar uma rede virtual ou atualizar uma rede virtual existente|
|/virtualNetworks/delete|Excluir uma rede virtual|
|/virtualNetworks/peer/action|Colocar uma rede virtual com outra rede virtual de mesmo nível|
|/virtualNetworks/virtualNetworkPeerings/read|Obter uma definição de emparelhamento de rede virtual|
|/virtualNetworks/virtualNetworkPeerings/write|Criar um emparelhamento de rede virtual ou atualizar um emparelhamento de rede virtual existente|
|/virtualNetworks/virtualNetworkPeerings/delete|Excluir um emparelhamento de redes virtuais|
|/virtualNetworks/subnets/read|Obter uma definição de sub-rede da rede virtual|
|/virtualNetworks/subnets/write|Criar uma sub-rede de rede virtual ou atualizar uma sub-rede de rede virtual existente|
|/virtualNetworks/subnets/delete|Excluir uma sub-rede de rede virtual|
|/virtualNetworks/subnets/join/action|Ingressar em uma rede virtual|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|Adicionar recurso como conta de armazenamento ou banco de dados SQL a uma sub-rede habilitada para encapsulamento do serviço.|
|/virtualNetworks/subnets/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual|
|/virtualNetworks/checkIpAddressAvailability/read|Verificar se o endereço IP está disponível na rede virtual especificada|
|/virtualNetworks/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma rede virtual|
|/expressRouteServiceProviders/read|Obter os provedores de serviços do ExpressRoute|
|/dnsoperationresults/read|Obter os resultados de uma operação de DNS|
|/localnetworkgateways/read|Obter o LocalNetworkGateway|
|/localnetworkgateways/write|Criar ou atualizar uma LocalNetworkGateway existente|
|/localnetworkgateways/delete|Excluir LocalNetworkGateway|
|/trafficManagerProfiles/read|Obter a configuração de perfil do Gerenciador de Tráfego. Isso inclui configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e lista de pontos de extremidade roteados por esse perfil do Gerenciador de Tráfego.|
|/trafficManagerProfiles/write|Criar um perfil do Gerenciador de Tráfego ou modificar a configuração de um perfil do Gerenciador de Tráfego existente. Isso inclui habilitar ou desabilitar um perfil e modificar as configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento do ponto de extremidade. Pontos de extremidade roteados pelo perfil do Gerenciador de Tráfego podem ser adicionados, removidos, habilitados ou desabilitados.|
|/trafficManagerProfiles/delete|Excluir o perfil do Gerenciador de Tráfego. Todas as configurações associadas ao perfil do Gerenciador de Tráfego serão perdidas e o perfil não poderá ser usado para rotear o tráfego.|
|/dnsoperationstatuses/read|Obter o status de uma operação de DNS |
|/operations/read|Obter operações disponíveis|
|/expressRouteCircuits/read|Obter um ExpressRouteCircuit|
|/expressRouteCircuits/write|Criar ou atualizar um ExpressRouteCircuit existente|
|/expressRouteCircuits/delete|Excluir um ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Obter status de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Obter um emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente|
|/expressRouteCircuits/peerings/delete|Excluir um emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Obter ArpTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Obter RouteTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|Obter um resumo de RouteTable de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Obter status de emparelhamento de ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/read|Obter uma autorização ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Criar ou atualizar uma autorização ExpressRouteCircuit existente|
|/expressRouteCircuits/authorizations/delete|Excluir uma autorização ExpressRouteCircuit|
|/connections/read|Obter o VirtualNetworkGatewayConnection|
|/connections/write|Criar ou atualizar uma VirtualNetworkGatewayConnection existente|
|/connections/delete|Excluir VirtualNetworkGatewayConnection|
|/connections/sharedKey/read|Obter SharedKey da VirtualNetworkGatewayConnection|
|/connections/sharedKey/write|Criar ou atualizar uma VirtualNetworkGatewayConnection SharedKey existente|
|/networkSecurityGroups/read|Obter uma definição de um grupo de segurança de rede|
|/networkSecurityGroups/write|Criar um grupo de segurança de rede ou atualizar um grupo de segurança de rede existente|
|/networkSecurityGroups/delete|Excluir um grupo de segurança de rede|
|/networkSecurityGroups/join/action|Ingressar em um grupo de segurança de rede|
|/networkSecurityGroups/defaultSecurityRules/read|Obter uma definição padrão da regra de segurança|
|/networkSecurityGroups/securityRules/read|Obter uma definição da regra de segurança|
|/networkSecurityGroups/securityRules/write|Criar uma regra de segurança ou atualizar uma regra de segurança existente|
|/networkSecurityGroups/securityRules/delete|Excluir uma regra de segurança|
|/applicationGateways/read|Obter um gateway de aplicativo|
|/applicationGateways/write|Criar ou atualizar um gateway de aplicativo|
|/applicationGateways/delete|Excluir um gateway de aplicativo|
|/applicationGateways/backendhealth/action|Obter uma integridade de back-end do Gateway de Aplicativo|
|/applicationGateways/start/action|Inicia um gateway de aplicativo|
|/applicationGateways/stop/action|Interrompe um gateway de aplicativo|
|/applicationGateways/backendAddressPools/join/action|Ingressar em um pool de endereços de back-end do gateway de aplicativo|
|/routeTables/read|Obter uma definição de tabela de rota|
|/routeTables/write|Criar uma tabela de rotas ou atualizar uma tabela de rotas existente|
|/routeTables/delete|Excluir uma definição de tabela de rota|
|/routeTables/join/action|Ingressar em uma tabela de rotas|
|/routeTables/routes/read|Obter uma definição de rota|
|/routeTables/routes/write|Criar uma rota ou atualizar uma rota existente|
|/routeTables/routes/delete|Excluir uma definição de rota|
|/locations/operationResults/read|Obter o resultado de uma operação POST ou DELETE assíncrona|
|/locations/checkDnsNameAvailability/read|Verificar se o rótulo de DNS está disponível no local especificado|
|/locations/usages/read|Obter a métrica de uso dos recursos|
|/locations/operations/read|Obter o recurso de operação que representa o status de uma operação assíncrona|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos NotifciationHubs e permite a criação de Namespaces e NotificationHubs|
|/CheckNamespaceAvailability/action|Verificar se determinado nome de recurso do Namespace está disponível no serviço NotificationHub.|
|/Namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e status do namespace são as propriedades que podem ser atualizadas.|
|/Namespaces/read|Obter a lista de descrição do recurso de namespace|
|/Namespaces/Delete|Excluir o recurso de namespace|
|/Namespaces/authorizationRules/action|Obter a lista de descrição de regras de autorização de namespaces.|
|/Namespaces/CheckNotificationHubAvailability/action|Verificar se determinado nome de NotificationHub está disponível em um Namespace.|
|/Namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/Namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/Namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/Namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/Namespaces/authorizationRules/regenerateKeys/action|Regra de autorização de namespace regenera chave primária/secundária; especifique a chave que precisa ser regenerada|
|/Namespaces/NotificationHubs/write|Criar um Hub de notificação e atualizar suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Tempo de vida útil e regras de autorização|
|/Namespaces/NotificationHubs/read|Obter lista de descrições de recursos do Hub de notificação|
|/Namespaces/NotificationHubs/Delete|Excluir recurso de Hub de notificação|
|/Namespaces/NotificationHubs/authorizationRules/action|Obter a lista de regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obter todas as credenciais PNS do Hub de notificação. Isso inclui credenciais WNS, MPNS, APNS, GCM e Baidu|
|/Namespaces/NotificationHubs/debugSend/action|Enviar notificação por push de teste.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Criar regras de autorização do Hub de notificação e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Obter a lista de regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Excluir regras de autorização do Hub de notificação|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Obter a cadeia de conexão para o Hub de notificação|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|Regra de autorização de Hub de notificação regenera chave primária/secundária; especifique a chave que precisa ser regenerada|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operação | Descrição |
|---|---|
|/register/action|Registrar uma assinatura de um provedor de recursos.|
|/linkTargets/read|Listar as contas existentes que não estão associadas uma assinatura do Azure. Para vincular essa assinatura do Azure a um espaço de trabalho, use uma ID de cliente retornada pela operação na propriedade customer id da operação Criar espaço de trabalho.|
|/workspaces/write|Criar um novo espaço de trabalho ou links para um espaço de trabalho existente fornecendo a ID do cliente da área de trabalho existente.|
|/workspaces/read|Obter um espaço de trabalho existente|
|/workspaces/delete|Excluir um espaço de trabalho. Se o espaço de trabalho foi vinculado a um espaço de trabalho existente no momento da criação, o espaço de trabalho a que ele foi vinculado não será excluído.|
|/workspaces/generateregistrationcertificate/action|Gerar certificado de registro para o espaço de trabalho. Esse certificado é usado para conectar o Microsoft System Center Operations Manager ao espaço de trabalho.|
|/workspaces/sharedKeys/action|Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/search/action|Executar uma consulta de pesquisa|
|/workspaces/datasources/read|Obter fontes de dados em um espaço de trabalho.|
|/workspaces/datasources/write|Criar/atualizar fontes de dados em um espaço de trabalho.|
|/workspaces/datasources/delete|Excluir fontes de dados em um espaço de trabalho.|
|/workspaces/managementGroups/read|Obter os nomes e os metadados para grupos de gerenciamento do System Center Operations Manager conectados ao espaço de trabalho.|
|/workspaces/schema/read|Obter o esquema de pesquisa do espaço de trabalho.  O esquema de pesquisa inclui os campos expostos e seus tipos.|
|/workspaces/usages/read|Obter dados de uso de um espaço de trabalho e a quantidade de dados lidos pelo espaço de trabalho.|
|/workspaces/intelligencepacks/read|Lista todos os pacotes de inteligência visíveis para determinado espaço de trabalho e também indica se o pacote está habilitado ou desabilitado para esse espaço de trabalho.|
|/workspaces/intelligencepacks/enable/action|Habilitar um pacote de inteligência para determinado espaço de trabalho.|
|/workspaces/intelligencepacks/disable/action|Desabilitar um pacote de inteligência para determinado espaço de trabalho.|
|/workspaces/sharedKeys/read|Recupera as chaves compartilhadas do espaço de trabalho. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao espaço de trabalho.|
|/workspaces/savedSearches/read|Obter uma consulta de pesquisa salva|
|/workspaces/savedSearches/write|Criar uma consulta de pesquisa salva|
|/workspaces/savedSearches/delete|Excluir uma consulta de pesquisa salva|
|/workspaces/storageinsightconfigs/write|Criar uma nova configuração de armazenamento. Essas configurações são usadas para extrair dados de um local em uma conta de armazenamento existente.|
|/workspaces/storageinsightconfigs/read|Obter uma configuração de armazenamento.|
|/workspaces/storageinsightconfigs/delete|Excluir uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelo Insights Operacionais.|
|/workspaces/configurationScopes/read|Obter escopo de configuração|
|/workspaces/configurationScopes/write|Definir o escopo da configuração|
|/workspaces/configurationScopes/delete|Excluir escopo de configuração|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operação | Descrição |
|---|---|
|/register/action|Registrar uma assinatura de um provedor de recursos.|
|/solutions/write|Criar nova solução do OMS|
|/solutions/read|Obter solução OMS de saída|
|/solutions/delete|Excluir a solução do OMS existente|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operação | Descrição |
|---|---|
|/Vaults/backupJobsExport/action|Exportar trabalhos|
|/Vaults/write|A operação Criar cofre cria um recurso do Azure do tipo 'cofre'|
|/Vaults/read|A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/Vaults/delete|A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado|
|/Vaults/refreshContainers/read|Atualizar a lista de contêineres|
|/Vaults/backupJobsExport/operationResults/read|Retornar o resultado da operação do trabalho de exportação.|
|/Vaults/backupOperationResults/read|Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/monitoringAlerts/read|Obter os alertas para o cofre dos Serviços de Recuperação.|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|Obter os detalhes do alerta.|
|/Vaults/backupSecurityPIN/read|Retornar a informação de PIN de segurança para o cofre dos Serviços de Recuperação.|
|/vaults/replicationEvents/read|Ler eventos|
|/Vaults/backupProtectableItems/read|Retornar a lista de todos os itens que podem ser protegidos.|
|/vaults/replicationFabrics/read|Ler malhas|
|/vaults/replicationFabrics/write|Criar ou atualizar malhas|
|/vaults/replicationFabrics/remove/action|Remover malha|
|/vaults/replicationFabrics/checkConsistency/action|Verificar consistência da malha|
|/vaults/replicationFabrics/delete|Excluir malhas|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|Implantar imagem do servidor de processo|
|/vaults/replicationFabrics/reassociateGateway/action|Reassociar gateway|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>leitura|Ler provedores de serviços de recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Remover o provedor dos Serviços de Recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>excluir|Excluir provedor dos Serviços de Recuperação|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|Atualizar provedor|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Ler classificações de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|Ler mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|Criar ou atualizar os mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|Excluir mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationvCenters/read|Ler todos os trabalhos|
|/vaults/replicationFabrics/replicationvCenters/write|Criar ou atualizar os trabalhos|
|/vaults/replicationFabrics/replicationvCenters/delete|Excluir trabalhos|
|/vaults/replicationFabrics/replicationNetworks/read|Ler redes|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|Ler mapeamentos de rede|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|Criar ou atualizar os mapeamentos de rede|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|Excluir mapeamentos de rede|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>leitura|Ler contêineres de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|Descobrir item que pode ser protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>gravação|Criar ou atualizar contêineres de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|Remover o contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|Alterar contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|Ler itens que podem ser protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|Ler mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|Criar ou atualizar os mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|Remover o mapeamento de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|Excluir mapeamentos de contêiner de proteção|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|Ler itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|Criar ou atualizar os itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|Excluir itens protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|Remover item protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|Failover planejado|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Failover de Teste|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|Limpeza do Failover de teste|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|Confirmação de failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|Proteger item protegido novamente|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|Atualizar serviço de mobilidade|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|Reparar replicação|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|Aplicar ponto de recuperação|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|Ler pontos de recuperação de replicação|
|/vaults/replicationPolicies/read|Ler políticas|
|/vaults/replicationPolicies/write|Criar ou atualizar as políticas|
|/vaults/replicationPolicies/delete|Excluir políticas|
|/vaults/replicationRecoveryPlans/read|Ler planos de recuperação|
|/vaults/replicationRecoveryPlans/write|Criar ou atualizar planos de recuperação|
|/vaults/replicationRecoveryPlans/delete|Excluir planos de recuperação|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plano de recuperação de failover planejado|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plano de recuperação de failover|
|/vaults/replicationRecoveryPlans/testFailover/action|Testar plano de recuperação de failover|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Testar plano de recuperação de limpeza do failover|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plano de recuperação de confirmação de failover|
|/vaults/replicationRecoveryPlans/reProtect/action|Proteja plano de recuperação novamente|
|/Vaults/extendedInformation/read|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/extendedInformation/write|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/extendedInformation/delete|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Vaults/backupManagementMetaData/read|Retornar metadados de gerenciamento de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/backupProtectionContainers/read|Retornar todos os contêineres pertencentes à assinatura|
|/Vaults/backupFabrics/operationResults/read|Retornar o status da operação|
|/Vaults/backupFabrics/protectionContainers/read|Retornar todos os contêineres registrados|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|Obter o resultado da operação executada no contêiner de proteção.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|Retornar detalhes do objeto do item protegido|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|Criar um item protegido de backup|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|Excluir item protegido|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|Executar um backup para um item protegido.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|Obter o resultado da operação executada em itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|Retornar o status da operação executada em itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|Obter pontos de recuperação para itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|Restaurar pontos de recuperação para itens protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|Provisionar recuperação de item instantânea para item protegido|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|Revogar a recuperação de item instantânea para item protegido|
|/Vaults/usages/read|Retornar os detalhes de uso para um cofre dos Serviços de Recuperação.|
|/vaults/usages/read|Ler usos de cofre|
|/Vaults/certificates/write|A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso.|
|/Vaults/tokenInfo/read|Retornar a informação de token para o cofre dos Serviços de Recuperação.|
|/vaults/replicationAlertSettings/read|Ler configurações de alertas|
|/vaults/replicationAlertSettings/write|Criar ou atualizar as configurações de alertas|
|/Vaults/backupOperations/read|Retornar o status da operação de backup para o cofre dos Serviços de Recuperação.|
|/Vaults/storageConfig/read|Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação.|
|/Vaults/storageConfig/write|Atualizar cofre de armazenamento para o cofre dos Serviços de Recuperação.|
|/Vaults/backupUsageSummaries/read|Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação.|
|/Vaults/backupProtectedItems/read|Retornar a lista de todos os itens protegidos.|
|/Vaults/backupconfig/vaultconfig/read|Retornar a configuração para cofre dos Serviços de Recuperação.|
|/Vaults/backupconfig/vaultconfig/write|Atualizar configuração para o cofre dos Serviços de Recuperação.|
|/Vaults/registeredIdentities/write|A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação.|
|/Vaults/registeredIdentities/read|A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso.|
|/Vaults/registeredIdentities/delete|A operação Cancelar o registro de contêiner pode ser usada para cancelar o registro de um contêiner.|
|/Vaults/registeredIdentities/operationResults/read|A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona|
|/vaults/replicationJobs/read|Ler todos os trabalhos|
|/vaults/replicationJobs/cancel/action|Cancelar trabalho|
|/vaults/replicationJobs/restart/action|Reiniciar o trabalho|
|/vaults/replicationJobs/resume/action|Retomar o trabalho|
|/Vaults/backupPolicies/read|Retornar todas as políticas de proteção|
|/Vaults/backupPolicies/write|Criar uma política de proteção|
|/Vaults/backupPolicies/delete|Excluir uma política de proteção|
|/Vaults/backupPolicies/operationResults/read|Obter os resultados da operação de política.|
|/Vaults/backupPolicies/operationStatus/read|Obter o status da operação de política.|
|/Vaults/vaultTokens/read|A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Obter a configuração de notificação do cofre dos Serviços de Recuperação.|
|/Vaults/backupJobs/read|Retornar todos os objetos de trabalho|
|/Vaults/backupJobs/cancel/action|Cancelar o trabalho|
|/Vaults/backupJobs/operationResults/read|Retornar o resultado da operação do trabalho.|
|/locations/allocateStamp/action|AllocateStamp é uma operação interna usada pelo serviço|
|/locations/allocatedStamp/read|GetAllocatedStamp é uma operação interna usada pelo serviço|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operação | Descrição |
|---|---|
|/checkNamespaceAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/register/action|Registrar a assinatura do provedor de recursos Relay e permitir a criação de recursos Relay|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e status do namespace são as propriedades que podem ser atualizadas.|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/HybridConnections/write|Criar ou atualizar propriedades HybridConnection.|
|/namespaces/HybridConnections/read|Obter lista de descrições de recursos HybridConnection|
|/namespaces/HybridConnections/Delete|Operação para excluir o recurso HybridConnection|
|/namespaces/HybridConnections/authorizationRules/write|Criar regras de autorização HybridConnection e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/HybridConnections/authorizationRules/delete|Operação para excluir regras de autorização HybridConnection|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obter a cadeia de conexão para HybridConnection|
|/namespaces/WcfRelays/write|Criar ou atualizar propriedades WcfRelay.|
|/namespaces/WcfRelays/read|Obter lista de descrições de recursos WcfRelay|
|/namespaces/WcfRelays/Delete|Operação para excluir o recurso WcfRelay|
|/namespaces/WcfRelays/authorizationRules/write|Criar regras de autorização WcfRelay e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/WcfRelays/authorizationRules/delete|Operação para excluir regras de autorização WcfRelay|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obter a cadeia de conexão para WcfRelay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operação | Descrição |
|---|---|
|/AvailabilityStatuses/read|Obter os status de disponibilidade para todos os recursos no escopo especificado|
|/AvailabilityStatuses/current/read|Obter o status de disponibilidade do recurso especificado|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operação | Descrição |
|---|---|
|/checkResourceName/action|Verificar a validade do nome do recurso.|
|/providers/read|Obter a lista de provedores.|
|/subscriptions/read|Obter a lista de assinaturas.|
|/subscriptions/operationresults/read|Obter os resultados da operação da assinatura.|
|/subscriptions/providers/read|Obter ou listar provedores de recursos.|
|/subscriptions/tagNames/read|Obter ou listar marcações de assinatura.|
|/subscriptions/tagNames/write|Adicionar uma marcação de assinatura.|
|/subscriptions/tagNames/delete|Excluir uma marcação de assinatura.|
|/subscriptions/tagNames/tagValues/read|Obter ou listar valores de marcação de assinatura.|
|/subscriptions/tagNames/tagValues/write|Adicionar um valor de marcação de assinatura.|
|/subscriptions/tagNames/tagValues/delete|Excluir um valor de marcação de assinatura.|
|/subscriptions/resources/read|Obter os recursos de uma assinatura.|
|/subscriptions/resourceGroups/read|Obter ou listar de grupos de recursos.|
|/subscriptions/resourceGroups/write|Criar ou atualizar um grupo de recursos.|
|/subscriptions/resourceGroups/delete|Excluir um grupo de recursos e todos os seus recursos.|
|/subscriptions/resourceGroups/moveResources/action|Mover recursos de um grupo de recursos para outro.|
|/subscriptions/resourceGroups/validateMoveResources/action|Validar a movimentação de recursos de um grupo de recursos para outro.|
|/subscriptions/resourcegroups/resources/read|Obter os recursos do grupo de recursos.|
|/subscriptions/resourcegroups/deployments/read|Obter ou lista implantações.|
|/subscriptions/resourcegroups/deployments/write|Criar ou atualizar uma implantação.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obter ou listar o status da operação de implantação.|
|/subscriptions/resourcegroups/deployments/operations/read|Obter ou lista operações de implantação.|
|/subscriptions/locations/read|Obter a lista de locais com suporte.|
|/links/read|Obter ou listar links de recursos.|
|/links/write|Criar ou atualizar um link de recurso.|
|/links/delete|Excluir um link de recurso.|
|/tenants/read|Obter a lista de locatários.|
|/resources/read|Obter a lista de recursos com base em filtros.|
|/deployments/read|Obter ou lista implantações.|
|/deployments/write|Criar ou atualizar uma implantação.|
|/deployments/delete|Excluir uma implantação.|
|/deployments/cancel/action|Cancelar uma implantação.|
|/deployments/validate/action|Validar uma implantação.|
|/deployments/operations/read|Obter ou lista operações de implantação.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operação | Descrição |
|---|---|
|/jobcollections/read|Obter coleção de trabalhos|
|/jobcollections/write|Criar ou atualizar a coleção de trabalhos.|
|/jobcollections/delete|Excluir coleção de trabalhos.|
|/jobcollections/enable/action|Habilita coleção de trabalhos.|
|/jobcollections/disable/action|Desabilita coleção de trabalhos.|
|/jobcollections/jobs/read|Obter o trabalho.|
|/jobcollections/jobs/write|Criar ou atualizar o trabalho.|
|/jobcollections/jobs/delete|Excluir trabalho.|
|/jobcollections/jobs/run/action|Executa o trabalho.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Gerar a definição de aplicativo lógico com base em um trabalho do Agendador.|
|/jobcollections/jobs/jobhistories/read|Obtém o histórico de trabalhos.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operação | Descrição |
|---|---|
|/register/action|Registrar a assinatura do provedor de recursos de pesquisa e permitir a criação de serviços de pesquisa.|
|/checkNameAvailability/action|Verificar a disponibilidade do nome do serviço.|
|/searchServices/write|Criar ou atualizar o serviço de pesquisa.|
|/searchServices/read|Ler o serviço de pesquisa.|
|/searchServices/delete|Excluir o serviço de pesquisa.|
|/searchServices/start/action|Iniciar o serviço de pesquisa.|
|/searchServices/stop/action|Interromper o serviço de pesquisa.|
|/searchServices/listAdminKeys/action|Ler as chaves de administração.|
|/searchServices/regenerateAdminKey/action|Regenerar a chave de administração.|
|/searchServices/createQueryKey/action|Criar a chave de consulta.|
|/searchServices/queryKey/read|Ler as chaves de consulta.|
|/searchServices/queryKey/delete|Excluir a chave de consulta.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operação | Descrição |
|---|---|
|/jitNetworkAccessPolicies/read|Obter as políticas de acesso de rede just-in-time|
|/jitNetworkAccessPolicies/write|Criar uma nova política de acesso de rede just-in-time ou atualizar uma existente|
|/jitNetworkAccessPolicies/initiate/action|Iniciar uma política de acesso de rede just-in-time|
|/securitySolutionsReferenceData/read|Obter dados de referência das soluções de segurança|
|/securityStatuses/read|Obter status de integridade da segurança para recursos do Azure|
|/webApplicationFirewalls/read|Obter firewalls do aplicativo Web|
|/webApplicationFirewalls/write|Criar um novo firewall do aplicativo Web ou atualizar um existente|
|/webApplicationFirewalls/delete|Excluir um firewall do aplicativo Web|
|/securitySolutions/read|Obter as soluções de segurança|
|/securitySolutions/write|Criar uma nova solução de segurança ou atualizar uma existente|
|/securitySolutions/delete|Excluir uma solução de segurança|
|/tasks/read|Obter todas as recomendações de segurança disponíveis|
|/tasks/dismiss/action|Ignorar recomendação de segurança|
|/tasks/activate/action|Ativar recomendação de segurança|
|/policies/read|Obter a política de segurança|
|/policies/write|Atualizar a política de segurança|
|/applicationWhitelistings/read|Obter a lista de liberações do aplicativo|
|/applicationWhitelistings/write|Criar uma nova lista de exceções do aplicativo ou atualizar uma existente|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Operação | Descrição |
|---|---|
|/subscriptions/write|Criar ou atualizar uma assinatura|
|/gateways/write|Criar ou atualizar um gateway|
|/gateways/delete|Excluir um gateway|
|/gateways/read|Obter um gateway|
|/gateways/regenerateprofile/action|Gerar novamente o perfil de gateway|
|/gateways/upgradetolatest/action|Atualizar o gateway para a versão mais recente|
|/nodes/write|criar ou atualizar um nó|
|/nodes/delete|Excluir um nó|
|/nodes/read|Obter um nó|
|/sessions/write|Criar ou atualizar uma sessão|
|/sessions/read|Obter uma sessão|
|/sessions/delete|Excluir uma sessão|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operação | Descrição |
|---|---|
|/checkNameAvailability/action|Verificar a disponibilidade do namespace em determinada assinatura.|
|/register/action|Registrar a assinatura do provedor de recursos ServiceBus e permitir a criação de recursos ServiceBus|
|/namespaces/write|Criar um recurso de namespace e atualizar suas propriedades. Marcas e status do namespace são as propriedades que podem ser atualizadas.|
|/namespaces/read|Obter a lista de descrição do recurso de namespace|
|/namespaces/Delete|Excluir o recurso de namespace|
|/namespaces/metricDefinitions/read|Obter lista de métrica de descrições de recurso de métrica do namespace|
|/namespaces/authorizationRules/write|Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/authorizationRules/read|Obter a lista de descrição de regras de autorização de namespaces.|
|/namespaces/authorizationRules/delete|Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída. |
|/namespaces/authorizationRules/listkeys/action|Obter a cadeia de conexão para o namespace|
|/namespaces/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/diagnosticSettings/read|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/diagnosticSettings/write|Obter lista de descrições de recurso de configurações de diagnóstico do namespace|
|/namespaces/queues/write|Criar ou atualizar propriedades Queue.|
|/namespaces/queues/read|Obter lista de descrições do recurso Queue|
|/namespaces/queues/Delete|Operação para excluir o recurso Queue|
|/namespaces/queues/authorizationRules/write|Criar regras de autorização de fila e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/queues/authorizationRules/read| Obter a lista de regras de autorização de fila|
|/namespaces/queues/authorizationRules/delete|Operação para excluir regras de autorização de fila|
|/namespaces/queues/authorizationRules/listkeys/action|Obter a cadeia de conexão para fila|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/logDefinitions/read|Obter lista de descrições do recurso de log do namespace|
|/namespaces/topics/write|Criar ou atualizar propriedades Topic.|
|/namespaces/topics/read|Obter lista de descrições de recurso Topic|
|/namespaces/topics/Delete|Operação para excluir o recurso Topic|
|/namespaces/topics/authorizationRules/write|Criar regras de autorização do tópico e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas.|
|/namespaces/topics/authorizationRules/read| Obter a lista de regras de autorização do tópico|
|/namespaces/topics/authorizationRules/delete|Operação para excluir regras de autorização do tópico|
|/namespaces/topics/authorizationRules/listkeys/action|Obter a cadeia de conexão para tópico|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Regenerar a chave primária ou secundária para o recurso|
|/namespaces/topics/subscriptions/write|Criar ou atualizar propriedades TopicSubscription.|
|/namespaces/topics/subscriptions/read|Obter lista de descrições do recurso TopicSubscription|
|/namespaces/topics/subscriptions/Delete|Operação para excluir o recurso TopicSubscription|
|/namespaces/topics/subscriptions/rules/write|Criar ou atualizar propriedades Rule.|
|/namespaces/topics/subscriptions/rules/read|Obter lista de descrições do recurso Rule|
|/namespaces/topics/subscriptions/rules/Delete|Operação para excluir o recurso Rule|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operação | Descrição |
|---|---|
|/servers/read|Retornar uma lista de servidores em um grupo de recursos de uma assinatura|
|/servers/write|Criar um novo servidor ou modificar propriedades de servidor existente em um grupo de recursos de uma assinatura|
|/servers/delete|Excluir um servidor e todos os bancos de dados e pools elásticos contidos|
|/servers/import/action|Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac|
|/servers/upgrade/action|Habilitar novas funcionalidades disponíveis na versão mais recente do servidor e especificar o mapa de conversão da edição do bancos de dados|
|/servers/VulnerabilityAssessmentScans/action|Executar verificação de servidor para avaliação de vulnerabilidade|
|/servers/operationResults/read|A operação é usada para controlar o andamento da atualização do servidor da versão anterior para uma posterior|
|/servers/operationResults/delete|Anular a atualização de versão do servidor em andamento|
|/servers/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor|
|/servers/securityAlertPolicies/write|Alterar a detecção de ameaças do servidor para determinado servidor|
|/servers/securityAlertPolicies/operationResults/read|Recuperar os resultados da operação Definir da política de detecção de ameaças do servidor|
|/servers/administrators/read|Recuperar detalhes do administrador do servidor|
|/servers/administrators/write|Criar ou atualizar o administrador do servidor|
|/servers/administrators/delete|Excluir do servidor o administrador do servidor|
|/servers/recoverableDatabases/read|Essa operação é usada para a recuperação de desastres do banco de dados dinâmico a fim de restaurar o banco de dados no último ponto de backup bom conhecido. Ela retorna informações sobre o último backup, mas não restaura o banco de dados efetivamente.|
|/servers/serviceObjectives/read|Recuperar a lista de objetivos de nível de serviço (também conhecido como níveis de desempenho) disponíveis em determinado servidor|
|/servers/firewallRules/read|Recuperar detalhes de regra de firewall do servidor|
|/servers/firewallRules/write|Criar ou atualizar regra de firewall de servidor que controla o intervalo de endereços IP permitido para se conectar ao servidor|
|/servers/firewallRules/delete|Excluir regra de firewall do servidor|
|/servers/administratorOperationResults/read|Recuperar resultados de operação do administrador do servidor|
|/servers/recommendedElasticPools/read|Recuperar recomendação para pools de banco de dados elástico a fim de reduzir custos ou melhorar o desempenho com base no histórico de utilização de recursos|
|/servers/recommendedElasticPools/metrics/read|Recuperar a métrica de pools de banco de dados elástico recomendados para determinado servidor|
|/servers/recommendedElasticPools/databases/read|Recuperar bancos de dados que devem ser adicionados a pools de banco de dados elásticos recomendados para determinado servidor|
|/servers/elasticPools/read|Recuperar detalhes do pool de banco de dados elástico em determinado servidor|
|/servers/elasticPools/write|Criar um novo pool de banco de dados elástico ou alterar as propriedades de um existente|
|/servers/elasticPools/delete|Excluir pool de banco de dados elástico existente|
|/servers/elasticPools/operationResults/read|Recuperar detalhes sobre determinada operação de pool de banco de dados elástico|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/elasticPools/metrics/read|Retornar métrica de utilização de recursos do pool de banco de dados elástico|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recuperar atividades e detalhes em um banco de dados que faz parte do pool de banco de dados elástico|
|/servers/elasticPools/advisors/read|Retornar a lista de consultores disponíveis para o pool elástico|
|/servers/elasticPools/advisors/write|Atualizar status de execução automática de um assistente no nível do pool elástico.|
|/servers/elasticPools/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o pool elástico|
|/servers/elasticPools/advisors/recommendedActions/write|Aplicar a ação recomendada no pool elástico|
|/servers/elasticPools/elasticPoolActivity/read|Recuperar atividades e detalhes em um pool de banco de dados elástico|
|/servers/elasticPools/databases/read|Recuperar a lista e os detalhes de bancos de dados que fazem parte do pool de banco de dados elástico em determinado servidor|
|/servers/auditingPolicies/read|Recuperar detalhes da política de auditoria de tabela do servidor padrão configurada em determinado servidor|
|/servers/auditingPolicies/write|Alterar a auditoria da tabela de servidor padrão para determinado servidor|
|/servers/disasterRecoveryConfiguration/operationResults/read|Obter resultados de operação de configuração da recuperação de desastre|
|/servers/advisors/read|Retornar a lista de consultores disponíveis para o servidor|
|/servers/advisors/write|Atualizar status de execução automática de um assistente no nível do servidor.|
|/servers/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o servidor|
|/servers/advisors/recommendedActions/write|Aplicar a ação recomendada no servidor|
|/servers/usages/read|Retornar cota de DTU de servidor e DTU de consumo atual por todos os bancos de dados no servidor|
|/servers/elasticPoolEstimates/read|Retornar a lista de estimativas de pool elástico já criado para o servidor|
|/servers/elasticPoolEstimates/write|Criar nova estimativa de pool elástico para a lista de bancos de dados fornecida|
|/servers/auditingSettings/read|Recuperar detalhes da política de auditoria de blob do servidor configurada em determinado servidor|
|/servers/auditingSettings/write|Alterar a auditoria de blob de servidor para determinado servidor|
|/servers/auditingSettings/operationResults/read|Recuperar o resultado da operação Definir da política de auditoria do blob do servidor|
|/servers/backupLongTermRetentionVaults/read|Essa operação é usada para obter um cofre de retenção de backup de longo prazo. Retornar informações sobre o cofre registrado para esse servidor.|
|/servers/backupLongTermRetentionVaults/write|Registrar um cofre de retenção de backup de longo prazo|
|/servers/restorableDroppedDatabases/read|Recuperar uma lista de bancos de dados que foram descartados em determinado servidor ainda na política de retenção. Essa operação retorna uma lista de bancos de dados e metadados associados, como a data de exclusão.|
|/servers/databases/read|Retornar uma lista de servidores em um grupo de recursos de uma assinatura|
|/servers/databases/write|Criar um novo servidor ou modificar propriedades de servidor existente em um grupo de recursos de uma assinatura|
|/servers/databases/delete|Excluir um servidor e todos os bancos de dados e pools elásticos contidos|
|/servers/databases/export/action|Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac|
|/servers/databases/VulnerabilityAssessmentScans/action|Executar verificação de banco de dados para avaliação de vulnerabilidade.|
|/servers/databases/pause/action|Pausar um banco de dados de edição do DataWarehouse|
|/servers/databases/resume/action|Retomar um banco de dados de edição do DataWarehouse|
|/servers/databases/operationResults/read|A operação é usada para controlar o andamento da operação de banco de dados de longo prazo, como escala.|
|/servers/databases/replicationLinks/read|Retornar detalhes sobre links de replicação estabelecidos para determinado banco de dados|
|/servers/databases/replicationLinks/delete|Encerrar a relação de replicação de modo forçado e com potencial perda de dados|
|/servers/databases/replicationLinks/unlink/action|Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro|
|/servers/databases/replicationLinks/failover/action|Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados o principal da relação de replicação e tornando o primário remoto um secundário|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Failover imediatamente com possibilidade de perda de dados, tornando esse banco de dados o principal da relação de replicação e tornando o primário remoto um secundário|
|/servers/databases/replicationLinks/updateReplicationMode/action|Atualizar o modo de replicação para o vincular ao modo síncrono ou assíncrono|
|/servers/databases/replicationLinks/operationResults/read|Obter status de operações de longa duração em links de replicação de banco de dados|
|/servers/databases/dataMaskingPolicies/read|Recuperar detalhes da política de mascaramento de dados configurada em determinado servidor|
|/servers/databases/dataMaskingPolicies/write|Alterar política de mascaramento de dados para determinado banco de dados|
|/servers/databases/dataMaskingPolicies/rules/read|Recuperar detalhes da regra de política de mascaramento de dados configurada em determinado servidor|
|/servers/databases/dataMaskingPolicies/rules/write|Alterar regra de política de mascaramento de dados para determinado banco de dados|
|/servers/databases/securityAlertPolicies/read|Recuperar detalhes da política de detecção de ameaças configurada em determinado servidor|
|/servers/databases/securityAlertPolicies/write|Alterar política de detecção de ameaças para determinado banco de dados|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|Retornar tipos de métricas que estão disponíveis para bancos de dados|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|Obter a configuração de diagnóstico para o recurso|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|Criar ou atualizar a configuração de diagnóstico para o recurso|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|Obter os logs disponíveis para bancos de dados|
|/servers/databases/topQueries/read|Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado|
|/servers/databases/topQueries/queryText/read|Retornar o texto Transact-SQL para a ID de consulta selecionada|
|/servers/databases/topQueries/statistics/read|Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado|
|/servers/databases/connectionPolicies/read|Recuperar detalhes da política de conexão configurada em determinado servidor|
|/servers/databases/connectionPolicies/write|Alterar política de conexão para determinado banco de dados|
|/servers/databases/metrics/read|Retornar métrica de utilização de recursos do banco de dados|
|/servers/databases/auditRecords/read|Recuperar os registros de auditoria do blob do banco de dados|
|/servers/databases/transparentDataEncryption/read|Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados|
|/servers/databases/transparentDataEncryption/write|Habilitar ou desabilitar o Transparent Data Encryption para determinado banco de dados|
|/servers/databases/transparentDataEncryption/operationResults/read|Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados|
|/servers/databases/auditingPolicies/read|Recuperar detalhes da política de auditoria de tabela configurada em determinado servidor|
|/servers/databases/auditingPolicies/write|Alterar a política de auditoria de tabela para determinado banco de dados|
|/servers/databases/dataWarehouseQueries/read|Retornar as informações de consulta de distribuição do data warehouse para a ID de consulta selecionada|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|Retornar as informações de etapa de consulta distribuída da consulta de data warehouse para ID da etapa selecionada|
|/servers/databases/serviceTierAdvisors/read|Retornar sugestão sobre escalar ou reduzir o banco de dados verticalmente com base nas estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos|
|/servers/databases/advisors/read|Retornar a lista de consultores disponíveis para o banco de dados|
|/servers/databases/advisors/write|Atualizar status de um assistente no nível do banco de dados.|
|/servers/databases/advisors/recommendedActions/read|Retornar a lista de ações recomendadas do assistente especificado para o banco de dados|
|/servers/databases/advisors/recommendedActions/write|Aplicar a ação recomendada no banco de dados|
|/servers/databases/usages/read|Retornar o tamanho máximo do banco de dados que pode ser alcançado e o tamanho atual ocupado por dados|
|/servers/databases/queryStore/read|Retornar os valores atuais das configurações de repositório de consulta do banco de dados|
|/servers/databases/queryStore/write|Atualizar a configuração do repositório de consultas do banco de dados|
|/servers/databases/auditingSettings/read|Recuperar detalhes da política de auditoria de blob configurada em determinado servidor|
|/servers/databases/auditingSettings/write|Alterar a política de auditoria de blob para determinado banco de dados|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recuperar a lista de recomendações de índice em um banco de dados|
|/servers/databases/schemas/tables/recommendedIndexes/write|Aplicar a recomendação de índice|
|/servers/databases/schemas/tables/columns/read|Recuperar a lista de colunas de uma tabela|
|/servers/databases/missingindexes/read|Retornar sugestões sobre os índices do banco de dados que devem ser criados, modificados ou excluídos a fim de melhorar o desempenho de consulta|
|/servers/databases/missingindexes/write|Usar recomendação de índice de banco de dados em determinado banco de dados|
|/servers/databases/importExportOperationResults/read|Retornar detalhes sobre a operação de importação ou exportação de banco de dados de DacPac localizado na conta de armazenamento|
|/servers/importExportOperationResults/read|Retornar a lista com detalhes de operações de importação de banco de dados da conta de armazenamento em determinado servidor|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operação | Descrição |
|---|---|
|/register/action|Registra a assinatura do provedor de recursos de armazenamento e permite a criação de contas de armazenamento.|
|/checknameavailability/read|Verificar se o nome dessa conta é válido e não está em uso.|
|/storageAccounts/write|Criar uma conta de armazenamento com os parâmetros especificados, atualizar as propriedades ou marcas ou adicionar um domínio personalizado à conta de armazenamento especificada.|
|/storageAccounts/delete|Excluir uma conta de armazenamento existente.|
|/storageAccounts/listkeys/action|Retornar as chaves de acesso da conta de armazenamento especificada.|
|/storageAccounts/regeneratekey/action|Regenerar as chaves de acesso da conta de armazenamento especificada.|
|/storageAccounts/read|Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada.|
|/storageAccounts/listAccountSas/action|Retornar o token SAS da conta para a conta de armazenamento especificada.|
|/storageAccounts/listServiceSas/action|Token SAS do serviço de armazenamento|
|/storageAccounts/services/diagnosticSettings/write|Criar/atualizar definições de diagnóstico da conta de armazenamento.|
|/skus/read|Listar os SKUs com suporte pelo Microsoft.Storage.|
|/usages/read|Retornar o limite e a contagem atual de uso de recursos na assinatura especificada|
|/operations/read|Monitorar o status de uma operação assíncrona.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notificar o Microsoft.Storage sobre a exclusão de uma rede virtual ou sub-rede|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operação | Descrição |
|---|---|
|/managers/clearAlerts/action|Limpar todos os alertas associados ao Gerenciador de Dispositivos.|
|/managers/getActivationKey/action|Obter a chave de ativação para o gerenciador de dispositivos.|
|/managers/regenerateActivationKey/action|Regenerar a chave de ativação para o gerenciador de dispositivos.|
|/managers/regenarateRegistationCertificate/action|Regenerar o certificado de registro para os gerenciadores de dispositivo.|
|/managers/getEncryptionKey/action|Obter a chave de criptografia para o gerenciador de dispositivos.|
|/managers/read|Listar ou obter os Gerenciadores de Dispositivo|
|/managers/delete|Excluir os Gerenciadores de Dispositivo|
|/managers/write|Criar ou atualizar os Gerenciadores de Dispositivo|
|/managers/configureDevice/action|Configurar um dispositivo|
|/managers/listActivationKey/action|Obter a chave de ativação do Gerenciador de Dispositivo StorSimple.|
|/managers/listPublicEncryptionKey/action|Listar chaves de criptografia públicas de um Gerenciador de Dispositivo StorSimple.|
|/managers/listPrivateEncryptionKey/action|Obter a chave de criptografia particular para um Gerenciador de Dispositivos StorSimple.|
|/managers/provisionCloudAppliance/action|Criar um novo dispositivo de nuvem.|
|/Managers/write|A operação Criar cofre cria um recurso do Azure do tipo 'cofre'|
|/Managers/read|A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/Managers/delete|A operação Excluir cofre exclui o recurso do Azure do tipo 'cofre' especificado|
|/managers/storageAccountCredentials/write|Criar ou atualizar as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/read|Lista ou obtém as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/delete|Excluir as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/listAccessKey/action|Listar chaves de acesso de credenciais da conta de armazenamento|
|/managers/accessControlRecords/read|Listar ou obter os registros de controle de acesso|
|/managers/accessControlRecords/write|Criar ou atualizar registros de controle de acesso|
|/managers/accessControlRecords/delete|Excluir os registros de controle de acesso|
|/managers/metrics/read|Listar ou obter a métrica|
|/managers/bandwidthSettings/read|Listar as configurações de largura de banda (somente 8000 Series)|
|/managers/bandwidthSettings/write|Criar novas configurações de largura de banda ou atualizá-las (somente 8000 Series)|
|/managers/bandwidthSettings/delete|Excluir uma configuração de largura de banda existente (somente 8000 Series)|
|/Managers/extendedInformation/read|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Managers/extendedInformation/write|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/Managers/extendedInformation/delete|A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault?|
|/managers/alerts/read|Listar ou obter os alertas|
|/managers/storageDomains/read|Listar ou obter os domínios de armazenamento|
|/managers/storageDomains/write|Criar ou atualizar os domínios de armazenamento|
|/managers/storageDomains/delete|Excluir os domínios de armazenamento|
|/managers/devices/scanForUpdates/action|Verificar se há atualizações em um dispositivo.|
|/managers/devices/download/action|Baixar atualizações para um dispositivo.|
|/managers/devices/install/action|Instalar atualizações em um dispositivo.|
|/managers/devices/read|Listar ou obter os dispositivos|
|/managers/devices/write|Criar ou atualizar os dispositivos|
|/managers/devices/delete|Excluir os dispositivos|
|/managers/devices/deactivate/action|Desativar um dispositivo.|
|/managers/devices/publishSupportPackage/action|Publicar o pacote de suporte de um dispositivo para solução de problemas pelo Suporte da Microsoft.|
|/managers/devices/failover/action|Failover do dispositivo.|
|/managers/devices/sendTestAlertEmail/action|Enviar email de alerta de teste para destinatários de email configurados.|
|/managers/devices/installUpdates/action|Instalar as atualizações nos dispositivos|
|/managers/devices/listFailoverSets/action|Listar os conjuntos de failover para um dispositivo existente.|
|/managers/devices/listFailoverTargets/action|Listar ou obter de destinos de failover dos dispositivos|
|/managers/devices/publicEncryptionKey/action|Listar chave de criptografia pública do Gerenciador de Dispositivos|
|/managers/devices/hardwareComponentGroups/<br>leitura|Listar os grupos de componentes de hardware|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|Alterar o estado de energia do controlador dos grupos de componentes de hardware|
|/managers/devices/metrics/read|Listar ou obter a métrica|
|/managers/devices/chapSettings/write|Criar ou atualizar as configurações de Chap|
|/managers/devices/chapSettings/read|Listar ou obter as configurações de Chap|
|/managers/devices/chapSettings/delete|Excluir as configurações de Chap|
|/managers/devices/backupScheduleGroups/read|Listar ou obter os grupos de agendamento de backup|
|/managers/devices/backupScheduleGroups/write|Criar ou atualizar os grupos de agendamento de backup|
|/managers/devices/backupScheduleGroups/delete|Excluir os grupos de agendamento de backup|
|/managers/devices/updateSummary/read|Listar ou obter o resumo de atualização|
|/managers/devices/migrationSourceConfigurations/<br>import/action|Importar configurações de origem para migração|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|Iniciar um trabalho para estimar a duração do processo de migração.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|Iniciar migração usando as configurações de origem|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|Confirmar uma migração bem-sucedida.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|Buscar o status do trabalho de estimativa de migração.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|Buscar o status para a migração.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|Buscar o status de confirmação da migração.|
|/managers/devices/alertSettings/read|Listar ou obter as configurações de alerta|
|/managers/devices/alertSettings/write|Criar ou atualizar as configurações de alerta|
|/managers/devices/networkSettings/read|Listar ou obter as configurações de rede|
|/managers/devices/networkSettings/write|Criar novas ou atualizar as configurações de rede|
|/managers/devices/jobs/read|Listar ou obter os trabalhos|
|/managers/devices/jobs/cancel/action|Cancelar um trabalho em execução|
|/managers/devices/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/volumeContainers/write|Criar novos ou atualizar os contêineres de volume (somente 8000 Series)|
|/managers/devices/volumeContainers/read|Listar os contêineres de volume (somente 8000 Series)|
|/managers/devices/volumeContainers/delete|Excluir contêineres de Volume existentes (somente 8000 Series)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Listar chaves de criptografia da lista de contêineres de volume|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Chaves de criptografia de substituição de contêineres de volume|
|/managers/devices/volumeContainers/metrics/read|Listar a métrica|
|/managers/devices/volumeContainers/volumes/read|Listar os volumes|
|/managers/devices/volumeContainers/volumes/write|Criar ou atualizar volumes|
|/managers/devices/volumeContainers/volumes/delete|Excluir um volume existente|
|/managers/devices/volumeContainers/volumes/metrics/read|Listar a métrica|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Lista as definições de métrica|
|/managers/devices/volumeContainers/metricsDefinitions/read|Lista as definições de métrica|
|/managers/devices/iscsiservers/read|Listar ou obter os servidores iSCSI|
|/managers/devices/iscsiservers/write|Criar ou atualizar servidores iSCSI|
|/managers/devices/iscsiservers/delete|Excluir servidores iSCSI|
|/managers/devices/iscsiservers/backup/action|Fazer backup de um servidor iSCSI.|
|/managers/devices/iscsiservers/metrics/read|Listar ou obter a métrica|
|/managers/devices/iscsiservers/disks/read|Listar ou obter os discos|
|/managers/devices/iscsiservers/disks/write|Criar ou atualizar os discos|
|/managers/devices/iscsiservers/disks/delete|Excluir os discos|
|/managers/devices/iscsiservers/disks/metrics/read|Listar ou obter a métrica|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/iscsiservers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/backups/read|Listar ou obter o conjunto de backup|
|/managers/devices/backups/delete|Excluir o conjunto de backup|
|/managers/devices/backups/restore/action|Restaurar todos os volumes de um conjunto de backup.|
|/managers/devices/backups/elements/clone/action|Clonar um compartilhamento ou volume usando um elemento de backup.|
|/managers/devices/backupPolicies/write|Criar novas ou atualizar as políticas de Backup (somente 8000 Series)|
|/managers/devices/backupPolicies/read|Listar as políticas de backup (somente 8000 Series)|
|/managers/devices/backupPolicies/delete|Excluir políticas de backup existentes (somente 8000 Series)|
|/managers/devices/backupPolicies/backup/action|Fazer um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política.|
|/managers/devices/backupPolicies/schedules/write|Criar ou atualizar agendas|
|/managers/devices/backupPolicies/schedules/read|Listar as agendas|
|/managers/devices/backupPolicies/schedules/delete|Excluir agendas existentes|
|/managers/devices/securitySettings/update/action|Atualizar as configurações de segurança.|
|/managers/devices/securitySettings/read|Listar as configurações de segurança|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|Sincronizar o certificado de gerenciamento remoto de um dispositivo.|
|/managers/devices/securitySettings/write|Criar ou atualizar as configurações de segurança|
|/managers/devices/fileservers/read|Listar ou obter os servidores de arquivos|
|/managers/devices/fileservers/write|Criar ou atualizar os servidores de arquivos|
|/managers/devices/fileservers/delete|Excluir servidores de arquivos|
|/managers/devices/fileservers/backup/action|Fazer backup de um servidor de arquivos.|
|/managers/devices/fileservers/metrics/read|Listar ou obter a métrica|
|/managers/devices/fileservers/shares/write|Criar ou atualizar os compartilhamentos|
|/managers/devices/fileservers/shares/read|Listar ou obter os compartilhamentos|
|/managers/devices/fileservers/shares/delete|Excluir os compartilhamentos|
|/managers/devices/fileservers/shares/metrics/read|Listar ou obter a métrica|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/fileservers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/devices/timeSettings/read|Listar ou obter as configurações de tempo|
|/managers/devices/timeSettings/write|Criar ou atualizar as configurações de tempo|
|/Managers/certificates/write|A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso.|
|/managers/cloudApplianceConfigurations/read|Listar configurações com suporte pelo dispositivo de nuvem|
|/managers/metricsDefinitions/read|Listar ou obter as definições de métrica|
|/managers/encryptionSettings/read|Listar ou obter as configurações de criptografia|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operação | Descrição |
|---|---|
|/streamingjobs/Start/action|Iniciar trabalho do Stream Analytics|
|/streamingjobs/Stop/action|Interromper trabalho do Stream Analytics|
|/streamingjobs/Read|Ler trabalho do Stream Analytics|
|/streamingjobs/Write|Gravar trabalho de Stream Analytics|
|/streamingjobs/Delete|Excluir trabalho de Stream Analytics|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Obter a métrica disponível para streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Ler configuração de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Gravar configuração de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Obter os logs disponíveis para streamingjobs|
|/streamingjobs/transformations/Read|Ler transformação do trabalho do Stream Analytics|
|/streamingjobs/transformations/Write|Gravar transformação do trabalho do Stream Analytics|
|/streamingjobs/transformations/Delete|Excluir transformação do trabalho do Stream Analytics|
|/streamingjobs/inputs/Read|Ler entrada do trabalho do Stream Analytics|
|/streamingjobs/inputs/Write|Gravar entrada de trabalho do Stream Analytics|
|/streamingjobs/inputs/Delete|Excluir entrada de trabalho do Stream Analytics|
|/streamingjobs/outputs/Read|Ler saída do trabalho do Stream Analytics|
|/streamingjobs/outputs/Write|Gravar saída de trabalho do Stream Analytics|
|/streamingjobs/outputs/Delete|Excluir saída de trabalho do Stream Analytics|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operação | Descrição |
|---|---|
|/register/action|Registrar para dar suporte ao provedor de recursos|
|/supportTickets/read|Obter detalhes de tíquete de suporte (incluindo status, severidade, detalhes de contato e comunicações) ou obtém a lista de tíquetes de suporte entre assinaturas.|
|/supportTickets/write|Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes.|

## <a name="microsoftweb"></a>Microsoft.Web

| Operação | Descrição |
|---|---|
|/unregister/action|Cancelar o registro do provedor de recursos Microsoft.Web para a assinatura.|
|/validate/action|Validar.|
|/register/action|Registrar o provedor de recursos Microsoft.Web para a assinatura.|
|/hostingEnvironments/Read|Obter as propriedades de um Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/Write|Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/hostingEnvironments/Delete|Excluir ambiente do Serviço de Aplicativo|
|/hostingEnvironments/reboot/Action|Reinicializar todas as máquinas em um Ambiente do Serviço de Aplicativo|
|/hostingenvironments/resume/action|Retomar ambientes de hospedagem.|
|/hostingenvironments/suspend/action|Suspender ambientes de hospedagem.|
|/hostingenvironments/metricdefinitions/read|Obter definições de métrica de ambientes de hospedagem.|
|/hostingEnvironments/workerPools/Read|Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/workerPools/Write|Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/hostingenvironments/workerpools/metricdefinitions/read|Obter definições de métrica de pools de trabalho dos ambientes de hospedagem.|
|/hostingenvironments/workerpools/metrics/read|Obter a métrica de pools de trabalho de Ambientes de Hospedagem.|
|/hostingenvironments/workerpools/skus/read|Obter SKUs de pools de trabalho dos ambientes de hospedagem.|
|/hostingenvironments/workerpools/usages/read|Obter uso dos pools de trabalho dos ambientes de hospedagem.|
|/hostingenvironments/sites/read|Obter aplicativos Web dos ambientes de hospedagem.|
|/hostingenvironments/serverfarms/read|Obter Planos do Serviço de Aplicativo dos Ambientes de Hospedagem.|
|/hostingenvironments/usages/read|Obter usos dos ambientes de hospedagem.|
|/hostingenvironments/capacities/read|Obter as capacidades dos ambientes de hospedagem.|
|/hostingenvironments/operations/read|Obter operações dos ambientes de hospedagem.|
|/hostingEnvironments/multiRolePools/Read|Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo|
|/hostingEnvironments/multiRolePools/Write|Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente|
|/hostingenvironments/multirolepools/metricdefinitions/read|Obter definições de métrica de pools multifunção dos ambientes de hospedagem.|
|/hostingenvironments/multirolepools/metrics/read|Obter a métrica de pools multifunção em Ambientes de Hospedagem.|
|/hostingenvironments/multirolepools/skus/read|Obter SKUs de pools multifunção dos ambientes de hospedagem.|
|/hostingenvironments/multirolepools/usages/read|Obter usos dos pools multifunção dos ambientes de hospedagem.|
|/hostingenvironments/diagnostics/read|Obter diagnóstico dos ambientes de hospedagem.|
|/publishingusers/read|Obter usuários de publicação.|
|/publishingusers/write|Atualizar usuários de publicação.|
|/checknameavailability/read|Verificar se o nome do recurso está disponível.|
|/geoRegions/Read|Obter a lista de regiões geográficas.|
|/sites/Read|Obter as propriedades de um aplicativo Web|
|/sites/Write|Criar um novo aplicativo Web ou atualizar um existente|
|/sites/Delete|Excluir um aplicativo Web existente|
|/sites/backup/Action|Criar um novo backup do aplicativo Web|
|/sites/publishxml/Action|Obter XML do perfil de publicação para um aplicativo Web|
|/sites/publish/Action|Publicar um aplicativo Web|
|/sites/restart/Action|Reiniciar um aplicativo Web|
|/sites/start/Action|Iniciar um aplicativo Web|
|/sites/stop/Action|Interromper um aplicativo Web|
|/sites/slotsswap/Action|Trocar slots de implantação de aplicativo Web|
|/sites/slotsdiffs/Action|Obter as diferenças de configuração entre aplicativo web e slots|
|/sites/applySlotConfig/Action|Aplicar a configuração de slot de aplicativo Web do slot de destino ao aplicativo Web atual|
|/sites/resetSlotConfig/Action|Redefinir a configuração de aplicativo Web|
|/sites/functions/action|Aplicativos Web do Functions.|
|/sites/listsyncfunctiontriggerstatus/action|Listar Aplicativos Web de status do gatilho da função de sincronização.|
|/sites/networktrace/action|Aplicativos Web de rastreamento de rede.|
|/sites/newpassword/action|Aplicativos Web Newpassword.|
|/sites/sync/action|Sincronizar pplicativos Web.|
|/sites/operationresults/read|Obter resultados de operação de aplicativos Web.|
|/sites/webjobs/read|Obter trabalhos da Web de aplicativos Web.|
|/sites/backup/read|Obter o backup de aplicativos Web.|
|/sites/backup/write|Atualizar o backup de aplicativos Web.|
|/sites/metricdefinitions/read|Obter definições de métrica de aplicativos Web.|
|/sites/metrics/read|Métrica de aplicativos Web.|
|/sites/continuouswebjobs/delete|Excluir trabalhos da Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/read|Obter trabalhos de Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/start/action|Iniciar trabalhos de Web contínuos de aplicativos Web.|
|/sites/continuouswebjobs/stop/action|Interromper trabalhos de Web contínuos de aplicativos Web.|
|/sites/domainownershipidentifiers/read|Obter identificadores de propriedade de domínio de aplicativos Web.|
|/sites/domainownershipidentifiers/write|Atualizar de identificadores de propriedade de domínio de aplicativos Web.|
|/sites/premieraddons/delete|Excluir complementos Premier de aplicativos Web.|
|/sites/premieraddons/read|Obter complementos Premier de aplicativos Web.|
|/sites/premieraddons/write|Atualizar complementos Premier de aplicativos Web.|
|/sites/triggeredwebjobs/delete|Excluir trabalhos da Web disparados para aplicativos Web.|
|/sites/triggeredwebjobs/read|Obter trabalhos da Web disparados de aplicativos Web.|
|/sites/triggeredwebjobs/run/action|Executar trabalhos da Web disparados de aplicativos Web.|
|/sites/hostnamebindings/delete|Excluir associações de nome de host de aplicativos Web.|
|/sites/hostnamebindings/read|Obter associações de nome de host de aplicativos Web.|
|/sites/hostnamebindings/write|Atualizar associações de nome de host de aplicativos Web.|
|/sites/virtualnetworkconnections/delete|Excluir as conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/read|Obter as conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/write|Atualizar as conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/gateways/read|Obter gateways de conexões de rede virtual de aplicativos Web.|
|/sites/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual de aplicativos Web.|
|/sites/publishxml/read|Obter XML de publicação de aplicativos Web.|
|/sites/hybridconnectionrelays/read|Obter transmissões de conexão híbrida de aplicativos Web.|
|/sites/perfcounters/read|Obter contadores de desempenho de aplicativos Web.|
|/sites/usages/read|Obter usos de aplicativos Web.|
|/sites/slots/Write|Criar um novo slot do aplicativo Web ou atualizar um existente|
|/sites/slots/Delete|Excluir um slot de aplicativo Web existente|
|/sites/slots/backup/Action|Criar o novo backup do slot do aplicativo Web.|
|/sites/slots/publishxml/Action|Obter XML do perfil de publicação para um slot de aplicativo Web|
|/sites/slots/publish/Action|Publicar um slot do aplicativo Web|
|/sites/slots/restart/Action|Reiniciar um slot de aplicativo Web|
|/sites/slots/start/Action|Iniciar um slot de aplicativo Web|
|/sites/slots/stop/Action|Interromper um slot de aplicativo Web|
|/sites/slots/slotsswap/Action|Trocar slots de implantação de aplicativo Web|
|/sites/slots/slotsdiffs/Action|Obter as diferenças de configuração entre aplicativo web e slots|
|/sites/slots/applySlotConfig/Action|Aplicar a configuração de slot de aplicativo Web do slot de destino ao slot atual.|
|/sites/slots/resetSlotConfig/Action|Redefinir a configuração de slot do aplicativo Web|
|/sites/slots/Read|Obter as propriedades de um slot de implantação de aplicativo Web|
|/sites/slots/newpassword/action|Slots de aplicativos Web Newpassword.|
|/sites/slots/sync/action|Sincronizar slots de aplicativos Web.|
|/sites/slots/operationresults/read|Obter resultados de operação de slots de aplicativos Web.|
|/sites/slots/webjobs/read|Obter trabalhos da Web dos slots de aplicativos Web.|
|/sites/slots/backup/write|Atualizar o backup de slots de aplicativos Web.|
|/sites/slots/metricdefinitions/read|Obter definições de métrica de slots de aplicativos Web.|
|/sites/slots/metrics/read|Obter métrica dos slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/delete|Excluir trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/read|Obter trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/start/action|Iniciar trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/continuouswebjobs/stop/action|Interromper trabalhos da Web contínuos de slots de aplicativos Web.|
|/sites/slots/premieraddons/delete|Excluir complementos de Premier de slots de aplicativos Web.|
|/sites/slots/premieraddons/read|Obter complementos Premier de slots de aplicativos Web.|
|/sites/slots/premieraddons/write|Atualizar complementos Premier de slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/delete|Excluir trabalhos da Web disparados de slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/read|Obter trabalhos da Web disparados de slots de aplicativos Web.|
|/sites/slots/triggeredwebjobs/run/action|Executar trabalhos da Web dos slots de aplicativos Web.|
|/sites/slots/hostnamebindings/delete|Excluir vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/hostnamebindings/read|Obter vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/hostnamebindings/write|Atualizar vínculos de nome de host de slots de aplicativos Web.|
|/sites/slots/phplogging/read|Obter Phplogging dos slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/delete|Excluir conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/read|Obter conexões de rede virtual dos slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/write|Atualizar conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual de slots de aplicativos Web.|
|/sites/slots/usages/read|Obter usos dos slots de aplicativos Web.|
|/sites/slots/hybridconnection/delete|Excluir a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/hybridconnection/read|Obter a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/hybridconnection/write|Atualizar a conexão híbrida de slots de aplicativos Web.|
|/sites/slots/config/Read|Obter definições de configuração do slot do aplicativo Web|
|/sites/slots/config/list/Action|Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão|
|/sites/slots/config/Write|Atualizar definições de configuração do slot do aplicativo Web|
|/sites/slots/config/delete|Excluir configuração de slots de aplicativos Web.|
|/sites/slots/instances/read|Obter instâncias dos slots de aplicativos Web.|
|/sites/slots/instances/processes/read|Obter os processos de instâncias dos slots de aplicativos Web.|
|/sites/slots/instances/deployments/read|Obter as implantações de instâncias dos slots de aplicativos Web.|
|/sites/slots/sourcecontrols/Read|Obter definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/sourcecontrols/Write|Atualizar definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/sourcecontrols/Delete|Excluir definições de configuração de controle de origem do slot do aplicativo Web|
|/sites/slots/restore/read|Obter a restauração dos slots de aplicativos Web.|
|/sites/slots/analyzecustomhostname/read|Obter slots de aplicativos Web Analisar nome de host personalizado.|
|/sites/slots/backups/Read|Obter as propriedades do backup dos slots de um aplicativo Web|
|/sites/slots/backups/list/action|Listar backups de slots de aplicativos de Web.|
|/sites/slots/backups/restore/action|Restaurar backups de slots de aplicativos Web.|
|/sites/slots/deployments/delete|Excluir as implantações de slots de aplicativos Web.|
|/sites/slots/deployments/read|Obter as implantações dos slots de aplicativos Web.|
|/sites/slots/deployments/write|Atualizar as implantações de slots de aplicativos Web.|
|/sites/slots/deployments/log/read|Obter o log de implantações dos slots de aplicativos Web.|
|/sites/hybridconnection/delete|Excluir a conexão híbrida de aplicativos Web.|
|/sites/hybridconnection/read|Obter a conexão híbrida de aplicativos Web.|
|/sites/hybridconnection/write|Atualizar a conexão híbrida de aplicativos Web.|
|/sites/recommendationhistory/read|Obter o histórico de recomendação de aplicativos Web.|
|/sites/recommendations/Read|Obter a lista de recomendações para o aplicativo Web.|
|/sites/recommendations/disable/action|Desabilitar as recomendações de aplicativos Web.|
|/sites/config/Read|Obter definições de configuração do aplicativo Web|
|/sites/config/list/Action|Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão|
|/sites/config/Write|Atualizar definições de configuração do aplicativo Web|
|/sites/config/delete|Excluir configuração de aplicativos Web.|
|/sites/instances/read|Obter instâncias de aplicativos Web.|
|/sites/instances/processes/delete|Excluir processos de instâncias de aplicativos Web.|
|/sites/instances/processes/read|Obter os processos de instâncias de aplicativos Web.|
|/sites/instances/deployments/read|Obter as implantações de instâncias de aplicativos Web.|
|/sites/sourcecontrols/Read|Obter configurações de controle de origem do aplicativo Web|
|/sites/sourcecontrols/Write|Atualizar definições de configuração de controle de origem do aplicativo Web|
|/sites/sourcecontrols/Delete|Excluir definições de configuração de controle de origem do aplicativo Web|
|/sites/restore/read|Obter a restauração de aplicativos Web.|
|/sites/analyzecustomhostname/read|Analisar nome de host personalizado.|
|/sites/backups/Read|Obter as propriedades do backup de um aplicativo Web|
|/sites/backups/list/action|Listar backups de aplicativos de Web.|
|/sites/backups/restore/action|Restaurar backups de aplicativos Web.|
|/sites/snapshots/read|Obter instantâneos de aplicativos Web.|
|/sites/functions/delete|Excluir funções de aplicativos Web.|
|/sites/functions/listsecrets/action|Listar segredos de funções de aplicativos Web.|
|/sites/functions/read|Obter funções de aplicativos Web.|
|/sites/functions/write|Atualizar funções de aplicativos Web.|
|/sites/deployments/delete|Excluir as implantações de aplicativos Web.|
|/sites/deployments/read|Obter as implantações de aplicativos Web.|
|/sites/deployments/write|Atualizar as implantações de aplicativos Web.|
|/sites/deployments/log/read|Obter o log de implantações de aplicativos Web.|
|/sites/diagnostics/read|Obter o diagnóstico de aplicativos Web.|
|/sites/diagnostics/workerprocessrecycle/read|Obter a reciclagem do processo de trabalho do diagnóstico de aplicativos Web.|
|/sites/diagnostics/workeravailability/read|Obter Workeravailability de diagnóstico de aplicativos Web.|
|/sites/diagnostics/runtimeavailability/read|Obter a disponibilidade de tempo de execução do diagnóstico de aplicativos Web.|
|/sites/diagnostics/cpuanalysis/read|Obter Cpuanalysis de diagnóstico de aplicativos Web.|
|/sites/diagnostics/servicehealth/read|Obter integridade do serviço de diagnóstico de aplicativos Web.|
|/sites/diagnostics/frebanalysis/read|Obter a análise FREB do diagnóstico de aplicativos Web.|
|/availablestacks/read|Obter pilhas disponíveis.|
|/isusernameavailable/read|Verificar se o nome de usuário está disponível.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|Obter a lista de APIs.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|Adicionar uma nova API ou atualizar uma existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|Excluir uma API existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|Obter a lista de conexões.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|Salvar uma nova conexão ou atualizar uma existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|Remover uma conexão existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|Ler ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|Adicionar ou atualizar ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|Excluir ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|Ler ConnectionAcls para a API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|Ler ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|Criar ou atualizar as ACLS de API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|Excluir ACLS de API|
|/serverfarms/Read|Obter as propriedades em um Plano do Serviço de Aplicativo|
|/serverfarms/Write|Criar um novo Plano do Serviço de Aplicativo ou atualizar um plano existente|
|/serverfarms/Delete|Excluir um Plano do Serviço de Aplicativo existente|
|/serverfarms/restartSites/Action|Reiniciar todos os aplicativos Web em um Plano do Serviço de Aplicativo|
|/serverfarms/operationresults/read|Obter resultados de operação dos Planos do Serviço de Aplicativo.|
|/serverfarms/capabilities/read|Obter recursos do Planos do Serviço de Aplicativo.|
|/serverfarms/metricdefinitions/read|Obter definições de métrica dos Planos do Serviço de Aplicativo.|
|/serverfarms/metrics/read|Obter métrica dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionplanlimits/read|Obter limites do plano de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/read|Obter as conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/delete|Excluir rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/read|Obter a rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/routes/write|Atualizar rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/virtualnetworkconnections/gateways/write|Atualizar gateways de conexões de rede virtual dos Planos do Serviço de Aplicativo.|
|/serverfarms/firstpartyapps/settings/delete|Excluir configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/firstpartyapps/settings/read|Obter configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/firstpartyapps/settings/write|Atualizar configurações de aplicativos próprios dos Planos do Serviço de Aplicativo.|
|/serverfarms/sites/read|Obter aplicativos Web dos Planos do Serviço de Aplicativo.|
|/serverfarms/workers/reboot/action|Reinicializar trabalhos dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionrelays/read|Obter transmissões de conexão híbrida dos Planos do Serviço de Aplicativo.|
|/serverfarms/skus/read|Obter as SKUs dos Planos do Serviço de Aplicativo.|
|/serverfarms/usages/read|Obter usos dos Planos do Serviço de Aplicativo.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Obter aplicativos Web das retransmissões dos namespaces da conexão híbrida dos Planos do Serviço de Aplicativo.|
|/ishostnameavailable/read|Verificar se o nome do host está disponível.|
|/connectionGateways/Read|Obter a lista de gateways de conexão.|
|/connectionGateways/Write|Criar ou atualizar um Gateway de Conexão.|
|/connectionGateways/Delete|Excluir um Gateway de Conexão.|
|/connectionGateways/Join/Action|Adicionar um Gateway de Conexão.|
|/classicmobileservices/read|Obter Serviços Móveis clássicos.|
|/skus/read|Obter SKUs.|
|/certificates/Read|Obter a lista de certificados.|
|/certificates/Write|Adicionar um novo certificado ou atualizar um existente.|
|/certificates/Delete|Excluir um certificado existente.|
|/operations/read|Obter Operações.|
|/recommendations/Read|Obter a lista de recomendações para assinaturas.|
|/ishostingenvironmentnameavailable/read|Obter confirmação se o nome do ambiente de hospedagem está disponível.|
|/apiManagementAccounts/Read|Obter a lista de ApiManagementAccounts.|
|/apiManagementAccounts/Write|Adicionar uma nova ApiManagementAccount ou atualizar uma existente|
|/apiManagementAccounts/Delete|Excluir uma APIManagementAccount existente|
|/apiManagementAccounts/connectionAcls/Read|Obter a lista de ACLS de Conexão.|
|/apiManagementAccounts/apiAcls/Read|Ler ConnectionAcls|
|/connections/Read|Obter a lista de conexões.|
|/connections/Write|Criar ou atualizar uma conexão.|
|/connections/Delete|Excluir uma conexão.|
|/connections/Join/Action|Ingressar em uma conexão.|
|/connections/confirmconsentcode/action|Confirmar o código de autorização de conexões.|
|/connections/listconsentlinks/action|Listar os links de autorização para conexões.|
|/deploymentlocations/read|Obter locais de implantação.|
|/sourcecontrols/read|Obter os controles de origem.|
|/sourcecontrols/write|Atualizar controles de origem.|
|/managedhostingenvironments/read|Obter ambientes de hospedagem gerenciados.|
|/managedhostingenvironments/sites/read|Obter aplicativos Web de ambientes de hospedagem gerenciados.|
|/managedhostingenvironments/serverfarms/read|Obter Planos do Serviço de Aplicativo de ambientes de hospedagem gerenciados.|
|/locations/managedapis/read|Obter as APIs gerenciadas dos locais.|
|/locations/apioperations/read|Obter operações API dos locais.|
|/locations/connectiongatewayinstallations/read|Obter as instalações de gateway de conexão locais.|
|/listSitesAssignedToHostName/Read|Obter nomes dos sites atribuídos ao nome de host.|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar uma função personalizada](role-based-access-control-custom-roles.md).

- Examine as [funções RBAC incorporadas](role-based-access-built-in-roles.md).

- Saiba como gerenciar atribuições de acesso [por usuário](role-based-access-control-manage-assignments.md) ou [por recurso](role-based-access-control-configure.md) 
