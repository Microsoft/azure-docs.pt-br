---
title: Detalhes de Conformidade Regulatória do CIS Microsoft Azure Foundations Benchmark 1.1.0
description: Detalhes da iniciativa interna de Conformidade Regulatória do CIS Microsoft Azure Foundations Benchmark 1.1.0. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 47e59a3ba1a31fd05ab9d1d87301b41c30c8bdfd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497737"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-110-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa interna de Conformidade Regulatória do CIS Microsoft Azure Foundations Benchmark 1.1.0

O artigo a seguir fornece detalhes sobre como a definição da iniciativa interna de Conformidade Regulatória do Azure Policy é mapeada para **domínios de conformidade** e **controles** no CIS Microsoft Azure Foundations Benchmark 1.1.0.
Para saber mais sobre esse padrão de conformidade, confira [CIS Microsoft Azure Foundations Benchmark 1.1.0](https://www.cisecurity.org/benchmark/azure/). Para entender a _Propriedade_, confira [Definição de política do Azure Policy](../concepts/definition-structure.md#type) e [Responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).

Os mapeamentos a seguir referem-se aos controles do **CIS Microsoft Azure Foundations Benchmark 1.1.0**. Use a navegação no lado direito para ir diretamente para um **domínio de conformidade** específico. Muitos dos controles são implementados com uma definição de iniciativa do [Azure Policy](../overview.md). Para examinar a definição da iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**.
Em seguida, encontre e selecione a definição da iniciativa interna de Conformidade Regulatória do **CIS Microsoft Azure Foundations Benchmark v1.1.0**.

Essa iniciativa interna é implantada como parte da [Amostra de blueprint do CIS Microsoft Azure Foundations Benchmark 1.1.0](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../overview.md).
> Essas políticas podem ajudar você a [avaliar a conformidade](../how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias definições de política e não garante que você esteja em conformidade total com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre os domínios de conformidade, os controles e as definições do Azure Policy para este padrão de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json).

## <a name="identity-and-access-management"></a>Gerenciamento de Identidades e Acesso

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Garantir que a autenticação multifator esteja habilitada para todos os usuários privilegiados

**ID**: CIS Azure 1.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Garantir que a autenticação multifator esteja habilitada para todos os usuários não privilegiados

**ID**: CIS Azure 1.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Garantir que não haja usuários convidados

**ID**: CIS Azure 1.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas externas com permissões de proprietário devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Contas externas com permissões de proprietário devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[As contas externas com permissões de leitura devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Contas externas com privilégios de leitura devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[As contas externas com permissões de gravação devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Contas externas com privilégios de gravação devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Garantir que nenhuma função personalizada de proprietário de assinatura seja criada

**ID**: CIS Azure 1.23 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Essa política garante que não exista nenhuma função personalizada de proprietário de assinatura. |Audit, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Central de Segurança

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Verificar se o tipo de preço Standard foi selecionado

**ID**: CIS Azure 2.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Defender para o Serviço de Aplicativo deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |O Azure Defender para o Serviço de Aplicativo utiliza a escala da nuvem e a visibilidade que o Azure tem como um provedor de nuvem para monitorar ataques comuns aos aplicativos Web. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[O Azure Defender para servidores do Banco de Dados SQL do Azure deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |O Azure Defender para SQL oferece funcionalidade para expor e reduzir possíveis vulnerabilidades de banco de dados, detectar atividades anômalas que podem indicar ameaças aos Bancos de Dados SQL e descobrir e classificar dados confidenciais. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[O Azure Defender para registros de contêiner deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |O Azure Defender para registros de contêiner fornece verificação de vulnerabilidade de qualquer imagem extraída nos últimos 30 dias, enviada por push ao registro ou importada e expõe as descobertas detalhadas por imagem. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[O Azure Defender para Key Vault deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |O Azure Defender para Key Vault oferece uma camada adicional de proteção e inteligência de segurança ao detectar tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Key Vault. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[O Azure Defender para Kubernetes deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |O Azure Defender para Kubernetes fornece proteção contra ameaças em tempo real para ambientes em contêineres e gera alertas sobre atividades suspeitas. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[O Azure Defender para servidores deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |O Azure Defender para servidores fornece proteção contra ameaças em tempo real para cargas de trabalho do servidor e gera recomendações de proteção, bem como alertas sobre atividades suspeitas. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[O Azure Defender para servidores SQL em computadores deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |O Azure Defender para SQL oferece funcionalidade para expor e reduzir possíveis vulnerabilidades de banco de dados, detectar atividades anômalas que podem indicar ameaças aos Bancos de Dados SQL e descobrir e classificar dados confidenciais. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[O Azure Defender para Armazenamento deve estar habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |O Azure Defender para Armazenamento detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. |AuditIfNotExists, desabilitado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Verificar se 'Provisionamento automático do agente de monitoramento' está definido como 'Ativo'

**ID**: CIS Azure 2.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O provisionamento automático do agente do Log Analytics deve ser habilitado na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorar as vulnerabilidades e ameaças à segurança, a Central de Segurança do Azure coleta dados de suas máquinas virtuais do Azure. Os dados são coletados pelo agente do Log Analytics, anteriormente conhecido como MMA (Microsoft Monitoring Agent), que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace do Log Analytics para análise. É recomendável habilitar o provisionamento automático para implantar automaticamente o agente em todas as VMs do Azure com suporte e nas VMs que forem criadas. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar Atualizações do Sistema" não esteja "Desabilitada"

**ID**: CIS Azure 2.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As atualizações do sistema devem ser instaladas em suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar Vulnerabilidades do SO" não esteja "Desabilitada"

**ID**: CIS Azure 2.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar a Proteção do Ponto de Extremidade" não esteja "Desabilitada"

**ID**: CIS Azure 2.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorar o Endpoint Protection ausente na Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar Criptografia de Disco" não esteja "Desabilitada"

**ID**: CIS Azure 2.6 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A criptografia de disco deve ser aplicada em Máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem uma criptografia de disco habilitada serão monitoradas pela Central de Segurança do Azure como recomendações. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Garantir que a configuração de política padrão do ASC "Monitorar Grupos de Segurança de Rede" não esteja "Desabilitada"

**ID**: CIS Azure 2.7 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Garantir que a configuração de política padrão do ASC "Habilitar Monitoramento de NGFW (Firewall de Próxima Geração)" não esteja "Desabilitada"

**ID**: CIS Azure 2.9 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar Avaliação de Vulnerabilidade" não esteja "Desabilitada"

**ID**: CIS Azure 2.10 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita as máquinas virtuais para detectar se elas estão executando uma solução de avaliação de vulnerabilidade compatível. Um componente principal de cada programa de segurança e risco cibernético é a identificação e a análise das vulnerabilidades. O tipo de preço padrão da Central de Segurança do Azure inclui a verificação de vulnerabilidade para as máquinas virtuais sem custo adicional. Além disso, a Central de Segurança pode implantar essa ferramenta automaticamente para você. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar o Acesso à Rede JIT" não esteja "Desabilitada"

**ID**: CIS Azure 2.12 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar a Inclusão de Aplicativos Adaptáveis à Lista de Permissões" não esteja "Desabilitada"

**ID**: CIS Azure 2.13 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar a Auditoria do SQL" não esteja "Desabilitada"

**ID**: CIS Azure 2.14 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Garantir que a configuração padrão da política do ASC "Monitorar a Criptografia do SQL" não esteja "Desabilitada"

**ID**: CIS Azure 2.15 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Transparent Data Encryption em bancos de dados SQL deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Garantir que a opção 'Emails de contato de segurança' esteja definida

**ID**: CIS Azure 2.16 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As assinaturas devem ter um endereço de email de contato para problemas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Para que as pessoas relevantes em sua organização sejam notificadas quando houver uma potencial violação de segurança em uma das suas assinaturas, defina um contato de segurança para receber notificações por email da Central de Segurança. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Garantir que a opção 'Enviar notificação por email para alertas de severidade alta' seja definida como 'Ativado'

**ID**: CIS Azure 2.18 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A notificação por email para alertas de severidade alta deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Para que as pessoas relevantes em sua organização sejam notificadas quando houver uma potencial violação de segurança em uma das suas assinaturas, habilite notificações por email para alertas de severidade alta na Central de Segurança. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Garantir que a opção 'Enviar email também para proprietários de assinatura' seja definida como 'Ativado'

**ID**: CIS Azure 2.19 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A notificação por email para o proprietário da assinatura para alertas de severidade alta deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Para que os proprietários de assinatura sejam notificados quando houver uma potencial violação de segurança na assinatura deles, defina que notificações para alertas de severidade alta sejam enviadas por email para os proprietários da assinatura na Central de Segurança. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Contas de Armazenamento

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Garantir que "Transferência segura necessária" esteja definida como "Habilitado"

**ID**: CIS Azure 3.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Verificar se "Nível de acesso público" está definido como Privado para contêineres de blobs

**ID**: CIS Azure 3.6 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público da conta de armazenamento não deve ser permitido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O acesso de leitura público anônimo a contêineres e blobs no Armazenamento do Azure é uma forma conveniente de compartilhar dados, mas pode representar riscos de segurança. Para evitar violações de dados causadas por acesso anônimo indesejado, a Microsoft recomenda impedir o acesso público a uma conta de armazenamento, a menos que o seu cenário exija isso. |auditar, negar, desabilitado |[2.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Garantir que a regra de acesso de rede padrão para Contas de Armazenamento estejam definidas como negar

**ID**: CIS Azure 3.7 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem restringir o acesso da rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso da rede às contas de armazenamento deve ser restrito. Configure as regras de rede de tal forma que somente os aplicativos das redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da Internet, o acesso pode ser permitido para o tráfego de redes virtuais específicas do Azure ou para intervalos de endereços IP públicos da Internet |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Garantir que a opção 'Serviços Confiáveis da Microsoft' esteja habilitada para acesso à conta de armazenamento

**ID**: CIS Azure 3.8 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Alguns serviços da Microsoft que interagem com contas de armazenamento operam a partir de redes que não podem ter o acesso concedido por meio de regras de rede. Para ajudar esse tipo de serviço a funcionar como esperado, você pode permitir que o conjunto de serviços Microsoft confiáveis ignore as regras de rede. Esses serviços usarão então a autenticação forte para acessar a conta de armazenamento. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Serviços de Banco de Dados

### <a name="ensure-that-auditing-is-set-to-on"></a>Garantir que "Auditoria" esteja definida como "Ativado"

**ID**: CIS Azure 4.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Garantir que "AuditActionGroups" na política de "auditoria" de um SQL Server esteja definido corretamente

**ID**: CIS Azure 4.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As configurações de Auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |A propriedade AuditActionsAndGroups deve conter pelo menos SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP e BATCH_COMPLETED_GROUP para garantir um log de auditoria completo |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Garantir que a retenção de "Auditoria" seja "maior que 90 dias"

**ID**: CIS Azure 4.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL com auditoria para o destino da conta de armazenamento devem ser configurados com retenção de 90 dias ou mais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Para fins de investigação de incidentes, é recomendável configurar a retenção de dados para a auditoria do SQL Server no destino de conta de armazenamento para pelo menos 90 dias. Confirme que você está cumprindo as regras de retenção necessárias para as regiões em que está operando. Às vezes, isso é necessário para que você tenha conformidade com os padrões regulatórios. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Garantir que a "Segurança de Dados Avançada" em um SQL Server esteja definida como "Ativado"

**ID**: CIS Azure 4.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Garantir que o administrador do Azure Active Directory esteja configurado

**ID**: CIS Azure 4.8 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um administrador do Azure Active Directory deve ser provisionado para servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Audite o provisionamento de um administrador do Active Directory Domain Services do Azure para o seu servidor SQL para habilitar a autenticação do AD do Azure. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Garantir que a 'Criptografia de Dados' esteja definida como 'Ativado' em um Banco de Dados SQL

**ID**: CIS Azure 4.9 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Transparent Data Encryption em bancos de dados SQL deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Garantir que o protetor de TDE do SQL Server seja criptografado com BYOK (Bring Your Own Key)

**ID**: CIS Azure 4.10 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a TDE (Transparent Data Encryption) com chave própria proporciona maior transparência e controle sobre o protetor de TDE, mais segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. Essa recomendação se aplica a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os SQL Servers devem usar chaves gerenciadas pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a TDE (Transparent Data Encryption) com sua chave proporciona maior transparência e controle sobre o protetor de TDE, mais segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. Essa recomendação se aplica a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, desabilitado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Garantir que a opção 'Impor conexão SSL' esteja definida para 'HABILITADO' para o servidor de banco de dados MySQL

**ID**: CIS Azure 4.11 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Garantir que o parâmetro de servidor 'log_checkpoints' seja definido como 'ON' para o servidor de banco de dados PostgreSQL

**ID**: CIS Azure 4.12 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os pontos de verificação de log devem ser habilitados para os servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Esta política ajuda a auditar os bancos de dados PostgreSQL no ambiente sem a configuração log_checkpoints habilitada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Garantir que a opção 'Impor conexão SSL' esteja definida para 'HABILITADO' para o servidor de banco de dados PostgreSQL

**ID**: CIS Azure 4.13 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Garantir que o parâmetro de servidor 'log_connections' seja definido como 'ON' para o servidor de banco de dados PostgreSQL

**ID**: CIS Azure 4.14 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As conexões de log devem ser habilitadas para os servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Esta política ajuda a auditar os bancos de dados PostgreSQL no ambiente sem a configuração log_connections habilitada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Garantir que o parâmetro de servidor 'log_disconnections' seja definido como 'ON' para o servidor de banco de dados PostgreSQL

**ID**: CIS Azure 4.15 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As desconexões devem ser registradas em log para os servidores de banco de dados PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Esta política ajuda a auditar os bancos de dados PostgreSQL no ambiente sem log_disconnections habilitada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Garantir que o parâmetro de servidor 'connection_throttling' seja definido como 'ON' para o servidor de banco de dados PostgreSQL

**ID**: CIS Azure 4.17 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A limitação de conexão deve estar habilitada para os servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Esta política ajuda a auditar os bancos de dados PostgreSQL no ambiente sem a limitação de conexão habilitada. Essa configuração permite a otimização temporária da conexão por IP para muitas falhas inválidas de login de senha. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

### <a name="ensure-that-a-log-profile-exists"></a>Garantir que exista um perfil de log

**ID**: CIS Azure 5.1.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As assinaturas do Azure devem ter um perfil de log para o Log de Atividades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Essa política verifica se um perfil de log está ativado para exportar logs de atividades. Ela audita se não há um perfil de log criado para exportar os logs para uma conta de armazenamento ou para um hub de eventos. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Garantir que a retenção do log de atividades esteja definida para 365 dias ou mais

**ID**: CIS Azure 5.1.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O log de atividades deve ser retido por pelo menos um ano](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Essa política auditará o log de atividades se a retenção não for definida para 365 dias ou para sempre (dias de retenção definidos como 0). |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Garantir que o perfil de auditoria capture todas as atividades

**ID**: CIS Azure 5.1.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de log do Azure Monitor deve coletar logs para as categorias "gravar", "excluir" e "ação"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Essa política garante que um perfil de log colete logs para as categorias "gravar", "excluir" e "ação" |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Garantir que o perfil de log capture logs de atividade para todas as regiões, incluindo global

**ID**: CIS Azure 5.1.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve coletar os logs de atividades de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Essa política audita o perfil de log do Azure Monitor que não exporta atividades de todas as regiões com suporte do Azure, incluindo global. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Verificar se o contêiner que armazena os logs de atividade não está acessível publicamente

**ID**: CIS Azure 5.1.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público da conta de armazenamento não deve ser permitido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O acesso de leitura público anônimo a contêineres e blobs no Armazenamento do Azure é uma forma conveniente de compartilhar dados, mas pode representar riscos de segurança. Para evitar violações de dados causadas por acesso anônimo indesejado, a Microsoft recomenda impedir o acesso público a uma conta de armazenamento, a menos que o seu cenário exija isso. |auditar, negar, desabilitado |[2.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Garanta que a conta de armazenamento que tem o contêiner com logs de atividades seja criptografada com BYOK (Bring Your Own Key)

**ID**: CIS Azure 5.1.6 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A conta de armazenamento que possui o contêiner com os logs de atividade deve ser criptografada com BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Essa política auditará se a conta de armazenamento que possui o contêiner com logs de atividades estiver criptografada com BYOK. A política funcionará apenas se a conta de armazenamento estiver na mesma assinatura dos logs de atividades por design. Mais informações sobre a criptografia de Armazenamento do Microsoft Azure em repouso podem ser encontradas aqui [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Garantir que o log do Azure Key Vault esteja 'Habilitado'

**ID**: CIS Azure 5.1.7 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recursos do HSM Gerenciado pelo Azure Key Vault devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Para recriar trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou quando a sua rede estiver comprometida, audite habilitando logs de recursos em HSMs gerenciados. Siga as instruções aqui: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging). |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Os logs de recurso no Key Vault devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Verifique se o alerta do log de atividades existe para criar atribuição de política

**ID**: CIS Azure 5.2.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações de política específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Essa política audita Operações de política específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Verifique se o alerta do log de atividades existe para criar ou atualizar o Grupo de Segurança de Rede

**ID**: CIS Azure 5.2.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Verifique se o alerta do log de atividades existe para excluir o grupo de segurança de rede

**ID**: CIS Azure 5.2.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Verifique se o alerta do log de atividades existe para criar ou atualizar a regra do grupo de segurança de rede

**ID**: CIS Azure 5.2.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Verifique se o alerta do log de atividades existe para excluir a regra do grupo de segurança de rede

**ID**: CIS Azure 5.2.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Verifique se o alerta do log de atividades existe para criar ou atualizar a solução de segurança

**ID**: CIS Azure 5.2.6 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Essa política audita Operações de segurança específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Verifique se o alerta do log de atividades existe para excluir a solução de segurança

**ID**: CIS Azure 5.2.7 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Essa política audita Operações de segurança específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Verifique se o alerta do log de atividades existe para criar ou atualizar ou excluir a regra de firewall do SQL Server

**ID**: CIS Azure 5.2.8 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Verifique se o alerta do log de atividades existe para atualizar a política de segurança

**ID**: CIS Azure 5.2.9 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Essa política audita Operações de segurança específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Rede

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Verifique se o acesso SSH está restrito da Internet

**ID**: CIS Azure 6.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso ao RDP da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Essa política audita qualquer regra de segurança de rede que permita acesso ao RDP da Internet |Audit, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Garantir que o acesso SSH seja restrito da Internet

**ID**: CIS Azure 6.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso ao SSH da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Essa política audita qualquer regra de segurança de rede que permita o acesso ao SSH da Internet |Audit, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Garantir que o Observador de Rede esteja 'Habilitado'

**ID**: CIS Azure 6.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Máquinas Virtuais

### <a name="ensure-that-os-disk-are-encrypted"></a>Garantir que o 'Disco do SO' seja criptografado

**ID**: CIS Azure 7.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A criptografia de disco deve ser aplicada em Máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem uma criptografia de disco habilitada serão monitoradas pela Central de Segurança do Azure como recomendações. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Garantir que os 'Discos de dados' sejam criptografados

**ID**: CIS Azure 7.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A criptografia de disco deve ser aplicada em Máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem uma criptografia de disco habilitada serão monitoradas pela Central de Segurança do Azure como recomendações. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Garantir que os 'Discos não anexados' sejam criptografados

**ID**: CIS Azure 7.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os discos desconectados devem ser criptografados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Essa política audita qualquer disco desconectado sem a criptografia habilitada. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Garantir que apenas as extensões aprovadas sejam instaladas

**ID**: CIS Azure 7.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Somente as extensões aprovadas da VM devem ser instaladas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Essa política rege as extensões da máquina virtual que não foram aprovadas. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Garantir que os Patches de SO mais recentes para todas as máquinas virtuais sejam aplicados

**ID**: CIS Azure 7.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As atualizações do sistema devem ser instaladas em suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Garantir que a proteção de ponto de extremidade para todas as máquinas virtuais esteja instalada

**ID**: CIS Azure 7.6 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorar o Endpoint Protection ausente na Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Outras considerações de segurança

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Verifique se a data de validade está definida em todas as chaves

**ID**: CIS Azure 8.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As chaves do Key Vault devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |As chaves de criptografia devem ter uma data de validade definida e não ser permanentes. As chaves válidas para sempre dão a um invasor potencial mais tempo para comprometer a chave. Uma prática de segurança recomendada é definir as datas de validade em chaves de criptografia. |Audit, Deny, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Verifique se a data de validade está definida em todos os segredos

**ID**: CIS Azure 8.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os segredos do Key Vault devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Os segredos devem ter uma data de validade definida e não ser permanentes. Os segredos são válidos para sempre dão a um invasor potencial mais tempo para comprometê-las. Uma prática de segurança recomendada é definir datas de validade nos segredos. |Audit, Deny, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Garantir que o cofre de chaves seja recuperável

**ID**: CIS Azure 8.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O HSM Gerenciado do Azure Key Vault deve ter a proteção contra limpeza habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |A exclusão mal-intencionada de um HSM Gerenciado do Azure Key Vault pode levar à perda permanente de dados. Um funcionário mal-intencionado na sua organização pode potencialmente excluir e limpar o HSM Gerenciado do Azure Key Vault. A proteção contra limpeza protege você contra ataques internos impondo um período de retenção obrigatório para o HSM Gerenciado do Azure Key Vault de exclusão temporária. Ninguém dentro da sua organização nem a Microsoft poderá limpar o HSM Gerenciado do Azure Key Vault durante o período de retenção de exclusão temporária. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Os cofres de chaves devem ter a proteção contra limpeza habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A exclusão mal-intencionada de um cofre de chaves pode levar à perda permanente de dados. Um funcionário mal-intencionado em sua organização pode potencialmente excluir e limpar os cofres de chaves. A proteção de limpeza protege você contra ataques de invasores impondo um período de retenção obrigatório para cofres de chaves de exclusão temporária. Ninguém dentro de sua organização nem a Microsoft poderá limpar os cofres de chaves durante o período de retenção de exclusão temporária. |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Habilitar o RBAC (controle de acesso baseado em função) nos Serviços de Kubernetes do Azure

**ID**: CIS Azure 8.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular das ações que os usuários podem executar, use o RBAC (controle de acesso baseado em função) para gerenciar permissões nos clusters do Serviço de Kubernetes e configurar políticas de autorização relevantes. |Audit, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Garantir que a autenticação do serviço de aplicativo esteja definida no Serviço de Aplicativo do Azure

**ID**: CIS Azure 9.1 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A autenticação deve ser habilitada em seu aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |A Autenticação do Serviço de Aplicativo do Azure é um recurso que pode impedir que solicitações HTTP anônimas cheguem ao aplicativo de API ou autenticar aqueles que possuem tokens antes de alcançarem o aplicativo de API |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A autenticação deve ser habilitada em seu aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |A Autenticação do Serviço de Aplicativo do Azure é um recurso que pode impedir que solicitações HTTP anônimas cheguem ao aplicativo de funções ou autenticar aqueles que possuem tokens antes de alcançarem o aplicativo de funções |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A autenticação deve ser habilitada em seu aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |A Autenticação do Serviço de Aplicativo do Azure é um recurso que pode impedir que solicitações HTTP anônimas cheguem ao aplicativo Web ou autenticar aqueles que possuem tokens antes de alcançarem o aplicativo Web |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Garantir que o aplicativo Web redirecione todo o tráfego HTTP para HTTPS no Serviço de Aplicativo do Azure

**ID**: CIS Azure 9.2 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Garantir que o aplicativo Web esteja usando a última versão da criptografia TLS

**ID**: CIS Azure 9.3 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Garantir que o aplicativo Web tenha a opção 'Certificados de Cliente (Certificados de cliente de entrada)' definida como 'Ativado'

**ID**: CIS Azure 9.4 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que o aplicativo de API tenha a opção "Certificados de Cliente (Certificados de cliente de entrada)" definida como "Ativado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Os certificados de cliente permitem que o aplicativo solicite um certificado para solicitações recebidas. Somente clientes que possuem um certificado válido poderão acessar o aplicativo. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Garantir que o aplicativo Web tenha a opção "Certificados de Cliente (Certificados de cliente de entrada)" definida como "Ativado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que o aplicativo solicite um certificado para solicitações recebidas. Somente clientes que possuem um certificado válido poderão acessar o aplicativo. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Os aplicativos de funções devem ter a opção 'Certificados do Cliente (Certificados do cliente de entrada)' habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Os certificados de cliente permitem que o aplicativo solicite um certificado para solicitações recebidas. Somente clientes com certificados válidos poderão acessar o aplicativo. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Garantir que o Registro com o Azure Active Directory esteja habilitado no Serviço de Aplicativo

**ID**: CIS Azure 9.5 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A identidade gerenciada deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Usar uma identidade gerenciada para uma segurança de autenticação aprimorada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[A identidade gerenciada deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Usar uma identidade gerenciada para uma segurança de autenticação aprimorada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A identidade gerenciada deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Usar uma identidade gerenciada para uma segurança de autenticação aprimorada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Garantir que a 'versão do PHP' seja a última, se usada para executar o aplicativo Web

**ID**: CIS Azure 9.7 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verificar se a "versão do PHP" é a última, se usada como parte do aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Periodicamente, versões mais recentes são lançadas para o software PHP devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da última versão do PHP para aplicativos de API, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Garantir que a "versão do PHP" seja a última, se usada como parte do aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Periodicamente, versões mais recentes são lançadas para o software PHP devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da última versão do PHP para aplicativos Web, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Garantir que a 'versão do Python' seja a última, se usada para executar o aplicativo Web

**ID**: CIS Azure 9.8 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verificar se a "versão do Python" é a última, se usada como parte do aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Periodicamente, versões mais recentes são lançadas para o software Python devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da versão mais recente do Python para aplicativos de API, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Garantir que a "versão do Python" seja a última, se usada como parte do aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, versões mais recentes são lançadas para o software Python devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da última versão do Python para aplicativos de funções, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Garantir que a "versão do Python" seja a última, se usada como parte do aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, versões mais recentes são lançadas para o software Python devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da última versão do Python para aplicativos Web, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Garantir que a 'versão do Java' seja a última, se usada para executar o aplicativo Web

**ID**: CIS Azure 9.9 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verificar se a "versão do Java" é a última, se usada como parte do aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Periodicamente, versões mais recentes são lançadas para Java devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da versão mais recente do Python para aplicativos de API, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Garantir que a "versão do Java" seja a última, se usada como parte do aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Periodicamente, versões mais recentes são lançadas para o software Java devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomendamos o uso da última versão do Java para aplicativos de funções, a fim de aproveitar as correções de segurança, se houver, e/ou as novas funcionalidades da última versão. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Garantir que a "versão do Java" seja a última, se usada como parte do aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Periodicamente, versões mais recentes são lançadas para o software Java devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomenda-se o uso da versão mais recente do Java para aplicativos Web, a fim de aproveitar as correções de segurança, se houver, e/ou novas funcionalidades da versão mais recente. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Garantir que a "Versão do HTTP" seja a última, se usada para executar o aplicativo Web

**ID**: CIS Azure 9.10 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verificar se a "Versão do HTTP" é a última, se usada para executar o aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Periodicamente, versões mais recentes são lançadas para HTTP devido a falhas de segurança ou para incluir funcionalidades adicionais. Usar a versão do HTTP mais recente para aplicativos Web para aproveitar as correções de segurança, se houver, e/ou novas funcionalidades da versão mais recente. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Garantir que a "Versão do HTTP" seja a última, se usada para executar o aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Periodicamente, versões mais recentes são lançadas para HTTP devido a falhas de segurança ou para incluir funcionalidades adicionais. Usar a versão do HTTP mais recente para aplicativos Web para aproveitar as correções de segurança, se houver, e/ou novas funcionalidades da versão mais recente. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Garantir que a "Versão do HTTP" seja a última, se usada para executar o aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Periodicamente, versões mais recentes são lançadas para HTTP devido a falhas de segurança ou para incluir funcionalidades adicionais. Usar a versão do HTTP mais recente para aplicativos Web para aproveitar as correções de segurança, se houver, e/ou novas funcionalidades da versão mais recente. Atualmente, essa política se aplica somente a aplicativos Web do Linux. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

Artigos adicionais sobre o Azure Policy:

- Visão geral da [Conformidade Regulatória](../concepts/regulatory-compliance.md).
- Consulte a [estrutura de definição da iniciativa](../concepts/initiative-definition-structure.md).
- Veja outros exemplos em [Amostras do Azure Policy](./index.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
