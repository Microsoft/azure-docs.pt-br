---
title: Detalhes da Conformidade Regulatória para o HIPAA HITRUST 9.2
description: Detalhes da iniciativa interna de Conformidade Regulatória do HIPAA HITRUST 9.2. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: ad5700090eda9fad4c607c1f24503f1483cd36fe
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506237"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa interna de Conformidade Regulatória do HIPAA HITRUST 9.2

O artigo a seguir fornece detalhes sobre como a definição da iniciativa interna de Conformidade Regulatória do Azure Policy é mapeada para **domínios de conformidade** e **controles** no HIPAA HITRUST 9.2.
Para obter mais informações sobre esse padrão de conformidade, confira [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Para entender a _Propriedade_, confira [Definição de política do Azure Policy](../concepts/definition-structure.md#type) e [Responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).

Os mapeamentos a seguir referem-se aos controles do **HIPAA HITRUST 9.2**. Use a navegação no lado direito para ir diretamente para um **domínio de conformidade** específico. Muitos dos controles são implementados com uma definição de iniciativa do [Azure Policy](../overview.md). Para examinar a definição da iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**.
Em seguida, encontre e selecione a definição de iniciativa interna de Conformidade Regulatória do **HITRUST/HIPAA**.

Essa iniciativa interna é implantada como parte do [exemplo de blueprint do HIPAA HITRUST 9.2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../overview.md).
> Essas políticas podem ajudar você a [avaliar a conformidade](../how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias definições de política e não garante que você esteja em conformidade total com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre os domínios de conformidade, os controles e as definições do Azure Policy para este padrão de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Gerenciamento de privilégios

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>O acesso a funções de gerenciamento ou consoles administrativos para sistemas que hospedam sistemas virtualizados é restrito ao pessoal com base no princípio de privilégios mínimos e com suporte por meio de controles técnicos.

**ID**: 11180.01c3System.6 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Os privilégios são formalmente autorizados e controlados, alocados aos usuários de acordo com a necessidade de uso e com o evento para a função deles (por exemplo, usuário ou administrador), e documentados para cada produto/elemento do sistema.

**ID**: 1143.01c1System.123 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Portas de gerenciamento devem ser fechadas nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |As portas abertas de gerenciamento remoto estão expondo sua VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam obter credenciais por força bruta para obter acesso de administrador à máquina. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>A organização autoriza explicitamente o acesso a funções relevantes de segurança específicas (implantadas em hardware, software e firmware) e informações relevantes de segurança.

**ID**: 1144.01c1System.4 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>O controle de acesso baseado em função é implementado e tem a capacidade de mapear cada usuário para uma ou mais funções e cada função para uma ou mais funções do sistema.

**ID**: 1145.01c2System.1 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>A organização promove o desenvolvimento e o uso de programas que dispensam a execução com privilégios elevados e rotinas do sistema para evitar a necessidade de conceder privilégios aos usuários.

**ID**: 1146.01c2System.23 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas externas com permissões de proprietário devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Contas externas com permissões de proprietário devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Privilégios elevados são atribuídos a uma ID de usuário diferente daquela usada para uso comercial normal e todos os usuários acessam serviços privilegiados em uma única função, e esse acesso privilegiado é minimizado.

**ID**: 1147.01c2System.456 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Contas descontinuadas com permissões de proprietário devem ser removidas da sua assinatura.  Contas descontinuadas são contas que foram impedidas de fazer login. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>A organização restringe o acesso a funções privilegiadas e a todas as informações relevantes de segurança.

**ID**: 1148.01c2System.78 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções internas, como 'Proprietário, Contribuidor, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Contas'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Os computadores Windows devem ter as configurações da Política de Grupo especificadas na categoria 'Opções de Segurança – Contas' para limitar o uso da conta local de senhas em branco e o status da conta convidado. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>A organização facilita o compartilhamento de informações, permitindo que os usuários autorizados determinem o acesso de um parceiro de negócios quando o critério é permitido, conforme definido pela organização, e empregando processos manuais ou mecanismos automatizados para ajudar os usuários a tomar decisões de compartilhamento/colaboração de informações.

**ID**: 1149.01c2System.9 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular das ações que os usuários podem executar, use o RBAC (controle de acesso baseado em função) para gerenciar permissões nos clusters do Serviço de Kubernetes e configurar políticas de autorização relevantes. |Audit, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>O sistema de controle de acesso para os componentes do sistema que armazenam, processam ou transmitem informações sigilosas é definido com uma configuração &quot;negar tudo&quot; padrão.

**ID**: 1150.01c2System.10 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Portas de gerenciamento devem ser fechadas nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |As portas abertas de gerenciamento remoto estão expondo sua VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam obter credenciais por força bruta para obter acesso de administrador à máquina. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>A organização limita a autorização para contas privilegiadas em sistemas de informações a um subconjunto predefinido de usuários.

**ID**: 1151.01c3System.1 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>A organização audita a execução de funções privilegiadas em sistemas de informações e garante que os sistemas de informações impeçam que usuários sem privilégios executem funções privilegiadas.

**ID**: 1152.01c3System.2 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Todo o acesso ao sistema de arquivos não explicitamente necessário está desabilitado, e somente os usuários autorizados têm permissão para acessar apenas o que é expressamente necessário para o desempenho das respectivas tarefas de trabalho.

**ID**: 1153.01c3System.35 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular das ações que os usuários podem executar, use o RBAC (controle de acesso baseado em função) para gerenciar permissões nos clusters do Serviço de Kubernetes e configurar políticas de autorização relevantes. |Audit, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Os prestadores de serviço só receberão acesso físico e mínimo ao sistema depois que a organização avaliar a capacidade do prestador de serviço de cumprir os requisitos de segurança e de ele concordar em cumpri-los.

**ID**: 1154.01c3System.4 – 01.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Autenticação de usuário para conexões externas

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Métodos de autenticação fortes, como multifator, Radius ou Kerberos (para acesso privilegiado) e CHAP (para criptografia de credenciais para métodos de conexão discada) são implementados para todas as conexões externas com a rede da organização.

**ID**: 1116.01j1Organizational.145 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>O acesso remoto por fornecedores e parceiros de negócios (por exemplo, para manutenção remota) é desabilitado/desativado quando não está em uso.

**ID**: 1117.01j1Organizational.23 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>As organizações implementam a criptografia (por exemplo, &nbsp;soluções de VPN ou linhas privadas) e registram em log o acesso remoto à rede da organização por funcionários, prestadores de serviço ou terceiros (por exemplo, fornecedores).

**ID**: 1118.01j2Organizational.124 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>O equipamento de rede é verificado quanto a funcionalidades de conexão discada inesperada.

**ID**: 1119.01j2Organizational.3 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Sessões de administração remota são autorizadas e criptografadas e empregam maiores medidas de segurança.

**ID**: 1121.01j3Organizational.2 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Se a criptografia não for usada para conexões discadas, o CIO ou o representante designado dele fornecerá uma autorização por escrito específica.

**ID**: 1173.01j1Organizational.6 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>A organização protege o acesso sem fio a sistemas que contêm informações confidenciais autenticando usuários e dispositivos.

**ID**: 1174.01j1Organizational.7 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>O acesso remoto às informações comerciais entre redes públicas só ocorre após a identificação e a autenticação bem-sucedidas.

**ID**: 1175.01j1Organizational.8 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>A organização exige uma funcionalidade de retorno de chamada com uma nova autenticação para verificar conexões discadas de localizações autorizadas.

**ID**: 1176.01j2Organizational.5 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>As IDs de usuário atribuídas a fornecedores são examinadas de acordo com a política de revisão de acesso da organização com uma frequência mínima anual.

**ID**: 1177.01j2Organizational.6 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>A autenticação do Node, incluindo técnicas de criptografia (por exemplo, certificados do computador), funciona como um método alternativo de autenticar grupos de usuários remotos quando eles estão conectados a uma instalação de computador segura e compartilhada.

**ID**: 1178.01j2Organizational.7 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>O sistema de informações monitora e controla os métodos de acesso remoto.

**ID**: 1179.01j3Organizational.1 – 01.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Diagnóstico remoto e proteção da porta de configuração

### <a name="access-to-network-equipment-is-physically-protected"></a>O acesso ao equipamento de rede está fisicamente protegido.

**ID**: 1192.01l1Organizational.1 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Os controles para o acesso às portas de configuração e diagnóstico incluem o uso de um bloqueio de chave e a implementação de procedimentos de suporte para controlar o acesso físico à porta.

**ID**: 1193.01l2Organizational.13 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Portas de gerenciamento devem ser fechadas nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |As portas abertas de gerenciamento remoto estão expondo sua VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam obter credenciais por força bruta para obter acesso de administrador à máquina. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Portas, serviços e aplicativos semelhantes instalados em um computador ou em sistemas de rede e que não são especificamente necessários para a funcionalidade comercial, são desabilitados ou removidos.

**ID**: 1194.01l2Organizational.2 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desativada para aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A depuração remota exige que as portas de entrada estejam abertas em um aplicativo Web. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>A organização examina o sistema de informações a cada 365 (trezentos e sessenta e cinco) dias para identificar e desabilitar funções, portas, protocolos e/ou serviços desnecessários e não seguros.

**ID**: 1195.01l3Organizational.1 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desativada para o aplicativos de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de funções. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>A organização identifica os programas de software não autorizados (na lista de bloqueios) no sistema de informações, impede a execução do programa de acordo com uma lista de programas de software não autorizados (na lista de bloqueios), emprega uma política "permitir tudo" e "negar por exceção" para proibir a execução de programas de software não autorizados (na lista de bloqueios) conhecidos, além de examinar e atualizar a lista de programas de software não autorizados (na lista de bloqueios) anualmente.

**ID**: 1196.01l3Organizational.24 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desligada para aplicativos de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de API. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>A organização desabilita o Bluetooth e os protocolos de rede ponto a ponto no sistema de informações considerados desnecessários ou não seguros.

**ID**: 1197.01l3Organizational.3 – 01.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Diferenciação em redes

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Os gateways de segurança da organização (por exemplo, firewalls) impõem políticas de segurança e são configurados para filtrar o tráfego entre domínios, bloquear acesso não autorizado e são usados para manter a diferenciação entre segmentos de rede internos com fio, internos sem fio e externos (por exemplo, a Internet), incluindo DMZs e imposição de políticas de controle de acesso para cada um dos domínios.

**ID**: 0805.01m1Organizational.12 – 01.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Essa política audita os Serviços de Aplicativo que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) Microsoft Azure Cosmos DB |Essa política audita os Cosmos DB que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Essa política audita os Hubs de Eventos que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes de gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Essa política negará se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. Atribuir um grupo de segurança de rede a uma sub-rede de gateway fará com que o gateway pare de funcionar. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Essa política audita os Key Vaults que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Essa política audita os Barramentos de Serviço que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Essa política audita os SQL Servers que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Essa política audita as Contas de Armazenamento que não estão configuradas para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>A rede de organizações é lógica e fisicamente segmentada com um perímetro de segurança definido e um conjunto graduado de controles, incluindo sub-redes de componentes do sistema publicamente acessíveis que são logicamente separados da rede interna, com base nos requisitos organizacionais; e o tráfego é controlado com base na funcionalidade necessária e na classificação dos dados/sistemas com base em uma avaliação de risco e nos respectivos requisitos de segurança.

**ID**: 0806.01m2Organizational.12356 – 01.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Essa política audita os Serviços de Aplicativo que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) Microsoft Azure Cosmos DB |Essa política audita os Cosmos DB que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Essa política audita os Hubs de Eventos que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes de gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Essa política negará se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. Atribuir um grupo de segurança de rede a uma sub-rede de gateway fará com que o gateway pare de funcionar. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Essa política audita os Key Vaults que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Essa política audita os Barramentos de Serviço que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Essa política audita os SQL Servers que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Essa política audita as Contas de Armazenamento que não estão configuradas para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>As redes são separadas das redes de nível de produção durante a migração de servidores físicos, aplicativos ou dados para servidores virtualizados.

**ID**: 0894.01m2Organizational.7 – 01.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Essa política audita os Serviços de Aplicativo que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) Microsoft Azure Cosmos DB |Essa política audita os Cosmos DB que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Implantar o observador de rede quando redes virtuais são criadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Essa política cria um recurso de observador de rede em regiões com redes virtuais. Você precisa garantir a existência de um grupo de recursos chamado networkWatcherRG, que será usado para implantar instâncias do observador de rede. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Essa política audita os Hubs de Eventos que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes de gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Essa política negará se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. Atribuir um grupo de segurança de rede a uma sub-rede de gateway fará com que o gateway pare de funcionar. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Essa política audita os Key Vaults que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Essa política audita os Barramentos de Serviço que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Essa política audita os SQL Servers que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Essa política audita as Contas de Armazenamento que não estão configuradas para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Controle de conexão de rede

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>O tráfego de rede é controlado de acordo com a política de controle de acesso da organização por meio do firewall e de outras restrições relacionadas à rede para cada ponto de acesso à rede ou interface gerenciada do serviço de telecomunicação externo.

