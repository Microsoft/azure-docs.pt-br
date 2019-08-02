---
title: Operações do provedor de recursos do Azure Resource Manager | Microsoft Docs
description: Lista as operações disponíveis nos provedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ce4765a512b81d13f735a05ad4fba5408284a607
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501416"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos do Azure Resource Manager

Este artigo lista as operações disponíveis para cada provedor de recursos do Azure Resource Manager. Essas operações podem ser usadas em [funções personalizadas](custom-roles.md) para fornecer [RBAC (Controle de acesso baseado em função)](overview.md) granular para recursos no Azure. As cadeias de caracteres de operação têm o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`

As operações do provedor de recursos estão sempre em evolução. Para obter as operações mais recentes, use [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.AAD/domainServices/delete | Excluir Serviço de Domínio |
> | Action | Microsoft.AAD/domainServices/oucontainer/delete | Excluir contêiner de unidade organizacional |
> | Action | Microsoft.AAD/domainServices/oucontainer/read | Ler contêineres de unidade organizacional |
> | Action | Microsoft.AAD/domainServices/oucontainer/write | Gravar contêiner de unidade organizacional |
> | Action | Microsoft.AAD/domainServices/read | Ler serviços de domínio |
> | Action | Microsoft.AAD/domainServices/replicaSets/delete | Excluir site de cluster |
> | Action | Microsoft.AAD/domainServices/replicaSets/read | Ler site do cluster |
> | Action | Microsoft.AAD/domainServices/replicaSets/write | Gravar site de cluster |
> | Action | Microsoft.AAD/domainServices/write | Gravar Serviço de Domínio |
> | Action | Microsoft.AAD/locations/operationresults/read |  |
> | Action | Microsoft.AAD/Operations/read |  |
> | Action | Microsoft.AAD/register/action | Registrar Serviço de Domínio |
> | Action | Microsoft.AAD/unregister/action | Cancelar registrar Serviço de Domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Action | microsoft.aadiam/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Action | microsoft.aadiam/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Action | microsoft.aadiam/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | Obtém as operações de RP compatíveis. |
> | Action | Microsoft.Addons/register/action | Registra a assinatura especificada com Microsoft.Addons |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista informações do plano de suporte atual para a assinatura especificada. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte da Canonical especificado |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obtém o estado do plano de suporte da Canonical especificado. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte da Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | Cria uma nova floresta para o locatário. |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome do serviço especificado. |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém detalhes de alertas da floresta como alertid, data da geração do alerta, último alerta detectado, descrição do alerta, última atualização, nível do alerta, estado do alerta, links de solução de problemas do alerta etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a Configuração de Serviço da floresta. Exemplo - Nome da floresta, nível funcional, função FSMO principal de nomeação de domínio, função FSMO principal do esquema etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | Exclui um serviço e seu servidores junto com os dados de integridade. |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de sites e domínios da floresta. Exemplo: status da integridade, alertas ativos, alertas resolvidos, propriedades como Nível Funcional do Domínio, Floresta, Mestre de Infraestrutura, Controlador de Domínio Primário, Mestre RID etc.  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a configuração de preferência do usuário da floresta.<br>Exemplo: MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configurações para Gráficos da Interface do Usuário etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém o resumo da floresta especificada, como o nome da floresta, o número de domínios sob essa floresta, o número de sites e detalhes do site etc. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, esta API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o AD FS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Essa API obtém a lista de todos os ADDomainServices incorporados para um locatário premium. |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | Obtém os detalhes do serviço para o nome do serviço especificado. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém detalhes de replicação de todos os servidores para o nome do serviço especificado. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e seus erros de replicação, se houver. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém a lista completa de controladores de domínio juntamente com detalhes de replicação da floresta especificada. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adiciona uma instância de servidor ao serviço. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registro do servidor de ADDomainService, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Exclui um servidor para um determinado serviço e locatário. |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o locatário. |
> | Action | Microsoft.ADHybridHealthService/configuration/action | Atualizar a configuração do locatário. |
> | Action | Microsoft.ADHybridHealthService/configuration/read | Ler a configuração de locatário. |
> | Action | Microsoft.ADHybridHealthService/configuration/write | Criar uma configuração de locatário. |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo da instalação do agente e os logs de registro armazenados no blob. |
> | Action | Microsoft.ADHybridHealthService/logs/read | Obtém os logs de registro e instalação do agente para o locatário. |
> | Action | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações compatíveis com o sistema. |
> | Action | Microsoft.ADHybridHealthService/register/action | Registra o Provedor de Recursos do Serviço de Integridade ADHybrid e habilita a criação do recurso de Serviço de Integridade ADHybrid. |
> | Action | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, usada pelo DevOps para dar suporte a incidentes de cliente. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas com senhas incorretas de todos os usuários no Serviço de Federação do Active Directory. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS do Blob que contém o status e o resultado eventual do trabalho de relatório recém-enfileirado para a frequência de Tentativas de Nome de Usuário/Senha incorretas por UserId por IPAddress por dia para um determinado locatário. |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de locatários permitidos do DevOps. Normalmente usado para suporte ao cliente. |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor indicando se o inquilino é DevOps consentido ou não. |
> | Action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Atualiza o Userid(objectid) para o locatário do DevOps selecionado. |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implantação selecionada para o locatário determinado. |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Dada uma ID de locatário, obtém o local de armazenamento do locatário. |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém a localização geográfica da qual os dados serão acessados. |
> | Action | Microsoft.ADHybridHealthService/services/action | Atualizar uma instância de serviço no locatário. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Ler os alertas de um serviço. |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dado um nome de recurso, verifica se um serviço tem tudo o que é necessário para usar tal recurso. |
> | Action | Microsoft.ADHybridHealthService/services/delete | Excluir uma instância de serviço no locatário. |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um determinado serviço de sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém os status de exportação para um determinado serviço. |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém os comentários de alertas para um determinado serviço e servidor. |
> | Action | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas compatíveis para um determinado serviço.<br>Por exemplo, Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o serviço ADFS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para ADDomainService.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o serviço ADSync. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Dado um serviço, essa API obtém a média das métricas para um determinado serviço.<br>Por exemplo, esta API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o AD FS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, esta API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o AD FS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Dado um serviço, essa API obtém a exibição agregada da métrica para um determinado serviço.<br>Por exemplo, esta API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o AD FS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adiciona ou atualiza a configuração de monitoramento de um serviço. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitoramento de um determinado serviço. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adiciona ou atualiza as configurações de monitoramento de um serviço. |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | Essa API obtém a lista de todos os serviços incorporados para um locatário premium. |
> | Action | Microsoft.ADHybridHealthService/services/read | Ler as instâncias de serviço no locatário. |
> | Action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Obtém todos os URIs de relatório IP arriscados nos últimos 7 dias. |
> | Action | Microsoft.ADHybridHealthService/services/reports/details/read | Obtém o relatório dos primeiros 50 usuários com erros de senhas incorretas dos últimos sete dias |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Gera um relatório IP arriscado e retorna um URI apontando para ele. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas de um servidor. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registro do servidor, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, essa API obtém uma lista dos tipos de dados que estão sendo carregados pelos servidores e a hora mais recente de cada upload. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | Exclui uma instância de servidor no serviço. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do Erro de Exportação de Sincronização para um determinado Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Dado um serviço, essa API obtém as informações de métricas.<br>Por exemplo, esta API pode ser usada para obter informações relacionadas a: Bloqueios de Conta de Extranet, Total de Solicitações com Falha, Solicitações de Token Pendentes (Proxy), Solicitações de Token/s etc. para o AD FS.<br>Autenticações NTLM/s, Associações LDAP com Êxito/s, Tempo de Associação LDAP, Threads LDAP Ativos, Autenticações Kerberos/s, Total de Threads ATQ etc. para o Serviço ADDomain.<br>Executar Latência do Perfil, Conexões TCP Estabelecidas, Bytes Particulares do Agente do Insights, Exportar Estatísticas para o Azure AD para o Serviço de Sincronização. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Obtém a lista de conectores e executa nomes de perfil para o serviço e o membro de serviço fornecidos. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço de um determinado locatário. |
> | Action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o status de lista de permissões de recurso de um determinado locatário. |
> | Action | Microsoft.ADHybridHealthService/services/write | Criar uma instância de serviço no locatário. |
> | Action | Microsoft.ADHybridHealthService/unregister/action | Cancela o registro da assinatura do Provedor de Recursos do Serviço de Integridade ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | Obter configurações |
> | Action | Microsoft.Advisor/configurations/write | Criar/atualizar configuração |
> | Action | Microsoft.Advisor/generateRecommendations/action | Gera recomendações |
> | Action | Microsoft.Advisor/generateRecommendations/read | Obter o status de gerar recomendações |
> | Action | Microsoft.Advisor/metadata/read | Obter Metadados |
> | Action | Microsoft.Advisor/operations/read | Obter as operações para Assistente do Azure |
> | Action | Microsoft.Advisor/recommendations/available/action | Nova recomendação está disponível no Microsoft advisor |
> | Action | Microsoft.Advisor/recommendations/read | Ler recomendações |
> | Action | Microsoft.Advisor/recommendations/suppressions/delete | Excluir supressão |
> | Action | Microsoft.Advisor/recommendations/suppressions/read | Obter supressões |
> | Action | Microsoft.Advisor/recommendations/suppressions/write | Criar/atualizar supressões |
> | Action | Microsoft.Advisor/register/action | Registra a assinatura do Microsoft Advisor |
> | Action | Microsoft.Advisor/suppressions/delete | Excluir supressão |
> | Action | Microsoft.Advisor/suppressions/read | Obter supressões |
> | Action | Microsoft.Advisor/suppressions/write | Criar/atualizar supressões |
> | Action | Microsoft.Advisor/unregister/action | Cancelar o registro da assinatura do Assistente do Azure |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/actionRules/delete | Exclua regra de ação em uma determinada assinatura. |
> | Action | Microsoft.AlertsManagement / actionRules / read | Obter todas as regras de ação para os filtros de entrada. |
> | Action | Microsoft.AlertsManagement / actionRules / write | Criar ou atualizar regra de ação em uma determinada assinatura |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | Altera o estado do alerta. |
> | Action | Microsoft.AlertsManagement/alerts/diagnostics/read | Obtém todos os diagnósticos para o alerta |
> | Action | Microsoft.AlertsManagement/alerts/history/read | Obtém o histórico do alerta |
> | Action | Microsoft.AlertsManagement/alerts/read | Obter todos os alertas para os filtros de entrada. |
> | Action | Microsoft.AlertsManagement/alertsList/read | Obtenha todos os alertas para os filtros de entrada nas assinaturas |
> | Action | Microsoft. AlertsManagement/alertsMetaData/Read | Obter metadados de alertas para o parâmetro de entrada. |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | Obter o resumo dos alertas |
> | Action | Microsoft.AlertsManagement / alertsSummaryList / read | Obtenha o resumo de alertas entre assinaturas |
> | Action | Microsoft.AlertsManagement/Operations/read | Ler as operações fornecidas |
> | Action | Microsoft.AlertsManagement/register/action | Registra a assinatura para o Gerenciamento de Alertas da Microsoft |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Excluir regra de alerta do detector inteligente em uma determinada assinatura |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Obter todas as regras de alerta do detector inteligente para os filtros de entrada |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Criar ou atualizar a regra de alerta do detector inteligente em uma determinada assinatura |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | Altera o estado do grupo inteligente |
> | Action | Microsoft.AlertsManagement/smartGroups/history/read | Obtém o histórico do grupo inteligente |
> | Action | Microsoft.AlertsManagement/smartGroups/read | Obtém todos os grupos inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verificar se determinado nome do Analysis Server é válido e não está em uso. |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Action | Microsoft.AnalysisServices/operations/read | Recuperar as informações das operações |
> | Action | Microsoft.AnalysisServices/register/action | Registrar o provedor de recursos do Analysis Services. |
> | Action | Microsoft.AnalysisServices/servers/delete | Excluir o Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Listar o status do gateway associado ao servidor. |
> | Action | Microsoft.AnalysisServices/servers/read | Recupera as informações do Analysis Server especificado. |
> | Action | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/skus/read | Recuperar informações de SKU disponíveis para o servidor |
> | Action | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/write | Criar ou atualizar o Analysis Server especificado. |
> | Action | Microsoft.AnalysisServices/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ApiManagement/checkNameAvailability/read | Verificar se o nome do serviço fornecido está disponível |
> | Action | Microsoft.ApiManagement/operations/read | Ler todas as operações de API disponíveis para o recurso Microsoft.ApiManagement |
> | Action | Microsoft.ApiManagement/register/action | Registrar a assinatura para o provedor de recursos Microsoft.ApiManagement |
> | Action | Microsoft.ApiManagement/reports/read | Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest. |
> | Action | Microsoft.ApiManagement/service/apis/delete | Exclui a API especificada da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/delete | Exclui o diagnóstico especificado de uma API. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/read | Lista todos os diagnósticos de uma API. ou obtém os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/write | Cria um novo diagnóstico para uma API ou atualiza um existente. ou atualiza os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Exclui o comentário especificado de um problema. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/read | Lista todos os anexos para o problema associado à API especificada. ou obtém os detalhes do anexo do problema para uma API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | Cria um novo anexo para o problema em uma API ou atualiza um existente. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/delete | Exclui o comentário especificado de um problema. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/read | Lista todos os comentários para o problema associado à API especificada. ou obtém os detalhes do comentário do problema para uma API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | Cria um novo comentário para o problema em uma API ou atualiza um existente. |
> | Action | Microsoft.ApiManagement/service/apis/issues/delete | Exclui o problema especificado de uma API. |
> | Action | Microsoft.ApiManagement/service/apis/issues/read | Lista todos os problemas associados à API especificada. ou obtém os detalhes do problema para uma API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/issues/write | Cria um novo problema para uma API ou atualiza um existente. ou atualiza um problema existente para uma API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/delete | Exclui a operação especificada na API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/delete | Exclui a configuração de política na operação de API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/read | Obtenha a lista de configurações de política no nível de operação da API. ou obtenha a configuração de política no nível de operação da API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/write | Cria ou atualiza a configuração de política para o nível de operação da API. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/delete | Excluir a configuração de política no nível de operação |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter a configuração de política no nível de operação |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/write | Criar configuração de política no nível de operação |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | Lista uma coleção das operações para a API especificada. ou obtém os detalhes da operação de API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | Desanexe a marca da operação. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | Lista todas as marcas associadas à operação. ou obter marca associada à operação. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/write | Atribua a marcação à operação. |
> | Action | Microsoft.ApiManagement/service/apis/operations/write | Cria uma nova operação na API ou atualiza uma existente. ou atualiza os detalhes da operação na API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | Lista uma coleção de operações associadas a marcas. |
> | Action | Microsoft.ApiManagement/service/apis/policies/delete | Exclui a configuração de política na API. |
> | Action | Microsoft.ApiManagement/service/apis/policies/read | Obtenha a configuração de política no nível da API. ou obtenha a configuração de política no nível da API. |
> | Action | Microsoft.ApiManagement/service/apis/policies/write | Cria ou atualiza a configuração de política para a API. |
> | Action | Microsoft.ApiManagement/service/apis/policy/delete | Excluir a configuração de política no nível da API |
> | Action | Microsoft.ApiManagement/service/apis/policy/read | Obter a configuração de política no nível da API |
> | Action | Microsoft.ApiManagement/service/apis/policy/write | Criar configuração de política no nível da API |
> | Action | Microsoft.ApiManagement/service/apis/products/read | Lista todos os produtos dos quais a API faz parte. |
> | Action | Microsoft.ApiManagement/service/apis/read | Lista todas as APIs da instância do serviço de gerenciamento de API. ou obtém os detalhes da API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/releases/delete | Remove todas as versões da API ou exclui a versão especificada na API. |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | Lista todas as versões de uma API.<br>Uma versão de API é criada ao tornar a revisão da API atual.<br>As versões também são usadas para reverter para revisões anteriores.<br>Os resultados serão paginados e poderão ser restritos pelos parâmetros $top e $skip.<br>ou retorna os detalhes de uma versão de API. |
> | Action | Microsoft.ApiManagement/service/apis/releases/write | Cria uma nova versão para a API. ou atualiza os detalhes da versão da API especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apis/revisions/delete | Remover todas as revisões de uma API |
> | Action | Microsoft.ApiManagement/service/apis/revisions/read | Lista todas as revisões de uma API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | Exclui a configuração de esquema na API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | Obtenha a configuração do esquema no nível da API. ou obtenha a configuração do esquema no nível da API. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | Cria ou atualiza a configuração de esquema para a API. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Exclua a descrição da marca para a API. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Lista todas as descrições de marcas no escopo da API. O modelo semelhante ao Swagger-tagDescription é definido no nível da API, mas a marca pode ser atribuída às operações ou obter a descrição da marca no escopo da API |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Criar/atualizar a descrição da marca no escopo da API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/delete | Desanexe a marca da API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/read | Lista todas as marcas associadas à API. ou obter marca associada à API. |
> | Action | Microsoft.ApiManagement/service/apis/tags/write | Atribua a marca à API. |
> | Action | Microsoft.ApiManagement/service/apis/write | Cria novas ou atualiza a API especificada existente da instância do serviço de gerenciamento de API. ou atualiza a API especificada da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | Lista uma coleção de APIs associadas a marcas. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/delete | Exclui o conjunto de versão de API específico. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/read | Lista uma coleção de conjuntos de versão de API na instância de serviço especificada. ou obtém os detalhes do conjunto de versão de API especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obter lista de entidades de versão |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/write | Cria ou atualiza um conjunto de versões de API. ou atualiza os detalhes do Versionset de API especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos Microsoft.ApiManagement em execução na rede virtual para obter configurações de rede atualizadas. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | Exclui instância de servidor de autorização específica. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | Lista uma coleção de servidores de autorização definidos em uma instância de serviço. ou obtém os detalhes do servidor de autorização especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | Cria um novo servidor de autorização ou atualiza um servidor de autorização existente. ou atualiza os detalhes do servidor de autorização especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/backends/delete | Exclui o back-end especificado. |
> | Action | Microsoft.ApiManagement/service/backends/read | Lista uma coleção de back-ends na instância de serviço especificada. ou obtém os detalhes do back-end especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/backends/reconnect/action | Notifica o proxy APIM para criar uma nova conexão com o back-end após o tempo limite especificado. Se nenhum tempo limite for especificado, o tempo limite de 2 minutos será usado. |
> | Action | Microsoft.ApiManagement/service/backends/write | Cria ou atualiza um back-end. ou atualiza um back-end existente. |
> | Action | Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Action | Microsoft.ApiManagement/service/caches/delete | Exclui o cache específico. |
> | Action | Microsoft.ApiManagement/service/caches/read | Lista uma coleção de todos os caches externos na instância de serviço especificada. ou obtém os detalhes do cache especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/caches/write | Cria ou atualiza um cache externo a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do cache especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/certificates/delete | Exclui um certificado específico. |
> | Action | Microsoft.ApiManagement/service/certificates/read | Lista uma coleção de todos os certificados na instância de serviço especificada. ou obtém os detalhes do certificado especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/certificates/write | Cria ou atualiza o certificado que está sendo usado para autenticação com o back-end. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Remove o item de conteúdo especificado. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Retorna a lista de itens de conteúdo ou retorna os detalhes do item de conteúdo |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Cria um item de conteúdo ou atualiza o item de conteúdo especificado |
> | Action | Microsoft.ApiManagement/service/contentTypes/read | Retorna a lista de tipos de conteúdo |
> | Action | Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/diagnostics/delete | Exclui o diagnóstico especificado. |
> | Action | Microsoft.ApiManagement/service/diagnostics/read | Lista todos os diagnósticos da instância do serviço de gerenciamento de API. ou obtém os detalhes do diagnóstico especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/diagnostics/write | Cria um novo diagnóstico ou atualiza um existente. ou atualiza os detalhes do diagnóstico especificado por seu identificador. |
> | Action | Microsoft. ApiManagement/serviço/gateways/ação | Recupera a configuração do gateway. ou atualiza a pulsação do gateway. |
> | Action | Microsoft. ApiManagement/Service/gateways/Delete | Exclui gateway específico. |
> | Action | Microsoft. ApiManagement/serviço/gateways/chaves/ação | Recupera as chaves de gateway. |
> | Action | Microsoft. ApiManagement/Service/gateways/leitura | Lista uma coleção de gateways registrados com a instância de serviço. ou obtém os detalhes do gateway especificado por seu identificador. |
> | Action | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Regenera a chave primária do gateway invalidationg todos os tokens criados com ele. |
> | Action | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Regenera a chave do gateway secundário invalidationg quaisquer tokens criados com ele. |
> | Action | Microsoft. ApiManagement/Service/gateways/token/ação | Obtém o token de autorização de acesso compartilhado para o gateway. |
> | Action | Microsoft. ApiManagement/Service/gateways/Write | Cria ou atualiza um gateway a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do gateway especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/getssotoken/action | Obter token de SSO que pode ser usado para fazer logon no portal de herdado do Serviço de Gerenciamento de API como administrador |
> | Action | Microsoft.ApiManagement/service/groups/delete | Exclui o grupo específico da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/groups/read | Lista uma coleção de grupos definidos em uma instância de serviço. ou obtém os detalhes do grupo especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | Remover usuário existente do grupo existente. |
> | Action | Microsoft.ApiManagement/service/groups/users/read | Lista uma coleção de entidades de usuário associadas ao grupo. |
> | Action | Microsoft.ApiManagement/service/groups/users/write | Adicionar usuário existente a grupo existente |
> | Action | Microsoft.ApiManagement/service/groups/write | Cria ou atualiza um grupo. ou atualiza os detalhes do grupo especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/identityProviders/delete | Exclui a configuração do provedor de identidade especificado. |
> | Action | Microsoft.ApiManagement/service/identityProviders/read | Lista uma coleção de provedor de identidade configurada na instância de serviço especificada. ou obtém os detalhes de configuração do provedor de identidade configurado na instância de serviço especificada. |
> | Action | Microsoft.ApiManagement/service/identityProviders/write | Cria ou atualiza a configuração Identityprovider. ou atualiza uma configuração existente do Identityprovider. |
> | Action | Microsoft.ApiManagement/service/issues/read | Lista uma coleção de problemas na instância de serviço especificada. ou obtém detalhes do problema de gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/locations/networkstatus/read | Obter o status de acesso à rede dos recursos em que o serviço depende da localização. |
> | Action | Microsoft.ApiManagement/service/loggers/delete | Exclui o agente de log especificado. |
> | Action | Microsoft.ApiManagement/service/loggers/read | Lista uma coleção de agentes de log na instância de serviço especificada. ou obtém os detalhes do agente de log especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/loggers/write | Cria ou atualiza um agente de log. ou atualiza um agente de log existente. |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/networkstatus/read | Obter o status de acesso à rede dos recursos dos quais o serviço depende. |
> | Action | Microsoft.ApiManagement/service/notifications/action | Envia uma notificação para um usuário especificado |
> | Action | Microsoft.ApiManagement/service/notifications/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da notificação especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remove o email da lista de notificações. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtém a lista dos emails de destinatário da notificação inscritos em uma notificação. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Adiciona o endereço de email à lista de destinatários da notificação. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Remove o usuário de gerenciamento de API da lista de notificações. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtém a lista do usuário do destinatário da notificação inscrito na notificação. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adiciona o usuário de gerenciamento de API à lista de destinatários da notificação. |
> | Action | Microsoft.ApiManagement/service/notifications/write | Criar ou atualizar a notificação do editor de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | Exclui o provedor do OpenID Connect específico da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | Listas de todos os provedores do OpenId Connect. ou Obtém um provedor do OpenID Connect específico. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/write | Cria ou atualiza o provedor do OpenID Connect. ou atualiza o provedor do OpenID Connect específico. |
> | Action | Microsoft.ApiManagement/service/operationresults/read | Obter o status atual da operação de longa duração |
> | Action | Microsoft.ApiManagement/service/policies/delete | Exclui a configuração de política global do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/policies/read | Lista todas as definições de política global do serviço de gerenciamento de API. ou obtenha a definição de política global do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/policies/write | Cria ou atualiza a configuração de política global do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/policy/delete | Excluir a configuração de política no nível do locatário |
> | Action | Microsoft.ApiManagement/service/policy/read | Obter a configuração de política no nível do locatário |
> | Action | Microsoft.ApiManagement/service/policy/write | Criar configuração de política no nível do locatário |
> | Action | Microsoft.ApiManagement/service/policySnippets/read | Lista todos os trechos de política. |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | Obtenha configurações de entrada para o portal ou obtenha configurações de inscrição para o portal ou obtenha configurações de delegação para o Portal. |
> | Action | Microsoft.ApiManagement/service/portalsettings/write | Atualize as configurações de entrada. ou criar ou atualizar configurações de entrada. ou atualize as configurações de inscrição ou atualize as configurações de inscrição ou atualize as configurações de delegação. ou criar ou atualizar configurações de delegação. |
> | Action | Microsoft.ApiManagement/service/products/apis/delete | Exclui a API especificada do produto especificado. |
> | Action | Microsoft.ApiManagement/service/products/apis/read | Lista uma coleção das APIs associadas a um produto. |
> | Action | Microsoft.ApiManagement/service/products/apis/write | Adiciona uma API ao produto especificado. |
> | Action | Microsoft.ApiManagement/service/products/delete | Excluir produto. |
> | Action | Microsoft.ApiManagement/service/products/groups/delete | Exclui a associação entre o grupo e o produto especificados. |
> | Action | Microsoft.ApiManagement/service/products/groups/read | Lista a coleção de grupos de desenvolvedores associados ao produto especificado. |
> | Action | Microsoft.ApiManagement/service/products/groups/write | Adiciona a associação entre o grupo de desenvolvedores especificado com o produto especificado. |
> | Action | Microsoft.ApiManagement/service/products/policies/delete | Exclui a configuração de política no produto. |
> | Action | Microsoft.ApiManagement/service/products/policies/read | Obtenha a configuração de política no nível do produto. ou obtenha a configuração de política no nível do produto. |
> | Action | Microsoft.ApiManagement/service/products/policies/write | Cria ou atualiza a configuração de política para o produto. |
> | Action | Microsoft.ApiManagement/service/products/policy/delete | Excluir a configuração de política no nível do produto |
> | Action | Microsoft.ApiManagement/service/products/policy/read | Obter a configuração de política no nível do produto |
> | Action | Microsoft.ApiManagement/service/products/policy/write | Criar configuração de política no nível do produto |
> | Action | Microsoft.ApiManagement/service/products/read | Lista uma coleção de produtos na instância de serviço especificada. ou obtém os detalhes do produto especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/products/subscriptions/read | Lista a coleção de assinaturas para o produto especificado. |
> | Action | Microsoft.ApiManagement/service/products/tags/delete | Desanexe a marca do produto. |
> | Action | Microsoft.ApiManagement/service/products/tags/read | Lista todas as marcas associadas ao produto. ou obtenha a marca associada ao produto. |
> | Action | Microsoft.ApiManagement/service/products/tags/write | Atribua a marca ao produto. |
> | Action | Microsoft.ApiManagement/service/products/write | Cria ou atualiza um produto. ou atualize os detalhes do produto existente. |
> | Action | Microsoft.ApiManagement/service/productsByTags/read | Lista uma coleção de produtos associados às marcas. |
> | Action | Microsoft.ApiManagement/service/properties/delete | Exclui a propriedade específica da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/properties/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da propriedade especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/properties/write | Cria ou atualiza uma propriedade. ou atualiza a propriedade específica. |
> | Action | Microsoft.ApiManagement/service/quotas/periods/read | Obter o valor do contador de cota para o período |
> | Action | Microsoft.ApiManagement/service/quotas/periods/write | Definir o valor atual do contador de cota |
> | Action | Microsoft.ApiManagement/service/quotas/read | Obter valores para cota |
> | Action | Microsoft.ApiManagement/service/quotas/write | Definir o valor atual do contador de cota |
> | Action | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/regions/read | Lista todas as regiões do Azure nas quais o serviço existe. |
> | Action | Microsoft.ApiManagement/service/reports/read | Obter relatório agregado por períodos de tempo, Obter relatório agregado por região geográfica ou Obter relatório agregado pelos desenvolvedores.<br>ou Obter relatório agregado por produtos.<br>ou Obter relatório agregado por APIs, Obter relatório agregado por operações ou Obter relatório agregado por assinatura.<br>ou Obter dados de relatórios de solicitações |
> | Action | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Action | Microsoft.ApiManagement/service/subscriptions/delete | Exclui a assinatura especificada. |
> | Action | Microsoft.ApiManagement/service/subscriptions/read | Lista todas as assinaturas da instância do serviço de gerenciamento de API. ou obtém a entidade de assinatura especificada. |
> | Action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenera a chave primária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenera a chave secundária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/subscriptions/write | Cria ou atualiza a assinatura do usuário especificado para o produto especificado. ou atualiza os detalhes de uma assinatura especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/tagResources/read | Lista uma coleção de recursos associados com marcas. |
> | Action | Microsoft.ApiManagement/service/tags/delete | Exclui a marca específica da instância do serviço de gerenciamento de API. |
> | Action | Microsoft.ApiManagement/service/tags/read | Lista uma coleção de marcas definidas em uma instância de serviço. ou obtém os detalhes da marca especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/tags/write | Cria uma marca. ou atualiza os detalhes da marca especificada por seu identificador. |
> | Action | Microsoft.ApiManagement/service/templates/delete | Redefinir o modelo padrão de email de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/templates/read | Obter todos os modelos de email ou Obter detalhes do modelo de email do Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar o modelo de email de Gerenciamento de API ou o modelo de email de Gerenciamento de APIs de atualização |
> | Action | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política do locatário |
> | Action | Microsoft.ApiManagement/service/tenant/deploy/action | Executar uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados. |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter o resultado de uma operação específica |
> | Action | Microsoft.ApiManagement/service/tenant/read | Obtenha a definição de política global do serviço de gerenciamento de API. ou obter detalhes de informações de acesso do locatário |
> | Action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar a chave de acesso primária |
> | Action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar a chave de acesso secundária |
> | Action | Microsoft.ApiManagement/service/tenant/save/action | Criar confirmação com instantâneo de configuração para uma ramificação específica no repositório |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | Obter status da última sincronização git |
> | Action | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações da ramificação git especificada |
> | Action | Microsoft.ApiManagement/service/tenant/write | Definir a configuração de política para o locatário ou Atualizar detalhes de informações de acesso do locatário |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado SSL para um Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/service/users/action | Registrar um novo usuário |
> | Action | Microsoft.ApiManagement/service/users/confirmations/send/action | Envia uma confirmação |
> | Action | Microsoft.ApiManagement/service/users/delete | Exclui um usuário específico. |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Recupera uma URL de redirecionamento que contém um token de autenticação para assinar um determinado usuário no portal do desenvolvedor. |
> | Action | Microsoft.ApiManagement/service/users/groups/read | Lista todos os grupos de usuários. |
> | Action | Microsoft.ApiManagement/service/users/identities/read | Lista de todas as identidades de usuário. |
> | Action | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | Action | Microsoft.ApiManagement/service/users/read | Lista uma coleção de usuários registrados na instância de serviço especificada. ou obtém os detalhes do usuário especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/users/subscriptions/read | Lista a coleção de assinaturas do usuário especificado. |
> | Action | Microsoft.ApiManagement/service/users/token/action | Obtém o token de autorização de acesso compartilhado para o usuário. |
> | Action | Microsoft.ApiManagement/service/users/write | Cria ou atualiza um usuário. ou atualiza os detalhes do usuário especificado por seu identificador. |
> | Action | Microsoft.ApiManagement/service/write | Criar uma nova instância do Serviço de Gerenciamento de API |
> | Action | Microsoft.ApiManagement/unregister/action | Cancelar o registro da assinatura para o provedor de recursos Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Authorization/classicAdministrators/delete | Remove o administrador da assinatura. |
> | Action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtém o status de operação do administrador da assinatura. |
> | Action | Microsoft.Authorization/classicAdministrators/read | Lê os administradores da assinatura. |
> | Action | Microsoft.Authorization/classicAdministrators/write | Adicionar ou modificar o administrador de uma assinatura. |
> | Action | Microsoft.Authorization/denyAssignments/delete | Excluir uma atribuição de negação no escopo especificado. |
> | Action | Microsoft.Authorization/denyAssignments/read | Obter informações sobre uma atribuição de negação. |
> | Action | Microsoft.Authorization/denyAssignments/write | Criar uma atribuição de negação no escopo especificado. |
> | Action | Microsoft.Authorization/elevateAccess/action | Concede ao chamador o acesso de Administrador de Acesso do Usuário no escopo do locatário |
> | Action | Microsoft.Authorization/locks/delete | Exclui os bloqueios no escopo especificado. |
> | Action | Microsoft.Authorization/locks/read | Obtém bloqueios no escopo especificado. |
> | Action | Microsoft.Authorization/locks/write | Adicionar bloqueios no escopo especificado. |
> | Action | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Action | Microsoft.Authorization/permissions/read | Lista todas as permissões que o chamador tem em um determinado escopo. |
> | Action | Microsoft.Authorization/policyAssignments/delete | Excluir uma atribuição de política no escopo especificado. |
> | Action | Microsoft.Authorization/policyAssignments/read | Obter informações sobre uma atribuição de política. |
> | Action | Microsoft.Authorization/policyAssignments/write | Criar uma atribuição de política no escopo especificado. |
> | Action | Microsoft.Authorization/policyDefinitions/delete | Excluir uma definição de política. |
> | Action | Microsoft.Authorization/policyDefinitions/read | Obter informações sobre uma definição de política. |
> | Action | Microsoft.Authorization/policyDefinitions/write | Criar uma definição de política personalizada. |
> | Action | Microsoft.Authorization/policySetDefinitions/delete | Excluir uma definição de conjunto de políticas. |
> | Action | Microsoft.Authorization/policySetDefinitions/read | Obtenha informações sobre uma definição do conjunto de políticas. |
> | Action | Microsoft.Authorization/policySetDefinitions/write | Criar uma definição de conjunto de políticas personalizada. |
> | Action | Microsoft.Authorization/providerOperations/read | Obter operações para todos os provedores de recurso que podem ser usados nas definições de função. |
> | Action | Microsoft.Authorization/roleAssignments/delete | Excluir uma atribuição de função no escopo especificado. |
> | Action | Microsoft.Authorization/roleAssignments/read | Obter informações sobre uma atribuição de função. |
> | Action | Microsoft.Authorization/roleAssignments/write | Criar uma atribuição de função no escopo especificado. |
> | Action | Microsoft.Authorization/roleDefinitions/delete | Excluir a definição de função personalizada especificada. |
> | Action | Microsoft.Authorization/roleDefinitions/read | Obter informações sobre uma definição de função. |
> | Action | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar uma definição de função personalizada com permissões especificadas e escopos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler uma informação de registro do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Gravar uma solicitação para regenerar chaves DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/certificates/delete | Excluir um ativo de certificado da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Action | Microsoft.Automation/automationAccounts/certificates/read | Obter um ativo de certificado da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/certificates/write | Criar ou atualizar um ativo de certificado da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Ler uma compilação do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Gravar uma compilação do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo de mídia de configuração |
> | Action | Microsoft.Automation/automationAccounts/configurations/delete | Excluir um conteúdo de DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Ler a contagem de um conteúdo do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/configurations/read | Obter o conteúdo de DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/configurations/write | Gravar um conteúdo de DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connections/delete | Excluir um ativo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de conexões |
> | Action | Microsoft.Automation/automationAccounts/connections/read | Obter um ativo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connections/write | Criar ou atualizar um ativo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/delete | Excluir um ativo de tipo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/read | Obter um ativo de tipo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/write | Criar um ativo de tipo de conexão da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | Excluir um ativo de credencial da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | Obter um ativo de credencial da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | Criar ou atualizar um ativo de credencial da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/delete | Excluir uma conta da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Excluir recursos do Hybrid Runbook Worker |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Action | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obter o conteúdo do runbook da Automação do Azure no momento da execução do trabalho |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/delete | Excluir uma agenda de trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o workspace vinculado à conta de automação |
> | Action | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves para a conta de automação |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | Obter atividades de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | Excluir um módulo Powershell da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos Powershell dentro da conta de automação |
> | Action | Microsoft.Automation/automationAccounts/modules/read | Obtém um módulo Powershell da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/modules/write | Cria ou atualiza um módulo Powershell da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Excluir uma configuração de nó do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Ler um conteúdo de configuração de nó do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Ler uma configuração de nó do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Gravar uma configuração de nó do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo da contagem de nós para o tipo especificado |
> | Action | Microsoft.Automation/automationAccounts/nodes/delete | Excluir nós DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodes/read | Ler os nós DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê os conteúdos do relatório do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/read | Ler relatórios do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/nodes/write | Cria ou atualiza nós do DSC de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obter TypeFields de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | Exclui um pacote Python 2 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/read | Obtém um pacote Python 2 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/write | Cria ou atualiza um pacote Python 2 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | Exclui um pacote Python 3 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/read | Obtém um pacote Python 3 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/write | Cria ou atualiza um pacote Python 3 da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/content/read | Obter o conteúdo de um runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | Excluir um runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Criar o conteúdo de um rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtém resultados de operação de rascunho do runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obter um rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obter um trabalho de teste de rascunho do runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Interrompe um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Criar um trabalho de teste de rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Desfazer edições em um rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém a contagem de runbooks da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho de runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | Criar ou atualizar um runbook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | Excluir um ativo de agendamento da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendamentos da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Excluir uma configuração de atualização do Software de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização do Software de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar uma configuração de atualização do Software de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/statistics/read | Obter Estatísticas de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obtém um computador de implantação de atualização da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtém um trabalho de patch de gerenciamento de atualização da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/usages/read | Obter o uso de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | Excluir um ativo de variável da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/variables/read | Ler um ativo de variável da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/variables/write | Criar ou atualizar um ativo de variável da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | Exclui um trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/read | Obtém um trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/start/action | Inicia um trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/stop/action | Para um trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/streams/read | Obter um fluxo de trabalho do observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Exclui ações de trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtém ações de trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Cria ações de trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/watchers/write | Cria um trabalho do Observador de Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | Excluir um webhook da Automação do Azure  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | Ler um webhook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Criar ou atualizar um webhook da Automação do Azure |
> | Action | Microsoft.Automation/automationAccounts/write | Criar ou atualizar uma conta da Automação do Azure |
> | Action | Microsoft.Automation/operations/read | Obtém as operações disponíveis para os recursos da Automação do Azure |
> | Action | Microsoft.Automation/register/action | Registra a assinatura à Automação do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Excluir recursos do Diretório B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | Exibir recursos do Diretório B2C |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar o recurso Diretório B2C |
> | Action | Microsoft.AzureActiveDirectory/b2ctenants/read | Lista todos os locatários B2C dos quais o usuário é membro |
> | Action | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o provedor de recursos Microsoft.AzureActiveDirectory |
> | Action | Microsoft.AzureActiveDirectory/register/action | Registrar a assinatura do provedor de recursos Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | Obter as propriedades de uma operação do provedor de recursos |
> | Action | Microsoft.AzureStack/register/action | Registrar assinatura com o provedor de recursos Microsoft.AzureStack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Excluir uma assinatura do cliente do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obter as propriedades uma assinatura do cliente do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Criar ou atualizar uma assinatura do cliente do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/delete | Excluir um registro do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | Obter a última chave de ativação do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Recuperar detalhes estendidos de um produto do Marketplace do Azure Stack |
> | Action | Microsoft.AzureStack/registrations/products/read | Obter as propriedades de um produto do Marketplace do Azure Stack |
> | Action | Microsoft.AzureStack/registrations/read | Obter as propriedades de um registro do Microsoft Azure Stack |
> | Action | Microsoft.AzureStack/registrations/write | Criar ou atualizar um registro do Microsoft Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Batch/batchAccounts/applications/delete | Excluir um aplicativo |
> | Action | Microsoft.Batch/batchAccounts/applications/read | Listar aplicativos ou obtém as propriedades de um aplicativo |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicativos |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/delete | Excluir um pacote de aplicativos |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/read | Obter as propriedades de um pacote de aplicativos |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/write | Criar um novo pacote de aplicativos ou atualizar um pacote de aplicativos existente |
> | Action | Microsoft.Batch/batchAccounts/applications/write | Criar um novo aplicativo ou atualizar um aplicativo existente |
> | Action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obter os resultados de uma operação de certificado de execução longa em uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancelar a exclusão de falha de um certificado em uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/certificates/delete | Excluir um certificado de uma conta do Batch |
> | Action | Microsoft.Batch/batchAccounts/certificates/read | Listar certificados em uma conta do Lote ou obter as propriedades de um certificado |
> | Action | Microsoft.Batch/batchAccounts/certificates/write | Criar um novo certificado em uma conta do Lote ou atualizar um certificado existente |
> | Action | Microsoft.Batch/batchAccounts/delete | Excluir uma conta do Lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/excluir | Exclui um trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/ler | Listar trabalhos em uma conta do lote ou obter as propriedades de um trabalho |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Write | Cria um novo trabalho em uma conta do lote ou atualiza um trabalho existente |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Excluir um plano de trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Listar agendas de trabalho em uma conta do lote ou obter as propriedades de uma agenda de trabalho |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Cria uma nova agenda de trabalho em uma conta do lote ou atualiza uma agenda de trabalho existente |
> | Action | Microsoft.Batch/batchAccounts/listkeys/action | Listar chaves de acesso para uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | Obter os resultados de uma operação de execução longa da conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obter os resultados de uma operação de pool de execução longa em uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/pools/delete | Excluir um pool de uma conta do Batch |
> | Action | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desabilitar o escalonamento automático de um pool de conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/pools/read | Listar pools em uma conta do Batch ou obter as propriedades de um pool |
> | Action | Microsoft.Batch/batchAccounts/pools/stopResize/action | Interromper uma operação de redimensionamento em andamento em um pool de conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/pools/write | Criar um novo pool em uma conta do Lote ou atualizar um pool existente |
> | Action | Microsoft.Batch/batchAccounts/read | Listar contas do Lote ou obtém as propriedades de uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera chaves de acesso para uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza as chaves de acesso da conta de armazenamento automática configurada para uma conta do Lote |
> | Action | Microsoft.Batch/batchAccounts/write | Criar uma nova conta do Lote ou atualizar uma conta do Lote existente |
> | Action | Microsoft.Batch/locations/accountOperationResults/read | Obter os resultados de uma operação de execução longa da conta do Lote |
> | Action | Microsoft.Batch/locations/checkNameAvailability/action | Verificar se o nome da conta é válido e não está em uso. |
> | Action | Microsoft.Batch/locations/quotas/read | Obter cotas do Lote da assinatura especificada na região do Azure especificada |
> | Action | Microsoft.Batch/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Batch |
> | Action | Microsoft.Batch/register/action | Registra a assinatura do provedor de recursos de lote e permite a criação de contas do Lote |
> | Action | Microsoft.Batch/unregister/action | Cancelar o registro da assinatura do Provedor de Recursos do Lote impedindo a criação de contas do Lote |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Billing/billingAccounts/departments/read | Lista todos os departamentos no escopo de uma conta de cobrança |
> | Action | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Lista todas as contas de registro no escopo de uma conta de cobrança |
> | Action | Microsoft.Billing/billingAccounts/read | Lista todas as contas de cobrança às quais o usuário tem acesso |
> | Action | Microsoft.Billing/billingPeriods/read | Listar os períodos de cobrança disponíveis |
> | Action | Microsoft.Billing/departments/read | Lista todos os departamentos aos quais o usuário tem acesso |
> | Action | Microsoft.Billing/invoices/read | Listar faturas disponíveis |
> | Action | Microsoft.Billing/register/action | Registra a Assinatura no provedor de recursos Microsoft.Billing |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | Operação de Exclusão |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | Lista os Segredos |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Leitura do token de autorização de logon único do recurso |
> | Action | Microsoft.BingMaps/mapApis/Read | Operação de Leitura |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a chave |
> | Action | Microsoft.BingMaps/mapApis/Write | Operação de Gravação |
> | Action | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Blockchain/blockchainMembers/delete | Exclui um membro Blockchain existente. |
> | Action | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Obtém ou lista as chaves de API de membro Blockchain existentes. |
> | Action | Microsoft.Blockchain/blockchainMembers/read | Obtém ou lista os membros Blockchain existentes. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Conecta-se a um nó de transação de membro Blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Exclui um nó de transação de membro Blockchain existente. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Obtém ou lista as chaves de API do nó de transação de membro Blockchain existente. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Cria ou atualiza um nó de transação de membro Blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/write | Cria ou atualiza um membro Blockchain. |
> | Action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Obtém os resultados da operação de membros do Blockchain. |
> | Action | Microsoft.Blockchain/locations/checkNameAvailability/action | Verifica se o nome do recurso é válido e não está em uso. |
> | Action | Microsoft.Blockchain/operations/read | Listar todas as operações no provedor de recursos do Microsoft Blockchain. |
> | Action | Microsoft. Blockchain/registro/ação | Registra a assinatura para o provedor de recursos Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Ler qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/delete | Excluir qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/read | Ler qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Obter a ID do objeto da entidade de serviço do Azure Blueprints. |
> | Action | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/delete | Excluir qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/read | Ler qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprints/delete | Excluir qualquer blueprint |
> | Action | Microsoft.Blueprint/blueprints/read | Ler qualquer blueprint |
> | Action | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler qualquer artefato de blueprint |
> | Action | Microsoft.Blueprint/blueprints/versions/delete | Excluir qualquer blueprint |
> | Action | Microsoft.Blueprint/blueprints/versions/read | Ler qualquer blueprint |
> | Action | Microsoft.Blueprint/blueprints/versions/write | Criar ou atualizar qualquer blueprint |
> | Action | Microsoft.Blueprint/blueprints/write | Criar ou atualizar qualquer blueprint |
> | Action | Microsoft.Blueprint/register/action | Registra o Provedor de Recursos do Azure Blueprints |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.BotService/botServices/channels/delete | Excluir um serviço de Bot |
> | Action | Microsoft.BotService/botServices/channels/read | Ler um serviço de Bot |
> | Action | Microsoft.BotService/botServices/channels/write | Gravar um serviço de Bot |
> | Action | Microsoft.BotService/botServices/connections/delete | Excluir um serviço de Bot |
> | Action | Microsoft.BotService/botServices/connections/read | Ler um serviço de Bot |
> | Action | Microsoft.BotService/botServices/connections/write | Gravar um serviço de Bot |
> | Action | Microsoft.BotService/botServices/delete | Excluir um serviço de Bot |
> | Action | Microsoft.BotService/botServices/read | Ler um serviço de Bot |
> | Action | Microsoft.BotService/botServices/write | Gravar um serviço de Bot |
> | Action | Microsoft.BotService/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Action | Microsoft.BotService/Operations/read | Ler as operações para todos os tipos de recursos |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Cache/checknameavailability/action | Verifica se um nome está disponível para ser usado com um novo Redis Cache |
> | Action | Microsoft.Cache/locations/operationresults/read | Obtém o resultado de uma operação de execução longa para a qual o cabeçalho 'Local' foi retornado anteriormente para o cliente |
> | Action | Microsoft.Cache/operations/read | Lista as operações que o provedor 'Microsoft.Cache' permite. |
> | Action | Microsoft.Cache/redis/delete | Excluir todo o Cache Redis |
> | Action | Microsoft.Cache/redis/export/action | Exportar dados Redis para os blobs de armazenamento prefixados no formato especificado |
> | Action | Microsoft.Cache/redis/firewallRules/delete | Exclui as regras de firewall de IP de um Redis Cache |
> | Action | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall de IP de um Redis Cache |
> | Action | Microsoft.Cache/redis/firewallRules/write | Editar as regras de firewall de IP de um Redis Cache |
> | Action | Microsoft.Cache/redis/forceReboot/action | Forçar reinicialização de uma instância de cache, possivelmente com perda de dados. |
> | Action | Microsoft.Cache/redis/import/action | Importar dados de um formato especificado de vários blobs para o Redis |
> | Action | Microsoft.Cache/redis/linkedservers/delete | Excluir o Servidor Vinculado de um Cache Redis |
> | Action | Microsoft.Cache/redis/linkedservers/read | Obter Servidores Vinculados associados a um cache redis. |
> | Action | Microsoft.Cache/redis/linkedservers/write | Adicionar o Servidor Vinculado a um Cache Redis |
> | Action | Microsoft.Cache/redis/listKeys/action | Exiba o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | Liste as últimas Notificações de Atualização do locatário de cache. |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | Obtém as métricas disponíveis para o Cache Redis |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | Exclui o agendamento de patch de um Redis Cache |
> | Action | Microsoft.Cache/redis/patchSchedules/read | Obtém o agendamento de patch de um Redis Cache |
> | Action | Microsoft.Cache/redis/patchSchedules/write | Modificar o agendamento da aplicação de patch de um Redis Cache |
> | Action | Microsoft.Cache/redis/read | Exiba as configurações do Cache Redis no portal de gerenciamento |
> | Action | Microsoft.Cache/redis/regenerateKey/action | Altere o valor das chaves de acesso do Cache Redis no portal de gerenciamento |
> | Action | Microsoft.Cache/redis/start/action | Inicie uma instância de cache. |
> | Action | Microsoft.Cache/redis/stop/action | Interrompa uma instância de cache. |
> | Action | Microsoft.Cache/redis/write | Modifique as configurações do Cache Redis no portal de gerenciamento |
> | Action | Microsoft.Cache/register/action | Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura |
> | Action | Microsoft.Cache/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.Cache' com uma assinatura |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/appliedreservations/read | Ler todas as Reservas |
> | Action | Microsoft.Capacity/calculateexchange/action | Computa a quantidade e o preço do Exchange de novos erros de compra e de política de retorno. |
> | Action | Microsoft.Capacity/calculateprice/action | Calcula o Preço de qualquer Reserva |
> | Action | Microsoft.Capacity/catalogs/read | Lê o catálogo de Reservas |
> | Action | Microsoft.Capacity/checkoffers/action | Verifica as Ofertas de Assinatura |
> | Action | Microsoft.Capacity/checkscopes/action | Verifica qualquer Assinatura |
> | Action | Microsoft.Capacity/commercialreservationorders/read | Obtém Pedidos de Reserva criados em qualquer Locatário |
> | Action | Microsoft.Capacity/exchange/action | Trocar qualquer reserva |
> | Action | Microsoft.Capacity/operations/read | Lê qualquer Operação |
> | Action | Microsoft.Capacity/register/action | Registrar o provedor de recursos de Capacidade e permitir a criação de recursos de Capacidade. |
> | Action | Microsoft.Capacity/reservationorders/action | Atualizar qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/availablescopes/action | Encontra qualquer Escopo Disponível |
> | Action | Microsoft.Capacity/reservationorders/calculaterefund/action | Computa a quantidade de restituição e o preço de novos erros de compra e de política de retorno. |
> | Action | Microsoft.Capacity/reservationorders/delete | Excluir qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/merge/action | Mescla qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/read | Ler todas as Reservas |
> | Action | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/reservations/delete | Excluir qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as Reservas |
> | Action | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as Reservas |
> | Action | Microsoft.Capacity/reservationorders/reservations/write | Criar qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/return/action | Retorna qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/split/action | Divide qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/swap/action | Troca qualquer Reserva |
> | Action | Microsoft.Capacity/reservationorders/write | Criar qualquer Reserva |
> | Action | Microsoft.Capacity/tenants/register/action | Registra qualquer Locatário |
> | Action | Microsoft.Capacity/unregister/action | Cancela o registro de qualquer Locatário |
> | Action | Microsoft.Capacity/validatereservationorder/action | Valida qualquer Reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/edgenodes/delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft.Cdn/edgenodes/write |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft.Cdn/operationresults/read |  |
> | Action | Microsoft.Cdn/operationresults/write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/write |  |
> | Action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/profiles/read |  |
> | Action | Microsoft.Cdn/profiles/write |  |
> | Action | Microsoft.Cdn/register/action | Registra a assinatura no provedor de recursos CDN e habilita a criação de perfis CDN. |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Excluir um certificado existente |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um existente |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | Excluir um AppServiceCertificate existente |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Emitir novamente um certificateorder existente |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um certificateorder existente |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar email de certificado |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar emails de solicitação para outro endereço de email |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recuperar o selo de site para um certificado do Serviço de Aplicativo emitido |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recuperar a lista de ações de certificado |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recuperar o histórico de email do certificado |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade do domínio |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar uma nova certificateOrder ou atualizar uma existente |
> | Action | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações do registro de certificado de serviço de aplicativo |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provisionar entidade de serviço para entidade de serviço de aplicativo |
> | Action | Microsoft.CertificateRegistration/register/action | Registrar o provedor de recursos do Microsoft Certificates da assinatura |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar objeto de compra de certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ClassicCompute/capabilities/read | Mostra as funcionalidades |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verifica a disponibilidade de determinado nome de domínio. |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Obtém a disponibilidade de um determinado nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/active/write | Define o nome de domínio ativo. |
> | Action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostrar o conjunto de disponibilidade do recurso. |
> | Action | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra os recursos do nome de domínio |
> | Action | Microsoft.ClassicCompute/domainNames/delete | Remover os nomes de domínio dos recursos. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os slots de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter função no slot de implantação do nome de domínio |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter instância de função para a função no slot de implantação do nome de domínio |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obtenha o estado do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicione o estado do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter o domínio de atualização para o slot de implantação no nome de domínio |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar o domínio de atualização para o slot de implantação no nome de domínio |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Cria ou atualiza a implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/delete | Remova as extensões do nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lê o status da operação das extensões dos nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/read | Retorna as extensões do nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/write | Adicione as extensões do nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remover um novo balanceamento de carga interno. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lê o status da operação dos balanceadores de carga internos de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtém os balanceadores de carga internos. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Cria um novo balanceamento de carga interno. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lê o status da operação dos de conjuntos de pontos de extremidade com balanceamento de carga de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obtenha os conjuntos de ponto de extremidade com balanceamento de carga. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicione o conjunto de ponto de extremidade com balanceamento de carga. |
> | Action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obtenha o status da operação do nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Lê o status da operação das extensões dos nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/read | Retornar os nomes de domínio dos recursos. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Excluir os certificados de serviço usados. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lê o status da operação de certificados de serviço de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Retorna os certificados de serviço usados. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adicionar ou modificar os certificados de serviço usados. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Anula a migração de um slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Confirma a migração de um slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/delete | Exclui determinado slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lê o status da operação de slots de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os slots de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remova a referência de extensão da função do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lê o status da operação de referências de extensão de funções de slots de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retorna a referência de extensão da função do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicionar ou modificar a referência de extensão da função do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obtenha a definição de métrica de função para o nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obtenha a métrica de função para o nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obtenha o status da operação para a função de slot de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métrica. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obter a função do slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Baixa o arquivo de conexão de área de trabalho remota para a instância de função na função do slot de nome de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o status da operação para a instância de função na função do slot de nomes de domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obter a instância de função. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recompila a instância de função. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Refaz imagem da instância de função. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia as instâncias de função. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obtenha o SKU de função para o slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicione uma função para o slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do slot de implantação para interrompido. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do slot de implantação para iniciado. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Percorrer a atualização do domínio. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Valida a migração de um slot de implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/write | Cria ou atualiza a implantação. |
> | Action | Microsoft.ClassicCompute/domainNames/swap/action | Alterna o slot de preparo para o slot de produção. |
> | Action | Microsoft.ClassicCompute/domainNames/write | Adicionar ou modificar os nomes de domínio dos recursos. |
> | Action | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mover todos os recursos clássicos para uma assinatura diferente. |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Lista as famílias de sistemas operacionais convidados disponíveis no Microsoft Azure e também lista as versões dos sistemas operacionais disponíveis para cada família. |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Lista as versões do sistema operacional convidado que estão atualmente disponíveis no Microsoft Azure. |
> | Action | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | Lê o status da operação do recurso. |
> | Action | Microsoft.ClassicCompute/quotas/read | Obter a cota para a assinatura. |
> | Action | Microsoft.ClassicCompute/register/action | Registrar na Computação Clássica |
> | Action | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtém a lista de Skus para tipos de recursos com suporte. |
> | Action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da assinatura para operação de movimentação clássica. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado à máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lê o status da operação de grupos de segurança de rede associados a máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtém as operações assíncronas possíveis |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | Capture uma máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/delete | Remove máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obtenha as configurações de diagnóstico de máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera a lista de discos de dados |
> | Action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Baixa o arquivo RDP da máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lê o status da operação de extensões de máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtém a extensão da máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obtenha a definição de métrica de máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtém a métrica. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado ao adaptador de rede. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lê o status da operação de grupos de segurança de rede associados a máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado ao adaptador de rede. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado ao adaptador de rede. |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lê o status da operação de máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executa a manutenção na máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métrica. |
> | Action | Microsoft.ClassicCompute/virtualMachines/read | Recupera a lista de máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplanta a máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia as máquinas virtuais. |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Desligar a máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/start/action | Iniciar a máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/stop/action | Interrompe a máquina virtual. |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obtenha um status de operação de conexão cruzada do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Exclua o emparelhamento de conexão cruzada do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obtenha um status de operação de emparelhamento de conexão cruzada do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obtenha o emparelhamento de conexão cruzada do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicione o emparelhamento de conexão cruzada do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obtenha conexões cruzadas do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicione conexões cruzadas do ExpressRoute. |
> | Action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera a lista de dispositivos com suporte. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Exclui o grupo de segurança de rede. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lê o status da operação do grupo de segurança de rede. |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as Configurações de Diagnóstico de Grupos de Segurança de Rede |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as configurações de diagnóstico dos Grupos de segurança de rede, essa operação é complementada pelo provedor de recursos de insights. |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obtém os eventos para o grupo de segurança de rede |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtém o grupo de segurança de rede. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Exclui a regra de segurança. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lê o status da operação de regras de segurança do grupo de segurança de rede. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtém a regra de segurança. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Action | Microsoft.ClassicNetwork/operations/read | Obtenha operações de rede clássicas. |
> | Action | Microsoft.ClassicNetwork/quotas/read | Obter a cota para a assinatura. |
> | Action | Microsoft.ClassicNetwork/register/action | Registrar na Rede Clássica |
> | Action | Microsoft.ClassicNetwork/reservedIps/delete | Excluir um Ip reservado. |
> | Action | Microsoft.ClassicNetwork/reservedIps/join/action | Associar um IP reservado |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lê o status da operação de IPs reservados. |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | Obtém os Ips reservados |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | Adicionar um novo Ip reservado |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Anula a migração de uma Rede Virtual |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra as funcionalidades |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verifica a disponibilidade de um endereço IP fornecido em uma rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Confirma a migração de uma Rede Virtual |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/delete | Exclui a rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cancela a revogação de um certificado do cliente. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Ler os certificados do cliente revogados. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado do cliente. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Exclui o certificado do cliente do gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Baixa o certificado por impressão digital. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Lista o pacote de certificados de gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localizar os certificados raiz do cliente. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado raiz do cliente. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Estabelece uma conexão de gateway site a site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desconecta uma conexão de gateway site a site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera a lista de conexões. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma conexão de gateway site a site. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Exclui o gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Baixa o script de configuração de dispositivo. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Baixa o diagnóstico de gateway. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera a chave de serviço de circuito. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Lista o pacote de gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lê o status da operação de gateways de redes virtuais. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtém o pacote de gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtém os gateways de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico do gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico do gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | Adiciona a rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lê o status da operação de redes virtuais. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Emparelha uma rede virtual com outra rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma Rede Virtual |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Exclui o proxy de emparelhamento de rede virtual remota. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de emparelhamento de rede virtual remota. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de emparelhamento de rede virtual remota. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado à sub-rede. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Ler o status da operação do grupo de segurança de rede associado à sub-rede da rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à sub-rede. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma Rede Virtual |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o emparelhamento de rede virtual. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/write | Adicionar uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ClassicStorage/capabilities/read | Mostra as funcionalidades |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verifica a disponibilidade de uma conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obtenha a disponibilidade de uma conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/disks/read | Retorna o disco da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o Status da Operação de Imagem. |
> | Action | Microsoft.ClassicStorage/images/read | Retorna a imagem. |
> | Action | Microsoft.ClassicStorage/operations/read | Obtém as operações de armazenamento clássicas |
> | Action | Microsoft.ClassicStorage/osImages/read | Retorna a imagem do sistema operacional. |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem da plataforma do sistema operacional. |
> | Action | Microsoft.ClassicStorage/publicImages/read | Obtém a imagem de máquina virtual pública. |
> | Action | Microsoft.ClassicStorage/quotas/read | Obter a cota para a assinatura. |
> | Action | Microsoft.ClassicStorage/register/action | Registrar para Armazenamento Clássico |
> | Action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Anula a migração de uma conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Confirma a migração de uma conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | Excluir a conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | Exclui um determinado disco da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lê o status da operação do recurso. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | Retorna o disco da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | Excluir uma imagem da conta de armazenamento específica. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retorna o status da operação da imagem da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso das contas de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lê o status da operação do recurso. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Exclui uma imagem fornecida do sistema operacional da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retorna a imagem do sistema operacional da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar configurações de diagnóstico. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métrica. |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obter as configurações de diagnóstico. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtém as definições de métrica. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtém a métrica. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | Obter os serviços disponíveis. |
> | Action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Validar migração de uma conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Exclui uma imagem de máquina virtual fornecida. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém o status da operação de imagem de uma determinada máquina virtual. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Retorna a imagem da máquina virtual. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona a imagem de uma determinada máquina virtual. |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Action | Microsoft.ClassicStorage/vmImages/read | Lista imagens da máquina virtual. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Esta operação extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Esta operação retorna uma caixa delimitadora em volta da área mais importante da imagem. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Esta operação gera uma descrição de uma imagem no idioma legível por humanos com sentenças completas.<br> A descrição se baseia em uma coleção de marcas de conteúdo, que também são retornadas pela operação.<br>Mais de uma descrição pode ser gerada para cada imagem.<br> As descrições são ordenadas por sua pontuação de confiança.<br>Todas as descrições estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Esta operação executa a detecção de objetos na imagem especificada.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Esta operação gera uma imagem em miniatura com a largura e a altura especificadas pelo usuário.<br> Por padrão, o serviço analisa a imagem, identifica a região de interesse (ROI) e gera coordenadas de corte inteligente com base no ROI.<br> O corte inteligente ajuda quando você especifica uma taxa de proporção que difere da imagem de entrada |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Essa operação reconhece o conteúdo em uma imagem aplicando um modelo específico de domínio.<br> A lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional pode ser recuperada usando a solicitação GET/Models.<br> Atualmente, a API fornece os seguintes modelos específicos de domínio: celebridades, pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Essa operação retorna a lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional.  Atualmente, a API dá suporte aos seguintes modelos específicos de domínio: celebridade Recognizer, Recognizer de pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | O OCR (reconhecimento óptico de caracteres) detecta o texto em uma imagem e extrai os caracteres reconhecidos em um fluxo de caracteres utilizável por máquina.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Use essa interface para obter o resultado de uma operação de Reconhecimento de Texto. Quando você usa a interface Reconhecimento de Texto, a resposta contém um campo chamado "Operation-Location". O campo "operação-local" contém a URL que você deve usar para a operação obter Reconhecimento de Texto resultado da operação. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Esta operação gera uma lista de palavras ou marcas que são relevantes para o conteúdo da imagem fornecida.<br>O API da Pesquisa Visual Computacional pode retornar marcas com base em objetos, seres de vida, cenário ou ações encontradas em imagens.<br>Diferentemente das categorias, as marcas não são organizadas de acordo com um sistema de classificação hierárquica, mas correspondem ao conteúdo da imagem.<br>As marcas podem conter dicas para evitar ambigüidade ou fornecer contexto, por exemplo, a marca "Cello" pode ser acompanhada pela dica "instrumento musical".<br>Todas as marcas estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Essa interface é usada para obter o resultado da operação de reconhecimento de texto. A URL para essa interface deve ser recuperada do campo <b>"Operation-Location"</b> retornado da interface reconhecimento de texto. |
> | Action | Microsoft.CognitiveServices/accounts/delete | Exclui as contas de API |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detecte faces humanas em uma imagem, retorne retângulos de face e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Excluir uma lista de face especificada. As imagens de face relacionadas na lista de face também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Excluir uma face de uma lista de rosto por faceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Adicione uma face a uma lista de face especificada, até 1.000 rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Recupere faceListId, Name, userData e faces da lista facial na lista de face. Liste as listas de face ' faceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crie uma lista de face vazia com faceListId especificado pelo usuário, o nome e um userData opcional. Até 64 listas de rosto são permitidas informações de atualização de uma lista de face, incluindo nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Tabela de face de consulta fornecida para pesquisar os rostos de aparência semelhante de uma matriz faceid, uma lista de face ou uma lista de rosto grande. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divida os rostos candidatas em grupos com base na semelhança de face. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | identificação de um-para-muitos para localizar as correspondências mais próximas da pessoa de consulta específica que se encontram de um grupo de pessoas ou grupo de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Excluir uma lista de face grande especificada. As imagens de face relacionadas na lista de rosto grande também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Exclua uma face de uma lista de rosto grande por largeFaceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Recuperar a face persistente em uma lista de face grande por largeFaceListId e persistedFaceId. Liste faces ' persistedFaceId e userData ' em uma lista de face grande especificada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Adicione uma face a uma lista de face grande especificada, até 1 milhão rostos. Atualizar o campo userData de uma face especificada em uma lista de rosto grande por seu persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Recupere largeFaceListId, Name, userData de uma lista de rosto grande. Listar as informações de grandes listas ' de largeFaceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Envie uma tarefa de treinamento de lista de rosto grande. O treinamento é uma etapa crucial que só pode ser usada por uma lista de rosto grande treinada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Para verificar se o status de treinamento da lista de rosto grande foi concluído ou ainda está em andamento. O treinamento do LargeFaceList é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crie uma lista de face grande vazia com largeFaceListId especificado pelo usuário, o nome e um userData opcional. Atualize as informações de uma lista de rosto grande, incluindo Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Excluir um grupo de pessoas grandes existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas grandes serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crie uma nova pessoa em um grupo de pessoas grandes especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas grandes. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas grandes. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu largePersonGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas grandes para identificação ou verificação facial. Para lidar com a imagem da atualização, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar todas as informações de todas as pessoas no grupo de pessoas grandes especificado, incluindo PERSONID, Name, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Recupere as informações de um grupo de pessoas grandes, incluindo seu nome e userData. Essa API retorna a lista de informações de grupo de pessoas grandes todos os grupos de pessoas grandes existentes largePesonGroupId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Envie uma tarefa de treinamento de grupo de pessoas grandes. O treinamento é uma etapa crucial que só pode ser usada por um grupo treinado de grande pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Para verificar se o status de treinamento de grupo de pessoa grande foi concluído ou ainda está em andamento. O treinamento do LargePersonGroup é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crie um novo grupo de pessoas grandes com largePersonGroupId especificado pelo usuário, nome e userData opcional. Atualizar nome e userData de um grupo de pessoas grandes existentes. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Excluir um grupo de pessoas existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Criar uma nova pessoa em um grupo de pessoas especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu personGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas para identificação ou verificação facial. Para lidar com a imagem de várias atualizações, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar as informações de todas as pessoas no grupo de pessoas especificado, incluindo PERSONID, Name, userData e persistedFaceIds de Registered. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Recupere o nome e o userData do grupo de pessoas. Para obter informações de pessoas sob esse grupo de pessoas, use listar pesonGroupId, nome e userData de grupos de pessoas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Envie uma tarefa de treinamento de grupo Person. O treinamento é uma etapa crucial que apenas um grupo de pessoas treinado pode usar. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Para verificar se o status de treinamento do grupo de pessoas foi concluído ou ainda está em andamento. O treinamento do Person é uma operação assíncrona disparada |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crie um novo grupo de pessoas com personGroupId especificado, nome e userData fornecido pelo usuário. Atualize o nome e o userData de um grupo de pessoas existente. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Verifique se duas faces pertencem a uma mesma pessoa ou se uma face pertence a uma pessoa. |
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | Lista de Chaves |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Obtém a previsão de ponto de extremidade publicado para a consulta fornecida. |
> | Action | Microsoft.CognitiveServices/accounts/read | Lê contas de API. |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar chave |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | Lê os SKUs disponíveis para um recurso existente. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | A API retorna uma lista de entidades conhecidas e entidades nomeadas gerais\"(\"pessoa \",\"local \",\" organização etc.) em um determinado documento. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | A API retorna uma lista de cadeias de caracteres representando os principais pontos de discussão no texto de entrada. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | A API retorna o idioma detectado e uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam 100% de certeza de que o idioma detectado é verdadeiro. Há 120 idiomas com suporte no total. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | A API retorna uma pontuação numérica entre 0 e 1.<br>Classificações próximas de 1 indicam sentimento positivo, enquanto as classificações próximas de 0 indicam sentimento negativo.<br>Uma pontuação de 0,5 indica a falta de sentimentos (por exemplo,<br>uma instrução factoname). |
> | Action | Microsoft.CognitiveServices/accounts/usages/read | Obter o uso da cota de um recurso existente. |
> | Action | Microsoft.CognitiveServices/accounts/write | Grava contas de API. |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | Ler os SKUs disponíveis para uma assinatura. |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Ler os SKUs disponíveis para uma assinatura. |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notificação da Microsoft. Network de exclusão de VirtualNetworks ou sub-redes. |
> | Action | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Action | Microsoft.CognitiveServices/register/action | Registra a assinatura para Serviços Cognitivos |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Commerce/RateCard/read | Retornar dados da oferta, metadados de recurso/medidor e tarifas da assinatura especificada. |
> | Action | Microsoft.Commerce/UsageAggregates/read | Recupera o consumo do Microsoft Azure para uma assinatura. O resultado contém dados totais de uso, assinatura e informações relativas a recursos em determinado intervalo de tempo. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Compute/availabilitySets/delete | Exclui conjunto de disponibilidade |
> | Action | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Action | Microsoft.Compute/availabilitySets/vmSizes/read | Listar tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Action | Microsoft.Compute/availabilitySets/write | Cria um novo conjunto de disponibilidade ou atualiza um existente |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI do SAS do Disco para acesso do blob |
> | Action | Microsoft.Compute/disks/delete | Excluir o Disco |
> | Action | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI do SAS do Disco |
> | Action | Microsoft.Compute/disks/read | Obter as propriedades de um Disco |
> | Action | Microsoft.Compute/disks/write | Cria um novo Disco ou atualizar um existente |
> | Action | Microsoft.Compute/galleries/delete | Exclui a Galeria |
> | Action | Microsoft.Compute/galleries/images/delete | Exclui a Imagem da Galeria |
> | Action | Microsoft.Compute/galleries/images/read | Obtém as propriedades da Imagem da Galeria |
> | Action | Microsoft.Compute/galleries/images/versions/delete | Exclui a Versão da Imagem da Galeria |
> | Action | Microsoft.Compute/galleries/images/versions/delete | Obtém as propriedades da Versão de Imagem da Galeria |
> | Action | Microsoft.Compute/galleries/images/versions/write | Cria uma nova Versão da Imagem da Galeria ou atualiza uma existente |
> | Action | Microsoft.Compute/galleries/images/write | Cria uma nova Imagem da Galeria ou atualiza uma existente |
> | Action | Microsoft.Compute/galleries/read | Obtém as propriedades da Galeria |
> | Action | Microsoft.Compute/galleries/write | Cria uma nova Galeria ou atualiza uma existente |
> | Action | Microsoft.Compute/hostGroups/delete | Exclui o grupo de hosts |
> | Action | Microsoft.Compute/hostGroups/read | Obter as propriedades de um grupo de hosts |
> | Action | Microsoft.Compute/hostGroups/write | Cria um novo grupo de hosts ou atualiza um grupo de hosts existente |
> | Action | Microsoft.Compute/hosts/delete | Exclui o host |
> | Action | Microsoft.Compute/hosts/read | Obter as propriedades de um host |
> | Action | Microsoft.Compute/hosts/write | Cria um novo host ou atualiza um host existente |
> | Action | Microsoft.Compute/images/delete | Exclui a imagem |
> | Action | Microsoft.Compute/images/read | Obtenha as propriedades da imagem |
> | Action | Microsoft.Compute/images/write | Cria uma nova imagem ou atualiza uma existente |
> | Action | Microsoft.Compute/locations/capsOperations/read | Obtém o status de uma operação de Limites assíncrona |
> | Action | Microsoft.Compute/locations/diskOperations/read | Obtém o status de uma operação de Disco assíncrona |
> | Action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Crie logs para mostrar o total de solicitações por intervalo de tempo para ajudar na limitação de diagnóstico. |
> | Action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Crie logs para mostrar as agregações de solicitações limitadas agrupadas por ResourceName, OperationName ou a Política de Restrição aplicada. |
> | Action | Microsoft.Compute/locations/operations/read | Obtém o status de uma operação assíncrona |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma Oferta de Imagem de Plataforma |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma Sku de Imagem de Plataforma |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma Versão de Imagem de Plataforma |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um tipo de VMExtension |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma Versão de VMExtension |
> | Action | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Editor |
> | Action | Microsoft.Compute/locations/runCommands/read | Lista os comandos de execução disponíveis no local |
> | Action | Microsoft.Compute/locations/usages/read | Obtém limites de serviço e quantidades de uso atuais para os recursos de computação da assinatura em um local |
> | Action | Microsoft.Compute/locations/vmSizes/read | Lista tamanhos de máquina virtual disponíveis em um local |
> | Action | Microsoft.Compute/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Compute |
> | Action | Microsoft.Compute/proximityPlacementGroups/delete | Exclui o Grupo de Posicionamento de Proximidade |
> | Action | Microsoft.Compute/proximityPlacementGroups/read | Obtém as Propriedades de um Grupo de Posicionamento de Proximidade |
> | Action | Microsoft.Compute/proximityPlacementGroups/write | Cria um Grupo de Posicionamento de Proximidade ou atualiza um que já existe |
> | Action | Microsoft.Compute/register/action | Registra a Assinatura com o provedor de recursos Microsoft.Compute |
> | Action | Microsoft.Compute/restorePointCollections/delete | Exclui a coleção de ponto de restauração e pontos de restauração contidos |
> | Action | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de ponto de restauração |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | Exclui o ponto de restauração |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades do ponto de restauração |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauração junto com os URIs SAS do blob |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | Criar um novo ponto de restauração |
> | Action | Microsoft.Compute/restorePointCollections/write | Cria uma nova coleção de ponto de restauração ou atualiza uma existente |
> | Action | Microsoft.Compute/sharedVMImages/delete | Exclui a SharedVMImage |
> | Action | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de uma SharedVMImage |
> | Action | Microsoft.Compute/sharedVMImages/versions/delete | Excluir um SharedVMImageVersion |
> | Action | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de uma SharedVMImageVersion |
> | Action | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar uma SharedVMImageVersion para as regiões de destino |
> | Action | Microsoft.Compute/sharedVMImages/versions/write | Crie um novo SharedVMImageVersion ou atualize um existente |
> | Action | Microsoft.Compute/sharedVMImages/write | Cria uma nova SharedVMImage ou atualiza uma existente |
> | Action | Microsoft.Compute/skus/read | Obtém a lista de SKUs do Microsoft.Compute disponíveis para a sua Assinatura |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do Instantâneo para acesso de blob |
> | Action | Microsoft.Compute/snapshots/delete | Excluir um Instantâneo |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do Instantâneo |
> | Action | Microsoft.Compute/snapshots/read | Obter as propriedades de um Instantâneo |
> | Action | Microsoft.Compute/snapshots/write | Criar um novo Instantâneo ou atualizar um existente |
> | Action | Microsoft.Compute/unregister/action | Cancela o Registro da Assinatura com o provedor de recursos Microsoft.Compute |
> | Action | Microsoft.Compute/virtualMachines/capture/action | Captura a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converter os discos baseados em blob da máquina virtual para discos gerenciados |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e libera os recursos de computação |
> | Action | Microsoft.Compute/virtualMachines/delete | Exclui a máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | Exclui a extensão da máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão de máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | Cria uma extensão da máquina virtual ou atualiza uma existente |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | Configura o estado da máquina virtual para Generalizado e prepara a máquina virtual para captura |
> | Action | Microsoft.Compute/virtualMachines/instanceView/read | Obtém o status de tempo de execução detalhado da máquina virtual e seus recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | Action | Microsoft.Compute/virtualMachines/performMaintenance/action | Executa a Operação de Manutenção na VM. |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | Desligar a máquina virtual. Observe que a máquina virtual continuará a ser cobrada. |
> | Action | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | Reimplanta a máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | Refaz a imagem da máquina virtual que está usando o disco diferencial. |
> | Action | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | Executa um script predefinido na máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | Action | Microsoft.Compute/virtualMachines/write | Cria uma nova máquina virtual ou atualiza uma existente |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desligar e liberar os recursos de computação para as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | Exclui o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | Exclui as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Exclui a Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obtém as propriedades de uma Extensão de Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Cria uma nova Extensão de Conjunto de Dimensionamento de Máquinas Virtuais ou atualiza um existente |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Percorra manualmente os domínios de atualização de plataforma em um Conjunto de Dimensionamento de Máquinas Virtuais do Service Fabric para concluir uma atualização pendente |
> | Action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtém a exibição de instância do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualiza manualmente instâncias para o modelo mais recente do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obtenha as propriedades de todos os adaptadores de rede de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Inicia uma atualização sem interrupção para mover todas as instâncias de conjunto de dimensionamento de máquinas virtuais para a versão mais recente disponível do sistema operacional de imagem de plataforma. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obtém o histórico de atualizações do SO para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Realiza a manutenção planejada nas instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desliga as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obtenha as propriedades de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | Obtenha as propriedades de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Reimplantar as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Refaz a imagem das instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) das instâncias de um Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reinicia as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancela a atualização sem interrupção de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obtenha o status mais recente de Atualização Sem Interrupção para um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verifique se um Conjunto de Dimensionamento de Máquinas Virtuais existente pode Reduzir/Expandir para a contagem de instâncias especificada |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | Lista os SKUs válidos para um Conjunto de Dimensionamento de Máquinas Virtuais existente |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | Inicia as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desliga e libera os recursos de computação para uma Máquina Virtual em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Exclua uma Máquina Virtual específica em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recupera a exibição de instância de uma Máquina Virtual em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obter propriedades do endereço IP público criado usando o Conjunto de Dimensionamento de Máquinas Virtuais. O Conjunto de Dimensionamento de Máquinas Virtuais pode criar no máximo um IP público por ipconfiguration (IP privado) |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou de todas as configurações IP de uma interface de rede criada usando o Conjunto de Dimensionamento de Máquinas Virtuais. Configurações de IP representam IPs privados |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obtenha as propriedades de um ou de todos os adaptadores de rede de uma máquina virtual criada usando o Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Realiza a manutenção planejada em uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desliga uma instância de Máquina Virtual em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recupera as propriedades de uma Máquina Virtual em um Conjunto de Escala de VM |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Reimplanta uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Refaz a imagem de uma instância de Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Refaz a imagem de todos os discos (Disco de SO e Discos de Dados) da instância da Máquinas Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reinicia uma instância de Máquina Virtual em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido em uma instância da Máquina Virtual em um Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Inicia uma instância de Máquina Virtual em um Conjunto de Escala de VM. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualiza as propriedades de uma Máquina Virtual em um Conjunto de Dimensionamento de VM |
> | Action | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de dimensionamento de máquinas virtuais |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | Cria um novo Conjunto de Dimensionamento de Máquinas Virtuais ou atualiza um existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Consumption/balances/read | Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/budgets/delete | Exclui os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/budgets/read | Listar os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/budgets/write | Cria ou atualiza os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/charges/read | Lista os encargos |
> | Action | Microsoft.Consumption/credits/read | Lista os créditos |
> | Action | Microsoft.Consumption/events/read | Lista os eventos |
> | Action | Microsoft.Consumption/forecasts/read | Lista as previsões |
> | Action | Microsoft.Consumption/lots/read | Lista os lotes |
> | Action | Microsoft.Consumption/marketplaces/read | Listar os detalhes de uso do recurso do marketplace para um escopo para assinaturas do EA e WebDirect. |
> | Action | Microsoft.Consumption/operationresults/read | Lista os resultados da operação |
> | Action | Microsoft.Consumption/operations/read | Listar todas as operações com suporte pelo provedor de recursos Microsoft.Consumption. |
> | Action | Microsoft.Consumption/operationstatus/read | Lista o status da operação |
> | Action | Microsoft.Consumption/pricesheets/read | Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/register/action | Registra-se no RP de Consumo |
> | Action | Microsoft.Consumption/reservationDetails/read | Listar os detalhes de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados detalhados são por instância por nível diário. |
> | Action | Microsoft.Consumption/reservationRecommendations/read | Lista recomendações únicas ou compartilhadas para Instâncias Reservadas para uma assinatura. |
> | Action | Microsoft.Consumption/reservationSummaries/read | Listar o resumo de utilização para instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo serão no nível mensal ou diário. |
> | Action | Microsoft.Consumption/reservationTransactions/read | Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento. |
> | Action | Microsoft.Consumption/tags/read | Lista as marcas do EA e das assinaturas. |
> | Action | Microsoft.Consumption/tenants/read | Listar locatários |
> | Action | Microsoft.Consumption/tenants/register/action | Registre a ação de escopo do Microsoft.Consumption por um locatário. |
> | Action | Microsoft.Consumption/terms/read | Listar os termos de uma assinatura ou um grupo de gerenciamento. |
> | Action | Microsoft.Consumption/usageDetails/read | Listar os detalhes de uso de um escopo para assinaturas do EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Execução em um contêiner específico. |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obter logs para um contêiner específico. |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | Exclua o grupo de contêineres específico. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do grupo de contêineres. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do grupo de contêineres. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o grupo de contêineres. |
> | Action | Microsoft.ContainerInstance/containerGroups/read | Obtém todos os grupos de contêineres. |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contêineres específico. |
> | Action | Microsoft.ContainerInstance/containerGroups/start/action | Inicia um grupo de contêineres específico. |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | Interrompe um grupo de contêineres específico. Os recursos de computação serão desalocados e a cobrança será interrompida. |
> | Action | Microsoft.ContainerInstance/containerGroups/write | Criar ou atualizar um grupo de contêineres específico. |
> | Action | Microsoft.ContainerInstance/locations/cachedImages/read | Obtém as imagens armazenadas em cache da assinatura em uma região. |
> | Action | Microsoft.ContainerInstance/locations/capabilities/read | Obtém as funcionalidades para uma região. |
> | Action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.ContainerInstance de que uma rede virtual ou uma sub-rede está sendo excluída. |
> | Action | Microsoft.ContainerInstance/locations/usages/read | Obtém o uso para uma região específica. |
> | Action | Microsoft.ContainerInstance/register/action | Registra a assinatura para o provedor de recursos da instância do contêiner e habilita a criação de grupos de contêineres. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | Verificar se o nome do registro de contêiner está disponível para uso. |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.ContainerRegistry sobre a exclusão de uma rede virtual ou sub-rede |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | Obter um resultado de operação assíncrona |
> | Action | Microsoft.ContainerRegistry/operations/read | Listar todas as operações da API REST do Registro de Contêiner do Azure disponíveis |
> | Action | Microsoft.ContainerRegistry/register/action | Registra a assinatura do provedor de recursos de registro de contêiner e permite a criação de registros de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/artifacts/delete | Exclua o artefato em um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancelar um build existente. |
> | Action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obter um link para baixar os logs de build. |
> | Action | Microsoft.ContainerRegistry/registries/builds/read | Obter as propriedades de build especificado ou lista todos os builds para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/builds/write | Atualizar um build para um registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/delete | Excluir uma tarefa de build de um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Listar as propriedades de controle de origem para uma tarefa de build. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/read | Obter as propriedades da tarefa de build especificada ou lista todas as tarefas de build para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Excluir uma etapa de build de uma tarefa de build. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Listar os argumentos de build para uma etapa de build, incluindo os argumentos secretos. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obter as propriedades da etapa de build especificada ou listar todas as etapas de build da tarefa de build especificada. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Criar ou atualizar uma etapa de build para uma tarefa de build com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/write | Criar ou atualizar uma tarefa de build para um registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/delete | Excluir um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Excluir um filtro de grade de eventos de um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obter as propriedades do filtro de grade de eventos especificado ou listar todos os filtros de grade de eventos para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Criar ou atualizar um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obter o local de upload para o usuário poder carregar a fonte. |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | Importa a imagem para o registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obtenha o local de URL de upload de origem para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | Listar as credenciais de logon para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registro de contêiner especificado |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | Listar os usos de cotas para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | Obtém os metadados de um repositório específico para um registro de contêiner |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | Atualiza os metadados de um repositório para um registro de contêiner |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obter um status de operação assíncrona do registro |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/push/write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | Action | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | Criar um novo build com base nos parâmetros da solicitação e adicionar à fila de build. |
> | Action | Microsoft.ContainerRegistry/registries/read | Obter as propriedades do registro de contêiner especificado ou listar todos os registros de contêiner sob o grupo de recursos ou assinatura especificada. |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenerar uma das credenciais de logon para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | Excluir uma replicação de um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obter um status de operação assíncrona de replicação |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | Obter as propriedades da replicação especificada ou listar todas as replicações do registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/replications/write | Criar ou atualizar uma replicação para um registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/runs/cancel/action | Cancele um tempo de execução existente. |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtém o log da URL do SAS para uma execução. |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | Obtém as propriedades de uma execução em um registro de contêiner ou lista é executado. |
> | Action | Microsoft.ContainerRegistry/registries/runs/write | Atualiza uma execução. |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Agende uma execução em um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/delete | Exclui uma tarefa para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Liste todos os detalhes de uma tarefa para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | Obtém uma tarefa para um registro de contêiner ou lista todas as tarefas. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/write | Cria ou atualiza uma tarefa para um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registro de contêiner especificado |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | Excluir um webhook de um registro de contêiner. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obter a configuração de URI de serviço e cabeçalhos personalizados para o webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Listar eventos recentes para o webhook especificado. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obter um status de operação assíncrona do webhook |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Disparar um evento de ping para ser enviado ao webhook. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | Obter as propriedades do webhook especificado ou listar todos os webhooks para o registro de contêiner especificado. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | Criar ou atualizar um webhook para um registro de contêiner com os parâmetros especificados. |
> | Action | Microsoft.ContainerRegistry/registries/write | Criar ou atualizar um registro de contêiner com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | Exclui um serviço de contêiner |
> | Action | Microsoft.ContainerService/containerServices/read | Obter um serviço de contêiner |
> | Action | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contêiner ou atualiza um existente |
> | Action | Microsoft.ContainerService/locations/operationresults/read | Obtém o status do resultado de uma operação assíncrona |
> | Action | Microsoft.ContainerService/locations/operations/read | Obtém o status de uma operação assíncrona |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores com suporte |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtenha um perfil de acesso de cluster gerenciado pelo nome da função usando a credencial de lista |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obtenha um perfil de acesso de cluster gerenciado pelo nome da função |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/delete | Exclui um pool de agentes |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/read | Obter um pool de agentes |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/write | Cria um novo pool de agentes ou atualiza um existente |
> | Action | Microsoft.ContainerService/managedClusters/delete | Exclui um cluster gerenciado |
> | Action | Microsoft. ContainerService/managedClusters/detectores/leitura | Obter o detector de cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/read | Obter um cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Redefine o perfil do AAD de um cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Redefine o perfil da entidade de serviço de um cluster gerenciado |
> | Action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Obtém o perfil de atualização do cluster |
> | Action | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerenciado ou atualiza um existente |
> | Action | Microsoft.ContainerService/openShiftClusters/delete | Excluir um cluster de turno aberto |
> | Action | Microsoft.ContainerService/openShiftClusters/read | Obter um cluster de turno aberto |
> | Action | Microsoft.ContainerService/openShiftClusters/write | Cria um novo Open Shift Cluster ou atualiza um já existente |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/delete | Excluir um cluster gerenciado de turno aberto |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/read | Obter um cluster gerenciado de turno aberto |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/write | Cria um novo Cluster Gerenciado de Turno Aberto ou atualiza um existente |
> | Action | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.ContainerService |
> | Action | Microsoft.ContainerService/register/action | Registra a Assinatura com o provedor de recursos Microsoft.ContainerService |
> | Action | Microsoft.ContainerService/unregister/action | Cancela o Registro da Assinatura com o provedor de recursos de Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ContentModerator/applications/delete | Operação de Exclusão |
> | Action | Microsoft.ContentModerator/applications/listSecrets/action | Listar Segredos |
> | Action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Ler tokens de logon único |
> | Action | Microsoft.ContentModerator/applications/read | Operação de Leitura |
> | Action | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Action | Microsoft.ContentModerator/applications/write | Operação de Gravação |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | Listar preferência de comunicação |
> | Action | Microsoft.ContentModerator/operations/read | operações de leitura |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | Atualizar as preferências de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | Exclua o Cloudconnector for especificado. |
> | Action | Microsoft.CostManagement/cloudConnectors/read | Liste o cloudConnectors para o usuário autenticado. |
> | Action | Microsoft.CostManagement/cloudConnectors/write | Criar ou atualizar o Cloudconnector for especificado. |
> | Action | Microsoft.CostManagement/dimensions/read | Listar todas as dimensões com suporte por um escopo. |
> | Action | Microsoft.CostManagement/exports/action | Executa a exportação especificada. |
> | Action | Microsoft.CostManagement/exports/delete | Exclui a exportação especificada. |
> | Action | Microsoft.CostManagement/exports/read | Lista as exportações por escopo. |
> | Action | Microsoft.CostManagement/exports/run/action | Execute exportações. |
> | Action | Microsoft.CostManagement/exports/write | Cria ou atualiza a exportação especificada. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Liste o externalSubscriptions em um externalBillingAccount para o usuário autenticado. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | Liste o externalBillingAccounts para o usuário autenticado. |
> | Action | Microsoft.CostManagement/externalSubscriptions/read | Liste o externalSubscriptions para o usuário autenticado. |
> | Action | Microsoft.CostManagement/externalSubscriptions/write | Atualizar grupo de gerenciamento associado de externalSubscription |
> | Action | Microsoft.CostManagement/query/action | Consultar os dados de uso por um escopo. |
> | Action | Microsoft.CostManagement/query/read | Consultar os dados de uso por um escopo. |
> | Action | Microsoft.CostManagement/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por uma assinatura. |
> | Action | Microsoft.CostManagement/reports/action | Agendar relatórios sobre dados de uso por um escopo. |
> | Action | Microsoft.CostManagement/reports/read | Agendar relatórios sobre dados de uso por um escopo. |
> | Action | Microsoft.CostManagement/tenants/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por um locatário. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Criar ou atualizar qualquer Metadado do Adobe do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Ler qualquer Metadado do Adobe do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Excluir política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Ler política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Regenerar a chave primária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Regenerar a chave secundária da política de Assinatura de Acesso Compartilhado do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Criar ou atualizar política de assinatura de acesso compartilhado do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer Conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer Conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/delete | Excluir conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Obter qualquer status de tempo de execução do Azure Customer Insights Connector |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Ativar qualquer Mapeamento do Conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Excluir mapeamento do conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Ler qualquer resultado da operação Mapeamento do Conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Ler mapeamento do conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Criar ou atualizar mapeamento de conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/operations/read | Ler qualquer resultado de operação do Conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/read | Ler conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Criar ou atualizar qualquer informação de autenticação do conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/update/action | Atualizar qualquer conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/connectors/write | Criar ou atualizar conector do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/action | Criar ou atualizar quaisquer metadados do CRM do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/read | Ler todos os metadados do CRM do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/delete | Excluir Hub de informações do Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/delete | Excluir qualquer GDPR do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/read | Ler qualquer GDPR do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/write | Criar ou atualizar qualquer GDPR do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Obter créditos de cobrança do Hub do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Obter histórico de cobrança do Hub do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | Excluir qualquer imagem do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/read | Ler qualquer imagem do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/images/write | Criar ou atualizar qualquer imagem do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/delete | Excluir quaisquer interações do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/operations/read | Ler qualquer resultado da operação de interação do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/read | Ler interação do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugerir links do RelationShip para qualquer interação do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | Criar ou atualizar interação do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/delete | Excluir indicador principal de desempenho do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/operations/read | Ler qualquer resultado da operação de Indicadores chave de desempenho do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/read | Ler indicador principal de desempenho do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Reprocessar quaisquer indicadores chave de desempenho do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/kpi/write | Criar ou atualizar indicador principal de desempenho do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/delete | Excluir todos os links do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/operations/read | Ler qualquer resultado da operação de links do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/read | Ler todos os links do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/links/write | Criar ou atualizar qualquer link do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/action | Criar ou atualizar qualquer metadado MSE do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/read | Ler todos os metadados MSE do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/operationresults/read | Obter resultados da operação do Hub do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictions/delete | Excluir quaisquer previsões do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictions/operations/read | Ler qualquer resultado da operação Previsões do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictions/read | Ler todas as previsões do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictions/write | Criar ou atualizar qualquer previsão do Azure Customer |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Excluir qualquer Política de Correspondência Preditiva do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Ler qualquer resultado da operação de Política de Correspondência Preditiva do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Ler todas as Políticas de Correspondência Preditiva do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Criar ou atualizar qualquer Política de Correspondência Preditiva do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/delete | Excluir qualquer perfil do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/operations/read | Ler qualquer resultado da operação do Perfil do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/read | Ler perfil do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/profiles/write | Gravar qualquer perfil do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para o recurso |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Obter a métrica disponível para o recurso |
> | Action | Microsoft.CustomerInsights/hubs/read | Ler Hub de informações do Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Excluir todos os links do relacionamento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Ler qualquer resultado da operação Links do Relacionamento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Ler todos os links do relacionamento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Criar ou atualizar qualquer link do relacionamento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/delete | Excluir todos os relacionamentos do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/operations/read | Ler qualquer resultado da operação Relacionamentos do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/read | Ler todos os relacionamentos do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/relationships/write | Criar ou atualizar qualquer relacionamento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Excluir atribuição RBAC do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Ler qualquer resultado da operação de Atribuição RBAC do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/read | Ler atribuição RBAC do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/write | Criar ou atualizar atribuição RBAC do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/roles/read | Ler todos os papéis RBAC do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Criar ou atualizar quaisquer metadados do Salesforce para o Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Ler todos os metadados do Salesforce do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/segments/delete | Excluir todos os segmentos do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Gerenciar qualquer segmento dinâmico do Azure Customer Insight |
> | Action | Microsoft.CustomerInsights/hubs/segments/read | Ler segmentos do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/segments/static/action | Gerenciar segmentos estáticos do Azure Customer Insight |
> | Action | Microsoft.CustomerInsights/hubs/segments/write | Criar ou atualizar qualquer segmento do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Excluir qualquer SqlConnectionStrings do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Ler qualquer SqlConnectionStrings do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Criar ou atualizar qualquer SqlConnectionStrings do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Gerar Esquema de Tipo de Sugestão a partir de dados de exemplo |
> | Action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Gerenciar qualquer configuração de hub do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/delete | Excluir modo de exibição de aplicativo do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/read | Ler modo de exibição de aplicativo do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/write | Criar ou atualizar modo de exibição de aplicativo do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/widgettypes/read | Ler todos os tipos de widget do aplicativo do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/write | Criar ou atualizar um Hub de informações do Customer Insights |
> | Action | Microsoft.CustomerInsights/operations/read | Ler os Metadados da Api do Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/register/action | Registrar a assinatura do provedor de recursos do Customer Insights e permitir a criação de recursos do Customer Insights |
> | Action | Microsoft.CustomerInsights/unregister/action | Cancelar o registro da assinatura do provedor de recursos do Customer Insights |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite agendar uma retirada para remessas de devolução. |
> | Action | Microsoft.DataBox/jobs/cancel/action | Cancela um pedido em andamento. |
> | Action | Microsoft.DataBox/jobs/delete | Excluir as Ordens |
> | Action | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Action | Microsoft.DataBox/jobs/read | Liste ou obtenha as Ordens |
> | Action | Microsoft.DataBox/jobs/write | Cria ou atualiza a Ordens |
> | Action | Microsoft.DataBox/locations/availableSkus/action | Este método retorna a lista de SKUs disponíveis. |
> | Action | Microsoft. Data Box/Locations/availableSkus/Read | Listar ou obter os SKUs disponíveis |
> | Action | Microsoft.DataBox/locations/operationResults/read | Lista ou obtém os Resultados da Operação |
> | Action | Microsoft.DataBox/locations/validateAddress/action | Validará o endereço de entrega e fornecerá endereços alternativos, se houver algum. |
> | Action | Microsoft. Data Box/Locations/validateInputs/Action |  |
> | Action | Microsoft.DataBox/register/action | Registra o Provedor Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Lista ou obtém os alertas |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Exclui os cronogramas de largura de banda |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Lista ou obtém os cronogramas de largura de banda |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza os cronogramas de largura de banda |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Exclui os dispositivos do Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Baixar Atualizações no dispositivo |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Recupera as informações estendidas do recurso |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar Atualizações no dispositivo |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Lista ou obtém os trabalhos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Lista ou obtém as configurações de rede do dispositivo |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Listar ou obter o status da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Exclui os pedidos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Criar ou atualizar os pedidos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Lista ou obtém os dispositivos do Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Exclui as funções |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as funções |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Procurar atualizações |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar as configurações de segurança |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Exclui os compartilhamentos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lista ou obtém os compartilhamentos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Atualizar os metadados de compartilhamento com os dados da nuvem |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Cria ou atualiza os compartilhamentos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Exclui as credenciais da conta de armazenamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Cria ou atualiza as credenciais da conta de armazenamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Exclui os gatilhos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Criar ou atualizar os gatilhos |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Lista ou obtém o resumo de atualização |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Fazer upload do certificado para registro do dispositivo |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Exclui os usuários de compartilhamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Listar ou obter o resultado da operação |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lista ou obtém os usuários de compartilhamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Cria ou atualiza os usuários de compartilhamento |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos do Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Databricks/register/action | Registre-se no Databricks. |
> | Action | Microsoft.Databricks/workspaces/delete | Remove um workspace do Databricks. |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Define as configurações de diagnóstico disponíveis para o workspace do Databricks |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar configurações de diagnóstico. |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Recebe as definições de log disponíveis para o workspace do Databricks |
> | Action | Microsoft.Databricks/workspaces/read | Recupera uma lista de workspaces do Databricks. |
> | Action | Microsoft.Databricks/workspaces/write | Cria um workspace do Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | Excluir recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/catalogs/read | Ler recurso de catálogos para provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/catalogs/write | Gravar recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/checkNameAvailability/read | Verifique a disponibilidade do nome do catálogo para o provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/datacatalogs/delete | Excluir recurso datacatalog do provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/datacatalogs/read | Ler recurso datacatalog para provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/datacatalogs/write | Gravar recurso do datacatalog para o provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/operations/read | Lê todas as operações disponíveis no provedor de recursos do catálogo de dados. |
> | Action | Microsoft.DataCatalog/register/action | Registrar a assinatura para o provedor de recursos do catálogo de dados |
> | Action | Microsoft.DataCatalog/unregister/action | Cancelar o registro da assinatura do provedor de recursos do catálogo de dados |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Verifica se o Nome do Data Factory está disponível para uso. |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | Lê Janelas de Atividade no Data Factory com parâmetros especificados. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê Janelas de Atividade para a Atividade do Pipeline com parâmetros especificados. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê Janelas de Atividade do Pipeline com parâmetros especificados. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | Exclui qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausa qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer Pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê Janelas de Atividade para o Conjunto de Dados com parâmetros especificados. |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a Execução da Fatia de Dados do conjunto de dados especificado com a hora de início especificada. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém as Fatias de Dados no período determinado. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualiza o Status da Fatia de Dados. |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/delete | Exclui o Data Factory. |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as Informações de Conexão para qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | Exclui qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as Chaves de Autenticação para qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as Chaves de Autenticação para qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer Gateway. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | Exclui qualquer Serviço Vinculado. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | Lê qualquer Serviço Vinculado. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza qualquer Serviço Vinculado. |
> | Action | Microsoft.DataFactory/datafactories/read | Lê o Data Factory. |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contêiner de blob que contém os logs. |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | Exclui qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/datafactories/write | Cria ou atualiza o Data Factory. |
> | Action | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Cria uma sessão de depuração de fluxo de dados. |
> | Action | Microsoft.DataFactory/factories/dataflows/delete | Exclui o fluxo de dados. |
> | Action | Microsoft.DataFactory/factories/dataflows/read | Lê o fluxo de dados. |
> | Action | Microsoft.DataFactory/factories/dataflows/write | Criar ou atualizar o fluxo de dados |
> | Action | Microsoft.DataFactory/factories/datasets/delete | Exclui qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/factories/datasets/read | Lê qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer Conjunto de Dados. |
> | Action | Microsoft.DataFactory/factories/delete | Excluir o data factory. |
> | Action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Exclui uma sessão de depuração de fluxo de dados. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Obtém acesso ao serviço DataPlane do ADF. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Lê o acesso ao serviço DataPlane do ADF. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/action | Obtém o valor do recurso de controle de exposição para a localização específica. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | Lê o valor do recurso de controle de exposição para a localização específica. |
> | Action | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Obtém o token de acesso do GitHub. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | Exclui qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê Informações de Conexão do Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obtém os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê o Status do Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Cria uma Referência do Integration Runtime Vinculado ao Integration Runtime Compartilhado especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as Chaves de Autenticação para qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os Dados de Monitoramento para qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Exclui o nó para o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retorna o endereço IP para o nó especificado do Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o Nó para o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um Nó do Integration Runtime auto-hospedado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Atualiza os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as Chaves de Autenticação para o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove as Referências do Tempo de Execução de Integração do Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia a qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Interrompe qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as Credenciais para o Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Exclui o Integration Runtime especificado. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | Exclui o Serviço Vinculado. |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | Lê o Serviço Vinculado. |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | Cria ou atualiza o Serviço Vinculado |
> | Action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê as execuções de atividade para a ID de execução de pipeline especificada. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Consulta as execuções de atividade para a ID de execução de pipeline especificada. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Ler o resultado das execuções de atividade de consulta para a ID de execução do pipeline especificado. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução para o Pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | Excluir o pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obter o progresso das execuções de atividade. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê as Execuções de Pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/read | Ler pipeline. |
> | Action | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar pipeline |
> | Action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Consulta as Execuções de Pipeline de Depuração. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as Execuções de Pipeline. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/read | Ler o resultado das execuções de pipeline de consulta. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta as Execuções de Gatilho. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/read | Ler o resultado das execuções de gatilho. |
> | Action | Microsoft.DataFactory/factories/read | Ler Data Factory. |
> | Action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Inicia uma sessão de depuração de fluxo de dados. |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | Lê as Execuções de Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/delete | Exclui qualquer Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/read | Lê qualquer Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/stop/action | Interrompe qualquer Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as Execuções de Gatilho. |
> | Action | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer Gatilho. |
> | Action | Microsoft.DataFactory/factories/write | Criar ou atualizar data factory |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/action | Obtém o valor do recurso de controle de exposição para a localização específica. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | Lê o valor do recurso de controle de exposição para a localização específica. |
> | Action | Microsoft.DataFactory/operations/read | Ler todas as operações no provedor do Microsoft Azure Data Factory. |
> | Action | Microsoft.DataFactory/register/action | Registra a assinatura para o Provedor de Recursos do Data Factory. |
> | Action | Microsoft.DataFactory/unregister/action | Cancela o registro da assinatura para o Provedor de Recursos do Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Excluir uma política de computação. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obter informações sobre uma política de computação. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desvincular uma conta do Data Lake Store de uma conta do Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obter informações sobre uma conta vinculada do Data Lake Store de uma conta do Data Lake Analytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | Excluir uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | Obter informações sobre uma conta DataLakeAnalytics existente. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contêineres de uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obter informações sobre uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | Action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transfere SystemMaxAnalyticsUnits entre contas do DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso de DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/usages/read | Obtém informações de usos de cota de uma assinatura referentes ao uso do DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/operations/read | Obter operações disponíveis do DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/register/action | Registrar a assinatura no DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | Excluir uma conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Habilitar o Key Vault para uma conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Exclui um filtro EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtém um filtro EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Cria ou atualiza um Filtro EventGrid. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/accounts/read | Obter informações sobre uma conta DataLakeStore existente. |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Conceder Superusuário no Data Lake Store quando concedido com o Microsoft.Authorization/roleAssignments/write. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Excluir um provedor de identidade confiável. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obter informações sobre um provedor de identidade confiável. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um provedor de identidade confiável. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Action | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/capability/read | Obter informações de capacidade de uma assinatura sobre o uso do DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verificar a disponibilidade de um nome de conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | Obter o resultado de uma operação de conta DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/usages/read | Obtém informações de usos de cota de uma assinatura referentes ao uso do DataLakeStore. |
> | Action | Microsoft.DataLakeStore/operations/read | Obter operações disponíveis de DataLakeStore. |
> | Action | Microsoft.DataLakeStore/register/action | Registrar assinatura para DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 Aceito |
> | Action | Microsoft.DataMigration/register/action | Registra a assinatura com o provedor do Serviço de Migração de Banco de Dados do Azure |
> | Action | Microsoft.DataMigration/services/addWorker/action | Adiciona um trabalho do DMS aos trabalhos disponíveis do Serviço |
> | Action | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implantado e em execução |
> | Action | Microsoft.DataMigration/services/configureWorker/action | Configura um trabalho do DMS para os trabalhos disponíveis do Serviço |
> | Action | Microsoft.DataMigration/services/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gere uma URL que possa ser usada para executar GET ou PUT nos artefatos do projeto |
> | Action | Microsoft.DataMigration/services/projects/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/projects/files/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/projects/files/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/projects/files/read/action | Obter uma URL que pode ser usada para ler o conteúdo do arquivo |
> | Action | Microsoft.DataMigration/services/projects/files/readWrite/action | Obter uma URL que pode ser usada para ler ou gravar o conteúdo do arquivo |
> | Action | Microsoft.DataMigration/services/projects/files/write | Criar ou atualizar recursos e suas propriedades |
> | Action | Microsoft.DataMigration/services/projects/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancelar a tarefa se ela estiver em execução no momento |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | Executar tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Action | Microsoft.DataMigration/services/projects/write | Executar tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Action | Microsoft.DataMigration/services/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/removeWorker/action | Remove um trabalho do DMS dos trabalhos disponíveis do Serviço |
> | Action | Microsoft.DataMigration/services/serviceTasks/cancel/action | Cancelar a tarefa se ela estiver em execução no momento |
> | Action | Microsoft.DataMigration/services/serviceTasks/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/serviceTasks/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/serviceTasks/write | Executar tarefas do Serviço de Migração de Banco de Dados do Azure |
> | Action | Microsoft.DataMigration/services/slots/delete | Exclui um recurso e todos os seus filhos |
> | Action | Microsoft.DataMigration/services/slots/read | Ler informações sobre recursos |
> | Action | Microsoft.DataMigration/services/slots/write | Criar ou atualizar recursos e suas propriedades |
> | Action | Microsoft.DataMigration/services/start/action | Inicie o serviço do DMS para permitir que ele processe as migrações novamente |
> | Action | Microsoft.DataMigration/services/stop/action | Pare o serviço do DMS para minimizar seu custo |
> | Action | Microsoft.DataMigration/services/updateAgentConfig/action | Atualiza a configuração do agente DMS com os valores fornecidos. |
> | Action | Microsoft.DataMigration/services/write | Criar ou atualizar recursos e suas propriedades |
> | Action | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs compatíveis com os recursos do DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DBforMariaDB/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MariaDB |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar os resultados da operação do servidor MariaDB com base em resourcegroup |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar resultados da operação do servidor MariaDB |
> | Action | Microsoft.DBforMariaDB/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforMariaDB/operations/read | Retornar a lista de operações de MariaDB. |
> | Action | Microsoft.DBforMariaDB/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft.DBforMariaDB/register/action | Registrar provedor de recursos MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/administrators/delete | Exclui um administrador existente do MariaDB Server. |
> | Action | Microsoft.DBforMariaDB/servers/administrators/read | Obtém uma lista de administradores do servidor MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/administrators/write | Cria ou atualiza o administrador do servidor MariaDB com os parâmetros especificados. |
> | Action | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | Retorna a lista de assistentes |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | Retornar um supervisor |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Action | Microsoft.DBforMariaDB/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforMariaDB/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Action | Microsoft.DBforMariaDB/servers/databases/delete | Exclui um banco de dados MariaDB existente. |
> | Action | Microsoft.DBforMariaDB/servers/databases/read | Retornar a lista de bancos de dados MariaDB ou obter as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | Cria um banco de dados MariaDB com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforMariaDB/servers/delete | Excluir um servidor existente. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | Retornar a lista de arquivos de log MariaDB. |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Action | Microsoft.DBforMariaDB/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | Devolver as informações recuperáveis do MariaDB Server |
> | Action | Microsoft.DBforMariaDB/servers/replicas/read | Obter réplicas de leitura de um servidor MariaDB |
> | Action | Microsoft.DBforMariaDB/servers/restart/action | Reinicia um servidor específico. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Action | Microsoft.DBforMariaDB/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DBforMySQL/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MySQL |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL com base no resourcegroup |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforMySQL/operations/read | Retornar a lista de operações do MySQL. |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft.DBforMySQL/register/action | Registrar provedor de recursos MySQL |
> | Action | Microsoft.DBforMySQL/servers/administrators/delete | Exclui um administrador existente do MySQL Server. |
> | Action | Microsoft.DBforMySQL/servers/administrators/read | Obtém uma lista de administradores do servidor MySQL. |
> | Action | Microsoft.DBforMySQL/servers/administrators/write | Cria ou atualiza o administrador do servidor MySQL com os parâmetros especificados. |
> | Action | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | Retorna a lista de assistentes |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | Retornar um supervisor |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Action | Microsoft.DBforMySQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforMySQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Action | Microsoft.DBforMySQL/servers/databases/delete | Exclui um banco de dados MySQL existente. |
> | Action | Microsoft.DBforMySQL/servers/databases/read | Retornar a lista de bancos de dados MySQL ou obter as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforMySQL/servers/databases/write | Cria um banco de dados MySQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforMySQL/servers/delete | Excluir um servidor existente. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Action | Microsoft.DBforMySQL/servers/logFiles/read | Retornar a lista de arquivos de log PostgreSQL. |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores MySQL |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Action | Microsoft.DBforMySQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Action | Microsoft.DBforMySQL/servers/recoverableServers/read | Retornar as informações recuperáveis do MySQL Server |
> | Action | Microsoft.DBforMySQL/servers/replicas/read | Obter réplicas de leitura de um servidor MySQL |
> | Action | Microsoft.DBforMySQL/servers/restart/action | Reinicia um servidor específico. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Action | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Action | Microsoft.DBforMySQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Action | Microsoft.DBforMySQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL com base no resourcegroup |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Action | Microsoft.DBforPostgreSQL/operations/read | Retornar a lista de operações PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | Retornar a lista de Níveis de Desempenho disponíveis. |
> | Action | Microsoft.DBforPostgreSQL/register/action | Registrar provedor de recursos PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/delete | Exclui um administrador existente do servidor PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/read | Obtém uma lista de administradores do servidor PostgreSQL. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/write | Cria ou atualiza o administrador do servidor PostgreSQL com os parâmetros especificados. |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/read | Retorna a lista de assistentes |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | Exclui um banco de dados PostgreSQL existente. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | Retornar a lista de bancos de dados PostgreSQL ou obter as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | Cria um banco de dados PostgreSQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | Excluir um servidor existente. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Action | Microsoft.DBforPostgreSQL/servers/logFiles/read | Retornar a lista de arquivos de log PostgreSQL. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores Postgres |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Retornar os textos de uma lista de consultas |
> | Action | Microsoft.DBforPostgreSQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Retornar as informações recuperáveis do PostgreSQL Server |
> | Action | Microsoft.DBforPostgreSQL/servers/replicas/read | Obter réplicas de leitura de um servidor PostgreSQL |
> | Action | Microsoft.DBforPostgreSQL/servers/restart/action | Reinicia um servidor específico. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em determinado servidor |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Retorne a lista de estatísticas de consulta para as principais consultas. |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Action | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Action | Microsoft.DBforPostgreSQL/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Atualize o valor para a configuração especificada |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | Excluir um servidor existente. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Excluir uma regra de firewall existente. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades para a regra de firewall especificada. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Criar uma regra de firewall com os parâmetros especificados ou atualizar uma regra existente. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para servidores Postgres |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.DBforPostgreSQL/serversv2/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Atualizar as configurações para o servidor especificado |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Devices/checkNameAvailability/Action | Verificar se o nome do IotHub está disponível |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verificar se o nome do serviço de provisionamento está disponível |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Exclui o Certificado |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o Certificado |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verificar recurso de Certificado |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Cria ou atualiza o Certificado |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Excluir o recurso de locatário IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obter o recurso de estatísticas do locatário IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obter a chave de locatário IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obter chaves de locatário IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métrica de cota |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obter o recurso de locatário IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de locatário IotHub |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | Exclui o Certificado |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Action | Microsoft.Devices/iotHubs/certificates/Read | Obtém o Certificado |
> | Action | Microsoft.Devices/iotHubs/certificates/verify/Action | Verificar recurso de Certificado |
> | Action | Microsoft.Devices/iotHubs/certificates/Write | Cria ou atualiza o Certificado |
> | Action | Microsoft.Devices/iotHubs/Delete | Excluir recurso IotHub |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Excluir o filtro de Grade de Eventos |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obter o filtro de Grade de Eventos |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar novo ou atualizar filtro de Grade de Eventos existente |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidores EventHub |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores EventHub |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Action | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obter a chave IotHub para o nome fornecido |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas de IotHub |
> | Action | Microsoft.Devices/iotHubs/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Action | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves IotHub |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Action | Microsoft.Devices/iotHubs/operationresults/Read | Obtém o resultado da operação (API obsoleta) |
> | Action | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métrica de cota |
> | Action | Microsoft.Devices/iotHubs/Read | Obter os recursos IotHub |
> | Action | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem com todas as rotas existentes |
> | Action | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em uma rota de teste fornecida |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obter a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Action | Microsoft.Devices/iotHubs/skus/Read | Obter SKUs IotHub válidos |
> | Action | Microsoft.Devices/iotHubs/Write | Criar ou atualizar recursos IotHub |
> | Action | Microsoft.Devices/locations/operationresults/Read | Obter resultado da operação baseado na localização |
> | Action | Microsoft.Devices/operationresults/Read | Obter Resultado da Operação |
> | Action | Microsoft.Devices/operations/Read | Obter todas as operações de ResourceProvider |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | Exclui o Certificado |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o Certificado |
> | Action | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verificar recurso de Certificado |
> | Action | Microsoft.Devices/provisioningServices/certificates/Write | Cria ou atualiza o Certificado |
> | Action | Microsoft.Devices/provisioningServices/Delete | Excluir recurso IotDps |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter Chaves IotDps para nome da chave |
> | Action | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves IotDps |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | Obter as definições de log disponíveis para o Serviço de provisionamento |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obter as métricas disponíveis para o serviço de provisionamento |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | Obtém o resultado da operação DPS |
> | Action | Microsoft.Devices/provisioningServices/Read | Obter recurso IotDps |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | Obter SKUs IotDps válido |
> | Action | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Action | Microsoft.Devices/register/action | Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub |
> | Action | Microsoft.Devices/register/action | Registrar a assinatura do provedor de recursos IotHub e permitir a criação de recursos IotHub |
> | Action | Microsoft.Devices/usages/Read | Obter detalhes de uso de assinatura para esse provedor. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DevSpaces/controllers/delete | Excluir o Controlador do Azure Dev Spaces e os serviços do plano de dados |
> | Action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Listar detalhes de conexão para a infraestrutura do Controlador de Infraestrutura do Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/controllers/read | Leia as propriedades do Controlador do Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/controllers/write | Criar ou atualizar as propriedades do Controlador do Azure Dev Spaces |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Verificar o mapeamento de controlador existente para um host de contêiner |
> | Action | Microsoft.DevSpaces/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Action | Microsoft.DevSpaces/register/action | Registre o provedor de recursos do Microsoft Dev Spaces com uma assinatura |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | Excluir centrais do laboratório. |
> | Action | Microsoft.DevTestLab/labCenters/read | Ler centrais do laboratório. |
> | Action | Microsoft.DevTestLab/labCenters/write | Adicionar ou modificar as centrais do laboratório. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Ler modelos do Azure Resource Manager. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo do ARM para o artefato determinado, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefatos. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | Excluir fontes de artefato. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/read | Ler fontes de artefato. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | Adicionar ou modificar fontes de artefato. |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | Action | Microsoft.DevTestLab/labs/costs/read | Ler custos. |
> | Action | Microsoft.DevTestLab/labs/costs/write | Adicionar ou modificar custos. |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | Criar máquinas virtuais em um laboratório. |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | Excluir imagens personalizadas. |
> | Action | Microsoft.DevTestLab/labs/customImages/read | Ler imagens personalizadas. |
> | Action | Microsoft.DevTestLab/labs/customImages/write | Adicionar ou modificar imagens personalizadas. |
> | Action | Microsoft.DevTestLab/labs/delete | Excluir laboratórios. |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exportar o uso de recursos de laboratório para uma conta de armazenamento |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Action | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Action | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | Ler imagens da galeria. |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gerar um URI para carregar imagens de disco personalizadas para um laboratório. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/delete | Excluir desligamentos ociosos. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/read | Leitura de desligamentos ociosos. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/write | Adicionar ou modificar desligamentos ociosos. |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importar uma máquina virtual para um laboratório diferente. |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponíveis para a criação de imagens personalizadas. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | Excluir notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Enviar notificação ao canal fornecido. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | Ler notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | Adicionar ou modificar notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avaliar a política de laboratório. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | Excluir políticas. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/read | Ler políticas. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar políticas. |
> | Action | Microsoft.DevTestLab/labs/policySets/read | Leia conjuntos de políticas. |
> | Action | Microsoft.DevTestLab/labs/read | Ler laboratórios. |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | Excluir agendas. |
> | Action | Microsoft.DevTestLab/labs/schedules/Execute/action | Executar uma agenda. |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Listar todas as agendas aplicáveis |
> | Action | Microsoft.DevTestLab/labs/schedules/read | Ler agendas. |
> | Action | Microsoft.DevTestLab/labs/schedules/write | Adicionar ou modificar agendamentos. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | Excluir executores de serviço. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | Ler os executores de serviço. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | Adicionar ou modificar os executores de serviço. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/delete | Excluir galerias compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/read | Ler galerias compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Excluir imagens compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Ler imagens compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Adicionar ou modificar imagens compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/write | Adicionar ou modificar galerias compartilhadas. |
> | Action | Microsoft.DevTestLab/labs/users/delete | Excluir perfis de usuário. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexar e criar a concessão do disco para a máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | Excluir discos. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexar e interromper a concessão do disco anexado à máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | Ler discos. |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | Adicionar ou modificar discos. |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | Excluir ambientes. |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | Ler ambientes. |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | Adicionar ou modificar ambientes. |
> | Action | Microsoft.DevTestLab/labs/users/read | Ler perfis de usuário. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | Excluir segredos. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | Ler segredos. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | Adicionar ou modificar segredos. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Excluir malhas de serviço. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Ler malhas de serviço. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Excluir agendas. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Executar uma agenda. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ler agendas. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicionar ou modificar agendamentos. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Iniciar uma malha de serviço. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Parar uma malha de serviço |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adicionar ou modificar malhas de serviço. |
> | Action | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de usuário. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexar um disco de dados novo ou existente à máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicar artefatos à máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Assumir o controle de uma máquina virtual existente |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | Excluir as máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexar o disco especificado da máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | Ler máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Reimplanta uma máquina virtual |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensiona a máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reiniciar uma máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Excluir agendas. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Executar uma agenda. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ler agendas. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicionar ou modificar agendamentos. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Iniciar uma máquina virtual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transferências de todos os discos de dados anexados à máquina virtual a ser possuída pelo usuário atual. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Liberar propriedade de uma máquina virtual existente |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Excluir redes virtuais. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | Ler redes virtuais. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | Adicionar ou modificar redes virtuais. |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | Exclui pools de máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | Lê pools de máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | Adiciona ou modifica pools de máquinas virtuais. |
> | Action | Microsoft.DevTestLab/labs/write | Adicionar ou modificar os laboratórios. |
> | Action | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Action | Microsoft.DevTestLab/register/action | Registra a assinatura |
> | Action | Microsoft.DevTestLab/schedules/delete | Excluir agendas. |
> | Action | Microsoft.DevTestLab/schedules/Execute/action | Executar uma agenda. |
> | Action | Microsoft.DevTestLab/schedules/read | Ler agendas. |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | Atualizar ID de recurso de destino de uma agenda. |
> | Action | Microsoft.DevTestLab/schedules/write | Adicionar ou modificar agendamentos. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | Verificar a disponibilidade do nome. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Excluir uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Ler uma coleção ou listar todas as coleções. Aplicável somente a tipos de API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Ler uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Atualizar uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Criar ou atualizar uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Excluir um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Ler um contêiner ou listar todos os contêineres. Aplicável somente a tipos de API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Ler uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Atualizar uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Criar ou atualizar um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Excluir um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Excluir um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Ler um grafo ou listar todos os grafos. Aplicável somente a tipos de API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Ler uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Atualizar uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Criar ou atualizar um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Ler um banco de dados ou listar todos os bancos. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Ler uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Atualizar uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Criar um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Excluir um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Ler um keyspace ou listar todos os keyspaces. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Ler uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Atualize uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Crie um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Table '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/backup/action | Envie uma solicitação para configurar o backup |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar o grupo de recursos de uma conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Ler a coleção de definições de métrica. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Ler a métrica da coleção. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler métricas de nível de chave de partição da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas de nível de partição da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Ler as partições da conta do banco de dados em uma coleção |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler usos do nível de partição da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Ler os usos da coleção. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Ler as definições de métrica do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Ler a métrica do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Ler os usos do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | Excluir as contas de banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de failover das regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual |
> | Action | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Obter a política de backup da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de caracteres de conexão para uma conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Ler o banco de dados de definições de métrica da conta. |
> | Action | Microsoft.DocumentDB/databaseAccounts/metrics/read | Ler a métrica da conta do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Torne offline uma região de uma conta de banco de dados.  |
> | Action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Torne online uma região de uma conta de banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Ler métricas de latência |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | Lê percentis de latências de replicação |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região específica de origem e destino |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Ler métricas de latência para uma região de destino específica |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Excluir um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Ler um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Validar um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Criar ou atualizar um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | Ler uma conta de banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Ler as chaves somente leitura da conta do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Girar chaves de uma conta de banco de dados |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Ler as métricas de coleção regional. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler as métricas de nível de chave de partição da conta do banco de dados regional |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Lear as métricas de nível de partição da conta do banco de dados regional |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Lê as partições da conta do banco de dados regional em uma coleção |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Ler a região e as métricas da conta do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | Ler os usos da conta do banco de dados. |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | Atualizar uma conta de banco de dados. |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notificar o Microsoft.DocumentDB que a VirtualNetwork ou Sub-rede está sendo excluída |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Lê o Status da operação assíncrona deleteVirtualNetworkOrSubnets |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | Lê o status de operações assíncronas |
> | Action | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Action | Microsoft.DocumentDB/operations/read | Operações de leitura disponíveis para o Microsoft DocumentDB  |
> | Action | Microsoft.DocumentDB/register/action |  Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verificar se um domínio está disponível para compra |
> | Action | Microsoft.DomainRegistration/domains/Delete | Excluir um modo de exibição existente. |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Excluir o identificador de propriedade |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Listar identificadores de propriedade |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter identificador de propriedade |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar identificador |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | Obter uma operação de domínio |
> | Action | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Action | Microsoft.DomainRegistration/domains/Read | Obter domínio |
> | Action | Microsoft.DomainRegistration/domains/renew/Action | Renovar um domínio existente. |
> | Action | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um existente |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | Gerar uma solicitação para entrar no centro de controle de domínio. |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recuperar as recomendações de domínio da lista com base em palavras-chave |
> | Action | Microsoft.DomainRegistration/operations/Read | Listar todas as operações do registro de domínio do serviço de aplicativo |
> | Action | Microsoft.DomainRegistration/register/action | Registrar o provedor de recursos Microsoft Domains para a assinatura |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listar ação de Contrato |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínios toplevel |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínio toplevel |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.EventGrid/domains/delete | Excluir um domínio |
> | Action | Microsoft.EventGrid/domains/listKeys/action | Listar chaves para um domínio |
> | Action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para domínios |
> | Action | Microsoft.EventGrid/domains/read | Ler um domínio |
> | Action | Microsoft.EventGrid/domains/regenerateKey/action | Regenerar chave para um domínio |
> | Action | Microsoft.EventGrid/domains/topics/read | Ler um tópico de domínio |
> | Action | Microsoft.EventGrid/domains/write | Criar ou atualizar um domínio |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | Excluir um eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter a URL completa para a assinatura de evento |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para assinaturas de eventos |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para assinaturas de eventos |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventSubscriptions |
> | Action | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Action | Microsoft.EventGrid/extensionTopics/read | Lê um extensionTopic. |
> | Action | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Action | Microsoft.EventGrid/locations/operationResults/read | Ler o resultado de uma operação de regional |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | Ler o status de uma operação de regional |
> | Action | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Action | Microsoft.EventGrid/operationResults/read | Ler o resultado de uma operação |
> | Action | Microsoft.EventGrid/operations/read | Lista as operações do EventGrid. |
> | Action | Microsoft.EventGrid/operationsStatus/read | Leia o status de uma operação de leitura |
> | Action | Microsoft.EventGrid/register/action | Registra a assinatura para o provedor de recursos do EventGrid. |
> | Action | Microsoft.EventGrid/topics/delete | Excluir um tópico |
> | Action | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para tópicos |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Action | Microsoft.EventGrid/topics/read | Ler um tópico |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Action | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Action | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Action | Microsoft.EventGrid/topictypes/eventtypes/read | Ler eventTypes compatíveis com um topictype |
> | Action | Microsoft.EventGrid/topictypes/read | Ler um topictype |
> | Action | Microsoft.EventGrid/unregister/action | Cancela o registro da assinatura no provedor de recursos EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/availableClusterRegions/read | Operação de leitura para listar clusters pré-configurados disponíveis pela região do Azure. |
> | Action | Microsoft.EventHub/checkNameAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. |
> | Action | Microsoft.EventHub/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. Essa API foi preterida. Use CheckNameAvailability. |
> | Action | Microsoft.EventHub/clusters/delete | Exclui um recurso de cluster existente. |
> | Action | Microsoft.EventHub/clusters/namespaces/read | Listar IDs de ARM de namespace para namespaces em um cluster. |
> | Action | Microsoft.EventHub/clusters/operationresults/read | Obtenha o status de uma operação de cluster assíncrona. |
> | Action | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém a lista de Descrições de Recursos de Métrica de Cluster |
> | Action | Microsoft.EventHub/clusters/read | Obtém a Descrição do Recurso de Cluster |
> | Action | Microsoft.EventHub/clusters/write | Cria ou modifica um recurso de cluster existente. |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras de VNet no Provedor de Recursos de EventHub para a VNet especificada |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter a Cadeia de Conexão para o Namespace |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | Obter a lista de descrições de Regras de Autorização de Namespace. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Action | Microsoft.EventHub/namespaces/Delete | Excluir Recurso de Namespace |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves de regras de autorização para o namespace principal de recuperação de desastre |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace principal de recuperação de desastre |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a Recuperação de desastre e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de GEO DR e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração de Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar EventHub. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para excluir regras de autorização EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Criar regras de autorização EventHub e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para excluir o recurso ConsumerGroup |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos ConsumerGroup |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Criar ou atualizar propriedades ConsumerGroup. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para excluir o recurso EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou atualizar propriedades EventHub. |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/delete | Excluir Recurso de Filtro IP |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/read | Obter Recurso de Filtro IP |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/write | Criar Recurso de Filtro IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Enviar mensagens |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft. EventHub/namespaces/networkruleset/Delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft. EventHub/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Action | Microsoft. EventHub/namespaces/networkruleset/Write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.EventHub/namespaces/operationresults/read | Obter o status da operação do Namespace |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Action | Microsoft.EventHub/namespaces/read | Obter a lista de Descrições dos Recursos de Namespace |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remover namespace de ACS |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra VNET |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.EventHub/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Action | Microsoft.EventHub/operations/read | Obter Operações |
> | Action | Microsoft.EventHub/register/action | Registra a assinatura para o provedor de recursos do EventHub e habilita a criação dos recursos do EventBus |
> | Action | Microsoft.EventHub/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Action | Microsoft.EventHub/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Action | Microsoft.EventHub/unregister/action | Registra o Provedor de Recurso do EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Features/features/read | Obtém os recursos de uma assinatura. |
> | Action | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Action | Microsoft.Features/providers/features/read | Obtém o recurso de uma assinatura em um determinado provedor de recursos. |
> | Action | Microsoft.Features/providers/features/register/action | Registra o recurso de uma assinatura em um determinado provedor de recursos. |
> | Action | Microsoft.Features/providers/features/unregister/action | Cancela registro do recurso para uma assinatura em um determinado provedor de recursos. |
> | Action | Microsoft.Features/register/action | Registra o recurso de uma assinatura. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Excluir atribuição de configuração de convidado. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obter atribuição de configuração de convidado. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Obtenha o relatório de atribuição de configuração de convidado. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Criar uma nova atribuição de configuração de convidado. |
> | Action | Microsoft.GuestConfiguration/register/action | Registra a assinatura para o provedor de recursos Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.HDInsight/clusters/applications/delete | Excluir o aplicativo do cluster do HDInsight |
> | Action | Microsoft.HDInsight/clusters/applications/read | Obter aplicativo para o cluster do HDInsight |
> | Action | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar aplicativo para o cluster do HDInsight |
> | Action | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar configuração de RDP para Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração do Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/configurations/action | Obter configurações de Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/delete | Excluir um cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter configurações de gateway para o cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para o recurso do Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o Cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as configurações do gateway para o cluster HDInsight |
> | Action | Microsoft.HDInsight/clusters/write | Criar ou atualizar o Cluster HDInsight |
> | Action | Microsoft.HDInsight/locations/capabilities/read | Obter recursos de assinatura |
> | Action | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar Disponibilidade do Nome |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ImportExport/jobs/delete | Excluir um trabalho existente. |
> | Action | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obter as chaves do BitLocker para o trabalho especificado. |
> | Action | Microsoft.ImportExport/jobs/read | Obter as propriedades para o trabalho especificado ou retornar a lista de trabalhos. |
> | Action | Microsoft.ImportExport/jobs/write | Criar um trabalho com os parâmetros especificados ou atualizar as propriedades ou marcações do trabalho especificado. |
> | Action | Microsoft.ImportExport/locations/read | Obter as propriedades do local especificado ou retornar a lista de locais. |
> | Action | Microsoft.ImportExport/register/action | Registra a assinatura do provedor de recursos importar/exportar e habilita a criação de trabalhos de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | Excluir um grupo de ações |
> | Action | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ações |
> | Action | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do Log de Atividades ativado |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | Excluir um alerta do log de atividades |
> | Action | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do log de atividades |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta do log de atividades |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássico ativado |
> | Action | Microsoft.Insights/AlertRules/Delete | Excluir um alerta de métrica clássico |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | Ler um incidente de alerta de métrica clássico |
> | Action | Microsoft.Insights/AlertRules/Read | Ler um alerta de métrica clássico |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássico resolvido |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássico acelerada |
> | Action | Microsoft.Insights/AlertRules/Write | Criar ou atualizar um alerta de métrica clássico |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | Excluir uma configuração de dimensionamento automático |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de log |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | Ler uma configuração de dimensionamento automático |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução do dimensionamento automático iniciada |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução do dimensionamento automático concluída |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Aumento do dimensionamento automático iniciado |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Aumento do dimensionamento automático concluído |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma configuração de dimensionamento automático |
> | Action | Microsoft.Insights/Baseline/Read | Ler uma linha de base de métrica (versão prévia) |
> | Action | Microsoft.Insights/CalculateBaseline/Read | Calcular a linha de base dos valores de métrica (versão prévia) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Excluindo um item de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Read | Lendo um item de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Write | Gravando um item de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Action | Ação da tabela de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | Excluindo um esquema de tabela de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Read | Lendo um esquema de tabela de análise do Application Insights |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Write | Gravando um esquema de tabela de análise do Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Delete | Excluindo uma anotação do Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Read | Lendo uma anotação do Application Insights |
> | Action | Microsoft.Insights/Components/Annotations/Write | Gravando uma anotação do Application Insights |
> | Action | Microsoft.Insights/Components/Api/Read | Lendo a API de dados do componente do Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Action | Gerando uma chave de API do Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | Excluindo uma chave de API do Application Insights |
> | Action | Microsoft.Insights/Components/ApiKeys/Read | Lendo uma chave de API do Application Insights |
> | Action | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lendo um plano de cobrança do componente do Application Insights |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lendo os recursos de cobrança atuais do componente do Application Insights |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Gravando os recursos de cobrança atuais do componente do Application Insights |
> | Action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lendo uma configuração de integração padrão de ALM do Application Insights |
> | Action | Microsoft.Insights/Components/Delete | Excluindo uma configuração de componente do Application Insights |
> | Action | Microsoft.Insights/Components/Events/Read | Obter logs do Application Insights usando o formato de consulta OData |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Action | Ação de configurações de exportação do Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Delete | Excluindo as configurações de exportação do Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Read | Lendo as configurações de exportação do Application Insights |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Write | Gravando as configurações de exportação do Application Insights |
> | Action | Microsoft.Insights/Components/ExtendQueries/Read | Lendo os resultados da consulta estendida do componente do Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Delete | Excluindo um favorito do Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Read | Lendo um favorito do Application Insights |
> | Action | Microsoft.Insights/Components/Favorites/Write | Gravando um favorito do Application Insights |
> | Action | Microsoft.Insights/Components/FeatureCapabilities/Read | Lendo as capacidades do recurso do componente do Application Insights |
> | Action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lendo os recursos de cobrança disponíveis do componente do Application Insights |
> | Action | Microsoft.Insights/Components/GetToken/Read | Lendo um token do componente do Application Insights |
> | Action | Microsoft.Insights/Components/MetricDefinitions/Read | Lendo as definições de métrica do componente do Application Insights |
> | Action | Microsoft.Insights/Components/Metrics/Read | Lendo as métricas do componente do Application Insights |
> | Action | Microsoft.Insights/Components/Move/Action | Mover um Componente do Application Insights para outra assinatura ou outro grupo de recursos |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Excluindo um item de análise pessoal do Application Insights |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lendo um item de análise pessoal do Application Insights |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Write | Gravando um item de análise pessoal do Application Insights |
> | Action | Microsoft.Insights/Components/MyFavorites/Read | Lendo um favorito pessoal do Application Insights |
> | Action | Microsoft.Insights/Components/Operations/Read | Obter o status de operações de longa duração no Application Insights |
> | Action | Microsoft.Insights/Components/PricingPlans/Read | Lendo um plano de preços do componente do Application Insights |
> | Action | Microsoft.Insights/Components/PricingPlans/Write | Gravando um plano de preços do componente do Application Insights |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lendo a configuração de detecção proativa do Application Insights |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Gravando a configuração de detecção proativa do Application Insights |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Action | Microsoft.Insights/Components/Purge/Action | Limpando dados do Application Insights |
> | Action | Microsoft.Insights/Components/Query/Read | Executar consultas em logs do Application Insights |
> | Action | Microsoft.Insights/Components/QuotaStatus/Read | Lendo o status de cota do componente do Application Insights |
> | Action | Microsoft.Insights/Components/Read | Lendo uma configuração de componente do Application Insights |
> | Action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lendo as localizações de teste na Web do Application Insights |
> | Action | Microsoft.Insights/Components/Webtests/Read | Lendo uma configuração de teste da web |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Delete | Excluindo uma configuração de integração de ALM do Application Insights |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Read | Lendo uma configuração de integração de ALM do Application Insights |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Write | Gravando uma configuração de integração de ALM do Application Insights |
> | Action | Microsoft.Insights/Components/Write | Gravando em uma configuração de componente do Application Insights |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | Excluir uma configuração de diagnóstico de recurso |
> | Action | Microsoft.Insights/DiagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Action | Microsoft.Insights/DiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Action | Microsoft.Insights/EventCategories/Read | Ler categorias de evento do Log de Atividades disponíveis |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | Ler resumo do tipo de evento de gerenciamento |
> | Action | Microsoft.Insights/eventtypes/values/Read | Ler eventos do Log de Atividades |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Excluir uma configuração de diagnóstico do log de fluxo de rede |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma configuração de diagnóstico do log de fluxo de rede |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico do log de fluxo de rede |
> | Action | Microsoft.Insights/ListMigrationDate/Action | Recuperar a data de migração da Assinatura |
> | Action | Microsoft.Insights/ListMigrationDate/Read | Recuperar a data de migração da assinatura |
> | Action | Microsoft.Insights/LogDefinitions/Read | Ler definições de log |
> | Action | Microsoft.Insights/LogProfiles/Delete | Excluir um perfil do Log de Atividades |
> | Action | Microsoft.Insights/LogProfiles/Read | Ler um perfil do Log de Atividades |
> | Action | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil do Log de Atividades |
> | Action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Ler dados da tabela ADAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/ADReplicationResult/Read | Ler dados da tabela ADReplicationResult |
> | Action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/Alert/Read | Ler dados da tabela Alerta |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | Ler dados da tabela AlertHistory |
> | Action | Microsoft.Insights/Logs/ApplicationInsights/Read | Ler dados da tabela ApplicationInsights |
> | Action | Microsoft.Insights/Logs/AzureActivity/Read | Ler dados da tabela AzureActivity |
> | Action | Microsoft.Insights/Logs/AzureMetrics/Read | Ler dados da tabela AzureMetrics |
> | Action | Microsoft.Insights/Logs/BoundPort/Read | Ler dados da tabela BoundPort |
> | Action | Microsoft.Insights/Logs/CommonSecurityLog/Read | Ler dados da tabela CommonSecurityLog |
> | Action | Microsoft.Insights/Logs/ComputerGroup/Read | Ler dados da tabela ComputerGroup |
> | Action | Microsoft.Insights/Logs/ConfigurationChange/Read | Ler dados da tabela ConfigurationChange |
> | Action | Microsoft.Insights/Logs/ConfigurationData/Read | Ler dados da tabela ConfigurationData |
> | Action | Microsoft.Insights/Logs/ContainerImageInventory/Read | Ler dados da tabela ContainerImageInventory |
> | Action | Microsoft.Insights/Logs/ContainerInventory/Read | Ler dados da tabela ContainerInventory |
> | Action | Microsoft.Insights/Logs/ContainerLog/Read | Ler dados da tabela ContainerLog |
> | Action | Microsoft.Insights/Logs/ContainerServiceLog/Read | Ler dados da tabela ContainerServiceLog |
> | Action | Microsoft.Insights/Logs/DeviceAppCrash/Read | Ler dados da tabela DeviceAppCrash |
> | Action | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Ler dados da tabela DeviceAppLaunch |
> | Action | Microsoft.Insights/Logs/DeviceCalendar/Read | Ler dados da tabela DeviceCalendar |
> | Action | Microsoft.Insights/Logs/DeviceCleanup/Read | Ler dados da tabela DeviceCleanup |
> | Action | Microsoft.Insights/Logs/DeviceConnectSession/Read | Ler dados da tabela DeviceConnectSession |
> | Action | Microsoft.Insights/Logs/DeviceEtw/Read | Ler dados da tabela DeviceEtw |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Ler dados da tabela DeviceHardwareHealth |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | Ler dados da tabela DeviceHealth |
> | Action | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Ler dados da tabela DeviceHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Ler dados da tabela DeviceSkypeSignIn |
> | Action | Microsoft.Insights/Logs/DeviceSleepState/Read | Ler dados da tabela DeviceSleepState |
> | Action | Microsoft.Insights/Logs/DHAppFailure/Read | Ler dados da tabela DHAppFailure |
> | Action | Microsoft.Insights/Logs/DHAppReliability/Read | Ler dados da tabela DHAppReliability |
> | Action | Microsoft.Insights/Logs/DHDriverReliability/Read | Ler dados da tabela DHDriverReliability |
> | Action | Microsoft.Insights/Logs/DHLogonFailures/Read | Ler dados da tabela DHLogonFailures |
> | Action | Microsoft.Insights/Logs/DHLogonMetrics/Read | Ler dados da tabela DHLogonMetrics |
> | Action | Microsoft.Insights/Logs/DHOSCrashData/Read | Ler dados da tabela DHOSCrashData |
> | Action | Microsoft.Insights/Logs/DHOSReliability/Read | Ler dados da tabela DHOSReliability |
> | Action | Microsoft.Insights/Logs/DHWipAppLearning/Read | Ler dados da tabela DHWipAppLearning |
> | Action | Microsoft.Insights/Logs/DnsEvents/Read | Ler dados da tabela DnsEvents |
> | Action | Microsoft.Insights/Logs/DnsInventory/Read | Ler dados da tabela DnsInventory |
> | Action | Microsoft.Insights/Logs/ETWEvent/Read | Ler dados da tabela ETWEvent |
> | Action | Microsoft.Insights/Logs/Event/Read | Ler dados da tabela Eventos |
> | Action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/Heartbeat/Read | Ler dados da tabela Pulsação |
> | Action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Ler dados da tabela IISAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/InboundConnection/Read | Ler dados da tabela InboundConnection |
> | Action | Microsoft.Insights/Logs/KubeNodeInventory/Read | Ler dados da tabela KubeNodeInventory |
> | Action | Microsoft.Insights/Logs/KubePodInventory/Read | Ler dados da tabela KubePodInventory |
> | Action | Microsoft.Insights/Logs/LinuxAuditLog/Read | Ler dados da tabela LinuxAuditLog |
> | Action | Microsoft.Insights/Logs/MAApplication/Read | Ler dados da tabela MAApplication |
> | Action | Microsoft.Insights/Logs/MAApplicationHealth/Read | Ler dados da tabela MAApplicationHealth |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Action | Microsoft.Insights/Logs/MAApplicationInstance/Read | Ler dados da tabela MAApplicationInstance |
> | Action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Ler dados da tabela MAApplicationReadiness |
> | Action | Microsoft.Insights/Logs/MADeploymentPlan/Read | Ler dados da tabela MADeploymentPlan |
> | Action | Microsoft.Insights/Logs/MADevice/Read | Ler dados da tabela MADevice |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Action | Microsoft.Insights/Logs/MADeviceReadiness/Read | Ler dados da tabela MADeviceReadiness |
> | Action | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MADriverReadiness/Read | Ler dados da tabela MADriverReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddin/Read | Ler dados da tabela MAOfficeAddin |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Ler dados da tabela MAOfficeAddinHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Ler dados da tabela MAOfficeAddinInstance |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Ler dados da tabela MAOfficeAddinReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeApp/Read | Ler dados da tabela MAOfficeApp |
> | Action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Ler dados da tabela MAOfficeAppHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Ler dados da tabela MAOfficeAppInstance |
> | Action | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Ler dados da tabela MAOfficeAppReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Ler dados da tabela MAOfficeBuildInfo |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Ler dados da tabela MAOfficeMacroSummary |
> | Action | Microsoft.Insights/Logs/MAOfficeSuite/Read | Ler dados da tabela MAOfficeSuite |
> | Action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Ler dados da tabela MAOfficeSuiteInstance |
> | Action | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Ler dados da tabela MAProposedPilotDevices |
> | Action | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Ler dados da tabela MAWindowsBuildInfo |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft.Insights/Logs/NetworkMonitoring/Read | Ler dados da tabela NetworkMonitoring |
> | Action | Microsoft.Insights/Logs/OfficeActivity/Read | Ler dados da tabela OfficeActivity |
> | Action | Microsoft.Insights/Logs/Operation/Read | Ler dados da tabela Operação |
> | Action | Microsoft.Insights/Logs/OutboundConnection/Read | Ler dados da tabela OutboundConnection |
> | Action | Microsoft.Insights/Logs/Perf/Read | Ler dados da tabela Desempenho |
> | Action | Microsoft.Insights/Logs/ProtectionStatus/Read | Ler dados da tabela ProtectionStatus |
> | Action | Microsoft.Insights/Logs/Read | Lendo dados de todos os seus logs |
> | Action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Ler dados da tabela ReservedAzureCommonFields |
> | Action | Microsoft.Insights/Logs/ReservedCommonFields/Read | Ler dados da tabela ReservedCommonFields |
> | Action | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SecurityAlert/Read | Ler dados da tabela SecurityAlert |
> | Action | Microsoft.Insights/Logs/SecurityBaseline/Read | Ler dados da tabela SecurityBaseline |
> | Action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Ler dados da tabela SecurityBaselineSummary |
> | Action | Microsoft.Insights/Logs/SecurityDetection/Read | Ler dados da tabela SecurityDetection |
> | Action | Microsoft.Insights/Logs/SecurityEvent/Read | Ler dados da tabela SecurityEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Ler dados da tabela SfBAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Ler dados da tabela SPAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Ler dados da tabela SQLAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Ler dados da tabela SQLQueryPerformance |
> | Action | Microsoft.Insights/Logs/Syslog/Read | Ler dados da tabela Syslog |
> | Action | Microsoft.Insights/Logs/SysmonEvent/Read | Ler dados da tabela SysmonEvent |
> | Action | Microsoft.Insights/Logs/UAApp/Read | Ler dados da tabela UAApp |
> | Action | Microsoft.Insights/Logs/UAComputer/Read | Ler dados da tabela UAComputer |
> | Action | Microsoft.Insights/Logs/UAComputerRank/Read | Ler dados da tabela UAComputerRank |
> | Action | Microsoft.Insights/Logs/UADriver/Read | Ler dados da tabela UADriver |
> | Action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Ler dados da tabela UADriverProblemCodes |
> | Action | Microsoft.Insights/Logs/UAFeedback/Read | Ler dados da tabela UAFeedback |
> | Action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Ler dados da tabela UAHardwareSecurity |
> | Action | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Ler dados da tabela UAIESiteDiscovery |
> | Action | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Ler dados da tabela UAOfficeAddIn |
> | Action | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Ler dados da tabela UAProposedActionPlan |
> | Action | Microsoft.Insights/Logs/UASysReqIssue/Read | Ler dados da tabela UASysReqIssue |
> | Action | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Ler dados da tabela UAUpgradedComputer |
> | Action | Microsoft.Insights/Logs/Update/Read | Ler dados da tabela Atualização |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Ler dados da tabela UpdateRunProgress |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | Ler dados da tabela UpdateSummary |
> | Action | Microsoft.Insights/Logs/Usage/Read | Ler os dados da tabela Uso |
> | Action | Microsoft.Insights/Logs/W3CIISLog/Read | Ler dados da tabela W3CIISLog |
> | Action | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Ler dados da tabela WaaSDeploymentStatus |
> | Action | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Ler dados da tabela WaaSInsiderStatus |
> | Action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Ler dados da tabela WaaSUpdateStatus |
> | Action | Microsoft.Insights/Logs/WDAVStatus/Read | Ler dados da tabela WDAVStatus |
> | Action | Microsoft.Insights/Logs/WDAVThreat/Read | Ler dados da tabela WDAVThreat |
> | Action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/WindowsFirewall/Read | Ler dados da tabela WindowsFirewall |
> | Action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Action | Microsoft.Insights/Logs/WireData/Read | Ler dados da tabela WireData |
> | Action | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Ler dados da tabela WUDOAggregatedStatus |
> | Action | Microsoft.Insights/Logs/WUDOStatus/Read | Ler dados da tabela WUDOStatus |
> | Action | Microsoft.Insights/MetricAlerts/Delete | Excluir um alerta de métrica |
> | Action | Microsoft.Insights/MetricAlerts/Read | Ler um alerta de métrica |
> | Action | Microsoft.Insights/MetricAlerts/Status/Read | Ler status de alerta de métrica |
> | Action | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Action | Microsoft.Insights/MetricBaselines/Read | Ler linhas de base de métrica |
> | Action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métrica |
> | Action | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métrica |
> | Action | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Action | Microsoft.Insights/Metricnamespaces/Read | Ler namespaces de métrica |
> | Action | Microsoft.Insights/Metrics/Action | Ação de Métrica |
> | Action | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Ler as métricas |
> | Action | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler as métricas |
> | Action | Microsoft.Insights/Metrics/Read | Ler as métricas |
> | DataAction | Microsoft.Insights/Metrics/Write | Gravar métricas |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar a assinatura para o novo modelo de preços |
> | Action | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Action | Microsoft.Insights/Register/Action | Registre o provedor do Microsoft Insights |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Reverter a assinatura para o modelo de preços herdado |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | Excluindo uma regra de consulta agendada |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | Lendo uma regra de consulta agendada |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | Escrevendo uma regra de consulta agendada |
> | Action | Microsoft.Insights/Tenants/Register/Action | Inicializa o provedor do Microsoft Insights |
> | Action | Microsoft.Insights/Unregister/Action | Registre o provedor do Microsoft Insights |
> | Action | Microsoft.Insights/Webtests/Delete | Excluindo uma configuração de teste da web |
> | Action | Microsoft.Insights/Webtests/GetToken/Read | Lendo um token de teste na Web |
> | Action | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lendo as definições de métrica do teste na Web |
> | Action | Microsoft.Insights/Webtests/Metrics/Read | Lendo as métricas de um teste na Web |
> | Action | Microsoft.Insights/Webtests/Read | Lendo uma configuração de teste da web |
> | Action | Microsoft.Insights/Webtests/Write | Gravando em uma configuração de teste da web |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Leitura de uma configuração de diagnóstico |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Gravação de uma configuração de diagnóstico |
> | Action | Microsoft.Intune/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.IoTCentral/appTemplates/action | Obtém todos os modelos de aplicativo disponíveis no Azure IoT Central |
> | Action | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se um nome de aplicativo do IoT Central está disponível |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se um subdomínio do aplicativo do IoT Central está disponível |
> | Action | Microsoft.IoTCentral/IoTApps/delete | Exclui um aplicativo do IoT Central |
> | Action | Microsoft.IoTCentral/IoTApps/read | Obtém um único aplicativo do IoT Central |
> | Action | Microsoft.IoTCentral/IoTApps/write | Cria ou atualiza um aplicativo do IoT Central |
> | Action | Microsoft.IoTCentral/operations/read | Obtém todas as operações disponíveis em aplicativos de IoT Central |
> | Action | Microsoft.IoTCentral/register/action | Registrar a assinatura do provedor de recursos do IoT Central do Azure |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.IoTSpaces/Graph/delete | Exclui o recurso Microsoft.IoTSpaces Graph |
> | Action | Microsoft.IoTSpaces/Graph/read | Obtém os recursos Microsoft.IoTSpaces Graph |
> | Action | Microsoft.IoTSpaces/Graph/write | Cria o recurso Microsoft.IoTSpaces Graph |
> | Action | Microsoft.IoTSpaces/register/action | Registra a assinatura no provedor de recursos Microsoft.IoTSpaces Graph para habilitar a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.KeyVault/checkNameAvailability/read | Verifica se um nome do cofre de chaves é válido e se não está em uso |
> | Action | Microsoft.KeyVault/deletedVaults/read | Exiba as propriedades de cofres de chaves temporários excluídos |
> | Action | Microsoft.KeyVault/hsmPools/delete | Excluir um pool do HSM |
> | Action | Microsoft.KeyVault/hsmPools/joinVault/action | Ingressar em um cofre de chaves para um pool do HSM |
> | Action | Microsoft.KeyVault/hsmPools/read | Exibir as propriedades de um pool do HSM |
> | Action | Microsoft.KeyVault/hsmPools/write | Criar um novo pool do HSM de atualização das propriedades de um pool do HSM existente |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves temporário excluído |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | Exiba as propriedades de um cofre de chaves temporário excluído |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.KeyVault que uma rede virtual ou sub-rede está sendo excluída |
> | Action | Microsoft.KeyVault/locations/operationResults/read | Verifique o resultado de uma operação de longa execução |
> | Action | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | Action | Microsoft.KeyVault/register/action | Registra uma assinatura |
> | Action | Microsoft.KeyVault/unregister/action | Cancela o registro de uma assinatura |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | Atualize uma política de acesso existente ao mesclar, substituir ou adicionar uma nova política de acesso a um cofre. |
> | Action | Microsoft.KeyVault/vaults/delete | Excluir um cofre de chaves |
> | Action | Microsoft.KeyVault/vaults/deploy/action | Habilita o acesso aos segredos em um cofre de chaves ao implantar os recursos do Azure |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifica o Microsoft.KeyVault que uma assinatura EventGrid para o Key Vault está sendo excluída |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifica o Microsoft.KeyVault que uma assinatura EventGrid para o Key Vault está sendo exibida |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifica o Microsoft.KeyVault que está sendo criada uma nova assinatura EventGrid para o Key Vault |
> | Action | Microsoft.KeyVault/vaults/read | Exibir as propriedades de um cofre de chaves |
> | Action | Microsoft.KeyVault/vaults/secrets/read | Exiba as propriedades de um segredo, mas não seu valor. |
> | Action | Microsoft.KeyVault/vaults/secrets/write | Crie um novo segredo ou atualize o valor de um segredo existente. |
> | Action | Microsoft.KeyVault/vaults/write | Crie um novo cofre de chaves ou atualize as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Kusto/Clusters/Activate/action | Inicia o cluster. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Exclui um recurso de configuração de banco de dados anexado. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Lê um recurso de configuração de banco de dados anexado. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Grava um recurso de configuração de banco de dados anexado. |
> | Action | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Verifica a disponibilidade do nome do cluster. |
> | Action | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adiciona entidades de banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Verifica a disponibilidade do nome de um determinado tipo. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Exclui um recurso de conexões de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lê um recurso de conexões de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Grava um recurso de conexões de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valida a conexão de dados do banco de dado. |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | Exclui um recurso de banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Exclui um recurso de conexões do hub de eventos. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê um recurso de conexões do hub de eventos. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Grava um recurso de conexões do hub de eventos. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valida a conexão do hub de eventos do banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Lista as entidades de segurança do banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/read | Lê um recurso de banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Remove as entidades de segurança do banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Databases/write | Grava um recurso de banco de dados. |
> | Action | Microsoft.Kusto/Clusters/Deactivate/action | Interrompe o cluster. |
> | Action | Microsoft.Kusto/Clusters/delete | Exclui um recurso de cluster. |
> | Action | Microsoft.Kusto/Clusters/read | Lê um recurso de cluster. |
> | Action | Microsoft.Kusto/Clusters/SKUs/read | Lê um recurso de SKU do cluster. |
> | Action | Microsoft. Kusto/clusters/iniciar/ação | Inicia o cluster. |
> | Action | Microsoft. Kusto/clusters/parar/ação | Interrompe o cluster. |
> | Action | Microsoft.Kusto/Clusters/write | Grava um recurso de cluster. |
> | Action | Microsoft.Kusto/Locations/CheckNameAvailability/action | Verifica a disponibilidade do nome do recurso. |
> | Action | Microsoft.Kusto/locations/operationresults/read | Lê os recursos de operações |
> | Action | Microsoft.Kusto/Operations/read | Lê os recursos de operações |
> | Action | Microsoft. Kusto/registro/ação | Registra a assinatura no provedor de recursos Kusto. |
> | Action | Microsoft.Kusto/SKUs/read | Lê um recurso de SKU. |
> | Action | Microsoft. Kusto/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | Criar um laboratório em uma conta de laboratório. |
> | Action | Microsoft.LabServices/labAccounts/delete | Excluir contas de laboratório. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | Excluir imagens da galeria. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | Ler imagens da galeria. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | Adicionar ou modificar imagens da Galeria. |
> | Action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obter informações sobre disponibilidade regional para cada categoria de tamanho configurado em uma conta de laboratório |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Delete | Exclua idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Read | Leia idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Write | Adicionar ou modificar idleshutdowns. |
> | Action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar usuários a um laboratório |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | Excluir laboratórios. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Declara um ambiente aleatório para um usuário nas configurações de um ambiente |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Excluir configuração de ambiente. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Declara o ambiente e o atribui ao usuário |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Excluir ambientes. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ler ambientes. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Reiniciar a senha do usuário em um ambiente |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adicionar ou modificar ambientes. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisionar/desprovisionar recursos necessários para uma configuração de ambiente com base no estado atual da configuração de ambiente/laboratório. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler configuração de ambiente. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Redefine a senha na máquina virtual do modelo. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Salva a imagem de modelo atual na Galeria compartilhada na conta do laboratório |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Excluir agendas. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Ler agendas. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Adicionar ou modificar agendamentos. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Interrompe um modelo interrompendo todos os recursos dentro do modelo. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar configuração de ambiente. |
> | Action | Microsoft.LabServices/labAccounts/labs/read | Ler laboratórios. |
> | Action | Microsoft.LabServices/labAccounts/labs/Register/action | Registra no laboratório gerenciado. |
> | Action | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Envia um email com o link de registro para o laboratório |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | Excluir usuários. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | Ler usuários. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | Adicionar ou modificar usuários. |
> | Action | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar os laboratórios. |
> | Action | Microsoft.LabServices/labAccounts/read | Ler contas de laboratório. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Exclui as galerias compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Lê as galerias compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Adiciona ou modifica as galerias compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | Exclui as imagens compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | Lê as imagens compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | Adiciona ou modifica as imagens compartilhadas. |
> | Action | Microsoft.LabServices/labAccounts/write | Adicionar ou modificar contas de laboratório. |
> | Action | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Action | Microsoft.LabServices/register/action | Registra a assinatura |
> | Action | Microsoft.LabServices/users/GetEnvironment/action | Obtém os detalhes da máquina virtual |
> | Action | Microsoft.LabServices/users/GetOperationBatchStatus/action | Obter o status da operação de imagem |
> | Action | Microsoft.LabServices/users/GetOperationStatus/action | Obtém o status da operação de execução longa |
> | Action | Microsoft.LabServices/users/GetPersonalPreferences/action | Obter preferências pessoais para um usuário |
> | Action | Microsoft.LabServices/users/ListAllEnvironments/action | Listar todos os ambientes do usuário |
> | Action | Microsoft.LabServices/users/ListEnvironments/action | Lista Ambientes para o usuário |
> | Action | Microsoft.LabServices/users/ListLabs/action | Lista os laboratórios para o usuário. |
> | Action | Microsoft.LabServices/users/Register/action | Registra um usuário em um laboratório gerenciado |
> | Action | Microsoft.LabServices/users/ResetPassword/action | Reiniciar a senha do usuário em um ambiente |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Action | Microsoft.LabServices/users/StopEnvironment/action | Interrompe um ambiente interrompendo todos os recursos dentro do ambiente |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | Exclui o contrato na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | Lê o contrato na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | Cria ou atualiza o contrato na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | Exclui o assembly na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | Lê o assembly na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | Cria ou atualiza o assembly na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Exclui a configuração do lote na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lê a configuração do lote na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Cria ou atualiza a configuração do lote na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | Exclui o certificado na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | Lê o certificado da conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | Cria ou atualiza o certificado da conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/delete | Exclui a conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/join/action | Realiza a junção da Conta de Integração. |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém a URL de retorno de chamada para conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtém as chaves no cofre de chaves. |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra os eventos de acompanhamento na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | Exclui o mapa na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | Lê o mapa na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | Cria ou atualiza o mapa na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | Exclui o parceiro na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo de parceiro de conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | Lê o parceiro na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | Cria ou atualiza o parceiro de conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de log da Conta de Integração. |
> | Action | Microsoft.Logic/integrationAccounts/read | Lê a conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da tecla de acesso. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | Exclui o esquema na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | Lê o esquema na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | Cria ou atualiza o esquema na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | Exclui a sessão na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | Lê a configuração do lote na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | Cria ou atualiza a sessão na conta de integração. |
> | Action | Microsoft.Logic/integrationAccounts/write | Cria ou atualiza a conta de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | Exclui o ambiente do serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | Une o Ambiente do Serviço de Integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação de API gerenciada do ambiente do serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerenciada do ambiente do serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lê os status de operação do ambiente de serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do ambiente do serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operação de recomendação do fluxo de trabalho. |
> | Action | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Action | Microsoft.Logic/operations/read | Obtém a operação. |
> | Action | Microsoft.Logic/register/action | Registra o provedor de recursos Microsoft.Logic de uma determinada assinatura. |
> | Action | Microsoft.Logic/workflows/accessKeys/delete | Exclui a tecla de acesso. |
> | Action | Microsoft.Logic/workflows/accessKeys/list/action | Lista os segredos da tecla de acesso. |
> | Action | Microsoft.Logic/workflows/accessKeys/read | Lê a tecla de acesso. |
> | Action | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da tecla de acesso. |
> | Action | Microsoft.Logic/workflows/accessKeys/write | Cria ou atualiza a tecla de acesso. |
> | Action | Microsoft.Logic/workflows/delete | Exclui o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/disable/action | Desabilita o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/enable/action | Habilita o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | Obtém a URL de retorno de chamada para fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/listSwagger/action | Obtém definições de swagger do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/move/action | Move o Fluxo de Trabalho de sua ID de assinatura, grupo de recursos e/ou nome existente para uma ID de assinatura, grupo de recursos e/ou nome diferente. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lê as configurações de diagnóstico de fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico de fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de log de fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica de fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/read | Lê o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da tecla de acesso. |
> | Action | Microsoft.Logic/workflows/run/action | Inicia uma execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtém os rastreamentos de expressão da ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/read | Lê a ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtém os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lê a repetição da ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de solicitações da ação de repetição de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de solicitações da ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lê a repetição do escopo da ação de execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/cancel/action | Cancela a execução de um fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/delete | Exclui uma execução de um fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/operations/read | Lê o status de operação de execução de fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/runs/read | Lê a execução do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/suspend/action | Suspende o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/triggers/histories/read | Lê os históricos de gatilho. |
> | Action | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Reenvia o gatilho do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtém a URL de retorno de chamada para o gatilho. |
> | Action | Microsoft.Logic/workflows/triggers/read | Lê o gatilho. |
> | Action | Microsoft.Logic/workflows/triggers/reset/action | Redefine o gatilho. |
> | Action | Microsoft.Logic/workflows/triggers/run/action | Executa o gatilho. |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | Define o estado do gatilho. |
> | Action | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/versions/read | Lê a versão do fluxo de trabalho. |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtém a URL de retorno de chamada para o gatilho. |
> | Action | Microsoft.Logic/workflows/write | Cria ou atualiza o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover associações de plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler associações de plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | Excluir plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/join/action | Ingressar em um plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | Ler um plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar plano de compromisso de Machine Learning |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | Obter o resultado de uma operação do Machine Learning |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o status de uma operação do Machine Learning em andamento |
> | Action | Microsoft.MachineLearning/operations/read | Obter operações do Machine Learning |
> | Action | Microsoft.MachineLearning/register/action | Registra a assinatura do provedor de recursos do serviço Web do Machine Learning e permite a criação de serviços Web. |
> | Action | Microsoft.MachineLearning/skus/read | Obter SKUs do plano de compromisso do Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/action | Criar propriedades do serviço Web regionais para regiões com suporte |
> | Action | Microsoft.MachineLearning/webServices/delete | Excluir um serviço Web do Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Obtém chaves para um Serviço Web do Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/read | Ler um serviço Web do Machine Learning |
> | Action | Microsoft.MachineLearning/webServices/write | Criar ou atualizar serviço Web do Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/delete | Criar um Workspace do Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para um Workspace do Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/read | Ler um Workspace do Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar as chaves da conta de armazenamento configurada para um Workspace do Machine Learning |
> | Action | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar Workspace do Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtém o status de uma operação de computação específica |
> | Action | Microsoft.MachineLearningServices/locations/usages/read | Relatório de uso dos recursos de computação do AML em uma assinatura |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | Obtém os tamanhos de VM compatíveis |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtém o status de uma operação de workspace específica |
> | Action | Microsoft.MachineLearningServices/register/action | Registra a assinatura para o Provedor de Recursos de Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/delete | Exclui os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista segredos para recursos de computação no Workspace dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista os nós dos recursos de computação no Workspace dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/read | Obtém os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | Cria ou atualiza os recursos de computação nos Workspaces dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Exclui os Workspaces dos Serviços de Machine Learning |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Cria ou atualiza experimentos em Serviços de Machine Learning espaço (s) de trabalho |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista segredos para um Workspace dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/read | Obtém os Workspaces dos Serviços de Machine Learning |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza Workspaces dos Serviços de Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft. ManagedIdentity/Identities/Read | Obtém uma identidade atribuída do sistema existente |
> | Action | Microsoft.ManagedIdentity/register/action | Registra a assinatura no provedor de recursos para a identidade gerenciada |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação RBAC para atribuir uma identidade atribuída ao usuário existente a um recurso |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Excluir uma identidade atribuída ao usuário existente |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obter uma identidade atribuída ao usuário existente |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft. Managedservices/marketplaceRegistrationDefinitions/Read | Recupera uma lista das definições de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/operationStatuses/Read | Lê o status da operação do recurso. |
> | Action | Microsoft. Managedservices/registrar/ação | Registre-se nos serviços gerenciados. |
> | Action | Microsoft. Managedservices/registrationAssignments/Delete | Remove a atribuição de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/registrationAssignments/Read | Recupera uma lista das atribuições de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/registrationAssignments/Write | Adicionar ou modificar a atribuição de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/registrationDefinitions/Delete | Remove a definição de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/registrationDefinitions/Read | Recupera uma lista das definições de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/registrationDefinitions/Write | Adicionar ou modificar a definição de registro de Serviços Gerenciados. |
> | Action | Microsoft. Managedservices/cancelar registro/ação | Cancelar o registro dos serviços gerenciados. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | Verificar se o nome do grupo de gerenciamento especificado é válido e exclusivo. |
> | Action | Microsoft.Management/getEntities/action | Listar todas as entidades (Grupos de Gerenciamento, Assinaturas e etc.) para o usuário autenticado. |
> | Action | Microsoft.Management/managementGroups/delete | Excluir um grupo de gerenciamento. |
> | Action | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | Desassociar a assinatura do grupo de gerenciamento. |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | Associar a assinatura existente ao grupo de gerenciamento. |
> | Action | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | Action | Microsoft.Management/register/action | Registra a assinatura especificada com Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados a uma conta de mapas. |
> | Action | Microsoft.Maps/accounts/delete | Exclui uma Conta de Mapas. |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | Excluir um filtro de Grade de Eventos |
> | Action | Microsoft.Maps/accounts/eventGridFilters/read | Obtenha um filtro de Grade de Eventos |
> | Action | Microsoft.Maps/accounts/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Action | Microsoft.Maps/accounts/listKeys/action | Lista chaves da Conta de Mapas |
> | Action | Microsoft.Maps/accounts/read | Obtém uma Conta de Mapas. |
> | Action | Microsoft.Maps/accounts/regenerateKey/action | Gera uma nova chave primária ou secundária da Conta de Mapas |
> | Action | Microsoft.Maps/accounts/write | Cria ou atualiza uma Conta de Mapas. |
> | Action | Microsoft.Maps/operations/read | Ler as operações do provedor |
> | Action | Microsoft.Maps/register/action | Registrar o provedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Retorna um Contrato. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem ao ACR do usuário final. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retorna uma configuração. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Salva uma configuração. |
> | Action | Microsoft.Marketplace/register/action | Registra o provedor de recursos Microsoft.Marketplace na assinatura. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação DELETE em um recurso de serviço de desenvolvimento clássico. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obter as chaves de gerenciamento de recursos de serviço de desenvolvimento clássico. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obter a URL de Logon Único para um serviço de desenvolvimento clássico. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação GET em um serviço de desenvolvimento clássico. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gerar chaves clássicas de gerenciamento de recursos do serviço de desenvolvimento. |
> | Action | Microsoft.MarketplaceApps/Operations/read | Ler as operações para todos os tipos de recursos. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um contrato para um item específico do marketplace |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Retornar um contrato para um item específico do marketplace |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um item específico do marketplace |
> | Action | Microsoft.MarketplaceOrdering/agreements/read | Retornar todos os contratos sob determinada assinatura |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um contrato para um determinado item de máquina virtual do marketplace |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Assinar ou Cancelar um contrato para um determinado item de máquina virtual do marketplace |
> | Action | Microsoft.MarketplaceOrdering/operations/read | Listar todas as operações possíveis na API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Media/checknameavailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Action | Microsoft.Media/locations/checkNameAvailability/action | Verificar se um nome de conta de Serviços de Mídia está disponível |
> | Action | Microsoft.Media/mediaservices/accountfilters/delete | Exclui qualquer Filtro de Conta |
> | Action | Microsoft.Media/mediaservices/accountfilters/read | Lê qualquer Filtro de Conta |
> | Action | Microsoft.Media/mediaservices/accountfilters/write | Cria ou atualiza qualquer Filtro de Conta |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/delete | Exclui qualquer Filtro de Ativo |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/read | Lê qualquer Filtro de Ativo |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/write | Cria ou atualiza qualquer Filtro de Ativo |
> | Action | Microsoft.Media/mediaservices/assets/delete | Excluir qualquer Ativo |
> | Action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obtém a Chave de Criptografia do Ativo |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista URLs da SAS de Contêiner de Ativos |
> | Action | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lista os Localizadores de Streaming para o Ativo |
> | Action | Microsoft.Media/mediaservices/assets/read | Lê qualquer Ativo |
> | Action | Microsoft.Media/mediaservices/assets/write | Cria ou atualiza qualquer Ativo |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Exclui qualquer Política de Chave de Conteúdo |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtém Propriedades da Política com Segredos |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | Lê qualquer Política de Chave de Conteúdo |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | Cria ou atualiza qualquer Política de Chave de Conteúdo |
> | Action | Microsoft.Media/mediaservices/delete | Excluir qualquer Conta de Serviços de Mídia |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | Exclui qualquer Filtro de Grade de Eventos |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | Lê qualquer Filtro de Grade de Eventos |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | Cria ou atualiza qualquer Filtro de Grade de Eventos |
> | Action | Microsoft.Media/mediaservices/liveEventOperations/read | Lê qualquer Operação de Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/delete | Exclui qualquer Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Exclui qualquer Saída em Tempo Real |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Lê qualquer Saída em Tempo Real |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Cria ou atualiza qualquer Saída em Tempo Real |
> | Action | Microsoft.Media/mediaservices/liveEvents/read | Lê qualquer Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/reset/action | Redefine qualquer Operação de Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/start/action | Inicia qualquer Operação de Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/stop/action | Interrompe qualquer Operação de Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveEvents/write | Cria ou atualiza qualquer Evento ao Vivo |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | Lê qualquer Operação de Saída em Tempo Real |
> | Action | Microsoft.Media/mediaservices/read | Ler qualquer Conta de Serviços de Mídia |
> | Action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Lê qualquer Operação de Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/delete | Exclui qualquer Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/read | Lê qualquer Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensiona qualquer Operação de Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Inicia qualquer Operação de Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Interrompe qualquer Operação de Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/write | Cria ou atualiza qualquer Ponto de Extremidade de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/delete | Exclui qualquer Localizador de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista Chaves de Conteúdo |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista Caminhos |
> | Action | Microsoft.Media/mediaservices/streamingLocators/read | Lê qualquer Localizador de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingLocators/write | Cria ou atualiza qualquer Localizador de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | Exclui qualquer Política de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | Lê qualquer Política de Streaming |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/write | Cria ou atualiza qualquer Política de Streaming |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as Chaves de Armazenamento para uma conta de Armazenamento do Microsoft Azure anexada |
> | Action | Microsoft.Media/mediaservices/transforms/delete | Exclui qualquer Transformação |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar Trabalho |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | Exclui qualquer Trabalho |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/read | Lê qualquer Trabalho |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/write | Cria ou atualiza qualquer Trabalho |
> | Action | Microsoft.Media/mediaservices/transforms/read | Lê qualquer Transformação |
> | Action | Microsoft.Media/mediaservices/transforms/write | Cria ou atualiza qualquer Transformação |
> | Action | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer Conta de Serviços de Mídia |
> | Action | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Action | Microsoft.Media/register/action | Registra a assinatura do provedor de recursos dos Serviços de Mídia e habilitar a criação de contas dos Serviços de Mídia |
> | Action | Microsoft.Media/unregister/action | Cancela o registro da assinatura para o provedor de recursos do Serviços de Mídia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft. Migration/assessmentprojects/assessmentoptions/Read | Obtém as opções de avaliação disponíveis no local fornecido |
> | Action | Microsoft.Migrate/assessmentprojects/assessments/read | Lista avaliações dentro de um projeto |
> | Action | Microsoft.Migrate/assessmentprojects/delete | Excluir o projeto de avaliação |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Obter as propriedades de um computador avaliado |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Exclui uma avaliação |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Baixa uma URL do relatório de avaliação |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Cria uma avaliação ou atualiza uma existente |
> | Action | Microsoft.Migrate/assessmentprojects/groups/delete | Exclui um grupo |
> | Action | Microsoft.Migrate/assessmentprojects/groups/read | Obter as propriedades de um grupo |
> | Action | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Atualizar grupo adicionando ou removendo computadores |
> | Action | Microsoft.Migrate/assessmentprojects/groups/write | Cria um grupo ou atualiza um grupo existente |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Exclui o coletor HyperV |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Obter as propriedades do coletor HyperV |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Cria um novo coletor do HyperV ou atualiza um coletor HyperV existente |
> | Action | Microsoft.Migrate/assessmentprojects/machines/read | Obtém as propriedades de um computador |
> | Action | Microsoft.Migrate/assessmentprojects/read | Obter as propriedades do projeto de avaliação |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Exclui o coletor do VMware |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Obter as propriedades do coletor do VMware |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Cria um novo coletor VMware ou atualiza um coletor VMware existente |
> | Action | Microsoft.Migrate/assessmentprojects/write | Cria um novo projeto de avaliação ou atualiza um projeto de avaliação existente |
> | Action | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação disponíveis no local fornecido |
> | Action | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a assinatura fornecida no local determinado |
> | Action | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Obtém as propriedades de uma instância de banco de dados |
> | Action | Microsoft.Migrate/migrateprojects/Databases/read | Obtém as propriedades de um banco de dados |
> | Action | Microsoft.Migrate/migrateprojects/delete | Excluir um projeto de migração |
> | Action | Microsoft.Migrate/migrateprojects/machines/read | Obtém as propriedades de um computador |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Exclui um evento de migração |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Obtém as propriedades de um evento de migração. |
> | Action | Microsoft.Migrate/migrateprojects/read | Obtém as propriedades do projeto de migração |
> | Action | Microsoft.Migrate/migrateprojects/registerTool/action | Registra a ferramenta para um projeto de migração |
> | Action | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Limpar os dados de solução de projeto de migração |
> | Action | Microsoft.Migrate/migrateprojects/solutions/Delete | Exclui uma solução de migração de projeto |
> | Action | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obtém a configuração da solução do projeto de migração |
> | Action | Microsoft.Migrate/migrateprojects/solutions/read | Obtém as propriedades da solução migrar projeto |
> | Action | Microsoft.Migrate/migrateprojects/solutions/write | Cria uma nova solução de migração de projeto ou atualiza uma solução de projeto de migração existente |
> | Action | Microsoft.Migrate/migrateprojects/write | Cria um novo projeto de migração ou atualiza um projeto de migração existente |
> | Action | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no provedor de recursos Microsoft.Migrate |
> | Action | Microsoft.Migrate/projects/assessments/read | Lista avaliações dentro de um projeto |
> | Action | Microsoft.Migrate/projects/delete | Exclui o projeto |
> | Action | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de um computador avaliado |
> | Action | Microsoft.Migrate/projects/groups/assessments/delete | Exclui uma avaliação |
> | Action | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Baixa uma URL do relatório de avaliação |
> | Action | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Action | Microsoft.Migrate/projects/groups/assessments/write | Cria uma avaliação ou atualiza uma existente |
> | Action | Microsoft.Migrate/projects/groups/delete | Exclui um grupo |
> | Action | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Action | Microsoft.Migrate/projects/groups/write | Cria um grupo ou atualiza um grupo existente |
> | Action | Microsoft.Migrate/projects/keys/action | Obtém chaves compartilhadas para o projeto |
> | Action | Microsoft.Migrate/projects/machines/read | Obtém as propriedades de um computador |
> | Action | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Action | Microsoft.Migrate/projects/write | Cria um projeto ou atualiza um existente |
> | Action | Microsoft.Migrate/register/action | Registra a Assinatura com o provedor de recursos Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.MixedReality/register/action | Registra uma assinatura para o provedor de recursos de realidade misturada. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Excluir âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para Microsoft. MixedReality/SpatialAnchorsAccounts |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para Microsoft. MixedReality/SpatialAnchorsAccounts |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Microsoft. MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado da operação. |
> | Action | Microsoft.NetApp/locations/read | Lê um recurso de verificação de disponibilidade. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Exclui um recurso de pool. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de pool. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Exclui um recurso de volume. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Lê um recurso de destino de montagem. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lê um recurso de volume. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Exclui um recurso de instantâneo. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lê um recurso de instantâneo. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Grava um recurso de instantâneo. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Grava um recurso de volume. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Grava um recurso de pool. |
> | Action | Microsoft.NetApp/netAppAccounts/delete | Exclui um recurso de conta. |
> | Action | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Action | Microsoft.NetApp/netAppAccounts/write | Grava um recurso de conta. |
> | Action | Microsoft.NetApp/Operations/read | Lê recursos de operação. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obtém os Cabeçalhos de Solicitação disponíveis do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obtém os Cabeçalhos de Resposta disponíveis do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obtém as Variáveis de Servidor disponíveis do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Política predefinida SSL do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções SSL disponíveis do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obter conjuntos de regras WAF disponíveis para o Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Action | Microsoft.Network/applicationGateways/backendhealth/action | Obter uma integridade de back-end do Gateway de Aplicativo |
> | Action | Microsoft.Network/applicationGateways/delete | Excluir um gateway de aplicativo |
> | Action | Microsoft.Network/applicationGateways/read | Obter um gateway de aplicativo |
> | Action | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicativo |
> | Action | Microsoft.Network/applicationGateways/stop/action | Interrompe um gateway de aplicativo |
> | Action | Microsoft.Network/applicationGateways/write | Criar ou atualizar um gateway de aplicativo |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Exclui uma política de WAF do gateway de aplicativo |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obter uma política de WAF do gateway de aplicativo |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Cria uma política de WAF do gateway de aplicativo ou atualiza uma política de WAF do gateway de aplicativo |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | Excluir um grupo de segurança de aplicativo |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Adicionar uma configuração IP aos grupos de segurança de aplicativo. Não é possível alertá-lo. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Adicionar uma regra de segurança aos grupos de segurança de aplicativo. Não é possível alertá-lo. |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lista as Configurações de IP no ApplicationSecurityGroup |
> | Action | Microsoft.Network/applicationSecurityGroups/read | Obter uma ID do grupo de segurança de aplicativo. |
> | Action | Microsoft.Network/applicationSecurityGroups/write | Criar um grupo de segurança de aplicativo ou atualizar um grupo de segurança de aplicativo existente. |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | Obtém as marcas FQDN do Firewall do Azure |
> | Action | Microsoft.Network/azurefirewalls/delete | Excluir o Firewall do Azure |
> | Action | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Action | Microsoft.Network/azurefirewalls/write | Cria ou atualiza um Firewall do Azure |
> | Action | Microsoft.Network/bastionHosts/delete | Exclui um host bastião |
> | Action | Microsoft.Network/bastionHosts/read | Obtém um host bastião |
> | Action | Microsoft.Network/bastionHosts/write | Criar ou atualizar um host bastião |
> | Action | Microsoft.Network/bgpServiceCommunities/read | Obter comunidades do serviço BGP |
> | Action | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se um nome de porta frontal está disponível |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verificar a disponibilidade de um nome DNS relativo do Gerenciador de Tráfego. |
> | Action | Microsoft.Network/connections/delete | Excluir VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/read | Obter o VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/revoke/action | Marca um status de Conexão do ExpressRoute como Revogado |
> | Action | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/read | Obter SharedKey da VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/sharedKey/write | Criar ou atualizar uma VirtualNetworkGatewayConnection SharedKey existente |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obter configuração do dispositivo VPN do VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/write | Criar ou atualizar uma VirtualNetworkGatewayConnection existente |
> | Action | Microsoft.Network/ddosCustomPolicies/delete | Exclui uma política personalizada de DDoS |
> | Action | Microsoft.Network/ddosCustomPolicies/read | Obtém uma definição de política personalizada de DDoS |
> | Action | Microsoft.Network/ddosCustomPolicies/write | Cria uma política personalizada de DDoS ou atualiza uma política personalizada de DDoS existente |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | Excluir um Plano de Proteção contra DDoS |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | Une um plano de proteção contra DDoS. Não é possível alertá-lo. |
> | Action | Microsoft.Network/ddosProtectionPlans/read | Obter um Plano de Proteção contra DDoS |
> | Action | Microsoft.Network/ddosProtectionPlans/write | Criar um Plano de Proteção contra DDoS ou atualizar um Plano de Proteção contra DDoS  |
> | Action | Microsoft.Network/dnsoperationresults/read | Obter os resultados de uma operação de DNS |
> | Action | Microsoft.Network/dnsoperationstatuses/read | Obter o status de uma operação de DNS  |
> | Action | Microsoft.Network/dnszones/A/delete | Remover o conjunto de registros de determinado nome e tipo ‘A’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/A/read | Obter o conjunto de registros do tipo 'A', no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registros do tipo 'A' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/AAAA/delete | Remover o conjunto de registros de determinado nome e digite ‘AAAA’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/AAAA/read | Obter o conjunto de registros do tipo 'AAAA' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo 'AAAA' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/all/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Action | Microsoft.Network/dnszones/CAA/delete | Remover o conjunto de registros de um determinado nome e digitar "CAA" de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/CAA/read | Obter o conjunto de registros do tipo ‘CAA’ no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Action | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registros do tipo ‘CAA’ em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/CNAME/delete | Remover o conjunto de registros de determinado nome e tipo ‘CNAME’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/CNAME/read | Obter o conjunto de registros do tipo 'CNAME' no formato JSON. O conjunto de registros contém TTL, marcações e etag. |
> | Action | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo 'CNAME' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/delete | Excluir a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Action | Microsoft.Network/dnszones/MX/delete | Remover o conjunto de registros de determinado nome e tipo ‘MX’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/MX/read | Obter o conjunto de registros do tipo 'MX' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registros do tipo 'MX' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/NS/delete | Excluir o conjunto de registros DNS do tipo NS |
> | Action | Microsoft.Network/dnszones/NS/read | Obter o conjunto de registros DNS do tipo NS |
> | Action | Microsoft.Network/dnszones/NS/write | Criar ou atualizar o conjunto de registros DNS do tipo NS |
> | Action | Microsoft.Network/dnszones/PTR/delete | Remover o conjunto de registros de determinado nome e tipo ‘PTR’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/PTR/read | Obter o conjunto de registros do tipo 'PTR' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registros do tipo 'PTR' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/read | Obter a zona DNS no formato JSON. As propriedades de zona incluem tags, etag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera conjuntos de registros contidos na zona. |
> | Action | Microsoft.Network/dnszones/recordsets/read | Obter os conjuntos de registros DNS em todos os tipos |
> | Action | Microsoft.Network/dnszones/SOA/read | Obter o conjunto de registros DNS do tipo SOA |
> | Action | Microsoft.Network/dnszones/SOA/write | Criar ou atualizar o conjunto de registros DNS do tipo SOA |
> | Action | Microsoft.Network/dnszones/SRV/delete | Remover o conjunto de registros de determinado nome e tipo ‘SRV’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/SRV/read | Obter o conjunto de registros do tipo 'SRV' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar o conjunto de registros do tipo SRV |
> | Action | Microsoft.Network/dnszones/TXT/delete | Remover o conjunto de registros de determinado nome e tipo ‘TXT’ de uma zona DNS. |
> | Action | Microsoft.Network/dnszones/TXT/read | Obter o conjunto de registros do tipo 'TXT' no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registros do tipo 'TXT' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS em um grupo de recursos.  Usado para atualizar as marcações em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros na zona. |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/delete | Excluir uma autorização ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/read | Obter uma autorização ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | Criar ou atualizar uma autorização ExpressRouteCircuit existente |
> | Action | Microsoft.Network/expressRouteCircuits/delete | Excluir um ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/join/action | Une um circuito de rota expressa. Não é possível alertá-lo. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Obter ArpTable de emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Excluir uma conexão de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obter uma conexão de circuito de ExpressRoute |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Criar ou atualizar um Recurso de Conexão de ExpressRouteCircuit existente |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | Excluir um emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtém a Conexão de Circuito Par do ExpressRoute |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | Obter um emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Obter RouteTable de emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Obter um resumo de RouteTable de emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obter status de emparelhamento de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente |
> | Action | Microsoft.Network/expressRouteCircuits/read | Obter um ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | Obter status de ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/write | Criar ou atualizar um ExpressRouteCircuit existente |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | Une uma conexão cruzada de rota expressa. Não é possível alertá-lo. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Obter uma Tabela ARP de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Excluir um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obter um Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Obter uma Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Obter uma Resumo de Tabela de Rotas de Emparelhamento de Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | Criar um emparelhamento de conexão cruzada de rota expressa ou atualizar um emparelhamento de conexão cruzada de rota expressa existente |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | Excluir Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Adicionar uma Conexão Cruzada de Rota Expressa |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Adicionar uma Conexão de Rota Expressa |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma Conexão de Rota Expressa ou Atualiza uma Conexão de Rota Expressa |
> | Action | Microsoft.Network/expressRouteGateways/join/action | Une um gateway de rota expressa. Não é possível alertá-lo. |
> | Action | Microsoft.Network/expressRouteGateways/read | Obtém o Gateway de Rota Expressa |
> | Action | Microsoft.Network/expressRoutePorts/delete | Exclui ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/join/action | Une portas de rota expressa. Não é possível alertá-lo. |
> | Action | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Action | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/write | Cria ou atualiza o ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | Obter Localizações de Porta do ExpressRoute |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Obter os provedores de serviços do ExpressRoute |
> | Action | Microsoft. Network/firewallPolicies/Delete | Exclui uma política de firewall |
> | Action | Microsoft. Network/firewallPolicies/junção/ação | Une uma política de firewall. Não é possível alertá-lo. |
> | Action | Microsoft. Network/firewallPolicies/Read | Obter uma política de firewall |
> | Action | Microsoft. Network/firewallPolicies/grupo/Delete | Excluir um grupo de regras de política de firewall |
> | Action | Microsoft. Network/firewallPolicies/grupo/Read | Obter um grupo de regras de política de firewall |
> | Action | Microsoft. Network/firewallPolicies/grupo/Write | Cria um grupo de regras de política de firewall ou atualiza um grupo de regras de política de firewall existente |
> | Action | Microsoft. Network/firewallPolicies/Write | Cria uma política de firewall ou atualiza uma política de firewall existente |
> | Action | Microsoft.Network/frontDoors/backendPools/delete | Exclui um pool de back-end |
> | Action | Microsoft.Network/frontDoors/backendPools/read | Obtém um pool de back-end |
> | Action | Microsoft.Network/frontDoors/backendPools/read | Cria ou atualiza um pool de back-end |
> | Action | Microsoft.Network/frontDoors/delete | Exclui uma Porta Frontal |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/delete | Exclui um ponto de extremidade de frontend |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Desabilita o HTTPS em um ponto de extremidade de frontend |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Ativa o HTTPS em um ponto de extremidade de frontend |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtém um ponto de extremidade de frontend |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/write | Cria ou atualiza um ponto de extremidade de frontend |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/delete | Configurações da investigação de integridade personalizada |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtém as configurações do probe de integridade |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/write | Cria ou atualiza as configurações de investigação de integridade |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Cria ou atualiza as configurações de balanceamento de carga |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtém configurações de balanceamento de carga |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Cria ou atualiza as configurações de balanceamento de carga |
> | Action | Microsoft.Network/frontDoors/purge/action | Eliminar conteúdo em cache de uma porta frontal |
> | Action | Microsoft.Network/frontDoors/read | Obtém uma Porta Frontal |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | Exclui uma regra de roteamento |
> | Action | Microsoft.Network/frontDoors/routingRules/read | Obtém uma regra de roteamento |
> | Action | Microsoft.Network/frontDoors/routingRules/write | Obtém uma regra de roteamento |
> | Action | Microsoft.Network/frontDoors/validateCustomDomain/action | Valida um endpoint frontend para uma porta frontal |
> | Action | Microsoft.Network/frontDoors/write | Cria ou atualiza uma Porta Frontal |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Obter conjuntos de regras gerenciadas pelo firewall do aplicativo Web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Exclui uma política de firewall de aplicativo da Web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obtém uma política de firewall de aplicativo da Web |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Cria ou atualiza uma política de firewall de aplicativo da Web |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/read | Obter uma definição de pool de endereços de back-end do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/delete | Excluir um balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Une uma configuração de IP de front-end do balanceador de carga. Não é possível alertá-lo. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obter uma definição de configuração de IP de front-end do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/read | Obter uma definição de pool NAT de entrada do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | Excluir uma regra NAT de entrada do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | Obter uma definição de regra NAT de entrada do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | Criar uma regra NAT de entrada do balanceador de carga ou atualizar uma regra NAT de entrada do balanceador de carga existente |
> | Action | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obter uma definição regra de balanceamento de carga do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | Obter as referências a todos os adaptadores de rede em um balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | Obtém uma definição de regra NAT de saída do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | Permite usar investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. Não é possível alertá-lo. |
> | Action | Microsoft.Network/loadBalancers/probes/read | Obter uma investigação do balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/virtualMachines/read | Obter as referências a todas as máquinas virtuais em um balanceador de carga |
> | Action | Microsoft.Network/loadBalancers/write | Criar um balanceador de carga ou atualizar um balanceador de carga existente |
> | Action | Microsoft.Network/localnetworkgateways/delete | Excluir LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/read | Obter o LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/write | Criar ou atualizar uma LocalNetworkGateway existente |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Obter serviços de vínculo privado aprovados automaticamente |
> | Action | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Action | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Obtém recursos de ponto de extremidade privados disponíveis |
> | Action | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um locatário do Bare Metal |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte à Rede Acelerada |
> | Action | Microsoft.Network/locations/checkDnsNameAvailability/read | Verificar se o rótulo de DNS está disponível no local especificado |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Verifica a visibilidade do serviço de vínculo privado |
> | Action | Microsoft.Network/locations/operationResults/read | Obter o resultado de uma operação POST ou DELETE assíncrona |
> | Action | Microsoft.Network/locations/operations/read | Obter o recurso de operação que representa o status de uma operação assíncrona |
> | Action | Microsoft.Network/locations/serviceTags/read | Obter marcas de serviço |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém os tamanhos de máquinas virtuais compatíveis |
> | Action | Microsoft.Network/locations/usages/read | Obter a métrica de uso dos recursos |
> | Action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obter uma lista de serviços de ponto de extremidade de Rede Virtual disponíveis |
> | Action | Microsoft.Network/networkIntentPolicies/delete | Exclui uma política de intenção de rede |
> | Action | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição de intenção de diretiva de rede |
> | Action | Microsoft.Network/networkIntentPolicies/write | Cria uma interface de rede ou atualiza uma política de intenção de rede existente |
> | Action | Microsoft.Network/networkInterfaces/delete | Excluir um adaptador de rede |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de configurados no adaptador de rede da máquina virtual |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter tabela de rota configurada no adaptador de rede da máquina virtual |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Une uma configuração IP do adaptador de rede. Não é possível alertá-lo. |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obter uma definição de configuração de IP do adaptador de rede.  |
> | Action | Microsoft.Network/networkInterfaces/join/action | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | Obter todos os balanceadores de carga dos quais o adaptador de rede faz parte |
> | Action | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Exclui uma configuração de toque do adaptador de rede. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque do adaptador de rede. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma configuração de toque do adaptador de rede ou atualiza uma configuração de toque do adaptador de rede existente. |
> | Action | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Action | Microsoft.Network/networkProfiles/delete | Excluir um perfil de rede |
> | Action | Microsoft.Network/networkProfiles/read | Obtém um perfil de rede |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | Remove os contêineres |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | Definir contêineres |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as Interfaces de rede de contêiner |
> | Action | Microsoft.Network/networkProfiles/write | Criar ou atualizar um perfil de rede |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obter uma definição padrão da regra de segurança |
> | Action | Microsoft.Network/networkSecurityGroups/delete | Excluir um grupo de segurança de rede |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Action | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Excluir uma regra de segurança |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | Obter uma definição da regra de segurança |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | Criar uma regra de segurança ou atualizar uma regra de segurança existente |
> | Action | Microsoft.Network/networkSecurityGroups/write | Criar um grupo de segurança de rede ou atualizar um grupo de segurança de rede existente |
> | Action | Microsoft.Network/networkWatchers/availableProvidersList/action | Retornar todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada. |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retornar a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure. |
> | Action | Microsoft.Network/networkWatchers/configureFlowLog/action | Configurar o registro de fluxo em log para um recurso de destino. |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/delete | Excluir um Monitor de Conexão |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Consultar monitoramento de conectividade entre os pontos de extremidades especificados |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter detalhes do Monitor de Conexão |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Iniciar monitoramento de conectividade entre pontos de extremidades especificados |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar monitoramento de conectividade entre pontos de extremidades especificados |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/write | Criar um Monitor de Conexão |
> | Action | Microsoft.Network/networkWatchers/connectivityCheck/action | Verificar a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário. |
> | Action | Microsoft.Network/networkWatchers/delete | Excluir um observador de rede |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | Retornar se o pacote é permitido ou negado em relação a um destino específico. |
> | Action | Microsoft.Network/networkWatchers/lenses/delete | Excluir uma Lente |
> | Action | Microsoft.Network/networkWatchers/lenses/query/action | Consultar monitoramento de tráfego em um ponto de extremidade especificado |
> | Action | Microsoft.Network/networkWatchers/lenses/read | Obter os detalhes de Lente |
> | Action | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar monitoramento de tráfego em um ponto de extremidade especificado |
> | Action | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar o monitoramento do tráfego de rede em um terminal especificado |
> | Action | Microsoft.Network/networkWatchers/lenses/write | Criar uma lente |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico de configuração de rede. |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | Para um destino especificado e endereço IP de destino especificados, retornar o tipo do próximo salto e próximo endereço IP esperado. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/delete | Excluir uma captura de pacote |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obter informações sobre propriedades e status de um recurso de captura de pacote. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacote |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Interromper a sessão de captura de pacote em execução. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/write | Criar uma captura de pacote |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | Exclui um PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/read | Obtenha detalhes de PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre VMs especificadas |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Iniciar PingMesh entre VMs especificadas |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Consultar em lote o monitoramento de conectividade entre os pontos de extremidades especificados |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obter o status do registro do fluxo em log em um recurso. |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obter o resultado da solução de problemas da operação de solução de problemas executada ou atualmente em execução. |
> | Action | Microsoft.Network/networkWatchers/read | Obter a definição de observador de rede |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | Exibir as regras de grupo de segurança de rede configuradas e em vigor aplicadas a uma máquina virtual. |
> | Action | Microsoft.Network/networkWatchers/topology/action | Obter uma exibição dos recursos em nível de rede e de suas relações em um grupo de recursos. |
> | Action | Microsoft.Network/networkWatchers/troubleshoot/action | Iniciar a solução de problemas em um recurso de rede no Azure. |
> | Action | Microsoft.Network/networkWatchers/write | Criar um observador de rede ou atualizar um observador de rede existente |
> | Action | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Action | Microsoft.Network/p2sVpnGateways/delete | Exclui um P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar o perfil de Vpn para o P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obtém uma Conexão P2S VPN íntegra para o P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Action | Microsoft.Network/privateDnsOperationResults/read | Obtém os resultados de uma operação de DNS privado |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | Obtém o status de uma operação de DNS privado |
> | Action | Microsoft.Network/privateDnsZones/A/delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/A/read | Obter o conjunto de registros do tipo ' A ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/A/write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | Obtenha o conjunto de registros do tipo ' AAAA ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/ALL/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | Obtenha o conjunto de registros do tipo ' CNAME ' dentro de uma zona de DNS privado, no formato JSON. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/delete | Excluir uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/MX/delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/MX/read | Obtenha o conjunto de registros do tipo ' MX ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/MX/write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/PTR/delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/PTR/read | Obter o conjunto de registros do tipo ' PTR ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/PTR/write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/read | Obtenha as propriedades da zona de DNS privado, no formato JSON. Observe que esse comando não recupera as redes virtuais às quais a zona de DNS privado está vinculada ou os conjuntos de registros contidos na zona. |
> | Action | Microsoft.Network/privateDnsZones/recordsets/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Action | Microsoft.Network/privateDnsZones/SOA/read | Obter o conjunto de registros do tipo ' SOA ' em uma zona de DNS privado, no formato JSON. |
> | Action | Microsoft.Network/privateDnsZones/SOA/write | Atualizar um conjunto de registros do tipo ' SOA ' em uma zona DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/SRV/delete | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona de DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/SRV/read | Obtenha o conjunto de registros do tipo ' SRV ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | Criar ou atualizar um conjunto de registros do tipo ' SRV ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS privado. |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | Obtenha o conjunto de registros do tipo ' TXT ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, o TTL, as marcações e as etags. |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Exclua um link de zona de DNS privado para a rede virtual. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Obtenha o link de zona de DNS privado para as propriedades de rede virtual, no formato JSON. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Crie ou atualize um link de zona de DNS privado para a rede virtual. |
> | Action | Microsoft.Network/privateDnsZones/write | Criar ou atualizar uma zona de DNS privado dentro de um grupo de recursos. Observe que esse comando não pode ser usado para criar ou atualizar links de rede virtual ou conjuntos de registros dentro da zona. |
> | Action | Microsoft.Network/privateEndpoints/delete | Exclui um recurso de ponto de extremidade privado. |
> | Action | Microsoft.Network/privateEndpoints/read | Obtém um recurso de ponto de extremidade privado. |
> | Action | Microsoft.Network/privateEndpoints/write | Cria um novo ponto de extremidade privado ou atualiza um ponto de extremidade privado existente. |
> | Action | Microsoft.Network/privateLinkServices/delete | Exclui um recurso do serviço de link privado. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privado. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Obtém uma definição de conexão de ponto de extremidade particular. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Cria uma nova conexão de ponto de extremidade privado ou atualiza uma conexão de ponto de extremidade privada existente. |
> | Action | Microsoft.Network/privateLinkServices/read | Obtém um recurso do serviço de link privado. |
> | Action | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de link privado ou atualiza um serviço de link privado existente. |
> | Action | Microsoft.Network/publicIPAddresses/delete | Excluir um endereço IP público. |
> | Action | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Action | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Action | Microsoft.Network/publicIPAddresses/write | Criar um endereço IP público ou atualizar um endereço IP público existente.  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | Exclui um prefixo IP público A |
> | Action | Microsoft.Network/publicIPPrefixes/join/action | Une um PublicIPPrefix. Não é possível alertá-lo. |
> | Action | Microsoft.Network/publicIPPrefixes/read | Obtém uma definição de prefixo IP público |
> | Action | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo IP público ou atualiza um prefixo IP público existente |
> | Action | Microsoft.Network/register/action | Registra a assinatura |
> | Action | Microsoft.Network/routeFilters/delete | Excluir uma definição de filtro de rota |
> | Action | Microsoft.Network/routeFilters/join/action | Une um filtro de rota. Não é possível alertá-lo. |
> | Action | Microsoft.Network/routeFilters/read | Obter uma definição de filtro de rota |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | Excluir uma definição de regra de filtro de rota |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | Obter uma definição de regra de filtro de rota |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | Criar uma regra de filtro de rota ou atualizar uma regra de filtro de rota existente |
> | Action | Microsoft.Network/routeFilters/write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Action | Microsoft.Network/routeTables/delete | Excluir uma definição de tabela de rota |
> | Action | Microsoft.Network/routeTables/join/action | Une uma tabela de rotas. Não é possível alertá-lo. |
> | Action | Microsoft.Network/routeTables/read | Obter uma definição de tabela de rota |
> | Action | Microsoft.Network/routeTables/routes/delete | Excluir uma definição de rota |
> | Action | Microsoft.Network/routeTables/routes/read | Obter uma definição de rota |
> | Action | Microsoft.Network/routeTables/routes/write | Criar uma rota ou atualizar uma rota existente |
> | Action | Microsoft.Network/routeTables/write | Cria uma tabela de rotas ou atualiza uma tabela de rotas existente |
> | Action | Microsoft.Network/securegateways/delete | Excluir um Gateway de Segurança |
> | Action | Microsoft.Network/securegateways/read | Obter um Gateway de Segurança |
> | Action | Microsoft.Network/securegateways/write | Criar ou atualizar um Gateway de Segurança |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | Excluir uma política de ponto de extremidade de serviço |
> | Action | Microsoft.Network/serviceEndpointPolicies/join/action | Une uma política de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Une uma sub-rede a políticas de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Action | Microsoft.Network/serviceEndpointPolicies/read | Obter uma descrição da política de ponto de extremidade de serviço |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Excluir uma descrição da política de ponto de extremidade de serviço |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtém uma descrição da definição da política de ponto de extremidade de serviço |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Criar uma definição de política de ponto de extremidade de serviço ou atualizar uma definição de política de ponto de extremidade de serviço existente |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | Criar uma política de ponto de extremidade de serviço ou atualizar uma política de ponto de extremidade de serviço existente |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obter a hierarquia de geográfica do Gerenciador de Tráfego contendo regiões que podem ser usadas com o método de roteamento de tráfego geográfico |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Excluir um Ponto de Extremidade do Azure de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o Ponto de Extremidade do Azure excluído. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obter um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicionar um novo Ponto de Extremidade do Azure em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de Tráfego. |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Excluir o perfil do Gerenciador de Tráfego. Todas as configurações associadas ao perfil do Gerenciador de Tráfego serão perdidas e o perfil não poderá ser usado para rotear o tráfego. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Excluir um ponto de extremidade externo de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego do Microsoft Azure interromperá o tráfego de roteamento para o ponto de extremidade externo excluído. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obter um ponto de extremidade externo que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade externo. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicionar um novo ponto de extremidade externo em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de Tráfego. |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obter o mapa de calor do Gerenciador de Tráfego para o perfil do Gerenciador de Tráfego fornecido, que contém contagens de consulta e dados de latência por local e IP de origem. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Excluir um ponto de extremidade aninhado de um perfil do Gerenciador de Tráfego existente. O Gerenciador de Tráfego interromperá o tráfego de roteamento para o ponto de extremidade aninhado excluído. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de Tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicionar um novo ponto de extremidade aninhado em um perfil do Gerenciador de Tráfego existente ou atualizar as propriedades de um ponto de extremidade aninhado existente nesse perfil do Gerenciador de Tráfego. |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Obter a configuração de perfil do Gerenciador de Tráfego. Isso inclui configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e lista de pontos de extremidade roteados por esse perfil do Gerenciador de Tráfego. |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gerenciador de Tráfego ou modificar a configuração de um perfil do Gerenciador de Tráfego existente.<br>Isso inclui habilitar ou desabilitar um perfil e modificar as configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento do ponto de extremidade.<br>Pontos de extremidade roteados pelo perfil do Gerenciador de Tráfego podem ser adicionados, removidos, habilitados ou desabilitados. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Excluir a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obter a chave do nível de assinatura usada para a coleção de métricas de usuário em tempo real. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Criar uma nova chave do nível de assinatura a ser usada para a coleção de métricas de usuário em tempo real. |
> | Action | Microsoft.Network/unregister/action | Cancelar o registro da assinatura |
> | Action | Microsoft.Network/virtualHubs/delete | Excluir um Hub Virtual |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Excluir um HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/read | Obter um Hub Virtual |
> | Action | Microsoft.Network/virtualHubs/write | Criar ou atualizar um Hub Virtual |
> | Action | microsoft.network/virtualnetworkgateways/connections/read | Obter um VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/virtualNetworkGateways/delete | Excluir um virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar pacote de VpnClient para virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar pacote de VpnProfile para VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obter rotas anunciadas do virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obter status do Par de BGP do virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obter rotas aprendidas do virtualnetworkgateway |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter por integridade de conexão de cliente Vpn para VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obter a URL de um pacote de perfil de cliente VPN gerado previamente |
> | Action | Microsoft.Network/virtualNetworkGateways/read | Obter um VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/reset/action | Reiniciar um virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reiniciar a chave compartilhada Vpnclient para o cliente P2S do VirtualNetworkGateway. |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Configurar parâmetros IPsec do Vpnclient para cliente P2S do VirtualNetworkGateway. |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listar dispositivos VPN com suporte |
> | Action | Microsoft.Network/virtualNetworkGateways/write | Criar ou atualizar um VirtualNetworkGateway |
> | Action | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtém referências de Host Bastião em uma Rede Virtual. |
> | Action | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Obtém referências de Host Bastião em uma Rede Virtual. |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verificar se o endereço IP está disponível na rede virtual especificada |
> | Action | Microsoft.Network/virtualNetworks/delete | Excluir uma rede virtual |
> | Action | Microsoft. Network/virtualNetworks/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Action | Microsoft.Network/virtualNetworks/peer/action | Colocar uma rede virtual com outra rede virtual de mesmo nível |
> | Action | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | Excluir uma sub-rede de rede virtual |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara uma sub-rede por meio da aplicação das Políticas de Rede necessárias |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | Criar uma sub-rede de rede virtual ou atualizar uma sub-rede de rede virtual existente |
> | Action | Microsoft.Network/virtualNetworks/usages/read | Obter os usos de IP para cada sub-rede da rede virtual |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | Obter referências a todas as máquinas virtuais em uma rede virtual |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um emparelhamento de redes virtuais |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obter uma definição de emparelhamento de rede virtual |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Criar um emparelhamento de rede virtual ou atualizar um emparelhamento de rede virtual existente |
> | Action | Microsoft.Network/virtualNetworks/write | Criar uma rede virtual ou atualizar uma rede virtual existente |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | Excluir Toque de Rede Virtual |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | Une um toque de rede virtual. Não é possível alertá-lo. |
> | Action | Microsoft.Network/virtualNetworkTaps/read | Obter Toque de Rede Virtual |
> | Action | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar Toque de Rede Virtual |
> | Action | Microsoft.Network/virtualWans/delete | Excluir uma WAN Virtual |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Exclui um P2SVpnServerConfiguration de WAN virtual |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obtém uma P2SVpnServerConfiguration da WAN Virtual |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Cria um P2SVpnServerConfiguration de WAN virtual ou atualiza um P2SVpnServerConfiguration de WAN virtual existente |
> | Action | Microsoft.Network/virtualWans/read | Obter uma WAN Virtual |
> | Action | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém os provedores de segurança de VirtualWan com suporte. |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os Hubs Virtuais que fazem referência a uma WAN Virtual. |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | Obter uma configuração de VPN |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os Sites VPN que fazem referência a uma WAN Virtual. |
> | Action | Microsoft.Network/virtualWans/write | Criar ou atualizar um uma WAN Virtual |
> | Action | Microsoft.Network/vpnGateways/delete | Exclui um VpnGateway. |
> | Action | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém a integridade da conexão para todas ou um subconjunto das conexões em um VpnGateway |
> | Action | Microsoft.Network/vpnGateways/read | Obter um VpnGateway. |
> | Action | microsoft.network/vpngateways/reset/action | Reinicia um VpnGateway |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | Exclui um VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | Obter um VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | Implementar um VpnConnection. |
> | Action | Microsoft.Network/vpnGateways/write | Implementar um VpnGateway. |
> | Action | Microsoft.Network/vpnsites/delete | Excluir um recurso de VPN site. |
> | Action | Microsoft.Network/vpnsites/read | Obter um recurso de VPN site. |
> | Action | Microsoft.Network/vpnsites/write | Criar ou atualizar um recurso de VPN site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verifica se um determinado nome de recurso de Namespace está ou não disponível no serviço do NotificationHub. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obter a lista de descrições de Regras de Autorização de Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter a Cadeia de Conexão para o Namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter a lista de descrições de Regras de Autorização de Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Chave Primária/Secundária Regenerada pela Regra de Autorização do Namespace, especifique a chave que precisa ser regenerada |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verifica se um determinado nome de NotificationHub está ou não disponível em um Namespace. |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | Excluir Recurso de Namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter a lista das Regras de Autorização do Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Excluir Regras de Autorização do Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter a Cadeia de Conexão para o Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter a lista das Regras de Autorização do Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Chave Primária/Secundária Regenerada pela Regra de Autorização do Hub de Notificação; especifique a chave que precisa ser regenerada |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar regras de autorização do Hub de notificação e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Envie uma notificação por push de teste. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Excluir o Recurso do Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obter todas as credenciais PNS do Hub de notificação. Isso inclui credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de Descrições dos Recursos do Hub de Notificação |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar um Hub de notificação e atualizar suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Tempo de vida útil e regras de autorização |
> | Action | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de Descrições dos Recursos de Namespace |
> | Action | Microsoft.NotificationHubs/Namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Action | Microsoft.NotificationHubs/operationResults/read | Retorna os resultados da operação para o provedor de Hubs de Notificação |
> | Action | Microsoft.NotificationHubs/operations/read | Retorna uma lista de operações com suporte para o provedor de Hubs de Notificação |
> | Action | Microsoft.NotificationHubs/register/action | Registra a assinatura no provedor de recursos NotificationHubs e permite a criação de Namespaces e NotificationHubs |
> | Action | Microsoft.NotificationHubs/unregister/action | Cancela o registro da assinatura no provedor de recursos NotificationHubs e permite a criação de Namespaces e NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza do cluster Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Exclui o site do Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um host Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o host do Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de trabalhos Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de máquinas Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/machines/start/action | Inicia computadores do Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/machines/stop/action | Interrompe as máquinas do Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de status de operação Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de site Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site do Hyper-V |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um Hyper-V como contas |
> | Action | Microsoft.OffAzure/HyperVSites/usage/read | Obtém os usos de um site HYPER-V |
> | Action | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site do Hyper-V |
> | Action | Microsoft.OffAzure/Operations/read | Ler as operações expostas |
> | Action | Microsoft.OffAzure/register/action | Registra a Assinatura no provedor de recursos Microsoft.OffAzure |
> | Action | Microsoft.OffAzure/VMwareSites/delete | Exclui o site de VMware |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de trabalhos VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de máquinas VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/start/action | Inicia computadores da VMware |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | Interrompe as máquinas de VMware |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de status de operação VMware |
> | Action | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site do VMware |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware como contas |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | Obtém os usos de um site VMware |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um vCenter VMware |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter VMware |
> | Action | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.OperationalInsights/linkTargets/read | Listar as contas existentes que não estão associadas uma assinatura do Azure. Para vincular essa assinatura do Azure a um workspace, use uma ID de cliente retornada pela operação na propriedade customer id da operação Criar workspace. |
> | Action | microsoft.operationalinsights/operations/read | Lista todas as operações da API de Rest OperationalInsights disponíveis. |
> | Action | microsoft.operationalinsights/register/action | Rergisters a assinatura. |
> | Action | Microsoft.OperationalInsights/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Action | microsoft.operationalinsights/unregister/action | Cancela o registro da assinatura. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter o esquema de pesquisa V2. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | Pesquisar usando o novo mecanismo. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter o esquema de pesquisa V2. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Excluir escopo de configuração |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter escopo de configuração |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir o escopo da configuração |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | Excluir fontes de dados em um workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/read | Obter fontes de dados em um workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar fontes de dados em um workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/delete | Excluir um workspace. Se o workspace foi vinculado a um workspace existente no momento da criação, o workspace a que ele foi vinculado não será excluído. |
> | Action | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para o espaço de trabalho. |
> | Action | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gerar certificado de registro para o workspace. Esse certificado é usado para conectar o Microsoft System Center Operations Manager ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desabilitar um pacote de inteligência para determinado workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Habilitar um pacote de inteligência para determinado workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lista todos os pacotes de inteligência visíveis para um determinado workspace e também indica se o pacote está habilitado ou desabilitado para esse workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Excluir serviços vinculados em um determinado workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços vinculados em um determinado workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços vinculados em determinado workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/action | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/read | Recuperar as chaves de lista para o workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obter os nomes e os metadados para grupos de gerenciamento do System Center Operations Manager conectados ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições métricas no workspace |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Excluir as configurações de notificação do usuário para o workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obter as configurações de notificação do usuário para o workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Definir as configurações de notificação do usuário para o workspace. |
> | Action | microsoft.operationalinsights/workspaces/operations/read | Obtém o status de uma operação de espaço de trabalho OperationalInsights. |
> | Action | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do workspace |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Ler dados da tabela AADDomainServicesAccountLogon |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Ler dados da tabela AADDomainServicesAccountManagement |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Ler dados da tabela AADDomainServicesDirectoryServiceAccess |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Ler dados da tabela AADDomainServicesLogonLogoff |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Ler dados da tabela AADDomainServicesPolicyChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Ler dados da tabela AADDomainServicesPrivilegeUse |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Ler dados da tabela AADDomainServicesSystemSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Ler dados da tabela ADAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Ler dados da tabela ADFActivityRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Ler dados da tabela ADFPipelineRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Ler dados da tabela ADFTriggerRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Ler dados da tabela ADReplicationResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/Alert/read | Ler dados da tabela Alerta |
> | Action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Ler dados da tabela AlertHistory |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Ler dados da tabela AppCenterError |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Ler dados da tabela ApplicationInsights |
> | Action | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Ler dados da tabela AWSCloudTrail |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Ler dados da tabela AzureActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Ler dados da tabela AzureAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Ler dados da tabela AzureMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Ler dados da tabela BlockchainApplicationLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Ler dados da tabela BlockchainProxyLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Ler dados da tabela BoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Ler dados da tabela CommonSecurityLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Ler dados da tabela ComputerGroup |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Ler dados da tabela ConfigurationChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Ler dados da tabela ConfigurationData |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Ler dados da tabela ContainerImageInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Ler dados da tabela ContainerInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Ler dados da tabela ContainerLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Ler dados da tabela ContainerNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Ler dados da tabela ContainerServiceLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Ler dados da tabela DatabricksAccounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Ler dados da tabela DatabricksClusters |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Ler dados da tabela DatabricksDBFS |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Ler dados da tabela DatabricksJobs |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Ler dados da tabela DatabricksNotebook |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Ler dados da tabela DatabricksSecrets |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Ler dados da tabela DatabricksSQLPermissions |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Ler dados da tabela DatabricksSSH |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Ler dados da tabela DatabricksTables |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Ler dados da tabela DatabricksWorkspace |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Ler dados da tabela DeviceAppCrash |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Ler dados da tabela DeviceAppLaunch |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Ler dados da tabela DeviceCalendar |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Ler dados da tabela DeviceCleanup |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Ler dados da tabela DeviceConnectSession |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Ler dados da tabela DeviceEtw |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Ler dados da tabela DeviceHardwareHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Ler dados da tabela DeviceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Ler dados da tabela DeviceHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Ler dados da tabela DeviceSkypeSignIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Ler dados da tabela DeviceSleepState |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Ler dados da tabela DHAppFailure |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Ler dados da tabela DHAppReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Ler dados da tabela DHDriverReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Ler dados da tabela DHLogonFailures |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Ler dados da tabela DHLogonMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Ler dados da tabela DHOSCrashData |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Ler dados da tabela DHOSReliability |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Ler dados da tabela DHWipAppLearning |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Ler dados da tabela DnsEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Ler dados da tabela DnsInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Ler dados da tabela ETWEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/Event/read | Ler dados da tabela Eventos |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Ler dados da tabela Pulsação |
> | Action | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Ler dados da tabela HuntingBookmark |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Ler dados da tabela IISAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Ler dados da tabela InboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Ler dados da tabela InsightsMetrics |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Lê dados da tabela IntuneAuditLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Lê dados da tabela IntuneOperationalLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Ler dados da tabela KubeNodeInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Ler dados da tabela KubePodInventory |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Ler dados da tabela KubeEvents |
> | Action | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Ler dados da tabela LinuxAuditLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Ler dados da tabela MAApplication |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Ler dados da tabela MAApplicationHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Ler dados da tabela MAApplicationInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Ler dados da tabela MAApplicationReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Ler dados da tabela MADeploymentPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Ler dados da tabela MADevice |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Ler dados da tabela MADeviceNotEnrolled |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Ler dados da tabela MADeviceNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Ler dados da tabela MADeviceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Ler dados da tabela MADriverReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Ler dados da tabela MAOfficeAddin |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Ler dados da tabela MAOfficeAddinEntityHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Ler dados da tabela MAOfficeAddinHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Ler dados da tabela MAOfficeAddinHealthEventNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Ler dados da tabela MAOfficeAddinInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Ler dados da tabela MAOfficeAddinReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Ler dados da tabela MAOfficeApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Ler dados da tabela MAOfficeAppCrashesNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Ler dados da tabela MAOfficeAppHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Ler dados da tabela MAOfficeAppInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Ler dados da tabela MAOfficeAppInstanceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Ler dados da tabela MAOfficeAppReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Ler dados da tabela MAOfficeAppSessionsNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Ler dados da tabela MAOfficeBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Ler dados da tabela MAOfficeDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Ler dados da tabela MAOfficeMacroErrorNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Ler dados da tabela MAOfficeMacroGlobalHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Ler dados da tabela MAOfficeMacroSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Ler dados da tabela MAOfficeSuite |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Ler dados da tabela MAOfficeSuiteInstance |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Ler dados da tabela MAProposedPilotDevices |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Ler dados da tabela MAWindowsBuildInfo |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Ler dados da tabela MAWindowsDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | Ler dados da tabela MicrosoftWebApplicationLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | Ler dados da tabela MicrosoftWebFunctionExecutionLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Ler dados da tabela MicrosoftWebStdOutStdErrLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Ler dados da tabela MicrosoftWebW3CLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Ler dados da tabela NetworkMonitoring |
> | Action | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Ler dados da tabela OfficeActivity |
> | Action | Microsoft.OperationalInsights/workspaces/query/Operation/read | Ler dados da tabela Operação |
> | Action | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Ler dados da tabela OutboundConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/Perf/read | Ler dados da tabela Desempenho |
> | Action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Ler dados da tabela ProtectionStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Ler dados da tabela ReservedAzureCommonFields |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Ler dados da tabela ReservedCommonFields |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Ler dados da tabela SecurityAlert |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Ler dados da tabela SecurityBaseline |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Ler dados da tabela SecurityBaselineSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Ler dados da tabela SecurityDetection |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Ler dados da tabela SecurityEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Ler dados da tabela SecurityIoTRawEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Ler dados da tabela SecurityRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Ler dados da tabela SfBAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Ler dados da tabela SPAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Ler dados da tabela SQLAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Ler dados da tabela SQLQueryPerformance |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Ler dados da tabela SqlThreatProtectionLoginAudits |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Ler dados da tabela SqlVulnerabilityAssessmentResult |
> | Action | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Ler dados da tabela Syslog |
> | Action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Ler dados da tabela SysmonEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Lendo dados de qualquer log personalizado |
> | Action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Ler dados da tabela ThreatIntelligenceIndicator |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Ler dados da tabela UAApp |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Ler dados da tabela UAComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Ler dados da tabela UAComputerRank |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Ler dados da tabela UADriver |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Ler dados da tabela UADriverProblemCodes |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Ler dados da tabela UAFeedback |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Ler dados da tabela UAHardwareSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Ler dados da tabela UAIESiteDiscovery |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Ler dados da tabela UAOfficeAddIn |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Ler dados da tabela UAProposedActionPlan |
> | Action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Ler dados da tabela UASysReqIssue |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Ler dados da tabela UAUpgradedComputer |
> | Action | Microsoft.OperationalInsights/workspaces/query/Update/read | Ler dados da tabela Atualização |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Ler dados da tabela UpdateRunProgress |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Ler dados da tabela UpdateSummary |
> | Action | Microsoft.OperationalInsights/workspaces/query/Usage/read | Ler os dados da tabela Uso |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Lê dados da tabela VMBoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Lê dados da tabela VMConnection |
> | Action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Ler dados da tabela W3CIISLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Ler dados da tabela WaaSDeploymentStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Ler dados da tabela WaaSInsiderStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Ler dados da tabela WaaSUpdateStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Ler dados da tabela WDAVStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Ler dados da tabela WDAVThreat |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Ler dados da tabela WindowsFirewall |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/WireData/read | Ler dados da tabela WireData |
> | Action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Dados de leitura da tabela WorkloadMonitoringPerf |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Ler dados da tabela WUDOAggregatedStatus |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Ler dados da tabela WUDOStatus |
> | Action | Microsoft.OperationalInsights/workspaces/read | Obter um workspace existente |
> | Action | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave compartilhada do espaço de trabalho especificado |
> | Action | microsoft.operationalinsights/workspaces/rules/read | Obtenha todas as regras de alerta. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Excluir uma consulta de pesquisa salva |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obter uma consulta de pesquisa salva |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/results/read | Obter resultados de pesquisas salvos. Preterido |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Excluir ações de pesquisa programadas. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obtenha ações de pesquisa programadas. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Crie ou atualize ações de pesquisa agendadas. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Excluir pesquisas agendadas. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obtenha pesquisas agendadas. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Crie ou atualize pesquisas agendadas. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | Criar uma consulta de pesquisa salva |
> | Action | Microsoft.OperationalInsights/workspaces/schema/read | Obter o esquema de pesquisa do workspace.  O esquema de pesquisa inclui os campos expostos e seus tipos. |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Action | microsoft.operationalinsights/workspaces/search/read | Obtenha os resultados da pesquisa. Preterido. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Excluir uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelo Insights Operacionais. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obter uma configuração de armazenamento. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Criar uma nova configuração de armazenamento. Essas configurações são usadas para extrair dados de um local em uma conta de armazenamento existente. |
> | Action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter o log de falha de tradução de atualização da pesquisa para o espaço de trabalho |
> | Action | Microsoft.OperationalInsights/workspaces/usages/read | Obter dados de uso de um workspace e a quantidade de dados lidos pelo workspace. |
> | Action | Microsoft.OperationalInsights/workspaces/write | Criar um novo workspace ou links para um workspace existente fornecendo a ID do cliente do workspace existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | Excluir a Associação de Gerenciamento existente |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | Obter Associação de Gerenciamento existente |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | Crie uma nova Associação de Gerenciamento |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | Exclui a Configuração de Gerenciamento existente |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | Obter Configuração de Gerenciamento existente |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova Configuração de Gerenciamento |
> | Action | Microsoft.OperationsManagement/register/action | Registrar uma assinatura de um provedor de recursos. |
> | Action | Microsoft.OperationsManagement/solutions/delete | Excluir a solução do OMS existente |
> | Action | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Action | Microsoft.OperationsManagement/solutions/write | Criar nova solução do OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | Action | Microsoft.PolicyInsights/operations/read | Obtém operações com suporte no namespace Microsoft. PolicyInsights |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | Consultar as informações sobre os eventos de política. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/read | Consultar as informações sobre os eventos de política. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | Consultar as informações sobre os estados de política. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/read | Consultar as informações sobre os estados de política. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | Consultar as informações de resumo sobre os estados de política mais recentes. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | Consultar as informações de resumo sobre os estados de política mais recentes. |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Dispara uma nova avaliação de conformidade para o escopo selecionado. |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Informações de consulta sobre os recursos exigidos por políticas de DeployIfNotExists. |
> | Action | Microsoft.PolicyInsights/register/action | Registra o provedor de recursos de informações da política da Microsoft e habilita ações nele. |
> | Action | Microsoft.PolicyInsights/remediations/cancel/action | Cancelar as correções de política da Microsoft em andamento. |
> | Action | Microsoft.PolicyInsights/remediations/delete | Exclua correções da política. |
> | Action | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implantações exigidas por uma correção da política. |
> | Action | Microsoft.PolicyInsights/remediations/read | Obtenha correções da política. |
> | Action | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar as correções da política da Microsoft. |
> | Action | Microsoft.PolicyInsights/unregister/action | Cancela o registro do provedor de recursos de informações da política da Microsoft. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Portal/consoles/delete | Remove a instância do Cloud Shell. |
> | Action | Microsoft.Portal/consoles/read | Lê a instância do Cloud Shell. |
> | Action | Microsoft.Portal/consoles/write | Crie ou atualize uma instância do Cloud Shell. |
> | Action | Microsoft.Portal/dashboards/delete | Remove o painel da assinatura. |
> | Action | Microsoft.Portal/dashboards/read | Lê os painéis para a assinatura. |
> | Action | Microsoft.Portal/dashboards/write | Adicione ou modifique o painel em uma assinatura. |
> | Action | Microsoft.Portal/register/action | Registra-se no Portal |
> | Action | Microsoft.Portal/usersettings/delete | Remove as configurações de usuário do Cloud Shell. |
> | Action | Microsoft.Portal/usersettings/read | Lê as configurações de usuário do Cloud Shell. |
> | Action | Microsoft.Portal/usersettings/write | Crie ou atualize as configurações de usuário do Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.PowerBIDedicated/capacities/delete | Excluir a capacidade dedicada do Power BI. |
> | Action | Microsoft.PowerBIDedicated/capacities/read | Recuperar as informações da Capacidade Dedicada especificada do Power BI. |
> | Action | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a Capacidade. |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | Recuperar informações de SKU disponíveis para a Capacidade |
> | Action | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a Capacidade. |
> | Action | Microsoft.PowerBIDedicated/capacities/write | Criar ou atualizar a capacidade dedicada especificada do Power BI. |
> | Action | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Verificar se o nome da capacidade dedicada do Power BI é válido e não está em uso. |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | Recuperar as informações do resultado da operação especificada. |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recuperar as informações do status da operação especificada. |
> | Action | Microsoft.PowerBIDedicated/operations/read | Recuperar as informações das operações |
> | Action | Microsoft.PowerBIDedicated/register/action | Registra o provedor de recursos dedicado do Power BI. |
> | Action | Microsoft.PowerBIDedicated/skus/read | Recuperar as informações de SKUs |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | Criar um item protegido de backup |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | Retorna a lista de todos os Itens Protegidos. |
> | Action | microsoft.recoveryservices/Locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Validar recursos |
> | Action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Verifica se a Disponibilidade do Nome do Recurso é uma API para verificar se o nome do recurso está disponível |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Action | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Action | Microsoft.RecoveryServices/register/action | Registrar assinatura de um determinado provedor de recursos |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/write | Atualizar configuração para o cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupEngines/read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Excluir uma Intenção de Proteção de backup |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Retorna o status da operação |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Exclui o Contêiner registrado |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Consultar cargas de trabalho em um contêiner |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação realizada no Contêiner de Proteção. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Faz Backup de Item Protegido. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Excluir item protegido |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Realizada em Itens Protegidos. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retorna o status da Operação realizada em Itens Protegidos. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retorna os detalhes do objeto do Item Protegido |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar Pontos de Recuperação de Itens Protegidos. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Criar um contêiner registrado |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | Cancelar o trabalho |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | Retorna o Resultado da Operação de Trabalho. |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
> | Action | microsoft.recoveryservices/Vaults/backupJobsExport/action | Exportar Trabalhos |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | Retorna o Resultado da Operação de Backup do Cofre de Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/delete | Excluir uma política de proteção |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/write | Criar uma política de proteção |
> | Action | microsoft.recoveryservices/Vaults/backupProtectableItems/read | Retorna a lista de todos os Itens a Proteger. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Retorna a lista de todos os Itens Protegidos. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Retorna todos os contêineres pertencentes à assinatura |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Action | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Retornar a informação de PIN de segurança para o cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Atualizar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Action | microsoft.recoveryservices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar Certificado de Recurso atualiza o certificado de credencial de cofre/recurso. |
> | Action | Microsoft.RecoveryServices/Vaults/delete | A operação Excluir Cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obter a configuração de notificação do cofre dos Serviços de Recuperação. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configurar notificações por email para o cofre dos Serviços de Recuperação. |
> | Action | Microsoft.RecoveryServices/Vaults/read | A operação Obter Cofre obtém um objeto representando o recurso do Azure de tipo 'cofre' |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação Cancelar Registro do Contêiner pode ser usada para cancelar o registro de um contêiner. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter Resultados da Operação pode ser usada para obter o status e o resultado da operação para a operação enviada de forma assíncrona |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter Contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar Contêiner de Serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as configurações de alertas |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a Consistência da Malha |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Excluir malhas |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implantar imagem do servidor de processo |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar do Fabric para o AAD |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover malha |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler quaisquer redes lógicas |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Excluir quaisquer mapeamentos de rede |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar quaisquer mapeamentos de rede |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Descobrir item que pode ser protegido |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover o contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Exclui os Itens de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migra o Item |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Lê os Pontos de Recuperação de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Lê os Itens de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testa a Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testa a Limpeza de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Cria ou atualiza os Itens de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Adicionar discos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Excluir quaisquer itens protegidos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover Planejado |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover item protegido |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remover discos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Enviar Comentário |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler qualquer tamanhos da computação de destino |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar o Serviço de Mobilidade |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar os itens protegidos |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Excluir quaisquer mapeamentos de contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover o mapeamento de contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar os mapeamentos de contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Alterar contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar quaisquer contêineres de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Excluir quaisquer provedores dos Serviços de Recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover o provedor dos Serviços de Recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar Provedores de Serviços de Recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Excluir mapeamentos de classificação de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar os mapeamentos de classificação de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ler quaisquer vCenters |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Cria ou atualiza quaisquer vCenters |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar malhas |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar Trabalho |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar o trabalho |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar o trabalho |
> | Action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Lê os Itens de Migração |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler quaisquer redes |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Excluir políticas |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar as políticas |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Excluir planos de recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar planos de recuperação |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Lê qualquer  |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler usos de replicação do cofre |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler qualquer integridade de replicação do cofre |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar integridade de cofre |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Lê qualquer  |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Criar ou atualizar qualquer  |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler quaisquer vCenters |
> | Action | microsoft.recoveryservices/Vaults/usages/read | Retorna detalhes de uso do Cofre de Serviços de Recuperação. |
> | Action | Microsoft.RecoveryServices/vaults/usages/read | Ler usos de cofre |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação do Token do Cofre pode ser usada para obter o Token do Cofre para operações de back-end do nível do cofre. |
> | Action | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do Azure do tipo 'cofre' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Relay/checkNameAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. |
> | Action | Microsoft.Relay/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. Essa API foi preterida. Use CheckNameAvailability. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Action | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter a Cadeia de Conexão para o Namespace |
> | Action | Microsoft.Relay/namespaces/authorizationRules/read | Obter a lista de descrições de Regras de Autorização de Namespace. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.Relay/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Action | Microsoft.Relay/namespaces/Delete | Excluir Recurso de Namespace |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves de regras de autorização para o namespace principal de recuperação de desastre |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace principal de recuperação de desastre |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a Recuperação de desastre e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de GEO DR e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração de Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar o HybridConnection. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para excluir regras de autorização HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de conexão para HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista das Regras d Autorização do HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Criar regras de autorização HybridConnection e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para excluir o recurso HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos HybridConnection |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou atualizar propriedades HybridConnection. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/operationresults/read | Obter o status da operação do Namespace |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Action | Microsoft.Relay/namespaces/read | Obter a lista de Descrições dos Recursos de Namespace |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remover namespace de ACS |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar o WcfRelay. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para excluir regras de autorização WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de conexão para WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de Regras de Autorização do WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Criar regras de autorização WcfRelay e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para excluir o recurso WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou atualizar propriedades WcfRelay. |
> | Action | Microsoft.Relay/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Action | Microsoft.Relay/operations/read | Obter Operações |
> | Action | Microsoft.Relay/register/action | Registra a assinatura para o provedor de recursos de Retransmissão e habilita a criação de recursos de Retransmissão |
> | Action | Microsoft.Relay/unregister/action | Registra a assinatura para o provedor de recursos de Retransmissão e habilita a criação de recursos de Retransmissão |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtém o status de disponibilidade para o recurso especificado |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtém os status de disponibilidade para todos os recursos no escopo especificado |
> | Action | Microsoft.ResourceHealth/events/read | Obter Eventos de Integridade do Serviço para a assinatura informada |
> | Action | Microsoft.Resourcehealth/healthevent/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.Resourcehealth/healthevent/Activated/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.Resourcehealth/healthevent/InProgress/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.Resourcehealth/healthevent/Pending/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.Resourcehealth/healthevent/Resolved/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | Indica a alteração no estado de integridade do recurso especificado |
> | Action | Microsoft.ResourceHealth/impactedResources/read | Obter Recursos Afetados para a assinatura informada |
> | Action | Microsoft.ResourceHealth/metadata/read | Obtém metadados |
> | Action | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/register/action | Registra a assinatura para o Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/unregister/action | Cancela o registro da assinatura para o Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Resources/checkPolicyCompliance/action | Verifique o status de conformidade de um determinado recurso em relação às políticas de recurso. |
> | Action | Microsoft.Resources/checkResourceName/action | Verificar a validade do nome do recurso. |
> | Action | Microsoft.Resources/deployments/cancel/action | Cancela uma implantação. |
> | Action | Microsoft.Resources/deployments/delete | Exclui uma implantação. |
> | Action | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Action | Microsoft.Resources/deployments/read | Obtém ou lista implantações. |
> | Action | Microsoft.Resources/deployments/validate/action | Valida uma implantação. |
> | Action | Microsoft.Resources/deployments/whatIf/action | Prevê as alterações de implantação de modelo. |
> | Action | Microsoft.Resources/deployments/write | Cria ou atualiza uma implantação. |
> | Action | Microsoft.Resources/links/delete | Exclui um link de recursos. |
> | Action | Microsoft.Resources/links/read | Obtém ou lista links de recursos. |
> | Action | Microsoft.Resources/links/write | Cria ou atualiza um link de recursos. |
> | Action | Microsoft.Resources/marketplace/purchase/action | Compra um recurso do marketplace. |
> | Action | Microsoft.Resources/providers/read | Obter a lista de provedores. |
> | Action | Microsoft.Resources/resources/read | Obter a lista de recursos com base em filtros. |
> | Action | Microsoft.Resources/subscriptions/locations/read | Obtém a lista de localizações com suporte. |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de assinatura. |
> | Action | Microsoft.Resources/subscriptions/providers/read | Obtém ou lista os provedores de recurso. |
> | Action | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/delete | Exclui um grupo de recursos e todos os seus recursos. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtém ou lista status de operação de implantação. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtém ou lista implantações. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Cria ou atualiza uma implantação. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Move recursos de um grupo de recursos para outro. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtém os recursos para o grupo de recursos. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar movimentação de recursos de um grupo de recursos para outro. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/write | Cria ou atualiza um grupo de recursos. |
> | Action | Microsoft.Resources/subscriptions/resources/read | Obtém recursos de uma assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/delete | Exclui uma marca de assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/read | Obtém ou lista marcas de assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Exclui um valor da marca de assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtém ou lista os valores da marca de assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adiciona um valor da marca de assinatura. |
> | Action | Microsoft.Resources/subscriptions/tagNames/write | Adiciona uma marca de assinatura. |
> | Action | Microsoft.Resources/tags/delete | Remove todas as marcas em um recurso. |
> | Action | Microsoft.Resources/tags/read | Obtém todas as marcas em um recurso. |
> | Action | Microsoft.Resources/tags/write | Atualiza as marcas em um recurso substituindo ou mesclando as marcas existentes por um novo conjunto de marcas ou removendo as marcas existentes. |
> | Action | Microsoft.Resources/tenants/read | Obtém a lista de locatários. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Scheduler/jobcollections/delete | Exclui a coleção de trabalhos. |
> | Action | Microsoft.Scheduler/jobcollections/disable/action | Desabilita a coleção de trabalhos. |
> | Action | Microsoft.Scheduler/jobcollections/enable/action | Habilita a coleção de trabalhos. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/delete | Exclui o trabalho. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gera uma definição de Aplicativo Lógico com base em um Trabalho do Agendador. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtém o histórico de trabalhos. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/read | Obtém o trabalho. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa o trabalho. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/write | Cria ou atualiza o trabalho. |
> | Action | Microsoft.Scheduler/jobcollections/read | Obter Coleção de Trabalhos |
> | Action | Microsoft.Scheduler/jobcollections/write | Cria ou atualiza a coleção de trabalhos. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Search/checkNameAvailability/action | Verificar a disponibilidade do nome do serviço. |
> | Action | Microsoft.Search/operations/read | Lista todas as operações disponíveis do provedor Microsoft.Search. |
> | Action | Microsoft.Search/register/action | Registrar a assinatura do provedor de recursos de pesquisa e permitir a criação de serviços de pesquisa. |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | Criar a chave de consulta. |
> | Action | Microsoft.Search/searchServices/delete | Excluir o serviço de pesquisa. |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | Excluir a chave de consulta. |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | Ler as chaves de administração. |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | Retorna a lista de chaves de API de consulta para o serviço Azure Search especificado. |
> | Action | Microsoft.Search/searchServices/read | Ler o serviço de pesquisa. |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenerar a chave de administração. |
> | Action | Microsoft.Search/searchServices/start/action | Iniciar o serviço de pesquisa. |
> | Action | Microsoft.Search/searchServices/stop/action | Interromper o serviço de pesquisa. |
> | Action | Microsoft.Search/searchServices/write | Criar ou atualizar o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Impõe as regras de proteção de tráfego determinadas criando regras de segurança correspondentes nos grupos de segurança de rede determinados |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/read | Obtém recomendações de proteção de rede adaptável de um recurso protegido do Azure |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | Obtém as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | Atualiza as Configurações de Proteção de Ameaça Avançadas para o recurso |
> | Action | Microsoft.Security/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Action | Microsoft.Security/applicationWhitelistings/read | Obter a lista de liberações do aplicativo |
> | Action | Microsoft.Security/applicationWhitelistings/write | Criar uma nova lista de exceções do aplicativo ou atualizar uma existente |
> | Action | Microsoft.Security/complianceResults/read | Obter os resultados de conformidade para o recurso |
> | Action | Microsoft.Security/informationProtectionPolicies/read | Obtém as políticas de proteção de informações para o recurso |
> | Action | Microsoft.Security/informationProtectionPolicies/write | Atualiza as políticas de proteção de informações para o recurso |
> | Action | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | Ignorar um alerta de segurança |
> | Action | Microsoft.Security/locations/alerts/read | Obter todos os alertas de segurança disponíveis |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Exclui a política de acesso de rede just-in-time |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia uma solicitação de política de acesso de rede just-in-time |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obter as políticas de acesso de rede just-in-time |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Criar uma nova política de acesso de rede just-in-time ou atualizar uma existente |
> | Action | Microsoft.Security/locations/read | Obter o local dos dados de segurança |
> | Action | Microsoft.Security/locations/tasks/activate/action | Ativar recomendação de segurança |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | Ignorar recomendação de segurança |
> | Action | Microsoft.Security/locations/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Action | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Action | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Action | Microsoft.Security/policies/read | Obter a política de segurança |
> | Action | Microsoft.Security/policies/write | Atualizar a política de segurança |
> | Action | Microsoft.Security/pricings/delete | Excluir as configurações de preço para o escopo |
> | Action | Microsoft.Security/pricings/read | Obter as configurações de preço para o escopo |
> | Action | Microsoft.Security/pricings/write | Atualizar as configurações de preço para o escopo |
> | Action | Microsoft.Security/register/action | Registrar a assinatura da Central de Segurança do Azure |
> | Action | Microsoft.Security/securityContacts/delete | Excluir o contato de segurança |
> | Action | Microsoft.Security/securityContacts/read | Obter o contato de segurança |
> | Action | Microsoft.Security/securityContacts/write | Atualizar o contato de segurança |
> | Action | Microsoft.Security/securitySolutions/delete | Excluir uma solução de segurança |
> | Action | Microsoft.Security/securitySolutions/read | Obter as soluções de segurança |
> | Action | Microsoft.Security/securitySolutions/write | Criar uma nova solução de segurança ou atualizar uma existente |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | Obter dados de referência das soluções de segurança |
> | Action | Microsoft.Security/securityStatuses/read | Obter status de integridade da segurança para recursos do Azure |
> | Action | Microsoft.Security/securityStatusesSummaries/read | Obter os resumos de status de segurança para o escopo |
> | Action | Microsoft.Security/settings/read | Obtém as configurações para o escopo |
> | Action | Microsoft.Security/settings/write | Atualiza as configurações do escopo |
> | Action | Microsoft.Security/tasks/read | Obter todas as recomendações de segurança disponíveis |
> | Action | Microsoft.Security/unregister/action | Cancelar a assinatura da Central de Segurança do Azure |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | Excluir um firewall do aplicativo Web |
> | Action | Microsoft.Security/webApplicationFirewalls/read | Obter firewalls do aplicativo Web |
> | Action | Microsoft.Security/webApplicationFirewalls/write | Criar um novo firewall do aplicativo Web ou atualizar um existente |
> | Action | Microsoft.Security/workspaceSettings/connect/action | Alterar configurações de reconexão das configurações do workspace |
> | Action | Microsoft.Security/workspaceSettings/delete | Excluir as configurações do workspace |
> | Action | Microsoft.Security/workspaceSettings/read | Obter as configurações do workspace |
> | Action | Microsoft.Security/workspaceSettings/write | Atualizar as configurações do workspace |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | Exclusão de uma configuração de diagnóstico |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | Leitura de uma configuração de diagnóstico |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | Gravação de uma configuração de diagnóstico |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Leitura de uma categoria de configuração de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace na assinatura determinada. Essa API foi preterida. Use CheckNameAvailability. |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras de VNet no Provedor de Recursos do ServiceBus para a VNet especificada |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | Atualizar regra de autorização de namespace. Essa API está preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace. Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização do namespace padrão não pode ser excluída.  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter a Cadeia de Conexão para o Namespace |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obter a lista de descrições de Regras de Autorização de Namespace. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | Criar regras de autorização no nível do namespace e atualizar suas propriedades. Os direitos de acesso das regras de autorização; as chaves primárias e secundárias podem ser atualizadas. |
> | Action | Microsoft.ServiceBus/namespaces/Delete | Excluir Recurso de Namespace |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves de regras de autorização para o namespace principal de recuperação de desastre |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace principal de recuperação de desastre |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a Recuperação de desastre e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Excluir a configuração da recuperação de desastre associada ao namespace. Essa operação somente pode ser invocada por meio do namespace primário. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de GEO DR e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração de Recuperação de desastre associada ao namespace. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Exclui o filtro de Grade de Eventos associado com o namespace. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtém o filtro de Grade de Eventos associado com o namespace. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Cria ou atualiza o filtro de Grade de Eventos associado com o namespace. |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos de EventHub |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Excluir Recurso de Filtro IP |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obter Recurso de Filtro IP |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Criar Recurso de Filtro IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Procurar messges |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Adiar mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Mensagens cronograma |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Enviar mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Definir estado da sessão |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obter o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualizar o Plano de Mensagens para um namespace.<br>Essa API está preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso Namespace (pai) nas versões posteriores da API.<br>Esta operação não tem suporte na versão da API 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | Operação de migrar namespace |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Exclui a Configuração de migração. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtém a Configuração de migração que indica o estado da migração e operações de replicação pendentes |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverte a migração do namespace padrão ao premium |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Atribui o DNS associado com o namespace padrão ao namespace premium que conclui a migração e interrompe os recursos de sincronização do namespace padrão ao premium |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Cria ou Atualiza a Configuração de migração. Isso iniciará a sincronização de recursos do namespace padrão ao premium |
> | Action | Microsoft. ServiceBus/namespaces/networkruleset/Delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft. ServiceBus/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Action | Microsoft. ServiceBus/namespaces/networkruleset/Write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o status da operação do Namespace |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recurso de configurações de diagnóstico do namespace |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições do recurso de log do namespace |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de métrica de descrições de recurso de métrica do namespace |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a Fila. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para excluir regras de autorização de fila |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de conexão para fila |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Criar regras de autorização de fila e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para excluir o recurso Queue |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições do recurso Queue |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | Criar ou atualizar propriedades Queue. |
> | Action | Microsoft.ServiceBus/namespaces/read | Obter a lista de Descrições dos Recursos de Namespace |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remover namespace de ACS |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o Tópico. Esta operação não tem suporte na versão da API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a Regra de Autorização. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para excluir regras de autorização do tópico |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de conexão para tópico |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização do tópico |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária para o Recurso |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Criar regras de autorização do tópico e atualizar suas propriedades. Os Direitos de Acesso das Regras de Autorização podem ser atualizados. |
> | Action | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para excluir o recurso Topic |
> | Action | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recurso Topic |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para excluir o recurso TopicSubscription |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições do recurso TopicSubscription |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para excluir o recurso Rule |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições do recurso Rule |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou atualizar propriedades Rule. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou atualizar propriedades TopicSubscription. |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | Criar ou atualizar propriedades Topic. |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Excluir o Recurso de Regra VNET |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra VNET |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Action | Microsoft.ServiceBus/namespaces/write | Criar um recurso de namespace e atualizar suas propriedades. Marcas e Capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Action | Microsoft.ServiceBus/operations/read | Obter Operações |
> | Action | Microsoft.ServiceBus/register/action | Registra a assinatura para o provedor de recurso do ServiceBus e habilita a criação dos recursos do ServiceBus |
> | Action | Microsoft.ServiceBus/sku/read | Obter lista de Descrições de Recursos de SKU |
> | Action | Microsoft.ServiceBus/sku/regions/read | Obter lista de Descrições de Recursos SkuRegions |
> | Action | Microsoft.ServiceBus/unregister/action | Registra a assinatura para o provedor de recurso do ServiceBus e habilita a criação dos recursos do ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | Excluir qualquer Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | Excluir qualquer Serviço |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer Partição |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer Réplica |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer Serviço |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer Status de Serviço |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou Atualizar qualquer Serviço |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | Criar ou Atualizar qualquer Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Excluir qualquer Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Excluir qualquer Versão de Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer Versão de Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou Atualizar qualquer Versão de Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou Atualizar qualquer Tipo de Aplicativo |
> | Action | Microsoft.ServiceFabric/clusters/delete | Excluir qualquer Cluster |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer Nó |
> | Action | Microsoft.ServiceFabric/clusters/read | Ler qualquer Cluster |
> | Action | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer Status do Cluster |
> | Action | Microsoft.ServiceFabric/clusters/write | Criar ou Atualizar qualquer Cluster |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer Versão do Cluster |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer Versão de Cluster de um ambiente específico |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | Ler qualquer Resultado de Operação |
> | Action | Microsoft.ServiceFabric/locations/operations/read | Ler qualquer Operação por Local |
> | Action | Microsoft.ServiceFabric/operations/read | Ler qualquer Operação Disponível |
> | Action | Microsoft.ServiceFabric/register/action | Registrar qualquer Ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se um nome está disponível para uso com um novo serviço SignalR |
> | Action | Microsoft.SignalRService/locations/operationresults/signalr/read | Consultar o status de uma operação assíncrona |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Consultar o status de uma operação assíncrona |
> | Action | Microsoft.SignalRService/locations/usages/read | Obtém os usos de cota para o Serviço do Azure SignalR |
> | Action | Microsoft.SignalRService/operationresults/read | Consultar o status de uma operação assíncrona |
> | Action | Microsoft.SignalRService/operationstatus/read | Consultar o status de uma operação assíncrona |
> | Action | Microsoft.SignalRService/register/action | Registra o provedor de recursos 'Microsoft.SignalRService' com uma assinatura |
> | Action | Microsoft.SignalRService/SignalR/delete | Exclui todo o Serviço do SignalR |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Excluir um filtro de grade de eventos de um Signalr. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | Obtenha as propriedades do filtro de grade de eventos especificado ou liste todos os filtros de grade de eventos para o Signalr especificado. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | Crie ou atualize um filtro de grade de eventos para um Signalr com os parâmetros especificados. |
> | Action | Microsoft.SignalRService/SignalR/listkeys/action | Exibe o valor das chaves de acesso do SignalR no portal de gerenciamento ou por meio da API |
> | Action | Microsoft.SignalRService/SignalR/read | Exibe as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Action | Microsoft.SignalRService/SignalR/regeneratekey/action | Altera o valor das chaves de acesso do SignalR no portal de gerenciamento ou por meio da API |
> | Action | Microsoft.SignalRService/SignalR/restart/action | Reinicia um Serviço do Azure SignalR no portal de gerenciamento ou por meio da API. Haverá algum tempo de inatividade. |
> | Action | Microsoft.SignalRService/SignalR/write | Modifica as definições e as configurações do SignalR no portal de gerenciamento ou por meio da API |
> | Action | Microsoft.SignalRService/unregister/action | Cancela o registro do provedor de recursos 'Microsoft.SignalRService' com uma assinatura |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lista os artefatos de aplicativo da definição de aplicativo. |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | Remove uma definição de aplicativo. |
> | Action | Microsoft.Solutions/applicationDefinitions/read | Recupera uma lista de definições de aplicativos. |
> | Action | Microsoft.Solutions/applicationDefinitions/write | Adicione ou modifique uma definição de aplicativo. |
> | Action | Microsoft.Solutions/applications/applicationArtifacts/read | Lista os artefatos de aplicativo. |
> | Action | Microsoft.Solutions/applications/delete | Remove um aplicativo. |
> | Action | Microsoft.Solutions/applications/read | Recupera uma lista de aplicativos. |
> | Action | Microsoft.Solutions/applications/refreshPermissions/action | Atualiza as permissões de aplicativo. |
> | Action | Microsoft.Solutions/applications/updateAccess/action | Atualiza o acesso de aplicativo. |
> | Action | Microsoft.Solutions/applications/write | Cria um aplicativo. |
> | Action | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Action | Microsoft.Solutions/jitRequests/read | Recupera uma lista de JitRequests |
> | Action | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | Lê o status da operação do recurso. |
> | Action | Microsoft.Solutions/register/action | Registre-se no Solutions. |
> | Action | Microsoft.Solutions/unregister/action | Cancela o registro de Soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Sql/checkNameAvailability/action | Verificar se o nome do servidor fornecido está disponível para provisionamento no mundo inteiro para uma determinada assinatura. |
> | Action | Microsoft.Sql/instancePools/delete | Exclui um pool de instância |
> | Action | Microsoft.Sql/instancePools/read | Obtém um pool de instância |
> | Action | Microsoft.Sql/instancePools/usages/read | Obtém as informações de uso do pool de instâncias |
> | Action | Microsoft.Sql/instancePools/write | Cria ou atualiza um pool de instância |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Action | Microsoft.Sql/locations/capabilities/read | Obter as capacidades para essa assinatura em um determinado local |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | Obter o status de uma operação de banco de dados. |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obter operações em andamento no servidor excluído |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | Obter operações em andamento no servidor excluído |
> | Action | Microsoft.Sql/locations/deletedServers/read | Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado. |
> | Action | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor excluído |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obter a operação assíncrona do Azure para uma operação assíncrona de pool elástico |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obter o resultado de uma operação de pool elástico. |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recuperar resultado da política de auditoria de blob de servidor estendida Definir operação |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtém o status de uma operação de regra de firewall. |
> | Action | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtém o status de uma operação de regra de firewall. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Exclui um grupo de failover de instância existente. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa o failover planejado em um grupo de failover de instância existente. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa o failover forçado em um grupo de failover de instância existente. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | Retorna a lista de grupos de failover de instância ou obtém as propriedades do grupo de failover de instância especificado. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de failover de instância com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover de instância especificado. |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtém o status de uma operação de pool de instâncias |
> | Action | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtém o resultado de uma operação de pool de instâncias |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Devolve os detalhes de uma operação assíncrona do Azure de ponto de extremidade de interface específico |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Devolve os detalhes da operação de perfil de ponto de extremidade de interface específico |
> | Action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o status de uma operação do agente de trabalho. |
> | Action | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação do agente de trabalho. |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os servidores de um local |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em todos os bancos de dados em um servidor |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Exclui um backup de retenção de longo prazo |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para um banco de dados |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Concluir a operação de restauração do banco de dados gerenciado |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obter operações em andamento na criptografia de dados transparente do banco de dados gerenciado |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Action | Microsoft.Sql/locations/read | Obter os locais disponíveis para uma determinada assinatura |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/serverKeyOperationResults/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | Recuperar resultado da operação do recurso do agente de sincronização |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | Recuperar as IDs do banco de dados de sincronização para uma assinatura e região específica |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | Recuperar resultado da operação do recurso de grupo de sincronização |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | Recuperar resultado da operação do recurso de membro de sincronização |
> | Action | Microsoft.Sql/locations/usages/read | Obter uma coleção de métricas de uso para essa assinatura em um local |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retornar os detalhes da operação assíncrona de regras de rede virtual especificada do Azure  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Retornar os detalhes da operação de regras de rede virtual especificada  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | Excluir um administrador existente da instância gerenciada. |
> | Action | Microsoft.Sql/managedInstances/administrators/read | Obter uma lista de administradores de instância gerenciada. |
> | Action | Microsoft.Sql/managedInstances/administrators/write | Criar ou atualizar o administrador da instância gerenciada com os parâmetros especificados. |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Action | Microsoft.Sql/managedInstances/databases/delete | Excluir um banco de dados gerenciado existente |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para os bancos de dados de instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/databases/read | Obter o banco de dados gerenciado existente |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | Obtenha um esquema de banco de dados gerenciado. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Obter uma tabela de banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados gerenciadas configuradas para um determinado servidor |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recuperar os eventos de segurança do banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um givendatabase |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/managedInstances/databases/write | Cria um novo banco de dados ou atualiza um banco de dados existente. |
> | Action | Microsoft.Sql/managedInstances/delete | Excluir uma instância gerenciada existente. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Action | Microsoft.Sql/managedInstances/keys/delete | Excluir uma chave de Instância Gerenciada do Azure SQL. |
> | Action | Microsoft.Sql/managedInstances/keys/read | Retornar as chaves da lista de instância gerenciada ou obter as propriedades para a instância gerenciada especificada. |
> | Action | Microsoft.Sql/managedInstances/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições de métrica de instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para instâncias gerenciadas |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Devolver os tipos de métricas que estão disponíveis para instâncias gerenciadas |
> | Action | Microsoft.Sql/managedInstances/read | Retornar a lista de instâncias gerenciadas ou obter as propriedades para a instância gerenciada especificada. |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | Retorna uma lista de bancos de dados gerenciados recuperáveis |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado descartado |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Devolve uma lista de bancos de dados gerenciados soltos restauráveis. |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor gerenciado configuradas para um determinado servidor |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em uma determinada instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para determinada instância gerenciada |
> | Action | Microsoft.Sql/managedInstances/write | Criar uma instância gerenciada com os parâmetros especificados ou atualizar as propriedades ou marcas para a instância gerenciada especificada. |
> | Action | Microsoft.Sql/operations/read | Obter as operações REST disponíveis |
> | Action | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Action | Microsoft.Sql/register/action | Registrar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | Obter operações em andamento nos administradores do servidor |
> | Action | Microsoft.Sql/servers/administrators/delete | Excluir administrador do servidor |
> | Action | Microsoft.Sql/servers/administrators/read | Recuperar detalhes do administrador do servidor |
> | Action | Microsoft.Sql/servers/administrators/write | Criar ou atualizar o administrador do servidor |
> | Action | Microsoft.Sql/servers/advisors/read | Retornar a lista de consultores disponíveis para o servidor |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o servidor |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Action | Microsoft.Sql/servers/advisors/write | Atualizar status de execução automática de um assistente no nível do servidor. |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela do servidor padrão configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | Alterar a auditoria da tabela de servidor padrão para determinado servidor |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recuperar o resultado da operação Definir da política de auditoria do blob do servidor |
> | Action | Microsoft.Sql/servers/auditingSettings/read | Recuperar detalhes da política de auditoria de blob do servidor configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de blob de servidor para determinado servidor |
> | Action | Microsoft.Sql/servers/automaticTuning/read | Retornar as configurações de ajuste automático do servidor |
> | Action | Microsoft.Sql/servers/automaticTuning/write | Atualizar as configurações de ajuste automático do servidor e retornar as configurações atualizadas |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Excluir um cofre de arquivos de backup existente. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Essa operação é usada para obter um cofre de retenção de backup de longo prazo. Ela retorna informações sobre o cofre registrado para esse servidor |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é utilizada para registrar uma retenção de backup em longo prazo em um servidor |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | Excluir um link de comunicação com o servidor existente. |
> | Action | Microsoft.Sql/servers/communicationLinks/read | Retornar a lista de links de comunicação de um servidor especificado. |
> | Action | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar um link de comunicação com o servidor. |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | Retornar a lista de política de conexão do servidor de um servidor especificado. |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de conexão do servidor. |
> | Action | Microsoft.Sql/servers/databases/advisors/read | Retornar a lista de consultores disponíveis para o banco de dados |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o banco de dados |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada no banco de dados |
> | Action | Microsoft.Sql/servers/databases/advisors/write | Atualizar status de um assistente no nível do banco de dados. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | Recuperar detalhes da política de auditoria de blob configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de blob para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | Retornar as configurações de ajuste automático de um banco de dados |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | Atualizar as configurações de ajuste automático de um banco de dados e retornar as configurações atualizadas |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obter o status de uma operação de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivamento de backup de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | Recuperar detalhes da política de conexão configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar política de conexão para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Retornar a lista de políticas de máscara de dados do banco de dados. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Excluir regra de política de máscara de dados para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recuperar detalhes da regra de política de mascaramento de dados configurada em determinado servidor |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Alterar regra de política de mascaramento de dados para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar política de mascaramento de dados para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Retornar as informações de etapa de consulta distribuída da consulta de data warehouse para ID da etapa selecionada |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retornar as informações de consulta de distribuição do data warehouse para a ID de consulta selecionada |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recuperar as atividades do usuário de uma instância do SQL Data Warehouse que inclui consultas suspensas e em execução |
> | Action | Microsoft.Sql/servers/databases/delete | Excluir um banco de dados existente. |
> | Action | Microsoft.Sql/servers/databases/export/action | Exportar Banco de Dados SQL do Azure |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob estendida para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/extensions/read | Obter uma coleção de extensões para o banco de dados. |
> | Action | Microsoft.Sql/servers/databases/extensions/write | Alterar a extensão de um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/failover/action | Failover de banco de dados iniciado pelo cliente. |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recuperar políticas do backup geográfico para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política do backup geográfico de banco de dados |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para um banco de dados selecionado. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém a manutenção de configurações do windows para um banco de dados selecionado. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define a manutenção de configurações do windows para um banco de dados selecionado. |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.Sql/servers/databases/metrics/read | Métricas de retorno para bancos de dados |
> | Action | Microsoft.Sql/servers/databases/move/action | Altere o nome de um banco de dados existente. |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | Obter o status de uma operação de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | Cancelar a operação assíncrona pendente do Banco de Dados SQL do Azure que ainda não está concluída. |
> | Action | Microsoft.Sql/servers/databases/operations/read | Retornar a lista de operações realizadas no banco de dados |
> | Action | Microsoft.Sql/servers/databases/pause/action | Pausar Banco de Dados do DataWarehouse do Azure SQL |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para bancos de dados |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Retornar a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | Retornar os valores atuais das configurações do Repositório de Consultas do banco de dados. |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | Atualizar a configuração do repositório de consultas do banco de dados |
> | Action | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | Encerrar a relação de replicação de modo forçado e com potencial perda de dados |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na relação de replicação\u0027s primária e tornando o primário remoto em um secundário |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover imediatamente com possibilidade de perda de dados, tornando esse banco de dados o principal da relação de replicação\u0027s e tornando o primário remoto um secundário |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para o vincular ao modo síncrono ou assíncrono |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | Criar um novo ponto de restauração |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | Exclui um ponto de restauração do banco de dados. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | Retornar os pontos de restauração do banco de dados. |
> | Action | Microsoft.Sql/servers/databases/resume/action | Retomar o Banco de Dados do Datawarehouse do Azure SQL |
> | Action | Microsoft.Sql/servers/databases/schemas/read | Obtenha um esquema de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de confidencialidade de uma determinada coluna |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de banco de dados. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recuperar a lista de recomendações de índice em um banco de dados |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar a recomendação de índice |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados configuradas para um determinado servidor |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | Obter uma coleção de métricas de segurança de banco de dados |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Listar rótulos de confidencialidade de um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retornar sugestão sobre escalar ou reduzir o banco de dados verticalmente com base nas estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos |
> | Action | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de SKUs disponíveis para um banco de dados |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar a sincronização do grupo de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | Excluir um grupo de sincronização existente. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Retornar a lista de esquemas do banco de dados do hub de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | Retornar a lista de logs do grupo de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar o esquema do banco de dados do hub de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do hub de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Excluir um membro de sincronização existente. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar o esquema do membro de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recuperar resultado da operação de atualização do esquema do membro de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Retornar a lista de esquemas do banco de dados de membros de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Criar um membro de sincronização com os parâmetros especificados ou atualiza as propriedades do membro de sincronização especificado. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Disparar a sincronização do grupo de sincronização |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | Criar um grupo de sincronização com os parâmetros especificados ou atualizar as propriedades do grupo de sincronização especificado. |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | Retornar o texto Transact-SQL para a ID de consulta selecionada |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obter operações em andamento na criptografia de dados transparente |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recuperar o status e os detalhes do recurso de segurança Transparent Data Encryption para determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar o estado de criptografia de dados transparente |
> | Action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar o Banco de Dados do Datawarehouse do Azure SQL |
> | Action | Microsoft.Sql/servers/databases/usages/read | Obter as informações de uso do Banco de Dados SQL do Azure |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um givendatabase |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converter um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Retornar a lista de registros de exame de avaliação de vulnerabilidade de banco de dados ou obter o registro de exame para a ID de exame especificado. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Executar verificação de banco de dados para avaliação de vulnerabilidade. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recuperar o resultado de exame de avaliação de vulnerabilidade do banco de dados Executar operação |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Action | Microsoft.Sql/servers/databases/write | Criar um banco de dados com os parâmetros especificados ou atualizar as propriedades ou marcas para o banco de dados especificado. |
> | Action | Microsoft.Sql/servers/delete | Excluir um servidor existente. |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Excluir as configurações de recuperação de desastre existentes para um determinado servidor |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Failover de um DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar failover de um DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obter uma coleção de configurações de recuperação de desastre que incluem esse servidor |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar configuração de recuperação de desastre do servidor |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | Retornar a lista de estimativas de pool elástico já criado para o servidor |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | Criar nova estimativa de pool elástico para a lista de bancos de dados fornecida |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | Retornar a lista de consultores disponíveis para o pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Retornar a lista de ações recomendadas do assistente especificado para o pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | Atualizar status de execução automática de um assistente no nível do pool elástico. |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | Obter uma lista de bancos de dados para um pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/delete | Excluir pool elástico existente |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recuperar atividades e detalhes em um pool de banco de dados elástico |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recuperar atividades e detalhes em um banco de dados que faz parte do pool de banco de dados elástico |
> | Action | Microsoft.Sql/servers/elasticPools/failover/action | Failover de pool elástico iniciado pelo cliente. |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | Métricas de retorno para conjuntos de bancos de dados elásticos |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela o pool elástico do Azure SQL pendente de operação assíncrona que ainda não está concluída. |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | Retornar a lista de operações realizadas no pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do recurso |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de bancos de dados elásticos |
> | Action | Microsoft.Sql/servers/elasticPools/read | Recuperar detalhes do pool elástico em um determinado servidor |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de SKUs disponíveis para um pool elástico |
> | Action | Microsoft.Sql/servers/elasticPools/write | Criar um novo ou alterar propriedades do pool elástico existente |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | Retornar uma lista de protetores de criptografia do servidor ou obter as propriedades do protetor de criptografia do servidor especificado. |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | Atualizar as propriedades do Protetor de Criptografia do Servidor especificado. |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de blob de servidor estendida para um determinado servidor |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | Excluir um grupo de failover existente. |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | Executar o failover planejado em um grupo de failover existente. |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executar o failover forçado em um grupo de failover existente. |
> | Action | Microsoft.Sql/servers/failoverGroups/read | Retornar a lista de grupos de failover ou obter as propriedades do grupo de failover especificado. |
> | Action | Microsoft.Sql/servers/failoverGroups/write | Criar um grupo de failover com os parâmetros especificados ou atualizar as propriedades ou marcas para o grupo de failover especificado. |
> | Action | Microsoft.Sql/servers/firewallRules/delete | Excluir uma regra de firewall de servidor existente. |
> | Action | Microsoft.Sql/servers/firewallRules/read | Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificado. |
> | Action | Microsoft.Sql/servers/firewallRules/write | Criar uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades da regra especificada ou substituir todas as regras existentes pelas novas regras de firewall do servidor. |
> | Action | Microsoft.Sql/servers/import/action | Criar um novo banco de dados no servidor e implantar o esquema e os dados de um pacote DacPac |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Exclui o perfil de ponto de extremidade de interface especificado |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Devolve as propriedades para o perfil de ponto de extremidade de interface especificado |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto de extremidade de interface com os parâmetros especificados ou atualiza as propriedades ou marcas para o ponto de extremidade de interface especificado |
> | Action | Microsoft.Sql/servers/jobAgents/delete | Exclui um agente de trabalho de BD SQL do Azure |
> | Action | Microsoft.Sql/servers/jobAgents/read | Obtém um agente de trabalho de BD SQL do Azure |
> | Action | Microsoft.Sql/servers/jobAgents/write | Cria ou atualiza um agente de trabalho de BD SQL do Azure |
> | Action | Microsoft.Sql/servers/keys/delete | Excluir uma chave do servidor existente. |
> | Action | Microsoft.Sql/servers/keys/read | Retornar a lista de chaves do servidor ou obter as propriedades para a chave de servidor especificada. |
> | Action | Microsoft.Sql/servers/keys/write | Criar uma chave com os parâmetros especificados ou atualizar as propriedades ou marcas para a chave do servidor especificada. |
> | Action | Microsoft.Sql/servers/operationResults/read | Obter operações do servidor em andamento |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Action | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | Obter os recursos de link privado para o SQL Server correspondente |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas disponíveis para servidores |
> | Action | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recuperar a métrica de pools de banco de dados elástico recomendados para determinado servidor |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | Recupera a recomendação para pools de banco de dados elástico a fim de reduzir custos ou melhorar o desempenho com base no histórico de utilização de recursos |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | Essa operação é usada para a recuperação de desastres do banco de dados dinâmico a fim de restaurar o banco de dados no último ponto de backup bom conhecido. Ela retorna informações sobre o último backup, mas não\u0027t restaura o banco de dados efetivamente. |
> | Action | Microsoft.Sql/servers/replicationLinks/read | Retorne a lista de links de replicação ou obtém as propriedades para os links de replicação especificado. |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obter uma lista de bancos de dados que foram removidos em determinado servidor ainda na política de retenção. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor configuradas para um determinado servidor |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor para um determinado servidor |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | Recuperar a lista de objetivos de nível de serviço (também conhecido como níveis de desempenho) disponíveis em determinado servidor |
> | Action | Microsoft.Sql/servers/syncAgents/delete | Excluir um agente de sincronização existente. |
> | Action | Microsoft.Sql/servers/syncAgents/generateKey/action | Gera a chave de registro do agente de sincronização |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Retornar a lista de bancos de dados vinculados do agente de sincronização |
> | Action | Microsoft.Sql/servers/syncAgents/read | Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado. |
> | Action | Microsoft.Sql/servers/syncAgents/write | Criar um agente de sincronização com os parâmetros especificados ou atualizar as propriedades do agente de sincronização especificado. |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Action | Microsoft.Sql/servers/usages/read | Retornar cota de DTU de servidor e DTU de consumo atual por todos os bancos de dados no servidor |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | Excluir uma regra de rede virtual existente |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | Criar uma regra da rede virtual com os parâmetros especificados ou atualizar as propriedades ou marcas para a regra da rede virtual especificada. |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade de determinado servidor |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidades em um determinado servidor |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade de determinado servidor |
> | Action | Microsoft.Sql/servers/write | Criar um servidor com os parâmetros especificados ou atualizar as propriedades ou marcas para o servidor especificado. |
> | Action | Microsoft.Sql/unregister/action | Cancelar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | Action | Microsoft.Sql/virtualClusters/delete | Exclui um cluster virtual existente. |
> | Action | Microsoft.Sql/virtualClusters/read | Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado. |
> | Action | Microsoft.Sql/virtualClusters/write | Atualiza marcas de cluster virtual. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Storage/checknameavailability/read | Verifica se esse nome de conta é válido e não está em uso. |
> | Action | Microsoft.Storage/locations/checknameavailability/read | Verifica se esse nome de conta é válido e não está em uso. |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.Storage que uma rede virtual ou subrede está sendo excluída |
> | Action | Microsoft.Storage/locations/usages/read | Retorna o limite e a contagem de uso atual para recursos na assinatura especificada |
> | Action | Microsoft.Storage/operations/read | Sonda o status de uma operação assíncrona. |
> | Action | Microsoft.Storage/register/action | Registra a assinatura para o provedor de recursos de armazenamento e habilita a criação de contas de armazenamento. |
> | Action | Microsoft.Storage/skus/read | Lista as Skus com suporte no Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retorna o resultado da adição de conteúdo do blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Retorna o resultado da exclusão de um instantâneo automático |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Retorna a lista de blobs em uma conta com o filtro de marcas correspondentes |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Retorna o resultado da leitura de marcas de blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Retorna o resultado da gravação de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar a retenção legal do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retorna o resultado da exclusão de um contêiner |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Excluir política de imutabilidade de contêiner de blobs |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Estender a política de imutabilidade do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter a política de imutabilidade do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Inserir a política de imutabilidade do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Retorna o resultado do contêiner de blob de concessão |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna um contêiner |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna a lista de contêineres |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir a retenção legal do contêiner de blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob de patch |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob de colocação |
> | Action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço Blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | Retorna propriedades ou estatísticas do serviço blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | Retorna o resultado da inserção de propriedades do serviço blob |
> | Action | Microsoft.Storage/storageAccounts/delete | Exclui uma conta de armazenamento existente. |
> | Action | Microsoft.Storage/storageAccounts/failover/action | O cliente é capaz de controlar o failover no caso de problemas de disponibilidade |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | Obter as propriedades do serviço arquivo |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | Retorna ao token SAS de Conta para a conta de armazenamento especificado. |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | Retorna o token SAS de serviço para a conta de armazenamento especificada. |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/delete | Excluir as políticas de gerenciamento da conta de armazenamento |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/read | Obter políticas da conta de gerenciamento de armazenamento |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/write | Empregar políticas de gerenciamento de conta de armazenamento |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Excluir proxies de conexão de ponto de extremidade privado |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Colocar proxies de conexão de ponto de extremidade privado |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Excluir conexão de ponto de extremidade particular |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Obter conexão de ponto de extremidade privado |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Colocar conexão de ponto de extremidade particular |
> | Action | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Aprovar conexões de ponto de extremidade privado |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | Obter StorageAccount groupids |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retorna o resultado da exclusão de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retorna o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retorna o resultado da exclusão de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retorna o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retorna uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retorna o resultado da gravação de uma mensagem |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retorna uma fila ou uma lista de filas. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retorna o resultado da gravação de uma fila |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Obter as propriedades do serviço Fila |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Retorna propriedades ou estatísticas do serviço de fila. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | Retorna o resultado da configuração das propriedades do serviço de fila |
> | Action | Microsoft.Storage/storageAccounts/read | Retorna a lista de armazenamento de contas ou obtém as propriedades da conta de armazenamento especificada. |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso para a conta de armazenamento especificada. |
> | Action | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Restaurar intervalos de BLOB para o estado do tempo especificado |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revoga todas as chaves de delegação de usuário para a conta de armazenamento especificada. |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Crie/atualize as configurações de diagnóstico da conta de armazenamento. |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | Obter as propriedades do serviço Tabela |
> | Action | Microsoft.Storage/storageAccounts/write | Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou marcas ou adiciona um domínio personalizado para a conta de armazenamento especificada. |
> | Action | Microsoft.Storage/usages/read | Retorna o limite e a contagem de uso atual para recursos na assinatura especificada |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | microsoft.storagesync/locations/checkNameAvailability/action | Verifica se o nome do serviço de sincronização de armazenamento é válido e não está em uso. |
> | Action | microsoft.storagesync/locations/workflows/operations/read | Obtém o status de uma operação assíncrona |
> | Action | Microsoft. storagesync/operações/leitura | Obtém uma lista das operações com suporte |
> | Action | Microsoft. storagesync/registro/ação | Registra a assinatura para o provedor de sincronização de armazenamento |
> | Action | microsoft.storagesync/storageSyncServices/delete | Excluir os Serviços de Sincronização de Armazenamento |
> | Action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os Serviços de Sincronização de Armazenamento |
> | Action | microsoft.storagesync/storageSyncServices/read | Ler os Serviços de Sincronização de Armazenamento |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | Excluir um servidor registrado |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o Servidor Registrado |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler um servidor registrado |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar um servidor registrado |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Excluir Pontos de Extremidade de Nuvem |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o status de uma operação de backup/restauração assíncrona. |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar essa ação após o backup |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar essa ação após restaurar |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar essa ação antes do backup |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar essa ação antes de restaurar |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler Pontos de Extremidade de Nuvem |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar pulsação |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar Pontos de Extremidade de Nuvem |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | Excluir quaisquer Grupos de Sincronização |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Grupos de Sincronização |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | Lera quaisquer Grupos de Sincronização |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Excluir qualquer ponto de extremidade do servidor |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Pontos de Extremidade do Servidor |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler qualquer ponto de extremidade do servidor |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar essa ação para recuperar arquivos para um servidor |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar qualquer ponto de extremidade de servidor |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar Grupos de Sincronização |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obtém o status de uma operação assíncrona |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obtém o status de uma operação assíncrona |
> | Action | microsoft.storagesync/storageSyncServices/workflows/read | Ler fluxos de trabalho |
> | Action | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar Serviços de Sincronização de Armazenamento |
> | Action | Microsoft. storagesync/cancelar registro/ação | Cancela o registro da assinatura para o provedor de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | Excluir os registros de controle de acesso |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | Listar ou obter os registros de controle de acesso |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar registros de controle de acesso |
> | Action | Microsoft.StorSimple/managers/alerts/read | Listar ou obter os alertas |
> | Action | Microsoft.StorSimple/managers/backups/read | Listar ou obter o conjunto de backup |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | Excluir uma configuração de largura de banda existente (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | Listar as configurações de largura de banda (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | Criar novas configurações de largura de banda ou atualizá-las (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Action | Microsoft.StorSimple/Managers/certificates/write | A operação Atualizar Certificado de Recurso atualiza o certificado de credencial de cofre/recurso. |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | Limpar todos os alertas associados ao Gerenciador de Dispositivos. |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Listar configurações com suporte pelo dispositivo de nuvem |
> | Action | Microsoft.StorSimple/managers/configureDevice/action | Configurar um dispositivo |
> | Action | Microsoft.StorSimple/managers/delete | Excluir os Gerenciadores de Dispositivo |
> | Action | Microsoft.StorSimple/Managers/delete | A operação Excluir Cofre exclui o recurso do Azure do tipo 'cofre' especificado |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | Listar ou obter as configurações de alerta |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as configurações de alerta |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar para sobreposição da chave de criptografia de serviço de dispositivos |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Fazer um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Excluir políticas de backup existentes (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listar as políticas de backup (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Excluir agendas existentes |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Listar as agendas |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Criar ou atualizar agendas |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/write | Criar novas ou atualizar as políticas de Backup (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/backups/delete | Excluir o conjunto de backup |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clonar um compartilhamento ou volume usando um elemento de backup. |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/backups/read | Listar ou obter o conjunto de backup |
> | Action | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaurar todos os volumes de um conjunto de backup. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Excluir os grupos de agendamento de backup |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Listar ou obter os grupos de agendamento de backup |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agendamento de backup |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | Excluir as configurações de Chap |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | Listar ou obter as configurações de Chap |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as configurações de Chap |
> | Action | Microsoft.StorSimple/managers/devices/deactivate/action | Desativar um dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/delete | Excluir os dispositivos |
> | Action | Microsoft.StorSimple/managers/devices/disks/read | Listar ou obter os discos |
> | Action | Microsoft.StorSimple/managers/devices/download/action | Baixar atualizações para um dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/failover/action | Failover do dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/failoverTargets/read | Lista ou obtém os destinos de failover dos dispositivos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Fazer backup de um servidor de arquivos. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/delete | Excluir servidores de arquivos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/read | Listar ou obter os servidores de arquivos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Excluir os compartilhamentos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar ou obter os compartilhamentos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar os compartilhamentos |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de arquivos |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar o estado de energia do controlador dos grupos de componentes de hardware |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de hardware |
> | Action | Microsoft.StorSimple/managers/devices/install/action | Instalar atualizações em um dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala as atualizações nos dispositivos (apenas série 8000). |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Fazer backup de um servidor iSCSI. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Excluir servidores iSCSI |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Excluir os discos |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Listar ou obter os discos |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar servidores iSCSI |
> | Action | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar um trabalho em execução |
> | Action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/jobs/read | Listar ou obter os trabalhos |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Listar os conjuntos de failover para um dispositivo existente (apenas série 8000). |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Listar destinos de failover dos dispositivos (apenas Série 8000). |
> | Action | Microsoft.StorSimple/managers/devices/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirmar uma migração bem-sucedida. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Listar o status de miração de confirmação |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Buscar o status de confirmação da migração. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Buscar o status do trabalho de estimativa de migração. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Buscar o status para a migração. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Listar o status de migração |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar migração usando as configurações de origem |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Iniciar um trabalho para estimar a duração do processo de migração. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | Listar ou obter as configurações de rede |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | Criar novas ou atualizar as configurações de rede |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Listar chave de criptografia pública do Gerenciador de Dispositivos |
> | Action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publica o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para ajudar o Suporte da Microsoft na solução de problemas do dispositivo StorSimple. |
> | Action | Microsoft.StorSimple/managers/devices/read | Listar ou obter os dispositivos |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Verificar se há atualizações em um dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/read | Listar as configurações de segurança |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronizar o certificado de gerenciamento remoto de um dispositivo. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualizar as configurações de segurança. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | Criar ou atualizar as configurações de segurança |
> | Action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Enviar email de alerta de teste para destinatários de email configurados. |
> | Action | Microsoft.StorSimple/managers/devices/shares/read | Listar ou obter os compartilhamentos |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/read | Listar ou obter as configurações de tempo |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | Criar ou atualizar as configurações de tempo |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | Listar ou obter o resumo de atualização |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Excluir contêineres de Volume existentes (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar a métrica |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contêineres de volume (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Excluir um volume existente |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar a métrica |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista as definições de métrica |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista os resultados da operação |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os volumes |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Criar ou atualizar volumes |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/write | Criar novos ou atualizar os contêineres de volume (somente 8000 Series) |
> | Action | Microsoft.StorSimple/managers/devices/volumes/read | Listar os volumes |
> | Action | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | Listar ou obter as configurações de criptografia |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | Exclui as informações estendidas do cofre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | Lista ou obtém as informações estendidas do cofre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações estendidas do cofre |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | A operação Obter Informações Estendidas obtém as Informações Estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Action | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Action | Microsoft.StorSimple/managers/fileservers/read | Listar ou obter os servidores de arquivos |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | Obter a chave de criptografia para o gerenciador de dispositivos. |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Action | Microsoft.StorSimple/managers/jobs/read | Listar ou obter os trabalhos |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | Obter a chave de ativação do Gerenciador de Dispositivo StorSimple. |
> | Action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de criptografia públicas de um Gerenciador de Dispositivo StorSimple. |
> | Action | Microsoft.StorSimple/managers/metrics/read | Listar ou obter a métrica |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | Listar ou obter as definições de métrica |
> | Action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar do Clássico para o Gerenciador de Recursos dos Gerenciadores |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Listar as configurações de origem de migração (somente série 8000) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Criar um novo dispositivo de nuvem. |
> | Action | Microsoft.StorSimple/managers/read | Listar ou obter os Gerenciadores de Dispositivo |
> | Action | Microsoft.StorSimple/Managers/read | A operação Obter Cofre obtém um objeto representando o recurso do Azure de tipo 'cofre' |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenera a chave de ativação para um StorSimple Device Manager existente. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Excluir as credenciais da conta de armazenamento |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | Lista ou obtém as credenciais da conta de armazenamento |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | Excluir os domínios de armazenamento |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Lista ou obtém os resultados da operação |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | Listar ou obter os domínios de armazenamento |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Action | Microsoft.StorSimple/managers/write | Criar ou atualizar os Gerenciadores de Dispositivo |
> | Action | Microsoft.StorSimple/Managers/write | A operação Criar Cofre cria um recurso do Azure do tipo 'cofre' |
> | Action | Microsoft.StorSimple/operations/read | Lista ou obtém as operações |
> | Action | Microsoft.StorSimple/register/action | Registrar o provedor Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.StreamAnalytics/locations/quotas/Read | Ler Cota de assinatura do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/operations/Read | Ler Operações do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/Register/action | Registrar assinatura com provedor de recursos do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | Excluir trabalho de Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Excluir função de trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Ler os resultados da operação para Função de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Ler Função de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recuperar a definição padrão de uma Função de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testar Função de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Gravar Função de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Excluir entrada de trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Ler os resultados da operação para Entrada de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Ler entrada do trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Exemplo de Entrada de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testar Entrada de Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Gravar entrada de trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler Definições de Métrica |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Ler resultados da operação para Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Excluir saída de trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Ler resultados da operação para Saída do Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Ler saída do trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testar Saída do Trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Gravar saída de trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler configuração de diagnóstico. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Gravar configuração de diagnóstico. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para streamingjobs |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obter a métrica disponível para streamingjobs |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Read | Ler trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Interromper trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Excluir transformação do trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Ler transformação do trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Gravar transformação do trabalho do Stream Analytics |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | Gravar trabalho de Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft. Subscription/cancelamento/ação | Cancela a assinatura |
> | Action | Microsoft.Subscription/CreateSubscription/action | Criar uma assinatura do Azure |
> | Action | Microsoft.Subscription/register/action | Registra a Assinatura no provedor de recursos Microsoft.Subscription |
> | Action | Microsoft. Subscription/renomear/ação | Renomeia a assinatura |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | Obter uma definição de assinatura do Azure em um grupo de gerenciamento. |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de assinatura do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | Faz o registro no Provedor de Recursos de Suporte |
> | Action | Microsoft.Support/supportTickets/read | Obtém detalhes do Ticket de Suporte (incluindo status, gravidade, detalhes de contato e comunicações) ou obtém a lista de Tickets de Suporte nas assinaturas. |
> | Action | Microsoft.Support/supportTickets/write | Criar ou atualizar um tíquete de suporte. Você pode criar um tíquete de suporte para problemas técnicos, de cotas, de cobrança ou de gerenciamento de assinaturas. Você pode atualizar severidade, detalhes de contato e comunicações de tíquetes de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Excluir a política de acesso. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Criar uma nova política de acesso para um ambiente ou atualizar uma política de acesso existente. |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | Excluir o ambiente. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Excluir a origem do evento. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem do evento. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/write | Criar uma nova origem do evento para um ambiente ou atualizar uma origem do evento existente. |
> | Action | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Excluir o conjunto de dados de referência. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Criar um novo conjunto de dados de referência para um ambiente ou atualizar um conjunto de dados de referência existente. |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | Obter o status do ambiente, estado das operações associadas, como ingresso. |
> | Action | Microsoft.TimeSeriesInsights/environments/write | Criar um novo ambiente ou atualizar um ambiente existente. |
> | Action | Microsoft.TimeSeriesInsights/register/action | Registrar a assinatura do provedor de recursos do Time Series Insights e habilitar a criação de ambientes do Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | Excluir conta |
> | Action | Microsoft.VisualStudio/Account/Extension/Read | Conta de Leitura/Extensão |
> | Action | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Action | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Action | Microsoft.VisualStudio/Account/Read | Lê a Conta |
> | Action | Microsoft.VisualStudio/Account/Write | Define a Conta |
> | Action | Microsoft.VisualStudio/Extension/Delete | Exclui a Extensão |
> | Action | Microsoft.VisualStudio/Extension/Read | Lê a Extensão |
> | Action | Microsoft.VisualStudio/Extension/Write | Define a Extensão |
> | Action | Microsoft.VisualStudio/Project/Delete | Excluir Projeto |
> | Action | Microsoft.VisualStudio/Project/Read | Lê o Projeto |
> | Action | Microsoft.VisualStudio/Project/Write | Define o Projeto |
> | Action | Microsoft.VisualStudio/Register/Action | Registrar a assinatura do Azure com o provedor de Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | Obter Apiacls de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Excluir Apiacls de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obter Apiacls de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualizar Apiacls de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obter Connectionacls de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirmar código de consentimento de conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Excluir Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualizar Connectionacls de Conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | Excluir Conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obter links de consentimento para conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Listar chaves de conexão de conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Listar Segredos de conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | Obter conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | Atualizar conexões de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | Excluir APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Excluir definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obter definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Atualizar definições localizadas de APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/read | Obter APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/apis/write | Atualizar APIs de Contas de Gerenciamento de API. |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | Obter Connectionacls de Contas de Gerenciamento de API. |
> | Action | microsoft.web/availablestacks/read | Obter pilhas disponíveis. |
> | Action | Microsoft.Web/certificates/Delete | Excluir um certificado existente. |
> | Action | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Action | Microsoft.Web/certificates/Write | Adicionar um novo certificado ou atualizar um existente. |
> | Action | microsoft.web/checknameavailability/read | Verificar se o nome do recurso está disponível. |
> | Action | microsoft.web/classicmobileservices/read | Obter Serviços Móveis clássicos. |
> | Action | Microsoft.Web/connectionGateways/Delete | Excluir um Gateway de Conexão. |
> | Action | Microsoft.Web/connectionGateways/Join/Action | Adicionar um Gateway de Conexão. |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | Listar status de um gateway de conexão. |
> | Action | Microsoft.Web/connectionGateways/Move/Action | Mover um gateway de conexão. |
> | Action | Microsoft.Web/connectionGateways/Read | Obter a lista de gateways de conexão. |
> | Action | Microsoft.Web/connectionGateways/Write | Criar ou atualizar um Gateway de Conexão. |
> | Action | microsoft.web/connections/confirmconsentcode/action | Confirmar o código de autorização de conexões. |
> | Action | Microsoft.Web/connections/Delete | Excluir uma conexão. |
> | Action | Microsoft.Web/connections/Join/Action | Ingressar em uma conexão. |
> | Action | microsoft.web/connections/listconsentlinks/action | Listar os links de autorização para conexões. |
> | Action | Microsoft.Web/connections/Move/Action | Mover uma conexão. |
> | Action | Microsoft.Web/connections/Read | Obter a lista de conexões. |
> | Action | Microsoft.Web/connections/Write | Criar ou atualizar uma conexão. |
> | Action | Microsoft.Web/customApis/Delete | Excluir uma API personalizada. |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrair a definição de API de uma WSDL. |
> | Action | Microsoft.Web/customApis/Join/Action | Adicionar uma API personalizada. |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | Listar interfaces WSDL para uma API personalizada. |
> | Action | Microsoft.Web/customApis/Move/Action | Mover uma API personalizada. |
> | Action | Microsoft.Web/customApis/Read | Obter a lista de API personalizada. |
> | Action | Microsoft.Web/customApis/Write | Criar ou atualizar uma API personalizada. |
> | Action | Microsoft.Web/deletedSites/Read | Obtém as propriedades de um aplicativo Web excluído |
> | Action | microsoft.web/deploymentlocations/read | Obter locais de implantação. |
> | Action | Microsoft.Web/geoRegions/Read | Obter a lista de regiões geográficas. |
> | Action | microsoft.web/hostingenvironments/capacities/read | Obter as capacidades dos ambientes de hospedagem. |
> | Action | Microsoft.Web/hostingEnvironments/Delete | Excluir ambiente do Serviço de Aplicativo |
> | Action | microsoft.web/hostingenvironments/detectors/read | Obtém detectores de ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/diagnostics/read | Obter diagnóstico dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de entrada. |
> | Action | Microsoft.Web/hostingEnvironments/Join/Action | Une um Ambiente do Serviço de Aplicativo |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métrica de ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métrica de pools multifunção dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter a métrica de pools multifunção em Ambientes de Hospedagem. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de pools multifunção dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter usos dos pools multifunção dos ambientes de hospedagem. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Action | microsoft.web/hostingenvironments/operations/read | Obter operações dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de saída. |
> | Action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Action | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um Ambiente do Serviço de Aplicativo |
> | Action | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicializar todas as máquinas em um Ambiente do Serviço de Aplicativo |
> | Action | microsoft.web/hostingenvironments/resume/action | Retomar ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | Obter Planos do Serviço de Aplicativo dos Ambientes de Hospedagem. |
> | Action | microsoft.web/hostingenvironments/sites/read | Obter aplicativos Web dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/suspend/action | Suspender ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/usages/read | Obter usos dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter definições de métrica de pools de trabalho dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter a métrica de pools de trabalho de Ambientes de Hospedagem. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | Obter SKUs de pools de trabalho dos ambientes de hospedagem. |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | Obter uso dos pools de trabalho dos ambientes de hospedagem. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Action | Microsoft.Web/hostingEnvironments/Write | Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Action | microsoft.web/ishostingenvironmentnameavailable/read | Obter confirmação se o nome do ambiente de hospedagem está disponível. |
> | Action | microsoft.web/ishostnameavailable/read | Verificar se o nome do host está disponível. |
> | Action | microsoft.web/isusernameavailable/read | Verificar se o nome de usuário está disponível. |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes dos sites atribuídos ao nome de host. |
> | Action | microsoft.web/locations/apioperations/read | Obter operações API dos locais. |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | Obter as instalações de gateway de conexão locais. |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Notificação de exclusão de Vnet ou sub-rede para Locais. |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrair a definição de API da WSDL para locais. |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | Listar interfaces WSDL para locais. |
> | Action | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerenciadas dos locais. |
> | Action | Microsoft.Web/locations/managedapis/Join/Action | Adicionar uma API gerenciada. |
> | Action | microsoft.web/locations/managedapis/read | Obter as APIs gerenciadas dos locais. |
> | Action | microsoft.web/operations/read | Obter Operações. |
> | Action | microsoft.web/publishingusers/read | Obter usuários de publicação. |
> | Action | microsoft.web/publishingusers/write | Atualizar usuários de publicação. |
> | Action | Microsoft.Web/recommendations/Read | Obter a lista de recomendações para assinaturas. |
> | Action | microsoft.web/register/action | Registrar o provedor de recursos Microsoft.Web para a assinatura. |
> | Action | microsoft.web/resourcehealthmetadata/read | Obter metadados do Resource Health. |
> | Action | microsoft.web/serverfarms/capabilities/read | Obter recursos do Planos do Serviço de Aplicativo. |
> | Action | Microsoft.Web/serverfarms/Delete | Excluir um Plano do Serviço de Aplicativo existente |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | Excluir configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualizar configurações de aplicativos próprios dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter aplicativos Web das retransmissões dos namespaces da conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obter limites do plano de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | Obter transmissões de conexão híbrida dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | Obter definições de métrica dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/metrics/read | Obter métrica dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/operationresults/read | Obter resultados de operação dos Planos do Serviço de Aplicativo. |
> | Action | Microsoft.Web/serverfarms/Read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | Reiniciar todos os aplicativos Web em um Plano do Serviço de Aplicativo |
> | Action | microsoft.web/serverfarms/sites/read | Obter aplicativos Web dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/skus/read | Obter as SKUs dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/usages/read | Obter usos dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | Obter as conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Excluir rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter a rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualizar rotas de conexões de rede virtual dos Planos do Serviço de Aplicativo. |
> | Action | microsoft.web/serverfarms/workers/reboot/action | Reinicializar trabalhos dos Planos do Serviço de Aplicativo. |
> | Action | Microsoft.Web/serverfarms/Write | Criar um novo Plano do Serviço de Aplicativo ou atualizar um plano existente |
> | Action | microsoft.web/sites/analyzecustomhostname/read | Analisar nome de host personalizado. |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao aplicativo Web atual |
> | Action | Microsoft.Web/sites/backup/Action | Criar um novo backup do aplicativo Web |
> | Action | microsoft.web/sites/backup/read | Obter o backup de aplicativos Web. |
> | Action | microsoft.web/sites/backup/write | Atualizar o backup de aplicativos Web. |
> | Action | microsoft.web/sites/backups/action | Descobre um backup existente de aplicativo que pode ser restaurado de um blob no Armazenamento do Azure. |
> | Action | microsoft.web/sites/backups/delete | Excluir backups de aplicativos Web. |
> | Action | microsoft.web/sites/backups/list/action | Listar backups de aplicativos de Web. |
> | Action | Microsoft.Web/sites/backups/Read | Obter as propriedades do backup de um aplicativo Web |
> | Action | microsoft.web/sites/backups/restore/action | Restaurar backups de aplicativos Web. |
> | Action | microsoft.web/sites/backups/write | Atualiza os backup de aplicativos Web. |
> | Action | microsoft.web/sites/config/delete | Excluir configuração de aplicativos Web. |
> | Action | Microsoft.Web/sites/config/list/Action | Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Action | Microsoft.Web/sites/config/Read | Obter definições de configuração do aplicativo Web |
> | Action | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicativos Web. |
> | Action | Microsoft.Web/sites/config/Write | Atualizar definições de configuração do aplicativo Web |
> | Action | microsoft.web/sites/containerlogs/action | Obtém Logs de contêiner compactados para o aplicativo Web. |
> | Action | microsoft.web/sites/containerlogs/download/action | Baixa os Logs do Contêiner dos Aplicativos Web. |
> | Action | microsoft.web/sites/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de aplicativos Web. |
> | Action | microsoft.web/sites/continuouswebjobs/read | Obter trabalhos de Web contínuos de aplicativos Web. |
> | Action | microsoft.web/sites/continuouswebjobs/start/action | Iniciar trabalhos de Web contínuos de aplicativos Web. |
> | Action | microsoft.web/sites/continuouswebjobs/stop/action | Interromper trabalhos de Web contínuos de aplicativos Web. |
> | Action | Microsoft.Web/sites/Delete | Excluir um aplicativo Web existente |
> | Action | microsoft.web/sites/deployments/delete | Excluir as implantações de aplicativos Web. |
> | Action | microsoft.web/sites/deployments/log/read | Obter o log de implantações de aplicativos Web. |
> | Action | microsoft.web/sites/deployments/read | Obter as implantações de aplicativos Web. |
> | Action | microsoft.web/sites/deployments/write | Atualizar as implantações de aplicativos Web. |
> | Action | microsoft.web/sites/detectors/read | Obtém os detectores de aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/analyses/execute/Action | Executar Análise de Diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/analyses/read | Obter Análise de Diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | Obter Diagnóstico de Aplicativos Web para aplicativo ASP.NET Core. |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | Obter Diagnóstico de Aplicativos Web para Autoheal. |
> | Action | microsoft.web/sites/diagnostics/deployment/read | Obter a implantação de Diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/deployments/read | Obter implantações de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/detectors/execute/Action | Executar o Detector de Diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/detectors/read | Obter o Detector de Diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter solicitações de falha de diagnóstico de Aplicativos Web por URI. |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | Obter a análise FREB do diagnóstico de aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/runtimeavailability/read | Obter a disponibilidade de tempo de execução do diagnóstico de aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | Obter integridade do serviço de diagnóstico de aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obter análise de CPU do site de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas do Site de Diagnósticos de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/sitelatency/read | Obter a latência do Site de Diagnósticos de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnósticos de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter reinicialização do site de diagnóstico de Aplicativos Web iniciado pelo usuário. |
> | Action | microsoft.web/sites/diagnostics/siteswap/read | Obter troca de site de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de Aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/workeravailability/read | Obter Workeravailability de diagnóstico de aplicativos Web. |
> | Action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obter a reciclagem do processo de trabalho do diagnóstico de aplicativos Web. |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de aplicativos Web. |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | Atualizar de identificadores de propriedade de domínio de aplicativos Web. |
> | Action | microsoft.web/sites/extensions/delete | Excluir extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/extensions/read | Obter extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/extensions/write | Atualizar extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/functions/action | Aplicativos Web do Functions. |
> | Action | microsoft.web/sites/functions/delete | Excluir funções de aplicativos Web. |
> | Action | microsoft.web/sites/functions/listsecrets/action | Listar segredos de funções de aplicativos Web. |
> | Action | microsoft.web/sites/functions/masterkey/read | Obter a chave mestra de funções de Aplicativos Web. |
> | Action | microsoft.web/sites/functions/read | Obter funções de aplicativos Web. |
> | Action | microsoft.web/sites/functions/token/read | Obter token de funções de Aplicativos Web. |
> | Action | microsoft.web/sites/functions/write | Atualizar funções de aplicativos Web. |
> | Action | microsoft.web/sites/hostnamebindings/delete | Excluir associações de nome de host de aplicativos Web. |
> | Action | microsoft.web/sites/hostnamebindings/read | Obter associações de nome de host de aplicativos Web. |
> | Action | microsoft.web/sites/hostnamebindings/write | Atualizar associações de nome de host de aplicativos Web. |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | Obtém as teclas de função do host de tempo de execução dos Aplicativos Web. |
> | Action | Microsoft.Web/sites/hostruntime/host/_master/read | Obter chave mestra do Aplicativo de funções para operações de administrador |
> | Action | Microsoft.Web/sites/hostruntime/host/action | Executar a ação de tempo de execução do Aplicativo de funções como gatilhos de sincronização, adicionar funções, invocar funções, excluir funções, etc. |
> | Action | microsoft.web/sites/hostruntime/host/read | Obtém o host do host de tempo de execução dos Aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnection/delete | Excluir a conexão híbrida de aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnection/read | Obter a conexão híbrida de aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnection/write | Atualizar a conexão híbrida de aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Listar chaves de retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de Aplicativos Web. |
> | Action | microsoft.web/sites/hybridconnectionrelays/read | Obter transmissões de conexão híbrida de aplicativos Web. |
> | Action | microsoft.web/sites/instances/deployments/delete | Excluir implantações de instâncias de Aplicativos Web. |
> | Action | microsoft.web/sites/instances/deployments/read | Obter as implantações de instâncias de aplicativos Web. |
> | Action | microsoft.web/sites/instances/extensions/log/read | Obter log de extensões de instâncias de Aplicativos Web. |
> | Action | microsoft.web/sites/instances/extensions/processes/read | Obtém os Processos de Extensões de Instâncias dos Aplicativos Web. |
> | Action | microsoft.web/sites/instances/extensions/read | Obter extensões de instâncias de Aplicativos Web. |
> | Action | microsoft.web/sites/instances/processes/delete | Excluir processos de instâncias de aplicativos Web. |
> | Action | microsoft.web/sites/instances/processes/modules/read | Obtém os Módulos de Processos de Instâncias dos Aplicativos Web. |
> | Action | microsoft.web/sites/instances/processes/read | Obter os processos de instâncias de aplicativos Web. |
> | Action | microsoft.web/sites/instances/processes/threads/read | Obter threads de processos de instâncias de aplicativos Web. |
> | Action | microsoft.web/sites/instances/read | Obter instâncias de aplicativos Web. |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Listar Aplicativos Web de status do gatilho da função de sincronização. |
> | Action | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicativos Web. |
> | Action | microsoft.web/sites/metrics/read | Métrica de aplicativos Web. |
> | Action | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de Aplicativos Web. |
> | Action | microsoft.web/sites/migratemysql/action | Migrar Aplicativos Web do MySql. |
> | Action | microsoft.web/sites/migratemysql/read | Obter migração do MySql de Aplicativos Web. |
> | Action | microsoft.web/sites/networktrace/action | Aplicativos Web de rastreamento de rede. |
> | Action | microsoft.web/sites/networktraces/operationresults/read | Obter resultados da operação de rastreamento de rede de aplicativos Web. |
> | Action | microsoft.web/sites/newpassword/action | Aplicativos Web Newpassword. |
> | Action | microsoft.web/sites/operationresults/read | Obter resultados de operação de aplicativos Web. |
> | Action | microsoft.web/sites/operations/read | Obter operações de Aplicativos Web. |
> | Action | microsoft.web/sites/perfcounters/read | Obter contadores de desempenho de aplicativos Web. |
> | Action | microsoft.web/sites/premieraddons/delete | Excluir complementos Premier de aplicativos Web. |
> | Action | microsoft.web/sites/premieraddons/read | Obter complementos Premier de aplicativos Web. |
> | Action | microsoft.web/sites/premieraddons/write | Atualizar complementos Premier de aplicativos Web. |
> | Action | microsoft.web/sites/privateaccess/read | Obter dados sobre a habilitação de acesso a sites privados e Redes Virtuais autorizadas que podem acessar o site. |
> | Action | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Action | microsoft.web/sites/processes/modules/read | Obter módulos de processos de aplicativos Web. |
> | Action | microsoft.web/sites/processes/read | Obter processos de Aplicativos Web. |
> | Action | microsoft.web/sites/processes/threads/read | Obter threads de processos de aplicativos Web. |
> | Action | microsoft.web/sites/publiccertificates/delete | Excluir certificados públicos de Aplicativos Web. |
> | Action | microsoft.web/sites/publiccertificates/read | Obter certificados públicos de Aplicativos Web. |
> | Action | microsoft.web/sites/publiccertificates/write | Atualizar certificados públicos de Aplicativos Web. |
> | Action | Microsoft.Web/sites/publish/Action | Publicar um aplicativo Web |
> | Action | Microsoft.Web/sites/publishxml/Action | Obter XML do perfil de publicação para um aplicativo Web |
> | Action | microsoft.web/sites/publishxml/read | Obter XML de publicação de aplicativos Web. |
> | Action | Microsoft.Web/sites/Read | Obter as propriedades de um aplicativo Web |
> | Action | microsoft.web/sites/recommendationhistory/read | Obter o histórico de recomendação de aplicativos Web. |
> | Action | microsoft.web/sites/recommendations/disable/action | Desabilitar as recomendações de aplicativos Web. |
> | Action | Microsoft.Web/sites/recommendations/Read | Obter a lista de recomendações para o aplicativo Web. |
> | Action | microsoft.web/sites/recover/action | Recuperar Aplicativos Web. |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Redefinir a configuração de aplicativo Web |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Action | Microsoft.Web/sites/restart/Action | Reiniciar um aplicativo Web |
> | Action | microsoft.web/sites/restore/read | Obter a restauração de aplicativos Web. |
> | Action | microsoft.web/sites/restore/write | Restaurar Aplicativos Web. |
> | Action | microsoft.web/sites/restorefrombackupblob/action | Restaurar o aplicativo Web a partir do blob de backup. |
> | Action | Microsoft. Web/sites/restorefromdeletedapp/Action | Restaura os aplicativos Web do aplicativo excluído. |
> | Action | microsoft.web/sites/restoresnapshot/action | Restaura os Instantâneos de Aplicativos Web. |
> | Action | microsoft.web/sites/siteextensions/delete | Excluir extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/siteextensions/read | Obter extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/siteextensions/write | Atualizar extensões de site de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | Obter slots de aplicativos Web Analisar nome de host personalizado. |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplicar a configuração de slot de aplicativo Web do slot de destino ao slot atual. |
> | Action | Microsoft.Web/sites/slots/backup/Action | Criar o novo backup do slot do aplicativo Web. |
> | Action | microsoft.web/sites/slots/backup/read | Obter backup de Slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/backup/write | Atualizar o backup de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/backups/action | Descobrir backups de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/backups/delete | Excluir backups de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/backups/list/action | Listar backups de slots de aplicativos de Web. |
> | Action | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades do backup dos slots de um aplicativo Web |
> | Action | microsoft.web/sites/slots/backups/restore/action | Restaurar backups de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/config/delete | Excluir configuração de slots de aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/config/list/Action | Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Action | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração do slot do aplicativo Web |
> | Action | Microsoft.Web/sites/slots/config/Write | Atualizar definições de configuração do slot do aplicativo Web |
> | Action | microsoft.web/sites/slots/containerlogs/action | Obter logs de contêiner compactados para slot do aplicativo Web. |
> | Action | microsoft.web/sites/slots/containerlogs/download/action | Baixa os Logs do Contêiner de Slots dos Aplicativos Web. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Excluir trabalhos da Web contínuos de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | Obter trabalhos da Web contínuos de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/start/action | Iniciar trabalhos da Web contínuos de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/stop/action | Interromper trabalhos da Web contínuos de slots de aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/Delete | Excluir um slot de aplicativo Web existente |
> | Action | microsoft.web/sites/slots/deployments/delete | Excluir as implantações de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/deployments/log/read | Obter o log de implantações dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/deployments/read | Obter as implantações dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/deployments/write | Atualizar as implantações de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/detectors/read | Obter detectores de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Executar análise de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/read | Obter análise de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obter o diagnóstico de slots de Aplicativos Web para o aplicativo ASP.NET Core. |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | Obter o diagnóstico de slots de Aplicativos Web para Autoheal. |
> | Action | microsoft.web/sites/slots/diagnostics/deployment/read | Obter implantação de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implantações de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Executar o detector de diagnóstico de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/read | Obter detector de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análise FREB de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/read | Obter diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obter disponibilidade de tempo de execução de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter Integridade do Serviço do Azure do diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obter análise de CPU de site de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas nos sites de diagnóstico dos slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obter latência de sites de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter atualização de configuração de reinicialização do site de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obter diagnóstico de slots de Aplicativos Web iniciado pelo usuário. |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | Obter troca de sites de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obter disponibilidade do trabalho de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obter reciclagem de processo de trabalho de diagnóstico de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/functions/listsecrets/action | Lista as Funções de Slots de Segredos dos Aplicativos Web. |
> | Action | microsoft.web/sites/slots/functions/read | Obter funções de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | Excluir vínculos de nome de host de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | Obter vínculos de nome de host de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hostnamebindings/write | Atualizar vínculos de nome de host de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | Excluir a conexão híbrida de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnection/read | Obter a conexão híbrida de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnection/write | Atualizar a conexão híbrida de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Obter as implantações de instâncias dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Excluir processos de instâncias de slots de plicativos Web. |
> | Action | microsoft.web/sites/slots/instances/processes/read | Obter os processos de instâncias dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/instances/read | Obter instâncias dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métrica de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/metrics/read | Obter métrica dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/migratemysql/read | Obter migração do MySql de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/networktrace/action | Slots de Aplicativos Web de rastreamento de rede. |
> | Action | microsoft.web/sites/slots/networktraces/operationresults/read | Obter resultados de operação de rastreamento de rede de Slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/newpassword/action | Slots de aplicativos Web Newpassword. |
> | Action | microsoft.web/sites/slots/operationresults/read | Obter resultados de operação de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/operations/read | Obter operações de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/perfcounters/read | Obter contadores de desempenho de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/phplogging/read | Obter Phplogging dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Excluir complementos de Premier de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/premieraddons/read | Obter complementos Premier de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/premieraddons/write | Atualizar complementos Premier de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/processes/read | Obtém os Processos de Slots dos Aplicativos Web. |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | Excluir certificados públicos de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de slots de Aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/publish/Action | Publicar um slot do aplicativo Web |
> | Action | Microsoft.Web/sites/slots/publishxml/Action | Obter XML do perfil de publicação para um slot de aplicativo Web |
> | Action | Microsoft.Web/sites/slots/Read | Obter as propriedades de um slot de implantação de aplicativo Web |
> | Action | microsoft.web/sites/slots/recover/action | Recupera Slots de Aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Redefinir a configuração de slot do aplicativo Web |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter metadados do Resource Health de Aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/restart/Action | Reiniciar um slot de aplicativo Web |
> | Action | microsoft.web/sites/slots/restore/read | Obter a restauração dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/restore/write | Restaurar slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | Restaurar o slot de aplicativos Web a partir do blob de backup. |
> | Action | Microsoft. Web/sites/Slots/restorefromdeletedapp/Action | Restaura os slots de aplicativo Web do aplicativo excluído. |
> | Action | microsoft.web/sites/slots/restoresnapshot/action | Restaura os Instantâneos de Slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Excluir extensões de site de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/siteextensions/read | Obter extensões de site de slots de Aplicativos Web. |
> | Action | microsoft.web/sites/slots/siteextensions/write | Atualizar extensões de site de slots de Aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Action | Microsoft.Web/sites/slots/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Action | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de slots de Aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do slot do aplicativo Web |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controle de origem do slot do aplicativo Web |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do slot do aplicativo Web |
> | Action | Microsoft.Web/sites/slots/start/Action | Iniciar um slot de aplicativo Web |
> | Action | Microsoft.Web/sites/slots/stop/Action | Interromper um slot de aplicativo Web |
> | Action | microsoft.web/sites/slots/sync/action | Sincronizar slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | Excluir trabalhos da Web disparados de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | Obter trabalhos da Web disparados de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | Executar trabalhos da Web dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/usages/read | Obter usos dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Excluir conexões de rede virtual de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Obter conexões de rede virtual dos slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualizar conexões de rede virtual de slots de aplicativos Web. |
> | Action | microsoft.web/sites/slots/webjobs/read | Obter trabalhos da Web dos slots de aplicativos Web. |
> | Action | Microsoft.Web/sites/slots/Write | Criar um novo slot do aplicativo Web ou atualizar um existente |
> | Action | Microsoft.Web/sites/slotsdiffs/Action | Obter as diferenças de configuração entre aplicativo web e slots |
> | Action | Microsoft.Web/sites/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Action | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicativos Web. |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | Excluir definições de configuração de controle de origem do aplicativo Web |
> | Action | Microsoft.Web/sites/sourcecontrols/Read | Obter configurações de controle de origem do aplicativo Web |
> | Action | Microsoft.Web/sites/sourcecontrols/Write | Atualizar definições de configuração de controle de origem do aplicativo Web |
> | Action | Microsoft.Web/sites/start/Action | Iniciar um aplicativo Web |
> | Action | Microsoft.Web/sites/stop/Action | Interromper um aplicativo Web |
> | Action | microsoft.web/sites/sync/action | Sincronizar pplicativos Web. |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | Gatilhos de função de sincronização para Aplicativos Web. |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | Excluir trabalhos da Web disparados para aplicativos Web. |
> | Action | microsoft.web/sites/triggeredwebjobs/history/read | Obter o histórico de WebJobs disparados de Aplicativos Web. |
> | Action | microsoft.web/sites/triggeredwebjobs/read | Obter trabalhos da Web disparados de aplicativos Web. |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | Executar trabalhos da Web disparados de aplicativos Web. |
> | Action | microsoft.web/sites/usages/read | Obter usos de aplicativos Web. |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Excluir as conexões de rede virtual de aplicativos Web. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter gateways de conexões de rede virtual de aplicativos Web. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de aplicativos Web. |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Obter as conexões de rede virtual de aplicativos Web. |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Atualizar as conexões de rede virtual de aplicativos Web. |
> | Action | microsoft.web/sites/webjobs/read | Obter trabalhos da Web de aplicativos Web. |
> | Action | Microsoft.Web/sites/Write | Criar um novo aplicativo Web ou atualizar um existente |
> | Action | microsoft.web/skus/read | Obter SKUs. |
> | Action | microsoft.web/sourcecontrols/read | Obter os controles de origem. |
> | Action | microsoft.web/sourcecontrols/write | Atualizar controles de origem. |
> | Action | microsoft.web/unregister/action | Cancelar o registro do provedor de recursos Microsoft.Web para a assinatura. |
> | Action | microsoft.web/validate/action | Validar. |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | Verificar VNET no ambiente de hospedagem. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Ação | Operação | Descrição |
> | --- | --- | --- |
> | Action | Microsoft.WorkloadMonitor/components/read | Obter componentes para o recurso |
> | Action | Microsoft.WorkloadMonitor/componentsSummary/read | Obter resumo dos componentes |
> | Action | Microsoft.WorkloadMonitor/monitorInstances/read | Obter instâncias de monitores para o recurso |
> | Action | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obter resumo das instâncias do monitor |
> | Action | Microsoft.WorkloadMonitor/monitors/read | Obter monitores para o recurso |
> | Action | Microsoft.WorkloadMonitor/monitors/write | Configurar monitor para o recurso |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/read | Obter as configurações de notificação para o recurso |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/write | Configurar configurações de notificação para o recurso |
> | Action | Microsoft.WorkloadMonitor/operations/read | Obter as operações com suporte |

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Funções internas para recursos do Azure](built-in-roles.md)