**ID**: 0809.01n2Organizational.1234 – 01.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>As informações transmitidas são protegidas e, no mínimo, criptografadas em redes abertas e públicas.

**ID**: 0810.01n2Organizational.5 – 01.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>As exceções à política de fluxo de tráfego são documentadas com uma missão/necessidade de suporte, pela duração da exceção, e examinadas pelo menos anualmente; as exceções da política de fluxo de tráfego são removidas quando não há mais suporte a uma missão/necessidade de negócios explícita.

**ID**: 0811.01n2Organizational.6 – 01.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Dispositivos remotos que estabelecem uma conexão não remota não têm permissão para se comunicar com recursos externos (remotos).

**ID**: 0812.01n2Organizational.8 – 01.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>A capacidade dos usuários de se conectar à rede interna é restrita usando uma política de negação por padrão e de permissão por exceção em interfaces gerenciadas de acordo com a política de controle de acesso e os requisitos de aplicativos clínicos e de negócios.

**ID**: 0814.01n1Organizational.12 – 01.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[As máquinas virtuais para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja suas máquinas virtuais contra possíveis ameaças, restringindo o acesso a elas com um NSG (grupo de segurança de rede). Saiba mais sobre como controlar o tráfego com NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As sub-redes devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja sua sub-rede contra possíveis ameaças, restringindo o acesso a ela com um NSG (Grupo de Segurança de Rede). Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para sua sub-rede. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[As máquinas virtuais devem estar conectadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Essa política audita as máquinas virtuais que estão conectadas a uma rede virtual que não foi aprovada. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Identificação e autenticação do usuário

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>A organização verifica se as IDs de usuário redundantes não são emitidas a outros usuários e se todos os usuários são exclusivamente identificados e autenticados para acesso local e remoto aos sistemas de informações.

**ID**: 11109.01q1Organizational.57 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Usuários não organizacionais (todos os usuários de sistema de informações que não são usuários organizacionais, como pacientes, clientes, prestadores de serviço ou estrangeiros), ou processos que atuam em nome de usuários não organizacionais, determinados a precisar de acesso às informações que residem nos sistemas de informações da organização, são exclusivamente identificados e autenticados.

**ID**: 11110.01q1Organizational.6 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>Quando a autenticação baseada em PKI é usada, o sistema de informações valida os certificados construindo e confirmando um caminho de certificação para uma âncora de confiança aceita, incluindo a verificação das informações de status de certificado; impõe o acesso à chave privada correspondente; mapeia a identidade para a conta correspondente do indivíduo ou do grupo; e implementa um cache local de dados de revogação para dar suporte à descoberta e à validação de caminho no caso da impossibilidade de acessar as informações de revogação na rede.

**ID**: 11111.01q2System.4 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>O sistema de informações emprega mecanismos de autenticação resistentes à reprodução, como nonce, senhas únicas ou carimbos de data/hora para proteger o acesso à rede em contas privilegiadas; e, para a autenticação baseada em token de hardware, emprega mecanismos que atendem aos requisitos mínimos de token abordados no NIST SP 800-63-2, Diretrizes de Autenticação Eletrônica.

**ID**: 11112.01q2Organizational.67 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>A organização exige que as assinaturas eletrônicas, exclusivas de um indivíduo, não possam ser reutilizadas por outras pessoas nem transferidas para ninguém.

**ID**: 11208.01q1Organizational.8 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>As assinaturas eletrônicas e as assinaturas manuscritas executadas em registros eletrônicos devem estar vinculadas aos respectivos registros eletrônicos.

**ID**: 11210.01q2Organizational.10 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar computadores Windows que têm os membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Os registros eletrônicos assinados devem conter informações associadas à assinatura em formato legível.

**ID**: 11211.01q2Organizational.11 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar computadores Windows que não têm membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local não contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Os usuários que executaram funções privilegiadas (por exemplo, administração do sistema) usam contas separadas ao executar essas funções privilegiadas.

**ID**: 1123.01q1System.2 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar os computadores Windows que têm contas extras no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver membros que não estejam listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Os métodos de autenticação multifator são usados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

**ID**: 1125.01q2System.1 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar computadores Windows que têm os membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Quando tokens são fornecidos para a autenticação multifator, a verificação presencial é necessária antes de permitir o acesso.

**ID**: 1127.01q2System.3 – 01.q **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar computadores Windows que não têm membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local não contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação de riscos relacionados a partes externas

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>O acesso às informações e aos sistemas das organizações por terceiros não é permitido até uma diligência prévia ser realizada, os controles apropriados serem implementados e um contrato que reflita os requisitos de segurança seja assinado, confirmando que eles entendem e aceitam as respectivas obrigações.

**ID**: 1401.05i1Organizational.1239 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>As conexões de acesso remoto entre a organização e as partes externas são criptografadas.

**ID**: 1402.05i1Organizational.45 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>O acesso permitido a partes externas é limitado ao mínimo necessário e concedido apenas pela duração necessária.

**ID**: 1403.05i1Organizational.67 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>A diligência prévia da parte externa inclui entrevistas, revisão de documentos, listas de verificação, revisões de certificação (por exemplo, HITRUST)&nbsp;ou outros meios remotos.

**ID**: 1404.05i2Organizational.1 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>A identificação de riscos relacionados ao acesso de terceiros leva em conta um conjunto mínimo de problemas especificamente definidos.

**ID**: 1418.05i1Organizational.8 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>A organização obtém garantias satisfatórias de que há uma segurança razoável das informações na cadeia de fornecedores de informações executando uma revisão anual, que inclui todos os parceiros/provedores de terceiros dos quais a cadeia de fornecedores de informações depende.

**ID**: 1450.05i2Organizational.2 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Os provedores de serviços de nuvem criam e implementam controles para atenuar e conter os riscos de segurança de dados por meio da separação adequada de obrigações, do acesso baseado em função e do acesso de privilégio mínimo para todos os funcionários na cadeia de fornecedores.

**ID**: 1451.05iCSPOrganizational.2 – 05.i **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Log de auditoria

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Um registro de auditoria seguro é criado para todas as atividades no sistema (criação, leitura, atualização, exclusão) que envolvem informações sigilosas.

**ID**: 1202.09aa1System.1 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso no Azure Data Lake Storage devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Faça uma auditoria para verificar se faltam atualizações de segurança do sistema e atualizações críticas que devem ser instaladas para garantir que os seus conjuntos de dimensionamento de máquinas virtuais Windows e Linux estejam seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Os registros de auditoria incluem a ID de usuário exclusiva, a ID do titular dos dados exclusiva, a função executada e a data/hora em que o evento foi executado.

**ID**: 1203.09aa1System.2 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso nos Aplicativos Lógicos devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>As atividades de usuários com privilégios (administradores, operadores etc.) incluem o êxito/a falha do evento, a hora em que o evento ocorreu, a conta envolvida, os processos envolvidos e informações adicionais sobre o evento.

**ID**: 1204.09aa1System.3 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso no Hub IoT devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Os logs de mensagens enviadas e recebidas são mantidos incluindo a data, a hora, a origem e o destino da mensagem, mas não o conteúdo dela.

**ID**: 1205.09aa2System.1 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso em contas do Lote devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>A auditoria está sempre disponível enquanto o sistema está ativo e rastreia os principais eventos, o acesso a dados com êxito/falha, as alterações de configuração de segurança do sistema, o uso privilegiado ou de utilitário, os alarmes gerados, &nbsp;a ativação e a desativação de sistemas de proteção (por exemplo, A/V e IDS), a ativação e a desativação de mecanismos de identificação e autenticação e a criação e a exclusão de objetos no nível de sistema.

**ID**: 1206.09aa2System.23 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[os logs de recurso nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Recomenda-se habilitar os Logs para que a trilha de atividades possa ser recriada quando investigações forem necessárias no caso de um incidente ou um comprometimento. |AuditIfNotExists, desabilitado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Os registros de auditoria são mantidos por 90 dias e os registros de auditoria mais antigos são arquivados por um ano.

**ID**: 1207.09aa2System.4 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso no Azure Stream Analytics devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Os logs de recurso no Hub de Eventos devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Os logs de auditoria são mantidos para atividades de gerenciamento, inicialização/desligamento/erros de sistema e aplicativo, alterações de arquivo e alterações de política de segurança.

**ID**: 1208.09aa3System.1 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de recurso nos serviços de pesquisa devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Os logs de recurso no Barramento de Serviço devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>O sistema de informações gera registros de auditoria contendo as seguintes informações detalhadas: (i) nome de arquivo acessado; (ii) programa ou comando usado para iniciar o evento; e (iii) endereços de origem e destino.

**ID**: 1209.09aa3System.2 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Auditoria de ativação de logs de diagnóstico no aplicativo. Isso permite recriar trilhas de atividades para fins de investigação se ocorrer um incidente de segurança ou se sua rede estiver comprometida |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Todas as divulgações de informações sigilosas dentro ou fora da organização são registradas, incluindo o tipo de divulgação, a data/hora do evento, o destinatário e o remetente.

**ID**: 1210.09aa3System.3 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Configuração de diagnóstico de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditar configuração de diagnóstico para tipos de recursos selecionados |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Os logs de recurso no Data Lake Analytics devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>A organização verifica a cada 90 (noventa) dias em cada extrato de informações sigilosas registradas se os dados foram apagados ou se o uso deles ainda é necessário.

**ID**: 1211.09aa3System.4 – 09.aa **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Os logs de recursos do HSM Gerenciado pelo Azure Key Vault devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Para recriar trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou quando a sua rede estiver comprometida, audite habilitando logs de recursos em HSMs gerenciados. Siga as instruções aqui: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging). |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Os logs de recurso no Key Vault devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Uso do sistema de monitoramento

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Conexões remotas não autorizadas com os sistemas de informação são monitoradas e examinadas pelo menos trimestralmente, e uma ação apropriada será executada se for descoberta uma conexão não autorizada.

**ID**: 1120.09ab3System.9 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve coletar os logs de atividades de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Essa política audita o perfil de log do Azure Monitor que não exporta atividades de todas as regiões com suporte do Azure, incluindo global. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>A organização monitora o sistema de informações para identificar irregularidades ou anomalias que são indicadores de mau funcionamento ou comprometimento do sistema e ajudam a confirmar se o sistema está funcionando em um estado ideal, resiliente e seguro.

**ID**: 12100.09ab2System.15 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente do Log Analytics deve ser instalado nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Essa política audita as máquinas virtuais do Windows ou do Linux nas quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>A organização especifica com que frequência os logs de auditoria são examinados, como as revisões são documentadas e as funções e as responsabilidades específicas da equipe que realiza as revisões, incluindo as certificações profissionais ou outras qualificações necessárias.

**ID**: 12101.09ab1Organizational.3 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Essa política audita os Conjuntos de Dimensionamento de Máquinas Virtuais do Windows ou do Linux nos quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>A organização deve testar periodicamente os processos de monitoramento e detecção, corrigir deficiências e aprimorar os respectivos processos.

**ID**: 12102.09ab1Organizational.4 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar os computadores Windows nos quais o agente do Log Analytics não esteja conectado conforme o esperado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o agente não estiver instalado e nem se ele estiver instalado, mas o objeto COM AgentConfigManager.MgmtSvcCfg retornar que ele está registrado em um workspace diferente da ID especificada no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Todos os requisitos legais aplicáveis relacionados ao monitoramento de tentativas de acesso autorizado e não autorizado são cumpridos.

**ID**: 1212.09ab1System.1 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de log do Azure Monitor deve coletar logs para as categorias "gravar", "excluir" e "ação"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Essa política garante que um perfil de log colete logs para as categorias "gravar", "excluir" e "ação" |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Os sistemas automatizados implantados em todo o ambiente da organização são usados para monitorar eventos importantes e atividades anormais e analisar logs do sistema, cujos resultados são examinados regularmente.

**ID**: 1213.09ab2System.128 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O provisionamento automático do agente do Log Analytics deve ser habilitado na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorar as vulnerabilidades e ameaças à segurança, a Central de Segurança do Azure coleta dados de suas máquinas virtuais do Azure. Os dados são coletados pelo agente do Log Analytics, anteriormente conhecido como MMA (Microsoft Monitoring Agent), que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace do Log Analytics para análise. É recomendável habilitar o provisionamento automático para implantar automaticamente o agente em todas as VMs do Azure com suporte e nas VMs que forem criadas. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>O monitoramento inclui operações privilegiadas, tentativas de acesso autorizado ou não autorizado, incluindo tentativas de acessar contas desativadas e alertas ou falhas do sistema.

**ID**: 1214.09ab2System.3456 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve coletar os logs de atividades de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Essa política audita o perfil de log do Azure Monitor que não exporta atividades de todas as regiões com suporte do Azure, incluindo global. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Os sistemas de auditoria e monitoramento empregados pela organização dão suporte à redução da auditoria e à geração de relatórios.

**ID**: 1215.09ab2System.7 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente do Log Analytics deve ser instalado nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Essa política audita as máquinas virtuais do Windows ou do Linux nas quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Os sistemas automatizados são usados para examinar as atividades de monitoramento dos sistemas de segurança (por exemplo, IPS/IDS) e os registros do sistema diariamente e identificar e documentar anomalias.

**ID**: 1216.09ab3System.12 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Essa política audita os Conjuntos de Dimensionamento de Máquinas Virtuais do Windows ou do Linux nos quais o agente do Log Analytics não está instalado. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Os alertas são gerados para o pessoal técnico analisar e investigar uma atividade suspeita ou violações suspeitas.

**ID**: 1217.09ab3System.3 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar os computadores Windows nos quais o agente do Log Analytics não esteja conectado conforme o esperado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o agente não estiver instalado e nem se ele estiver instalado, mas o objeto COM AgentConfigManager.MgmtSvcCfg retornar que ele está registrado em um workspace diferente da ID especificada no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>O sistema de informações pode processar automaticamente registros de auditoria para eventos de interesse com base em critérios selecionáveis.

**ID**: 1219.09ab3System.10 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de log do Azure Monitor deve coletar logs para as categorias "gravar", "excluir" e "ação"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Essa política garante que um perfil de log colete logs para as categorias "gravar", "excluir" e "ação" |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>O monitoramento inclui comunicações de entrada e saída e monitoramento de integridade de arquivo.

**ID**: 1220.09ab3System.56 – 09.ab **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O provisionamento automático do agente do Log Analytics deve ser habilitado na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorar as vulnerabilidades e ameaças à segurança, a Central de Segurança do Azure coleta dados de suas máquinas virtuais do Azure. Os dados são coletados pelo agente do Log Analytics, anteriormente conhecido como MMA (Microsoft Monitoring Agent), que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace do Log Analytics para análise. É recomendável habilitar o provisionamento automático para implantar automaticamente o agente em todas as VMs do Azure com suporte e nas VMs que forem criadas. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Logs de administrador e operador

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>A organização garante que o registro em log apropriado esteja habilitado para auditar as atividades do administrador e revisa os logs do operador e do administrador do sistema regularmente.

**ID**: 1270.09ad1System.12 – 09.ad **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Um sistema de detecção de intrusões gerenciado fora do controle de administradores do sistema e da rede é usado para monitorar as atividades de administração do sistema e da rede para fins de conformidade.

**ID**: 1271.09ad1System.1 – 09.ad **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um alerta do log de atividades deve existir para Operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Essa política audita Operações administrativas específicas sem alertas do log de atividades configurados. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Diferenciação de tarefas

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>A separação de tarefas é usada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas.

**ID**: 1229.09c1Organizational.1 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular das ações que os usuários podem executar, use o RBAC (controle de acesso baseado em função) para gerenciar permissões nos clusters do Serviço de Kubernetes e configurar políticas de autorização relevantes. |Audit, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Nenhuma pessoa pode acessar, modificar ou usar sistemas de informações sem autorização ou detecção.

**ID**: 1230.09c2Organizational.1 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções internas, como 'Proprietário, Contribuidor, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>O acesso de indivíduos responsáveis por administrar &nbsp;os controles de acesso é limitado ao mínimo necessário, com base na função e nas responsabilidades de cada usuário, e esses indivíduos não podem acessar as funções de auditoria relacionadas a esses controles.

**ID**: 1232.09c3Organizational.12 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Atribuição de Direitos do Usuário'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Atribuição de Direitos do Usuário' para permitir o logon local, o RDP, o acesso na rede e muitas outras atividades do usuário. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>As atividades de auditoria de segurança são independentes.

**ID**: 1276.09c2Organizational.2 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Essa política garante que não exista nenhuma função personalizada de proprietário de assinatura. |Audit, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>A inicialização de um evento é separada da autorização dele para reduzir a possibilidade de colusão.

**ID**: 1277.09c2Organizational.4 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Controle de Conta de Usuário'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Opções de Segurança – Controle de Conta de Usuário' para o modo de administradores, o comportamento da solicitação de elevação e a virtualização de falhas de gravação de arquivo e no Registro. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>A organização identifica os direitos que exigem separação e define as autorizações de acesso do sistema de informações para dar suporte à separação de tarefas; as tarefas incompatíveis são separadas por vários usuários para minimizar a oportunidade de uso indevido ou fraude.

**ID**: 1278.09c2Organizational.56 – 09.c **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Essa política garante que não exista nenhuma função personalizada de proprietário de assinatura. |Audit, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Controles contra código mal-intencionado

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Antivírus e anti-spyware estão instalados, operando e atualizados em todos os dispositivos de usuário final para realizar verificações periódicas dos sistemas para identificar e remover software não autorizado. Os ambientes de servidor para os quais o desenvolvedor de software para servidores recomenda especificamente não instalar software antivírus e anti-spyware com base em host podem atender ao requisito por meio de uma solução NBMD (detecção de malware baseada em rede).

**ID**: 0201.09j1Organizational.124 – 09.j **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Essa política implanta uma extensão IaaSAntimalware da Microsoft com uma configuração padrão quando uma VM não está configurada com a extensão antimalware. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Faça a auditoria da existência de uma solução de proteção de ponto de extremidade e da sua integridade nos seus conjuntos de dimensionamento de máquinas virtuais, para protegê-los contra ameaças e vulnerabilidades. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[O Microsoft Antimalware para o Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Essa política audita qualquer máquina virtual do Windows não configurada com a atualização automática das assinaturas de proteção Microsoft Antimalware. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Monitorar o Endpoint Protection ausente na Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[As atualizações do sistema devem ser instaladas em suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Backup

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Cópias de backup de informações e software são feitas, e testes da mídia e dos procedimentos de restauração são executados regularmente em intervalos apropriados.

**ID**: 1616.09l1Organizational.16 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Esta política audita qualquer Banco de Dados SQL do Azure em que a opção de backup com redundância geográfica de longo prazo não está habilitada. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Uma definição formal do nível de backup necessário para cada sistema é definida e documentada, incluindo como cada sistema será restaurado, o escopo dos dados a serem configurados, a frequência de geração de imagens e a duração da retenção com base em requisitos contratuais, legais, regulatórios e comerciais relevantes.

**ID**: 1617.09l1Organizational.23 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |O Banco de Dados do Azure para MySQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Os backups são armazenados em uma localização fisicamente remota e segura, a uma distância suficiente para torná-los razoavelmente imunes contra danos aos dados no site primário, e os controles físicos e ambientais razoáveis estão em vigor para garantir a proteção na localização remoto.

**ID**: 1618.09l1Organizational.45 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |O Banco de Dados do Azure para PostgreSQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Os registros de inventário das cópias de backup, incluindo o conteúdo e a localização atual, são mantidos.

**ID**: 1619.09l1Organizational.7 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |O Banco de Dados do Azure para MariaDB permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Quando o serviço de backup é fornecido por terceiros, o SLA inclui as proteções detalhadas para controlar a confidencialidade, a integridade e a disponibilidade das informações de backup.

**ID**: 1620.09l1Organizational.8 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garanta a proteção de suas Máquinas Virtuais do Azure habilitando o Backup do Azure. O Backup do Azure é uma solução de proteção de dados segura e econômica para o Azure. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Ferramentas automatizadas são usadas para acompanhar todos os backups.

**ID**: 1621.09l2Organizational.1 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Esta política audita qualquer Banco de Dados SQL do Azure em que a opção de backup com redundância geográfica de longo prazo não está habilitada. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>A integridade e a segurança das cópias de backup são mantidas para garantir a disponibilidade futura, e todos os possíveis problemas de acessibilidade com as cópias de backup são identificados e atenuados no caso de um desastre em toda a área.

**ID**: 1622.09l2Organizational.23 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |O Banco de Dados do Azure para MySQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>As informações sigilosas são copiadas em backup em um formato criptografado para garantir a confidencialidade.

**ID**: 1623.09l2Organizational.4 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |O Banco de Dados do Azure para PostgreSQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>A organização executa backups incrementais ou diferenciais diários e completos semanalmente para separar a mídia.

**ID**: 1624.09l3Organizational.12 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |O Banco de Dados do Azure para MariaDB permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Três (3) gerações de backups (completo, além de todos os backups incrementais ou diferenciais relacionados) são armazenados fora do site, e os backups no site e fora dele são registrados com nome, data, hora e ação.

**ID**: 1625.09l3Organizational.34 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garanta a proteção de suas Máquinas Virtuais do Azure habilitando o Backup do Azure. O Backup do Azure é uma solução de proteção de dados segura e econômica para o Azure. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>A organização garante que uma cópia atual e recuperável das informações sigilosas esteja disponível antes da movimentação de servidores.

**ID**: 1626.09l3Organizational.5 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |O Banco de Dados do Azure para PostgreSQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>A organização testa as informações de backup seguindo cada backup para verificar a confiabilidade da mídia e a integridade das informações, pelo menos, anualmente.

**ID**: 1627.09l3Organizational.6 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |O Banco de Dados do Azure para MariaDB permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>As funções e as responsabilidades dos membros da força de trabalho no processo de backup de dados são identificadas e comunicadas à força de trabalho; em particular, os usuários BYOD (Traga seu próprio dispositivo) devem executar backups de dados organizacionais e/ou do cliente nos próprios dispositivos.

**ID**: 1699.09l1Organizational.10 – 09.l **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garanta a proteção de suas Máquinas Virtuais do Azure habilitando o Backup do Azure. O Backup do Azure é uma solução de proteção de dados segura e econômica para o Azure. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Controles de rede

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>A organização monitora todo o acesso sem fio autorizado e não autorizado ao sistema de informações e proíbe a instalação de WAPs (pontos de acesso sem fio), a menos que explicitamente autorizado por escrito pelo CIO ou pelo representante designado dele.

**ID**: 0858.09m1Organizational.4 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todas as portas de rede devem ser restritas nos grupos de segurança de rede associados à sua máquina virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |A Central de Segurança do Azure identificou algumas das regras de entrada de seus grupos de segurança de rede como permissivas demais. As regras de entrada não devem permitir o acesso por meio de intervalos "Qualquer" ou "Internet". Isso tem o potencial de tornar seus recursos alvos de invasores. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Os computadores Windows devem atender aos requisitos de 'Propriedades do Firewall do Windows'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Propriedades do Firewall do Windows' para estado do firewall, conexões, gerenciamento de regras e notificações. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>A organização garante a segurança das informações nas redes, a disponibilidade dos serviços de rede e dos serviços de informações usando a rede e a proteção dos serviços conectados contra o acesso não autorizado.

**ID**: 0859.09m1Organizational.78 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>A organização gerencia formalmente os equipamentos na rede, incluindo equipamentos em áreas de usuário.

**ID**: 0860.09m1Organizational.9 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Implantar configurações de diagnóstico para Grupos de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Essa política implanta automaticamente configurações de diagnóstico em grupos de segurança de rede. Uma conta de armazenamento com o nome "{storagePrefixParameter} {NSGLocation}" será criada automaticamente. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[O Barramento de Serviço deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Essa política audita os Barramentos de Serviço que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Para identificar e autenticar dispositivos em redes locais e/ou de longa distância, incluindo redes sem fio, &nbsp;o sistema de informações usa uma (i) solução de informações conhecidas compartilhadas ou (ii) uma solução de autenticação organizacional, cuja seleção e força exatas dependem da categorização de segurança do sistema de informações.

**ID**: 0861.09m2Organizational.67 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Essa política audita os Serviços de Aplicativo que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Acesso à Rede'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Opções de Segurança – Acesso à Rede' para incluir o acesso a usuários anônimos, contas locais e acesso remoto ao Registro. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>A organização garante que os sistemas de informações protejam a confidencialidade e a integridade das informações transmitidas, incluindo durante a preparação para transmissão e durante o recebimento.

**ID**: 0862.09m2Organizational.8 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O SQL Server deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Essa política audita os SQL Servers que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>A organização cria uma configuração de firewall que restringe as conexões entre redes não confiáveis e os componentes do sistema no ambiente das informações sigilosas; além disso, todas as alterações na configuração do firewall são atualizadas no diagrama de rede.

**ID**: 0863.09m2Organizational.910 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Essa política audita os Hubs de Eventos que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>As diretrizes de implementação e as restrições de uso são formalmente definidas para VoIP, incluindo a autorização e o monitoramento do serviço.

**ID**: 0864.09m2Organizational.12 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) Microsoft Azure Cosmos DB |Essa política audita os Cosmos DB que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>A organização (i) autoriza conexões do sistema de informações com outros sistemas de informações fora da organização por meio do uso de contratos de segurança de interconexão ou outro contrato formal; (ii) documenta cada conexão, as características da interface, os requisitos de segurança e a natureza das informações comunicadas; (iii) emprega uma política "negar tudo", "permitir por exceção" para permitir conexões do sistema de informações com outros sistemas de informações fora da organização; e (iv) aplica uma regra "negar por padrão" que descarta todo o tráfego por meio de firewalls baseados em host ou ferramentas de filtragem de porta nos pontos de extremidade (estações de trabalho, servidores etc.), exceto os serviços e as portas com permissão explícita.

**ID**: 0865.09m2Organizational.13 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Key Vault deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Essa política audita os Key Vaults que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>A organização descreve os grupos, as funções e as responsabilidades pelo gerenciamento lógico dos componentes de rede e garante a coordenação dos elementos da infraestrutura de rede e a consistência neles.

**ID**: 0866.09m3Organizational.1516 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem restringir o acesso da rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso da rede às contas de armazenamento deve ser restrito. Configure as regras de rede de tal forma que somente os aplicativos das redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da Internet, o acesso pode ser permitido para o tráfego de redes virtuais específicas do Azure ou para intervalos de endereços IP públicos da Internet |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Os pontos de acesso sem fio são colocados em áreas seguras e são desligados quando não estão em uso (por exemplo, à noite e nos fins de semana).

**ID**: 0867.09m3Organizational.17 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As Contas de Armazenamento devem usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Essa política audita as Contas de Armazenamento que não estão configuradas para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>A organização cria uma configuração de firewall para restringir o tráfego de entrada e saída a ela que é necessário para o ambiente de dados sigilosos.

**ID**: 0868.09m3Organizational.18 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Os arquivos de configuração do roteador são protegidos e sincronizados.

**ID**: 0869.09m3Organizational.19 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>O acesso a todos os proxies é negado, exceto para os hosts, as portas e os serviços explicitamente necessários.

**ID**: 0870.09m3Organizational.20 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Os servidores DNS autoritativos são separados em funções internas e externas.

**ID**: 0871.09m3Organizational.22 – 09.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registro de Contêiner deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita os Registros de Contêiner que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Segurança de Serviços de Rede

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Os serviços acordados fornecidos por um provedor/gerente de serviços de rede são formalmente gerenciados e monitorados para garantir que sejam fornecidos com segurança.

**ID**: 0835.09n1Organizational.1 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de coleta dos dados de tráfego de rede deve ser instalado nas máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |A Central de Segurança usa o Microsoft Dependency Agent para coletar dados de tráfego de rede de suas máquinas virtuais do Azure para habilitar recursos avançados de proteção de rede, como visualização de tráfego no mapa de rede, recomendações de proteção de rede e ameaças de rede específicas. |AuditIfNotExists, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Use o novo Azure Resource Manager para suas máquinas virtuais a fim de fornecer melhorias de segurança como: RBAC (controle de acesso) mais forte, melhor auditoria, implantação e governança baseadas no Azure Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves para segredos, autenticação baseada no Azure AD e suporte para marcas e grupos de recursos visando facilitar o gerenciamento da segurança |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>A organização autoriza e documenta formalmente as características de cada conexão em um sistema de informações para outros sistemas de informações fora da organização.

**ID**: 0836.09.n2Organizational.1 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de coleta de dados do tráfego de rede deve ser instalado nas máquinas virtuais do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |A Central de Segurança usa o Microsoft Dependency Agent para coletar dados de tráfego de rede de suas máquinas virtuais do Azure para habilitar recursos avançados de proteção de rede, como visualização de tráfego no mapa de rede, recomendações de proteção de rede e ameaças de rede específicas. |AuditIfNotExists, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Contratos formais com provedores de sistema de informações externos incluem obrigações específicas de segurança e privacidade.

**ID**: 0837.09.n2Organizational.2 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>A organização examina e atualiza os contratos de segurança de interconexão continuamente verificando a imposição de requisitos de segurança.

**ID**: 0885.09n2Organizational.3 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de coleta de dados do tráfego de rede deve ser instalado nas máquinas virtuais do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |A Central de Segurança usa o Microsoft Dependency Agent para coletar dados de tráfego de rede de suas máquinas virtuais do Azure para habilitar recursos avançados de proteção de rede, como visualização de tráfego no mapa de rede, recomendações de proteção de rede e ameaças de rede específicas. |AuditIfNotExists, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>A organização emprega e documenta, em um contrato formal ou em outro documento, i) "permitir todos", "negar por exceção" ou ii) "negar tudo", "permitir por exceção" (preferencial), política para permitir que sistemas de informações específicos se conectem a sistemas de informações externos.

**ID**: 0886.09n2Organizational.4 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>A organização exige provedores de serviços externos/terceirizados para identificar as funções, as portas e os protocolos específicos usados na provisão dos serviços externos/terceirizados.

**ID**: 0887.09n2Organizational.5 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de coleta dos dados de tráfego de rede deve ser instalado nas máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |A Central de Segurança usa o Microsoft Dependency Agent para coletar dados de tráfego de rede de suas máquinas virtuais do Azure para habilitar recursos avançados de proteção de rede, como visualização de tráfego no mapa de rede, recomendações de proteção de rede e ameaças de rede específicas. |AuditIfNotExists, desabilitado |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>O contrato com o provedor de serviços externo/terceirizado inclui a especificação de que o provedor de serviços é responsável pela proteção das informações sigilosas compartilhadas.

**ID**: 0888.09n2Organizational.6 – 09.n **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Gerenciamento de mídia removível

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>A organização, com base no nível de classificação de dados, registra mídias (incluindo laptops) antes do uso, coloca restrições cabíveis na maneira como essas mídias devem ser usadas e fornece um nível apropriado de proteção física e lógica (incluindo criptografia) para mídia que contenha informações sigilosas até que seja corretamente destruída ou limpa.

**ID**: 0301.09o1Organizational.123 – 09.o **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Transparent Data Encryption em bancos de dados SQL deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>A organização protege e controla a mídia que contém informações confidenciais durante o transporte fora de áreas controladas.

**ID**: 0302.09o2Organizational.1 – 09.o **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A criptografia de disco deve ser aplicada em Máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem uma criptografia de disco habilitada serão monitoradas pela Central de Segurança do Azure como recomendações. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Mídia digital e não digital que exige o uso restrito e as garantias específicas usadas para restringir o uso delas são identificadas.

**ID**: 0303.09o2Organizational.2 – 09.o **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os discos desconectados devem ser criptografados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Essa política audita qualquer disco desconectado sem a criptografia habilitada. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>A organização restringe o uso de mídia removível gravável e mídia removível de propriedade pessoal em sistemas organizacionais.

**ID**: 0304.09o3Organizational.1 – 09.o **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Exigir a criptografia em contas do Data Lake Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Essa política garantirá que a criptografia esteja habilitada em todas as contas do Data Lake Storage |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[As instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a TDE (Transparent Data Encryption) com chave própria proporciona maior transparência e controle sobre o protetor de TDE, mais segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. Essa recomendação se aplica a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, desabilitado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os SQL Servers devem usar chaves gerenciadas pelo cliente para criptografar dados inativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a TDE (Transparent Data Encryption) com sua chave proporciona maior transparência e controle sobre o protetor de TDE, mais segurança com um serviço externo com suporte a HSM e promoção de separação de tarefas. Essa recomendação se aplica a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, desabilitado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Procedimentos e políticas de troca de informações

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Os provedores de serviço de nuvem usam uma plataforma de virtualização reconhecida pela indústria e formatos de virtualização padrão (por exemplo, Open Virtualization Format, OVF) para ajudar a garantir a interoperabilidade e mantém documentadas as alterações personalizadas feitas em qualquer hipervisor em uso e todos os ganchos de virtualização específicos da solução disponíveis para revisão do cliente.

**ID**: 0662.09sCSPOrganizational.2 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que o aplicativo Web tenha a opção "Certificados de Cliente (Certificados de cliente de entrada)" definida como "Ativado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que o aplicativo solicite um certificado para solicitações recebidas. Somente clientes que possuem um certificado válido poderão acessar o aplicativo. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>A organização aborda formalmente várias proteções antes de permitir o uso de sistemas de informações para troca de informações.

**ID**: 0901.09s1Organizational.1 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acessem seus aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo Web. Permitir que apenas os domínios necessários interajam com seu aplicativo Web. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>O acesso remoto (externo) aos ativos de informações da organização e o acesso aos ativos de informações externos (sobre os quais a organização não tem controle) são baseados em termos e condições claramente definidos.

**ID**: 0902.09s2Organizational.13 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo de funções. Permitir que apenas os domínios necessários interajam com seu aplicativo de funções. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>A organização estabelece os termos e condições, consistentes com qualquer relação de confiança estabelecida com outras organizações que detêm, operam e/ou mantêm sistemas de informações externos, permitindo que indivíduos autorizados (i) acessem o sistema de informações por meio de sistemas de informações externos; e (ii) processem, armazenem ou transmitam informações controladas pela organização usando sistemas de informações externos.

**ID**: 0911.09s1Organizational.2 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo de API. Permitir que apenas os domínios necessários interajam com seu aplicativo de API. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>A criptografia é usada para proteger a confidencialidade e a integridade das sessões de acesso remoto à rede interna e aos sistemas externos.

**ID**: 0912.09s1Organizational.4 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desativada para aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A depuração remota exige que as portas de entrada estejam abertas em um aplicativo Web. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Protocolos de criptografia forte são usados para proteger informações sigilosas durante a transmissão em redes públicas menos confiáveis/abertas.

**ID**: 0913.09s1Organizational.5 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desativada para o aplicativos de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de funções. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>A organização garante que os requisitos de proteção da comunicação, incluindo a segurança das trocas de informações, estejam sujeitos a auditorias de conformidade e desenvolvimento de políticas.

**ID**: 0914.09s1Organizational.6 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desligada para aplicativos de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de API. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>A organização limita o uso de mídias de armazenamento portátil controlados por indivíduos autorizados em sistemas de informações externas.

**ID**: 0915.09s2Organizational.2 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que o aplicativo Web tenha a opção "Certificados de Cliente (Certificados de cliente de entrada)" definida como "Ativado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que o aplicativo solicite um certificado para solicitações recebidas. Somente clientes que possuem um certificado válido poderão acessar o aplicativo. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>O sistema de informações proíbe a ativação remota de dispositivos de computação colaborativos e fornece uma indicação explícita de uso para usuários fisicamente presentes nos dispositivos.

**ID**: 0916.09s2Organizational.4 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acessem seus aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo Web. Permitir que apenas os domínios necessários interajam com seu aplicativo Web. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Os provedores de serviços de nuvem usam protocolos de rede padronizados (por exemplo, texto criptografado e autenticado) para a importação e a exportação de dados e para gerenciar o serviço e disponibilizam um documento para os consumidores (locatários) detalhando os padrões relevantes de interoperabilidade e portabilidade envolvidos.

**ID**: 0960.09sCSPOrganizational.1 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo de funções. Permitir que apenas os domínios necessários interajam com seu aplicativo de funções. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Os funcionários são treinados adequadamente em princípios e práticas líderes para todos os tipos de troca de informações (orais, escritas e eletrônicas).

**ID**: 1325.09s1Organizational.3 – 09.s **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A depuração remota deve ser desativada para o aplicativos de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de funções. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Transações online

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Os dados envolvidos em comércio eletrônico e transações online são verificados para determinar se eles contêm informações sigilosas.

**ID**: 0943.09y1Organizational.1 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Os protocolos usados para comunicação entre todas as partes envolvidas são protegidos por meio de técnicas de criptografia (por exemplo, SSL).

**ID**: 0945.09y1Organizational.3 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar os computadores Windows que não contêm os certificados especificados na Raiz Confiável](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o repositório de certificados Raiz Confiável do computador (Cert:\LocalMachine\Root) não contiver um ou mais certificados listados pelo parâmetro de política. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>A organização exige o uso de criptografia entre cada uma das partes envolvidas na transação e o uso de assinaturas eletrônicas pelas partes.

**ID**: 0946.09y2Organizational.14 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>A organização garante que o armazenamento dos detalhes da transação esteja localizado fora de qualquer ambiente publicamente acessível (por exemplo, em uma plataforma de armazenamento existente na intranet da organização) e não seja mantido nem exposto em um meio de armazenamento diretamente acessível pela Internet.

**ID**: 0947.09y2Organizational.2 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |O Banco de Dados do Azure para PostgreSQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para PostgreSQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Quando uma autoridade confiável é usada (por exemplo, para a emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e incorporada a todo o processo de gerenciamento de certificados/assinaturas de ponta a ponta.

**ID**: 0948.09y2Organizational.3 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|['Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Essa configuração impõe que o SSL sempre esteja habilitado para acessar seu servidor de banco de dados. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Os protocolos usados para comunicações são aprimorados para resolver qualquer nova vulnerabilidade, e as versões atualizadas dos protocolos são adotadas assim que possível.

**ID**: 0949.09y2Organizational.5 – 09.y **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A última versão do TLS deve ser usada no aplicativo Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Atualizar a versão do TLS para a mais recente |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Controle de software operacional

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Somente os administradores autorizados têm permissão para implementar atualizações aprovadas em programas de software, aplicativos e bibliotecas de programas, com base nos requisitos de negócios e nas implicações de segurança da versão.

**ID**: 0605.10h1System.12 – 10.h **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Auditoria'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Opções de Segurança – Auditoria' para impor a subcategoria da política de auditoria e o desligamento se não for possível registrar em log as auditorias de segurança. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Gerenciamento de Contas'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Gerenciamento de Contas' para auditoria de aplicativo, segurança e gerenciamento do grupo de usuários e outros eventos de gerenciamento. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Os aplicativos e os sistemas operacionais são testados com êxito quanto à usabilidade, à segurança e ao impacto antes da produção.

**ID**: 0606.10h2System.1 – 10.h **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Audite vulnerabilidades na configuração de segurança em computadores com o Docker instalado e exiba como recomendações na Central de Segurança do Azure. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>A organização usa o programa de controle de configuração dela para manter o controle de todo o software implementado e a documentação do sistema dele e versões anteriores de arquivo do software implementado e da documentação do sistema associada.

**ID**: 0607.10h2System.23 – 10.h **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Procedimentos de controle de alterações

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Os gerentes responsáveis por sistemas de aplicativos também são responsáveis pelo controle estrito (segurança) do ambiente de suporte ou projeto e garantem que todas as alterações do sistema propostas foram examinadas para verificar se elas não comprometem a segurança do sistema ou do ambiente operacional.

**ID**: 0635.10k1Organizational.12 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>A organização aborda formalmente a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre as entidades organizacionais e a conformidade do gerenciamento de configuração (por exemplo, por meio de políticas, padrões, processos).

**ID**: 0636.10k2Organizational.1 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>A organização desenvolveu, documentou e implementou um plano de gerenciamento de configuração para o sistema de informações.

**ID**: 0637.10k2Organizational.2 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>As alterações são formalmente controladas, documentadas e impostas a fim de minimizar situações de sistemas de informação corrompidos.

**ID**: 0638.10k2Organizational.34569 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>As listas de verificação de instalação e as verificações de vulnerabilidade são usadas para validar a configuração de servidores, estações de trabalho, dispositivos e aparelhos e garantir que a configuração atenda aos padrões mínimos.

**ID**: 0639.10k2Organizational.78 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>Quando o desenvolvimento é terceirizado, os procedimentos de controle de alterações usados para lidar com a segurança são incluídos nos contratos e, especificamente, exigem que o desenvolvedor acompanhe as falhas de segurança e a resolução das falhas dentro do sistema, do componente ou do serviço e relate as descobertas à equipe ou às funções definidas pela organização.

**ID**: 0640.10k2Organizational.1012 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>A organização não usa atualizações automatizadas em sistemas críticos.

**ID**: 0641.10k2Organizational.11 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>A organização desenvolve, documenta e mantém sob controle uma configuração de linha de base atual do sistema de informações, além de examinar e atualizar a linha de base, conforme necessário.

**ID**: 0642.10k3Organizational.12 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>A organização (i) estabelece e documenta definições de configuração obrigatórias para produtos de tecnologia da informação empregados no sistema de informações usando as linhas de base de configuração de segurança mais recentes; (ii) identifica, documenta e aprova exceções das definições de configuração estabelecidas obrigatórias para componentes individuais com base em requisitos operacionais explícitos; e (iii) monitora e controla as alterações nas definições de configuração de acordo com as políticas e os procedimentos organizacionais.

**ID**: 0643.10k3Organizational.3 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>A organização emprega mecanismos automatizados para (i) gerenciar, aplicar e verificar de modo central as definições de configuração; (ii) responder a alterações não autorizadas nas definições de configuração relacionadas à segurança da rede e do sistema; e (iii) impor restrições de acesso e auditoria das ações de imposição.

**ID**: 0644.10k3Organizational.4 – 10.k **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Políticas de Auditoria do Sistema – Acompanhamento Detalhado' para auditoria de DPAPI, criação/encerramento de processos, eventos RPC e atividade PNP. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Controle de vulnerabilidades técnicas

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>As vulnerabilidades técnicas são identificadas, avaliadas quanto aos riscos e corrigidas em tempo hábil.

**ID**: 0709.10m1Organizational.1 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita as máquinas virtuais para detectar se elas estão executando uma solução de avaliação de vulnerabilidade compatível. Um componente principal de cada programa de segurança e risco cibernético é a identificação e a análise das vulnerabilidades. O tipo de preço padrão da Central de Segurança do Azure inclui a verificação de vulnerabilidade para as máquinas virtuais sem custo adicional. Além disso, a Central de Segurança pode implantar essa ferramenta automaticamente para você. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Audite vulnerabilidades na configuração de segurança em computadores com o Docker instalado e exiba como recomendações na Central de Segurança do Azure. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[As vulnerabilidades nos bancos de dados SQL devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[A avaliação de vulnerabilidades deve estar habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Audite cada Instância Gerenciada de SQL que não tem verificações recorrentes de avaliação de vulnerabilidade habilitadas. A avaliação de vulnerabilidades pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Audite servidores SQL do Azure que não têm verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Servidor de Rede da Microsoft'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Opções de Segurança – Servidor de Rede da Microsoft' para desabilitar o servidor SMB v1. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Existe um padrão de configuração reforçado para todos os componentes de sistema e de rede.

**ID**: 0710.10m2Organizational.1 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A avaliação de vulnerabilidades deve estar habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Audite cada Instância Gerenciada de SQL que não tem verificações recorrentes de avaliação de vulnerabilidade habilitadas. A avaliação de vulnerabilidades pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Um programa de gerenciamento de vulnerabilidades técnicas está em vigor para monitorar, avaliar, classificar e corrigir as vulnerabilidades identificadas nos sistemas.

**ID**: 0711.10m2Organizational.23 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita as máquinas virtuais para detectar se elas estão executando uma solução de avaliação de vulnerabilidade compatível. Um componente principal de cada programa de segurança e risco cibernético é a identificação e a análise das vulnerabilidades. O tipo de preço padrão da Central de Segurança do Azure inclui a verificação de vulnerabilidade para as máquinas virtuais sem custo adicional. Além disso, a Central de Segurança pode implantar essa ferramenta automaticamente para você. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Os patches são testados e avaliados antes de serem instalados.

**ID**: 0713.10m2Organizational.5 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>O programa de gerenciamento de vulnerabilidades técnicas é avaliado trimestralmente.

**ID**: 0714.10m2Organizational.7 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Os sistemas são protegidos de maneira adequada (por exemplo, configurados apenas com serviços, portas e protocolos seguros e necessários habilitados).

**ID**: 0715.10m2Organizational.8 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Audite vulnerabilidades na configuração de segurança em computadores com o Docker instalado e exiba como recomendações na Central de Segurança do Azure. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>A organização realiza uma revisão de postura de segurança empresarial conforme necessário, mas com uma frequência mínima de uma vez a cada 365 (trezentos e sessenta e cinco) dias, de acordo com os procedimentos organizacionais IS.

**ID**: 0716.10m3Organizational.1 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nos bancos de dados SQL devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>As ferramentas de verificação de vulnerabilidade incluem a capacidade de atualizar imediatamente as vulnerabilidades do sistema de informações verificadas.

**ID**: 0717.10m3Organizational.2 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>A organização examina se há vulnerabilidades no sistema de informações e nos aplicativos hospedados para determinar o estado da correção de falhas mensalmente (automaticamente) e novamente (manual ou automaticamente) quando novas vulnerabilidades que potencialmente afetam os sistemas e os ambientes em rede são identificadas e relatadas.

**ID**: 0718.10m3Organizational.34 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>A organização atualiza a lista de vulnerabilidades verificadas do sistema de informações a cada 30 (trinta) dias ou quando novas vulnerabilidades são identificadas e relatadas.

**ID**: 0719.10m3Organizational.5 – 10.m **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A avaliação de vulnerabilidades deve estar habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Audite cada Instância Gerenciada de SQL que não tem verificações recorrentes de avaliação de vulnerabilidade habilitadas. A avaliação de vulnerabilidades pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Continuidade dos negócios e avaliação de riscos

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>A organização identifica os processos de negócios críticos que exigem continuidade dos negócios.

**ID**: 1634.12b1Organizational.1 – 12.b **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem a recuperação de desastre configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais sem a recuperação de desastre configurada. Para saber mais sobre recuperação de desastre, acesse [https://aka.ms/asr-doc](https://aka.ms/asr-doc). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Os aspectos de segurança da informação da continuidade dos negócios são (i) baseados na identificação de eventos (ou na sequência de eventos) que podem causar interrupções nos processos de negócios críticos da organização (por exemplo, falha no equipamento, erros humanos, roubo, incêndio, desastres naturais, atos de terrorismo); (ii) seguidos por uma avaliação de risco para determinar a probabilidade e o impacto dessas interrupções, em termos de tempo, escala de dano e período de recuperação; (iii) baseados nos resultados da avaliação de risco, uma estratégia de continuidade dos negócios é desenvolvida para identificar a abordagem geral à continuidade dos negócios; e (iv) depois que essa estratégia tiver sido criada, o endosso será fornecido pela gerência, e um plano será criado e endossado para implementar essa estratégia.

**ID**: 1635.12b1Organizational.2 – 12.b **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O HSM Gerenciado do Azure Key Vault deve ter a proteção contra limpeza habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |A exclusão mal-intencionada de um HSM Gerenciado do Azure Key Vault pode levar à perda permanente de dados. Um funcionário mal-intencionado na sua organização pode potencialmente excluir e limpar o HSM Gerenciado do Azure Key Vault. A proteção contra limpeza protege você contra ataques internos impondo um período de retenção obrigatório para o HSM Gerenciado do Azure Key Vault de exclusão temporária. Ninguém dentro da sua organização nem a Microsoft poderá limpar o HSM Gerenciado do Azure Key Vault durante o período de retenção de exclusão temporária. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Os cofres de chaves devem ter a proteção contra limpeza habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A exclusão mal-intencionada de um cofre de chaves pode levar à perda permanente de dados. Um funcionário mal-intencionado em sua organização pode potencialmente excluir e limpar os cofres de chaves. A proteção de limpeza protege você contra ataques de invasores impondo um período de retenção obrigatório para cofres de chaves de exclusão temporária. Ninguém dentro de sua organização nem a Microsoft poderá limpar os cofres de chaves durante o período de retenção de exclusão temporária. |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>A análise de impacto nos negócios é usada para avaliar as consequências de desastres, falhas de segurança, perda de serviço e disponibilidade do serviço.

**ID**: 1637.12b2Organizational.2 – 12.b **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os computadores Windows devem atender aos requisitos de 'Opções de Segurança – Console de recuperação'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Os computadores Windows devem ter as configurações de Política de Grupo especificadas na categoria 'Opções de Segurança – Console de recuperação' para permitir a cópia de disquete e o acesso a todas as unidades e pastas. Essa política requer que os pré-requisitos de Configuração de Convidado tenham sido implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>As avaliações de risco de continuidade dos negócios (i) são realizadas anualmente com envolvimento completo de proprietários de recursos e processos de negócios; (ii) consideram todos os processos de negócios e não são limitadas aos ativos de informações, mas inclui os resultados específicos à segurança da informação; e (iii) identificam, quantificam e priorizam riscos com relação aos principais objetivos de negócios e critérios relevantes à organização, incluindo recursos críticos, impactos de interrupções, tempos de interrupção permitidos e prioridades de recuperação.

**ID**: 1638.12b2Organizational.345 – 12.b **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem a recuperação de desastre configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais sem a recuperação de desastre configurada. Para saber mais sobre recuperação de desastre, acesse [https://aka.ms/asr-doc](https://aka.ms/asr-doc). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

Artigos adicionais sobre o Azure Policy:

- Visão geral da [Conformidade Regulatória](../concepts/regulatory-compliance.md).
- Consulte a [estrutura de definição da iniciativa](../concepts/initiative-definition-structure.md).
- Veja outros exemplos em [Amostras do Azure Policy](./index.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
