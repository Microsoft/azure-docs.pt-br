---
title: Detalhes de Conformidade Regulatória para o NIST SP 800-53 R4
description: Detalhes da iniciativa interna de Conformidade Regulatória do NIST SP 800-53 R4. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 0fe88525bc574219729a7f90fba1bf82803e5854
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505336"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa interna de Conformidade Regulatória do NIST SP 800-53 R4

O artigo a seguir fornece detalhes sobre como a definição da iniciativa interna de Conformidade Regulatória do Azure Policy é mapeada para **domínios de conformidade** e **controles** no NIST SP 800-53 R4.
Para obter mais informações sobre esse padrão de conformidade, confira [NIST SP 800-53 R4](https://nvd.nist.gov/800-53). Para entender a _Propriedade_, confira [Definição de política do Azure Policy](../concepts/definition-structure.md#type) e [Responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).

Os seguintes mapeamentos referem-se aos controles do **NIST SP 800-53 R4**. Use a navegação no lado direito para ir diretamente para um **domínio de conformidade** específico. Muitos dos controles são implementados com uma definição de iniciativa do [Azure Policy](../overview.md). Para examinar a definição da iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**.
Em seguida, encontre e selecione a definição de iniciativa interna de Conformidade Regulatória **NIST SP 800-53 R4**.

Essa iniciativa interna é implantada como parte da [Amostra de blueprint do NIST SP 800-53 R4](../../blueprints/samples/nist-sp-800-53-r4.md).

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../overview.md).
> Essas políticas podem ajudar você a [avaliar a conformidade](../how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias definições de política e não garante que você esteja em conformidade total com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre os domínios de conformidade, os controles e as definições do Azure Policy para este padrão de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json).

## <a name="access-control"></a>Controle de acesso

### <a name="access-control-policy-and-procedures"></a>Procedimentos e Política de Controle de Acesso

**ID**: NIST SP 800-53 R4 AC-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1000 – Política e procedimentos de controle de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Controle Gerenciado da Microsoft 1001 – Política e procedimentos de controle de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Gerenciamento de Conta

**ID**: NIST SP 800-53 R4 AC-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas preteridas devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Contas descontinuadas devem ser removidas de suas assinaturas.  Contas descontinuadas são contas que foram impedidas de fazer login. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Contas descontinuadas com permissões de proprietário devem ser removidas da sua assinatura.  Contas descontinuadas são contas que foram impedidas de fazer login. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[As contas externas com permissões de proprietário devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Contas externas com permissões de proprietário devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[As contas externas com permissões de leitura devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Contas externas com privilégios de leitura devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[As contas externas com permissões de gravação devem ser removidas de sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Contas externas com privilégios de gravação devem ser removidas da sua assinatura para evitar o acesso não monitorado. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Controle Gerenciado da Microsoft 1002 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Controle Gerenciado da Microsoft 1003 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Controle Gerenciado da Microsoft 1004 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Controle Gerenciado da Microsoft 1005 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Controle Gerenciado da Microsoft 1006 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Controle Gerenciado da Microsoft 1007 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Controle Gerenciado da Microsoft 1008 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Controle Gerenciado da Microsoft 1009 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Controle Gerenciado da Microsoft 1010 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Controle Gerenciado da Microsoft 1011 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Controle Gerenciado da Microsoft 1012 – Gerenciamento de conta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Gerenciamento de conta | Gerenciamento de conta de sistema automatizado

**ID**: NIST SP 800-53 R4 AC-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1013 – Gerenciamento de conta \| Gerenciamento de conta de sistema automatizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Gerenciamento de conta | Remoção de contas temporárias / emergenciais

**ID**: NIST SP 800-53 R4 AC-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1014 – Gerenciamento de conta \| Remoção de contas temporárias / de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Gerenciamento de conta | Desabilitar contas inativas

**ID**: NIST SP 800-53 R4 AC-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1015 – Gerenciamento de conta \| Desabilitar contas inativas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Gerenciamento de conta | Ações de auditoria automatizadas

**ID**: NIST SP 800-53 R4 AC-2 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1016 – Gerenciamento de conta \| Ações de auditoria automatizadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Gerenciamento de conta | Inatividade por logout

**ID**: NIST SP 800-53 R4 AC-2 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1017 – Gerenciamento de conta \| Logoff por inatividade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Gerenciamento de conta | Esquemas com base em função

**ID**: NIST SP 800-53 R4 AC-2 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um administrador do Azure Active Directory deve ser provisionado para servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Audite o provisionamento de um administrador do Active Directory Domain Services do Azure para o seu servidor SQL para habilitar a autenticação do AD do Azure. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções internas, como 'Proprietário, Contribuidor, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Controle Gerenciado da Microsoft 1018 – Gerenciamento de conta \| Esquemas baseados em função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Controle Gerenciado da Microsoft 1019 – Gerenciamento de conta \| Esquemas baseados em função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Controle Gerenciado da Microsoft 1020 – Gerenciamento de conta \| Esquemas baseados em função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Auditar o uso da autenticação do cliente somente por meio do Active Directory do Azure no Service Fabric |Audit, Deny, desabilitado |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Gerenciamento de conta | Restrições no uso de contas compartilhadas / grupo

**ID**: NIST SP 800-53 R4 AC-2 (9) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1021 – Gerenciamento de conta \| Restrições de uso de contas compartilhadas / de grupo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Gerenciamento de conta | Encerramento de credencial de conta compartilhada / grupo

**ID**: NIST SP 800-53 R4 AC-2 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1022 – Gerenciamento de conta \| Término de credencial de conta compartilhada / de grupo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Gerenciamento de conta | Condições de uso

**ID**: NIST SP 800-53 R4 AC-2 (11) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1023 – Gerenciamento de conta \| Condições de uso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Gerenciamento de conta | Monitoramento de conta / Uso atípico

**ID**: NIST SP 800-53 R4 AC-2 (12) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Controle Gerenciado da Microsoft 1024 – Gerenciamento de conta \| Monitoramento de conta / Uso atípico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Controle Gerenciado da Microsoft 1025 – Gerenciamento de conta \| Monitoramento de conta / Uso atípico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Gerenciamento de conta | Desativar contas para pessoas físicas de alto risco

**ID**: NIST SP 800-53 R4 AC-2 (13) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1026 – Gerenciamento de conta \| Desabilitar conta para indivíduos de alto risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Regras de acesso

**ID**: NIST SP 800-53 R4 AC-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1027 – Imposição de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Política de fluxo de informações

**ID**: NIST SP 800-53 R4 AC-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acessem seus aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo Web. Permitir que apenas os domínios necessários interajam com seu aplicativo Web. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Controle Gerenciado da Microsoft 1028 – Imposição de fluxo de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Política de fluxo de informações | Filtros de política de segurança

**ID**: NIST SP 800-53 R4 AC-4 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1029 – Imposição de fluxo de informações \| Filtros de política de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Política de fluxo de informações | Separação lógica / física de fluxos de informações

**ID**: NIST SP 800-53 R4 AC-4 (21) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1030 – Imposição de fluxo de informações \| Separação lógica / física de fluxos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>Separação de funções

**ID**: NIST SP 800-53 R4 AC-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Auditar computadores Windows que não têm membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local não contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditar computadores Windows que têm os membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Controle Gerenciado da Microsoft 1031 – Separação de obrigações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Controle Gerenciado da Microsoft 1032 – Separação de obrigações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Controle Gerenciado da Microsoft 1033 – Separação de obrigações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[Deve haver mais de um proprietário atribuído à sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>Privilégio mínimo

**ID**: NIST SP 800-53 R4 AC-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1034 – Privilégios mínimos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>Privilégios mínimos | Autorizar o acesso a funções de segurança

**ID**: NIST SP 800-53 R4 AC-6 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1035 – Privilégios mínimos \| Autorizar o acesso a funções de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Privilégios mínimos | Acesso sem privilégios para funções de risco

**ID**: NIST SP 800-53 R4 AC-6 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1036 – Privilégios mínimos \| Acesso sem privilégios para funções não relacionadas à segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>Privilégios mínimos | Acesso à rede para comandos com privilégios

**ID**: NIST SP 800-53 R4 AC-6 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1037 – Privilégios mínimos \| Acesso à rede para comandos com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>Privilégios mínimos | Contas com privilégios

**ID**: NIST SP 800-53 R4 AC-6 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1038 – Privilégios mínimos \| Contas com privilégio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>Privilégios mínimos | Revisão de privilégios do usuário

**ID**: NIST SP 800-53 R4 AC-6 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de três proprietários deve ser designado para sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de assinaturas para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Auditar computadores Windows que não têm membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local não contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditar computadores Windows que têm os membros especificados no Grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se o Grupo de administradores local contiver um ou mais membros listados no parâmetro de política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Controle Gerenciado da Microsoft 1039 – Privilégios mínimos \| Revisão dos privilégios do usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Controle Gerenciado da Microsoft 1040 – Privilégios mínimos \| Revisão dos privilégios do usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[Deve haver mais de um proprietário atribuído à sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais de um proprietário de assinatura para ter redundância de acesso de administrador. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>Privilégios mínimos | Níveis de privilégio para execução de código

**ID**: NIST SP 800-53 R4 AC-6 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1041 – Privilégios mínimos \| Níveis de privilégio para execução de código](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Privilégios mínimos | Auditoria do uso de funções com privilégios

**ID**: NIST SP 800-53 R4 AC-6 (9) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1042 – Privilégios mínimos \| Auditoria do uso de funções com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Privilégios mínimos | Proibir usuários sem privilégios de executar funções com privilégios

**ID**: NIST SP 800-53 R4 AC-6 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1043 – Privilégios mínimos \| Proibir usuários sem privilégios de executar funções com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Tentativas de logon malsucedidas

**ID**: NIST SP 800-53 R4 AC-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1044 – Tentativas de logon malsucedidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Controle Gerenciado da Microsoft 1045 – Tentativas de logon malsucedidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Tentativas de logon malsucedidas | Limpar / apagar de dispositivo móvel

**ID**: NIST SP 800-53 R4 AC-7 (2) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1046 – Bloqueio de conta automático \| Limpar / apagar dispositivo móvel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Notificação do uso do sistema

**ID**: NIST SP 800-53 R4 AC-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1047 – Notificação de uso do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Controle Gerenciado da Microsoft 1048 – Notificação de uso do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Controle Gerenciado da Microsoft 1049 – Notificação de uso do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Controle de sessões simultâneas

**ID**: NIST SP 800-53 R4 AC-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1050 – Controle de sessão simultânea](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Bloqueio de sessão

**ID**: NIST SP 800-53 R4 AC-11 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1051 – Bloqueio de sessão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Controle Gerenciado da Microsoft 1052 – Bloqueio de sessão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Bloqueio de sessão | Exibir padrão de ocultação

**ID**: NIST SP 800-53 R4 AC-11 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1053 – Bloqueio de sessão \| Exibições de ocultação de padrões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Encerramento da sessão

**ID**: NIST SP 800-53 R4 AC-12 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1054 – Término de sessão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Encerramento da sessão | Logoffs iniciados pelo usuário / Exibição de mensagem

**ID**: NIST SP 800-53 R4 AC-12 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1055 – Término de sessão\| Logoffs iniciados pelo usuário / Exibições de mensagem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Controle Gerenciado da Microsoft 1056 – Término de sessão \| Logoffs iniciados pelo usuário / Exibições de mensagem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Ações permitidas sem identificação ou autenticação

**ID**: NIST SP 800-53 R4 AC-14 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1057 – Ações permitidas sem identificação nem autenticação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Controle Gerenciado da Microsoft 1058 – Ações permitidas sem identificação nem autenticação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Atributos de segurança

**ID**: **Propriedade** do NIST SP 800-53 R4 AC-16: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Acesso remoto

**ID**: NIST SP 800-53 R4 AC-17 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1059 – Acesso remoto](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Controle Gerenciado da Microsoft 1060 – Acesso remoto](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Acesso remoto | Controle / monitoramento automatizado

**ID**: NIST SP 800-53 R4 AC-17 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adicionar identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em máquinas virtuais sem identidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado, mas que não têm identidades gerenciadas. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Adicionar uma identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em VMs com uma identidade atribuída ao usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado e tem pelo menos uma identidade atribuída ao usuário, mas não tem uma identidade gerenciada atribuída ao sistema. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditar os computadores Linux que permitem conexões remotas de contas sem senhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Linux que permitem conexões remotas de contas sem senhas |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Implantar a extensão de Configuração de Convidado do Linux para habilitar atribuições de Configuração de Convidado em VMs do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Essa política implanta a extensão de Configuração de Convidado do Linux em máquinas virtuais do Linux hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Linux é um pré-requisito para todas as atribuições da Configuração de Convidado do Linux e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Linux. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Implantar a extensão de Configuração de Convidado do Windows para habilitar atribuições de Configuração de Convidado em VMs do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Essa política implanta a extensão de Configuração de Convidado do Windows em máquinas virtuais do Windows hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Windows é um pré-requisito para todas as atribuições da Configuração de Convidado do Windows e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Windows. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Controle Gerenciado da Microsoft 1061 – Acesso remoto \| Monitoramento / controle automatizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[A depuração remota deve ser desligada para aplicativos de API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de API. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[A depuração remota deve ser desativada para o aplicativos de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuração remota exige que as portas de entrada estejam abertas em aplicativos de funções. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[A depuração remota deve ser desativada para aplicativos Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A depuração remota exige que as portas de entrada estejam abertas em um aplicativo Web. A depuração remota deve ser desligada. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[As contas de armazenamento devem restringir o acesso da rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso da rede às contas de armazenamento deve ser restrito. Configure as regras de rede de tal forma que somente os aplicativos das redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da Internet, o acesso pode ser permitido para o tráfego de redes virtuais específicas do Azure ou para intervalos de endereços IP públicos da Internet |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Acesso remoto | Proteção de confidencialidade / Integridade usando a criptografia

**ID**: NIST SP 800-53 R4 AC-17 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1062 – Acesso remoto \| Proteção da confidencialidade / integridade usando criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Acesso remoto | Pontos de controle de acesso gerenciado

**ID**: NIST SP 800-53 R4 AC-17 (3) **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1063 – Acesso remoto \| Pontos do Controle de Acesso Gerenciado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Acesso remoto | comandos / acesso com privilégios

**ID**: NIST SP 800-53 R4 AC-17 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1064 – Acesso remoto \| Comandos / acesso privilegiado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Controle Gerenciado da Microsoft 1065 – Acesso remoto \| Comandos / acesso privilegiado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Acesso remoto | Desconectar / desabilitar o acesso

**ID**: NIST SP 800-53 R4 AC-17 (9) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1066 – Acesso remoto \| Desconectar / desabilitar o acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Acesso sem fio

**ID**: NIST SP 800-53 R4 AC-18 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1067 – Acesso sem fio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Controle Gerenciado da Microsoft 1068 – Acesso sem fio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Acesso sem fio | Autenticação e criptografia

**ID**: NIST SP 800-53 R4 AC-18 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1069 – Acesso sem fio \| Autenticação e criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Acesso sem fio | Desabilitar rede sem fio

**ID**: NIST SP 800-53 R4 AC-18 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1070 – Acesso sem fio \| Desabilitar a rede sem fio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Acesso sem fio | Restringir as configurações por usuários

**ID**: NIST SP 800-53 R4 AC-18 (4) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1071 – Acesso sem fio \| Restringir configurações por usuários](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Acesso sem fio | Antenas / níveis de energia de transmissão

**ID**: NIST SP 800-53 R4 AC-18 (5) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1072 – Acesso sem fio \| Níveis de potência de transmissão / antenas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Controle de acesso para dispositivos móveis

**ID**: NIST SP 800-53 R4 AC-19 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1073 – Controle de acesso para dispositivos móveis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Controle Gerenciado da Microsoft 1074 – Controle de acesso para dispositivos móveis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Controle de acesso para dispositivos móveis | total de dispositivo / baseado no contêiner de criptografia

**ID**: NIST SP 800-53 R4 AC-19 (5) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1075 – Controle de acesso para dispositivos móveis \| Criptografia baseada em contêiner / de dispositivo completo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Uso de sistemas de informações externas

**ID**: NIST SP 800-53 R4 AC-20 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1076 – Uso de sistemas de informações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Controle Gerenciado da Microsoft 1077 – Uso de sistemas de informações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Uso de sistemas de informação externas | Limites de uso autorizado

**ID**: NIST SP 800-53 R4 AC-20 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1078 – Uso de sistemas de informações externos \| Limites do uso autorizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Controle Gerenciado da Microsoft 1079 – Uso de sistemas de informações externos \| Limites do uso autorizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>Uso de sistemas de informação externas | Dispositivos de armazenamento portáteis

**ID**: NIST SP 800-53 R4 AC-20 (2) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1080 – Uso de sistemas de informações externos \| Dispositivos de armazenamento portáteis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Compartilhamento de informações

**ID**: NIST SP 800-53 R4 AC-21 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1081 – Compartilhamento de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Controle Gerenciado da Microsoft 1082 – Compartilhamento de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Conteúdo publicamente acessível

**ID**: NIST SP 800-53 R4 AC-22 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1083 – Conteúdo publicamente acessível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Controle Gerenciado da Microsoft 1084 – Conteúdo publicamente acessível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Controle Gerenciado da Microsoft 1085 – Conteúdo publicamente acessível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Controle Gerenciado da Microsoft 1086 – Conteúdo publicamente acessível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |A Microsoft implementa esse controle para Controle de Acesso |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Reconhecimento e Treinamento

### <a name="security-awareness-and-training-policy-and-procedures"></a>Política e procedimentos de reconhecimento e treinamento de segurança

**ID**: NIST SP 800-53 R4 AT-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1087 – Política e procedimentos de conscientização e treinamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Controle Gerenciado da Microsoft 1088 – Política e procedimentos de conscientização e treinamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Treinamento de reconhecimento de segurança

**ID**: NIST SP 800-53 R4 AT-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1089 – Treinamento de conscientização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Controle Gerenciado da Microsoft 1090 – Treinamento de conscientização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Controle Gerenciado da Microsoft 1091 – Treinamento de conscientização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Treinamento de reconhecimento de segurança | Ameaça interna

**ID**: NIST SP 800-53 R4 AT-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1092 – Treinamento de conscientização de segurança \| Ameaça interna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Treinamento de segurança baseada em funções

**ID**: NIST SP 800-53 R4 AT-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1093 – Treinamento de segurança baseada em funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Controle Gerenciado da Microsoft 1094 – Treinamento de segurança baseada em funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Controle Gerenciado da Microsoft 1095 – Treinamento de segurança baseada em funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Treinamento de segurança baseado em funções | Exercícios práticos

**ID**: NIST SP 800-53 R4 AT-3 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1096 – Treinamento de segurança baseada em funções \| Exercícios práticos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Treinamento de segurança baseado em funções | Comunicações suspeitas e comportamento anômalo do sistema

**ID**: NIST SP 800-53 R4 AT-3 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1097 – Treinamento de segurança baseada em funções \| Comunicações suspeitas e comportamento anômalo do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Registros de treinamento de segurança

**ID**: NIST SP 800-53 R4 AT-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1098 – Registros de treinamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Controle Gerenciado da Microsoft 1099 – Registros de treinamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |A Microsoft implementa esse controle de Conscientização e Treinamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Auditoria e Contabilidade

### <a name="audit-and-accountability-policy-and-procedures"></a>Política e Procedimentos de Auditoria e Responsabilidade

**ID**: NIST SP 800-53 R4 AU-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1100 – Política e procedimento de auditoria e responsabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Controle Gerenciado da Microsoft 1101 – Política e procedimento de auditoria e responsabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Eventos de Auditoria

**ID**: NIST SP 800-53 R4 AU-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1102 – Eventos de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Controle Gerenciado da Microsoft 1103 – Eventos de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Controle Gerenciado da Microsoft 1104 – Eventos de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Controle Gerenciado da Microsoft 1105 – Eventos de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Eventos de Auditoria | Revisões e Atualizações

**ID**: NIST SP 800-53 R4 AU-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1106 – Eventos de auditoria \| Revisões e atualizações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>Conteúdo de registros de auditoria

**ID**: NIST SP 800-53 R4 AU-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1107 – Conteúdo dos registros de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>Conteúdo de Registros de Auditoria | Informações de Auditoria Adicionais

**ID**: NIST SP 800-53 R4 AU-3 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1108 – Conteúdo dos registros de auditoria \| Informações de auditoria adicionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Conteúdo de Registros de Auditoria | Gerenciamento Centralizado do Conteúdo do Registro de Auditoria planejada

**ID**: NIST SP 800-53 R4 AU-3 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o workspace do Log Analytics para a VM – Relatar incompatibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Relata as VMs como sem conformidade se elas não estão registrando no log do workspace do Log Analytics especificado na atribuição de política ou de iniciativa. |auditoria |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[O Agente do Log Analytics deverá ser habilitado para imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Relata máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[O agente do Log Analytics deverá ser habilitado em conjuntos de dimensionamento de máquinas virtuais para obter imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Relata os conjuntos de dimensionamento de máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Controle Gerenciado da Microsoft 1109 – Conteúdo dos registros de auditoria \| Gerenciamento centralizado do conteúdo de registro de auditoria planejada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Capacidade de armazenamento de auditoria

**ID**: NIST SP 800-53 R4 AU-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1110 – Capacidade do armazenamento de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Resposta a Falhas de Processamento de Auditoria

**ID**: NIST SP 800-53 R4 AU-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Configuração de diagnóstico de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditar configuração de diagnóstico para tipos de recursos selecionados |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Controle Gerenciado da Microsoft 1111 – Resposta a falhas de processamento de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Controle Gerenciado da Microsoft 1112 – Resposta a falhas de processamento de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Resposta a Falhas de Processamento de Auditoria | Capacidade de Armazenamento de Auditoria

**ID**: NIST SP 800-53 R4 AU-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1113 – Resposta a falhas de processamento de auditoria \| Capacidade de armazenamento de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Resposta a Falhas de Processamento de Auditoria | Alertas em Tempo Real

**ID**: NIST SP 800-53 R4 AU-5 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1114 – Resposta a falhas de processamento de auditoria \| Alertas em tempo real](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Revisão de Auditoria, Análise e Relatórios

**ID**: NIST SP 800-53 R4 AU-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1115 – Revisão, análise e relatórios de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Controle Gerenciado da Microsoft 1116 – Revisão, análise e relatórios de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Revisão de Auditoria, análise e Relatórios | Integração do Processo

**ID**: NIST SP 800-53 R4 AU-6 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1117 – Revisão, análise e relatórios de auditoria \| Integração de processo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Revisão, análise e relatório de auditoria | Correlacionar repositórios de auditoria

**ID**: NIST SP 800-53 R4 AU-6 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1118 – Revisão, análise e relatórios de auditoria \| Repositórios de auditoria correlacionados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Revisão, análise e relatório de auditoria | Revisão e análise central

**ID**: NIST SP 800-53 R4 AU-6 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o workspace do Log Analytics para a VM – Relatar incompatibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Relata as VMs como sem conformidade se elas não estão registrando no log do workspace do Log Analytics especificado na atribuição de política ou de iniciativa. |auditoria |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[O Agente do Log Analytics deverá ser habilitado para imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Relata máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[O agente do Log Analytics deverá ser habilitado em conjuntos de dimensionamento de máquinas virtuais para obter imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Relata os conjuntos de dimensionamento de máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Controle Gerenciado da Microsoft 1119 – Revisão, análise e relatórios de auditoria \| Revisão e análise central](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Revisão, análise e relatório de auditoria | Recursos de integração/verificação e monitoramento

**ID**: NIST SP 800-53 R4 AU-6 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1120 – Revisão, análise e relatórios de auditoria \| Funcionalidades de integração / verificação e monitoramento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Revisão, análise e relatório de auditoria | Correlação com o monitoramento físico

**ID**: NIST SP 800-53 R4 AU-6 (6) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1121 – Revisão, análise e relatórios de auditoria \| Correlação com o monitoramento físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Revisão de Auditoria, análise e Relatórios | Ações Permitidas

**ID**: NIST SP 800-53 R4 AU-6 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1122 – Revisão, análise e relatórios de auditoria \| Ações permitidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Revisão de Auditoria, análise e Relatórios | Ajuste de Nível de Auditoria

**ID**: NIST SP 800-53 R4 AU-6 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1123 – Revisão, análise e relatórios de auditoria \| Ajuste de nível de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Redução de Auditoria e Geração de Relatórios

**ID**: NIST SP 800-53 R4 AU-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1124 – Redução de auditoria e geração de relatórios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Controle Gerenciado da Microsoft 1125 – Redução de auditoria e geração de relatórios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Redução de Auditoria e Geração de Relatórios | Processamento de Automação

**ID**: NIST SP 800-53 R4 AU-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1126 – Redução de auditoria e geração de relatórios \| Processamento automático](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Carimbos de Data/Hora

**ID**: NIST SP 800-53 R4 AU-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1127 – Carimbos de data/hora](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Controle Gerenciado da Microsoft 1128 – Carimbos de data/hora](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Carimbos de Data e Hora | Sincronização com fonte de horário autoritativo

**ID**: NIST SP 800-53 R4 AU-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1129 – Carimbos de data/hora \| Sincronização de fonte de tempo autoritativa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Controle Gerenciado da Microsoft 1130 – Carimbos de data/hora \| Sincronização de fonte de tempo autoritativa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>Proteção de Informações de Auditoria

**ID**: NIST SP 800-53 R4 AU-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1131 – Proteção de informações de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Proteção de informações de auditoria | Backup de auditoria em componentes / sistemas físicos separados

**ID**: NIST SP 800-53 R4 AU-9 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1132 – Proteção de informações de auditoria \| Audit Backup em sistemas / componentes físicos separados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>Proteção de informações de auditoria | Proteção de criptografia

**ID**: NIST SP 800-53 R4 AU-9 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1133 – Proteção de informações de auditoria \| Proteção de criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Proteção de informações de auditoria | Acesso ao subconjunto de usuários com privilégios

**ID**: NIST SP 800-53 R4 AU-9 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1134 – Proteção de informações de auditoria \| Acesso por subconjunto de usuários com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>Não recusa

**ID**: NIST SP 800-53 R4 AU-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1135 – Não recusa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Retenção de registros de auditoria

**ID**: NIST SP 800-53 R4 AU-11 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1136 – Retenção de registro de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Geração de auditoria

**ID**: NIST SP 800-53 R4 AU-12 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Configuração de diagnóstico de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Auditar configuração de diagnóstico para tipos de recursos selecionados |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Auditar o workspace do Log Analytics para a VM – Relatar incompatibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Relata as VMs como sem conformidade se elas não estão registrando no log do workspace do Log Analytics especificado na atribuição de política ou de iniciativa. |auditoria |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[A auditoria no SQL Server deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no SQL Server deve ser habilitada para acompanhar as atividades de banco de dados em todos os bancos de dados no servidor e salvá-las em um log de auditoria. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[O Agente do Log Analytics deverá ser habilitado para imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Relata máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[O agente do Log Analytics deverá ser habilitado em conjuntos de dimensionamento de máquinas virtuais para obter imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Relata os conjuntos de dimensionamento de máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Controle Gerenciado da Microsoft 1137 – Geração de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Controle Gerenciado da Microsoft 1138 – Geração de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Controle Gerenciado da Microsoft 1139 – Geração de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Geração de auditoria | Trilha de auditoria em todo o sistema / correlacionada ao tempo

**ID**: NIST SP 800-53 R4 AU-12 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1140 – Geração de auditoria \| Trilha de auditoria em todo o sistema / correlacionada ao tempo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Geração de auditoria | Alterações feitas por pessoas autorizadas

**ID**: NIST SP 800-53 R4 AU-12 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1141 – Geração de auditoria \| Alterações por pessoas autorizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |A Microsoft implementa esse controle de Auditoria e Responsabilidade |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Avaliação da Segurança e Autorização

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Políticas de Avaliação e Autorização de Segurança e Procedimentos

**ID**: NIST SP 800-53 R4 CA-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1142 – Política e procedimentos de avaliação e autorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Controle Gerenciado da Microsoft 1143 – Política e procedimentos de avaliação e autorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Avaliações de Segurança

**ID**: NIST SP 800-53 R4 CA-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1144 – Avaliações de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Controle Gerenciado da Microsoft 1145 – Avaliações de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Controle Gerenciado da Microsoft 1146 – Avaliações de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Controle Gerenciado da Microsoft 1147 – Avaliações de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Avaliações de Segurança | Avaliadores Independentes

**ID**: NIST SP 800-53 R4 CA-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1148 – Avaliações de segurança \| Avaliadores independentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Avaliações de Segurança | Avaliações Especializadas

**ID**: NIST SP 800-53 R4 CA-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1149 – Avaliações de segurança \| Avaliações especializadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Avaliações de segurança | Organizações Externas

**ID**: NIST SP 800-53 R4 CA-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1150 – Avaliações de segurança \| Organizações externas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Interconexões de Sistema

**ID**: NIST SP 800-53 R4 CA-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1151 – Interconexões de sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Controle Gerenciado da Microsoft 1152 – Interconexões de sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Controle Gerenciado da Microsoft 1153 – Interconexões de sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Interconexões do Sistema | Conexões de Sistema de Segurança Não Nacional Não Classificado

**ID**: NIST SP 800-53 R4 CA-3 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1154 – Interconexões de sistema \| Conexões com sistema de segurança não nacional não classificado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Interconexões do Sistema | Restrições em conexões de sistema externo

**ID**: NIST SP 800-53 R4 CA-3 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1155 – Interconexões de sistema \| Restrições para conexões com sistema externo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Plano de Ação e Etapas

**ID**: NIST SP 800-53 R4 CA-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1156 – Plano de ação e marcos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Controle Gerenciado da Microsoft 1157 – Plano de ação e marcos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Autorização de Segurança

**ID**: NIST SP 800-53 R4 CA-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1158 – Autorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Controle Gerenciado da Microsoft 1159 – Autorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Controle Gerenciado da Microsoft 1160 – Autorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Monitoramento Contínuo

**ID**: NIST SP 800-53 R4 CA-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1161 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Controle Gerenciado da Microsoft 1162 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Controle Gerenciado da Microsoft 1163 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Controle Gerenciado da Microsoft 1164 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Controle Gerenciado da Microsoft 1165 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Controle Gerenciado da Microsoft 1166 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Controle Gerenciado da Microsoft 1167 – Monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Monitoramento Contínuo | Avaliação Independente

**ID**: NIST SP 800-53 R4 CA-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1168 – Monitoramento contínuo \| Avaliação independente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Monitoramento contínuo | Análise de tendência

**ID**: NIST SP 800-53 R4 CA-7 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1169 – Monitoramento contínuo \| Análises de tendências](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Teste de penetração

**ID**: NIST SP 800-53 R4 CA-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1170 – Testes de penetração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Teste de Segurança | Agente de Penetração Independente ou Equipe

**ID**: NIST SP 800-53 R4 CA-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1171 – Testes de penetração \| Agente ou equipe de penetração independente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>Conexões de Sistema Interno

**ID**: NIST SP 800-53 R4 CA-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1172 – Conexões do sistema interno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Controle Gerenciado da Microsoft 1173 – Conexões do sistema interno](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |A Microsoft implementa esse controle de Avaliação e Autorização de Segurança |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Gerenciamento de configuração

### <a name="configuration-management-policy-and-procedures"></a>Procedimentos e Política de Gerenciamento de Configuração

**ID**: NIST SP 800-53 R4 CM-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1174 – Política e procedimentos de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Controle Gerenciado da Microsoft 1175 – Política e procedimentos de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Configuração de Linha de Base

**ID**: NIST SP 800-53 R4 CM-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1176 – Configuração de linha de base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de Linha de Base | Revisões e Atualizações

**ID**: NIST SP 800-53 R4 CM-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1177 – Configuração de linha de base \| Revisões e atualizações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Controle Gerenciado da Microsoft 1178 – Configuração de linha de base \| Revisões e atualizações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Controle Gerenciado da Microsoft 1179 – Configuração de linha de base \| Revisões e atualizações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configuração de linha de base | Suporte de Automação para Precisão / Moeda

**ID**: NIST SP 800-53 R4 CM-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1180 – Configuração de linha de base \| Suporte e automação para precisão / moeda](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configuração de Linha de Base | Retenção de Configurações Anteriores

**ID**: NIST SP 800-53 R4 CM-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1181 – Configuração de linha de base \| Retenção de configurações anteriores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuração de Linha de Base | Configurar dispositivos, Componentes ou Sistemas de Áreas de Alto Risco

**ID**: NIST SP 800-53 R4 CM-2 (7) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1182 – Configuração de linha de base \| Configurar sistemas, componentes ou dispositivos para áreas de alto risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Controle Gerenciado da Microsoft 1183 – Configuração de linha de base \| Configurar sistemas, componentes ou dispositivos para áreas de alto risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**ID**: NIST SP 800-53 R4 CM-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1184 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Controle Gerenciado da Microsoft 1185 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Controle Gerenciado da Microsoft 1186 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Controle Gerenciado da Microsoft 1187 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Controle Gerenciado da Microsoft 1188 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Controle Gerenciado da Microsoft 1189 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Controle Gerenciado da Microsoft 1190 – Controle de alterações de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**ID**: NIST SP 800-53 R4 CM-3 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1191 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Controle Gerenciado da Microsoft 1192 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Controle Gerenciado da Microsoft 1193 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Controle Gerenciado da Microsoft 1194 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Controle Gerenciado da Microsoft 1195 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Controle Gerenciado da Microsoft 1196 – Controle de alterações de configuração \| Documentação / notificação / proibição automatizada de alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Controle de Alterações de Configuração | Testar / Validar / Alterações de Documentos

**ID**: NIST SP 800-53 R4 CM-3 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1197 – Controle de alterações de configuração \| Testar / validar / documentar alterações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Controle de alterações de configuração | Representante de segurança

**ID**: NIST SP 800-53 R4 CM-3 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1198 – Controle de alterações de configuração \| Representante de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Controle de Alterações de Configuração | Gerenciamento de Criptografia

**ID**: NIST SP 800-53 R4 CM-3 (6) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1199 – Controle de alterações de configuração \| Gerenciamento de criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Análise de Impacto de Segurança

**ID**: NIST SP 800-53 R4 CM-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1200 – Análise do impacto de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Análise de Impacto de Segurança | Ambientes de Teste Separados

**ID**: NIST SP 800-53 R4 CM-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1201 – Análise do impacto de segurança \| Ambientes de teste separados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Restrições de Acesso para Alteração

**ID**: NIST SP 800-53 R4 CM-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1202 – Restrições de acesso para alteração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Acessar Restrições para Alterar | Automatizada de Imposição de Acesso / Auditoria

**ID**: NIST SP 800-53 R4 CM-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1203 – Restrições de acesso para alteração \| Imposição / auditoria de acesso automatizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Acessar Restrições para Alterar | Revise as Alterações do Sistema

**ID**: NIST SP 800-53 R4 CM-5 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1204 – Restrições de acesso para alteração \| Examinar alterações do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Restrições de Acesso para Alteração | Componentes Assinados

**ID**: NIST SP 800-53 R4 CM-5 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1205 – Restrições de acesso para alteração \| Componentes assinados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Acessar Restrições para Alterar | Limitar a Produção / Operação Privilégios

**ID**: NIST SP 800-53 R4 CM-5 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1206 – Restrições de acesso para alteração \| Limitar privilégios de produção / operacionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Controle Gerenciado da Microsoft 1207 – Restrições de acesso para alteração \| Limitar privilégios de produção / operacionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Definições de configuração

**ID**: NIST SP 800-53 R4 CM-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1208 – Definições de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Controle Gerenciado da Microsoft 1209 – Definições de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Controle Gerenciado da Microsoft 1210 – Definições de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Controle Gerenciado da Microsoft 1211 – Definições de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Conjunto de Configuração | Gerenciamento Central Automatizado / Aplicativo / Verificação

**ID**: NIST SP 800-53 R4 CM-6 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1212 – Definições de configuração \| Gerenciamento / aplicação / verificação central automatizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Conjunto de Configuração | Responder a Alterações Não Autorizadas

**ID**: NIST SP 800-53 R4 CM-6 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1213 – Definições de configuração \| Resposta a alterações não autorizadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>Funcionalidade Mínima

**ID**: NIST SP 800-53 R4 CM-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1214 – Funcionalidade mínima](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Controle Gerenciado da Microsoft 1215 – Funcionalidade mínima](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>Funcionalidade Mínima | Revisão periódica

**ID**: NIST SP 800-53 R4 CM-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1216 – Funcionalidade mínima \| Revisão periódica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Controle Gerenciado da Microsoft 1217 – Funcionalidade mínima \| Revisão periódica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>Funcionalidade Mínima | Impedir a Execução do Programa

**ID**: NIST SP 800-53 R4 CM-7 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Controle Gerenciado da Microsoft 1218 – Funcionalidade mínima \| Impedir execução do programa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade Mínima | Um Software autorizado / Lista de Permissões

**ID**: NIST SP 800-53 R4 CM-7 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Controle Gerenciado da Microsoft 1219 – Funcionalidade mínima \| Software autorizado / lista de permissões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Controle Gerenciado da Microsoft 1220 – Funcionalidade mínima \| Software autorizado / lista de permissões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Controle Gerenciado da Microsoft 1221 – Funcionalidade mínima \| Software autorizado / lista de permissões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Inventário de Componentes do Sistema de Informação

**ID**: NIST SP 800-53 R4 CM-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1222 – Inventários de componentes do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Controle Gerenciado da Microsoft 1223 – Inventários de componentes do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventário de Componente do Sistema de Informações | Atualizações durante Instalações / Remoções

**ID**: NIST SP 800-53 R4 CM-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1224 – Inventários de componentes do sistema de informações \| Atualizações durante instalações / remoções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Inventário de Componentes do Sistema de Informações | Manutenção Automatizada

**ID**: NIST SP 800-53 R4 CM-8 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1225 – Inventários de componentes do sistema de informações \| Manutenção automatizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventário de Componentes do Sistema de Informação | Detecção de Componente Não Autorizado Automatizada

**ID**: NIST SP 800-53 R4 CM-8 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1226 – Inventários de componentes do sistema de informações \| Detecção automatizada de componentes não autorizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Controle Gerenciado da Microsoft 1227 – Inventários de componentes do sistema de informações \| Detecção automatizada de componentes não autorizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Inventário de Componentes do Sistema de Informações | Informação de Responsabilidade

**ID**: NIST SP 800-53 R4 CM-8 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1228 – Inventários de componentes do sistema de informações \| Informações de responsabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventário de Componente do Sistema de Informações | Nenhuma Estatística Duplicada de Componentes

**ID**: NIST SP 800-53 R4 CM-8 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1229 – Inventários de componentes do sistema de informações \| Ausência de contabilidade duplicada de componentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Plano de Gerenciamento de Configuração

**ID**: NIST SP 800-53 R4 CM-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1230 – Plano de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Controle Gerenciado da Microsoft 1231 – Plano de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Controle Gerenciado da Microsoft 1232 – Plano de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Controle Gerenciado da Microsoft 1233 – Plano de gerenciamento de configuração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Restrições de Uso de Software

**ID**: NIST SP 800-53 R4 CM-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1234 – Restrições de uso de software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Controle Gerenciado da Microsoft 1235 – Restrições de uso de software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Controle Gerenciado da Microsoft 1236 – Restrições de uso de software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Restrições de Uso de Software | Software Livre

**ID**: NIST SP 800-53 R4 CM-10 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1237 – Restrições de uso de software \| Software livre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>Software Instalado pelo Usuário

**ID**: NIST SP 800-53 R4 CM-11 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controles de aplicativos adaptáveis para definir aplicativos seguros devem estar habilitados nos computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Permita que os controles de aplicativos definam a lista de aplicativos considerados seguros em execução nos seus computadores e alertem você quando outros aplicativos forem executados. Isso ajuda a proteger seus computadores contra malware. Para simplificar o processo de configuração e manutenção de suas regras, a Central de Segurança usa o machine learning para analisar os aplicativos em execução em cada computador e sugerir a lista de aplicativos considerados seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Controle Gerenciado da Microsoft 1238 – Software instalado pelo usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Controle Gerenciado da Microsoft 1239 – Software instalado pelo usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Controle Gerenciado da Microsoft 1240 – Software instalado pelo usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Software instalado pelo Usuário | Alertas para Instalações Não Autorizadas

**ID**: NIST SP 800-53 R4 CM-11 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1241 – Software instalado pelo usuário \| Alertas para instalações não autorizadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |A Microsoft implementa esse controle de Gerenciamento de Configuração |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Planejamento de Contingência

### <a name="contingency-planning-policy-and-procedures"></a>Política e procedimentos de planejamento de contingência

**ID**: NIST SP 800-53 R4 CP-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1242 – Política e procedimentos de planejamento de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Controle Gerenciado da Microsoft 1243 – Política e procedimentos de planejamento de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Plano de contingência

**ID**: NIST SP 800-53 R4 CP-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1244 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Controle Gerenciado da Microsoft 1245 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Controle Gerenciado da Microsoft 1246 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Controle Gerenciado da Microsoft 1247 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Controle Gerenciado da Microsoft 1248 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Controle Gerenciado da Microsoft 1249 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Controle Gerenciado da Microsoft 1250 – Plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Plano de contingência | Coordenar com os planos relacionados

**ID**: NIST SP 800-53 R4 CP-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1251 – Plano de contingência \| Coordenar com planos relacionados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Plano de contingência | Planejamento de capacidade

**ID**: NIST SP 800-53 R4 CP-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1252 – Plano de contingência \| Planejamento da capacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Plano de contingência | Retomar missões/funções de negócios essenciais

**ID**: NIST SP 800-53 R4 CP-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1253 – Plano de contingência \| Retomar as missões / funções de negócios essenciais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Plano de contingência | Retomar todas as missões/funções de negócios

**ID**: NIST SP 800-53 R4 CP-2 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1254 – Plano de contingência \| Retomar todas as missões / funções de negócios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Plano de contingência | Continuar missões/funções de negócios essenciais

**ID**: NIST SP 800-53 R4 CP-2 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1255 – Plano de contingência \| Continuar missões / funções de negócios essenciais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Plano de contingência | Identificar ativos críticos

**ID**: NIST SP 800-53 R4 CP-2 (8) **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1256 – Plano de contingência \| Identificar ativos críticos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Treinamento de contingência

**ID**: NIST SP 800-53 R4 CP-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1257 – Treinamento de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Controle Gerenciado da Microsoft 1258 – Treinamento de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Controle Gerenciado da Microsoft 1259 – Treinamento de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Treinamento de contingência | Eventos simulados

**ID**: NIST SP 800-53 R4 CP-3 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1260 – Treinamento de contingência \| Eventos simulados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Teste do plano de contingência

**ID**: NIST SP 800-53 R4 CP-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1261 – Teste do plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Controle Gerenciado da Microsoft 1262 – Teste do plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Controle Gerenciado da Microsoft 1263 – Teste do plano de contingência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Teste do plano de contingência | Coordenar com os planos relacionados

**ID**: NIST SP 800-53 R4 CP-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1264 – Teste do plano de contingência \| Coordenar com planos relacionados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Teste do plano de contingência | Site de processamento alternativo

**ID**: NIST SP 800-53 R4 CP-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1265 – Teste do plano de contingência \| Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Controle Gerenciado da Microsoft 1266 – Teste do plano de contingência \| Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Site de armazenamento alternativo

**ID**: NIST SP 800-53 R4 CP-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1267 – Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Controle Gerenciado da Microsoft 1268 – Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Site de armazenamento alternativo | Separação do site primário

**ID**: NIST SP 800-53 R4 CP-6 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1269 – Site de processamento alternativo \| Separação do site primário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Site de armazenamento alternativo| Objetivos de ponto/tempo de recuperação

**ID**: NIST SP 800-53 R4 CP-6 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1270 – Site de processamento alternativo \| Objetivos do tempo/ponto de recuperação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Site de armazenamento alternativo | Acessibilidade

**ID**: NIST SP 800-53 R4 CP-6 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1271 – Site de processamento alternativo \| Acessibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Site de processamento alternativo

**ID**: NIST SP 800-53 R4 CP-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem a recuperação de desastre configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais sem a recuperação de desastre configurada. Para saber mais sobre recuperação de desastre, acesse [https://aka.ms/asr-doc](https://aka.ms/asr-doc). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Controle Gerenciado da Microsoft 1272 – Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Controle Gerenciado da Microsoft 1273 – Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Controle Gerenciado da Microsoft 1274 – Site de processamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Site de processamento alternativo | Separação do site primário

**ID**: NIST SP 800-53 R4 CP-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1275 – Site de processamento alternativo \| Separação do site primário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Site de processamento alternativo | Acessibilidade

**ID**: NIST SP 800-53 R4 CP-7 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1276 – Site de processamento alternativo \| Acessibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Site de processamento alternativo | Prioridade de serviço

**ID**: NIST SP 800-53 R4 CP-7 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1277 – Site de processamento alternativo \| Prioridade de serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Site de processamento alternativo | Preparação para uso

**ID**: NIST SP 800-53 R4 CP-7 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1278 – Site de processamento alternativo \| Preparação para uso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Serviços de telecomunicações

**ID**: NIST SP 800-53 R4 CP-8 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1279 – Serviços de telecomunicações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Serviços de telecomunicação | Provisões de Prioridade de serviço

**ID**: NIST SP 800-53 R4 CP-8 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1280 – Serviços de telecomunicações \| Provisões de prioridade de serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Controle Gerenciado da Microsoft 1281 – Serviços de telecomunicações \| Provisões de prioridade de serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Serviços de telecomunicações | Pontos únicos de falha

**ID**: NIST SP 800-53 R4 CP-8 (2) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1282 – Serviços de telecomunicações \| Pontos únicos de falha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Serviços de telecomunicações | Separação de provedores primários/alternativos

**ID**: NIST SP 800-53 R4 CP-8 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1283 – Serviços de telecomunicações \| Separação de provedores primários / alternativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Serviços de telecomunicação | Plano de contingência do provedor

**ID**: NIST SP 800-53 R4 CP-8 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1284 – Serviços de telecomunicações \| Plano de contingência do provedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Controle Gerenciado da Microsoft 1285 – Serviços de telecomunicações \| Plano de contingência do provedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Controle Gerenciado da Microsoft 1286 – Serviços de telecomunicações \| Plano de contingência do provedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Backup do sistema de informações

**ID**: NIST SP 800-53 R4 CP-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1287 – Backup do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Controle Gerenciado da Microsoft 1288 – Backup do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Controle Gerenciado da Microsoft 1289 – Backup do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Controle Gerenciado da Microsoft 1290 – Backup do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Backup do sistema de informações| Testes de confiabilidade/integridade

**ID**: NIST SP 800-53 R4 CP-9 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1291 – Backup do sistema de informações \| Testes de confiabilidade / integridade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Backup do sistema de informações | Restauração de teste usando amostragem

**ID**: NIST SP 800-53 R4 CP-9 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1292 – Backup do sistema de informações \| Testar restauração usando amostragem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Backup do sistema de informações | Separar o armazenamento para informações críticas

**ID**: NIST SP 800-53 R4 CP-9 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1293 – Backup do sistema de informações \| Armazenamento separado para informações críticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Backup do sistema de informações | Transferência para o site de armazenamento alternativo

**ID**: NIST SP 800-53 R4 CP-9 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1294 – Backup do sistema de informações \| Transferir para site de armazenamento alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Recuperação e reconstituição do sistema de informações

**ID**: NIST SP 800-53 R4 CP-10 **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1295 – Recuperação e reconstituição do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Recuperação e reconstituição do sistema de informações | Recuperação de transação

**ID**: NIST SP 800-53 R4 CP-10 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1296 – Recuperação e reconstituição do sistema de informações \| Recuperação de transação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Recuperação e reconstituição do sistema de informações | Restaurar dentro do período de tempo

**ID**: NIST SP 800-53 R4 CP-10 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1297 – Recuperação e reconstituição do sistema de informações \| Restaurar dentro do período](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |A Microsoft implementa esse controle de Planejamento de Contingência |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Identificação e Autenticação

### <a name="identification-and-authentication-policy-and-procedures"></a>Política de Identificação e Autenticação e Procedimentos

**ID**: NIST SP 800-53 R4 IA-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1298 – Política e procedimento de identificação e autenticação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Controle Gerenciado da Microsoft 1299 – Política e procedimento de identificação e autenticação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Identificação e autenticação (Usuários organizacionais)

**ID**: NIST SP 800-53 R4 IA-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1300 – Identificação e autenticação (usuários organizacionais)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas com privilégios

**ID**: NIST SP 800-53 R4 IA-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A autenticação Multifator do Microsoft Azure (MFA) deve estar habilitada para todas as contas de assinatura com privilégios de gravação para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com permissões de proprietário para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Controle Gerenciado da Microsoft 1301 – Identificação e autenticação (usuários organizacionais) \| Acesso à rede para contas com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas sem privilégios

**ID**: NIST SP 800-53 R4 IA-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A autenticação Multifator do Microsoft Azure (MFA) deve ser ativada para todas as contas de assinatura com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Controle Gerenciado da Microsoft 1302 – Identificação e autenticação (usuários organizacionais) \| Acesso à rede para contas sem privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso local para contas com privilégios

**ID**: NIST SP 800-53 R4 IA-2 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1303 – Identificação e autenticação (usuários organizacionais) \| Acesso local a contas com privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso local para contas sem privilégios

**ID**: NIST SP 800-53 R4 IA-2 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1304 – Identificação e autenticação (usuários organizacionais) \| Acesso local a contas sem privilégios](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identificação e autenticação (Usuários organizacionais) | Autenticação de grupo

**ID**: NIST SP 800-53 R4 IA-2 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1305 – Identificação e autenticação (usuários organizacionais) \| Autenticação de grupo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas com privilégios - Resistente à reprodução

**ID**: NIST SP 800-53 R4 IA-2 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1306 – Identificação e autenticação (usuários organizacionais) \| Acesso de rede a contas privadas – Reprodução](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identificação e Autenticação (Usuários Organizacionais) | Acesso à rede para Contas Sem Privilégios - Resistente à Repetição

**ID**: NIST SP 800-53 R4 IA-2 (9) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1307 – Identificação e autenticação (usuários organizacionais) \| Acesso de rede a contas não privadas – Reprodução](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identificação e Autenticação (Usuários Organizacionais) | Acesso Remoto - Dispositivo Separado

**ID**: NIST SP 800-53 R4 IA-2 (11) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1308 – Identificação e autenticação (usuários organizacionais) \| Acesso remoto – Dispositivo separado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identificação e Autenticação (Usuários Organizacionais) | Aceitação de credenciais PIV

**ID**: NIST SP 800-53 R4 IA-2 (12) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1309 – Identificação e autenticação (usuários organizacionais) \| Aceitação de credenciais de PIV](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Identificação e Autenticação de Dispositivo

**ID**: NIST SP 800-53 R4 IA-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1310 – Identificação e autenticação do dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Gerenciamento de Identificador

**ID**: NIST SP 800-53 R4 IA-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1311 – Gerenciamento de identificador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Controle Gerenciado da Microsoft 1312 – Gerenciamento de identificador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Controle Gerenciado da Microsoft 1313 – Gerenciamento de identificador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Controle Gerenciado da Microsoft 1314 – Gerenciamento de identificador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Controle Gerenciado da Microsoft 1315 – Gerenciamento de identificador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Gerenciamento de Identificador | Identificar o Status do Usuário

**ID**: NIST SP 800-53 R4 IA-4 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1316 – Gerenciamento de identificador \| Identificar o status do usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Gerenciamento de autenticador

**ID**: NIST SP 800-53 R4 IA-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adicionar identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em máquinas virtuais sem identidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado, mas que não têm identidades gerenciadas. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Adicionar uma identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em VMs com uma identidade atribuída ao usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado e tem pelo menos uma identidade atribuída ao usuário, mas não tem uma identidade gerenciada atribuída ao sistema. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditar os computadores Linux que não têm as permissões de arquivo de senha definidas como 0644](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Linux que não têm as permissões de arquivo de senha definidas como 0644 |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Auditar os computadores Linux que têm contas sem senhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Linux que permitem contas sem senhas |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Auditar os computadores Windows que não armazenam senhas usando a criptografia reversível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não armazenam senhas usando a criptografia reversível |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Implantar a extensão de Configuração de Convidado do Linux para habilitar atribuições de Configuração de Convidado em VMs do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Essa política implanta a extensão de Configuração de Convidado do Linux em máquinas virtuais do Linux hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Linux é um pré-requisito para todas as atribuições da Configuração de Convidado do Linux e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Linux. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Implantar a extensão de Configuração de Convidado do Windows para habilitar atribuições de Configuração de Convidado em VMs do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Essa política implanta a extensão de Configuração de Convidado do Windows em máquinas virtuais do Windows hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Windows é um pré-requisito para todas as atribuições da Configuração de Convidado do Windows e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Windows. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Controle Gerenciado da Microsoft 1317 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Controle Gerenciado da Microsoft 1318 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Controle Gerenciado da Microsoft 1319 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Controle Gerenciado da Microsoft 1320 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Controle Gerenciado da Microsoft 1321 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Controle Gerenciado da Microsoft 1322 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Controle Gerenciado da Microsoft 1323 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Controle Gerenciado da Microsoft 1324 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Controle Gerenciado da Microsoft 1325 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Controle Gerenciado da Microsoft 1326 – Gerenciamento do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**ID**: NIST SP 800-53 R4 IA-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adicionar identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em máquinas virtuais sem identidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado, mas que não têm identidades gerenciadas. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Adicionar uma identidade gerenciada atribuída ao sistema para habilitar atribuições de Configuração de Convidado em VMs com uma identidade atribuída ao usuário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Essa política adiciona uma identidade gerenciada atribuída ao sistema a máquinas virtuais hospedadas no Azure com suporte da Configuração de Convidado e tem pelo menos uma identidade atribuída ao usuário, mas não tem uma identidade gerenciada atribuída ao sistema. Uma identidade gerenciada atribuída ao sistema é um pré-requisito para todas as atribuições de Configuração de Convidado e deve ser adicionada a computadores antes de usar definições de política da Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Auditar os computadores Windows que podem reutilizar as 24 senhas anteriores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que permitem a reutilização das 24 senhas anteriores |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[Auditar os computadores Windows que não têm uma duração máxima da senha de 70 dias](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não têm uma duração máxima da senha de 70 dias |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[Auditar os computadores Windows que não têm uma duração mínima da senha de 1 dia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não têm uma duração mínima de senha de 1 dia |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Auditar os computadores Windows que não têm a configuração de complexidade de senha habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não têm a configuração de complexidade de senha habilitada |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Auditar os computadores Windows que não restringem o tamanho mínimo da senha a 14 caracteres](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não restringem o tamanho mínimo da senha a 14 caracteres |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Auditar os computadores Windows que não armazenam senhas usando a criptografia reversível](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Requer que os pré-requisitos sejam implantados no escopo de atribuição de política. Para obter detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol). Os computadores não estarão em conformidade se forem computadores Windows que não armazenam senhas usando a criptografia reversível |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Implantar a extensão de Configuração de Convidado do Linux para habilitar atribuições de Configuração de Convidado em VMs do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Essa política implanta a extensão de Configuração de Convidado do Linux em máquinas virtuais do Linux hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Linux é um pré-requisito para todas as atribuições da Configuração de Convidado do Linux e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Linux. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Implantar a extensão de Configuração de Convidado do Windows para habilitar atribuições de Configuração de Convidado em VMs do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Essa política implanta a extensão de Configuração de Convidado do Windows em máquinas virtuais do Windows hospedadas no Azure com suporte da Configuração de Convidado. A extensão de Configuração de Convidado do Windows é um pré-requisito para todas as atribuições da Configuração de Convidado do Windows e deve ser implantada em computadores antes de usar qualquer definição de política de Configuração de Convidado do Windows. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Controle Gerenciado da Microsoft 1327 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Controle Gerenciado da Microsoft 1328 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Controle Gerenciado da Microsoft 1329 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Controle Gerenciado da Microsoft 1330 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Controle Gerenciado da Microsoft 1331 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Controle Gerenciado da Microsoft 1332 – Gerenciamento do Authenticator \| Autenticação baseada em senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Pki

**ID**: NIST SP 800-53 R4 IA-5 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1333 – Gerenciamento do Authenticator \| Autenticação baseada em PKI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Controle Gerenciado da Microsoft 1334 – Gerenciamento do Authenticator \| Autenticação baseada em PKI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Controle Gerenciado da Microsoft 1335 – Gerenciamento do Authenticator \| Autenticação baseada em PKI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Controle Gerenciado da Microsoft 1336 – Gerenciamento do Authenticator \| Autenticação baseada em PKI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Gerenciamento de autenticador | Registro em pessoa ou por terceiros confiáveis

**ID**: NIST SP 800-53 R4 IA-5 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1337 – Gerenciamento do Authenticator \| Registro pessoalmente ou por terceiros confiáveis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Gerenciamento de autenticador | Suporte automatizado para determinação de força de senha

**ID**: NIST SP 800-53 R4 IA-5 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1338 – Gerenciamento do Authenticator \| Suporte automatizado para determinação da força de senha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Gerenciamento de autenticador | Proteção dos autenticadores

**ID**: NIST SP 800-53 R4 IA-5 (6) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1339 – Gerenciamento do Authenticator \| Proteção de autenticadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Gerenciamento de autenticador | Nenhum autenticador estático não criptografado incorporado

**ID**: NIST SP 800-53 R4 IA-5 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1340 – Gerenciamento do Authenticator \| Nenhum autenticador estático não criptografado inserido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Gerenciamento de autenticador | Várias contas de sistema de informações

**ID**: NIST SP 800-53 R4 IA-5 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1341 – Gerenciamento do Authenticator \| Várias contas de sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Hardware Token

**ID**: NIST SP 800-53 R4 IA-5 (11) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1342 – Gerenciamento do Authenticator \| Autenticação baseada em token do hardware](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Gerenciamento de autenticador | Expiração de Autenticadores de Cache

**ID**: NIST SP 800-53 R4 IA-5 (13) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1343 – Gerenciamento do Authenticator \| Expiração de autenticadores em cache](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Comentários do autenticador

**ID**: NIST SP 800-53 R4 IA-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1344 – Comentários do Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Autenticação de Módulo Criptográfico

**ID**: NIST SP 800-53 R4 IA-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1345 – Autenticação de módulo criptográfico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Identificação e Autenticação (Usuários Não Organizacionais)

**ID**: NIST SP 800-53 R4 IA-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1346 – Identificação e autenticação (usuários não organizacionais)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identificação e Autenticação (Usuários não organizacionais) | Aceitação de credenciais PIV de outras agências

**ID**: NIST SP 800-53 R4 IA-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1347 – Identificação e autenticação (usuários não organizacionais) \| Aceitação de credenciais de PIV de outras agências](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identificação e Autenticação (Usuários não organizacionais) | Aceitação de credenciais de terceiros

**ID**: NIST SP 800-53 R4 IA-8 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1348 – Identificação e autenticação (usuários não organizacionais) \| Aceitação de credenciais de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identificação e Autenticação (Usuários Não Organizacionais) | Uso de produtos aprovados pelo FICAM

**ID**: NIST SP 800-53 R4 IA-8 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1349 – Identificação e autenticação (usuários não organizacionais) \| Uso de produtos aprovados pelo FICAM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identificação e Autenticação (Usuários Não Organizacionais) | Uso de Produtos Emitidos por FICAM

**ID**: NIST SP 800-53 R4 IA-8 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1350 – Identificação e autenticação (usuários não organizacionais) \| Uso de perfis emitidos pelo FICAM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |A Microsoft implementa esse controle de Identificação e Autenticação |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Resposta a incidentes

### <a name="incident-response-policy-and-procedures"></a>Política e procedimentos de resposta a incidentes

**ID**: NIST SP 800-53 R4 IR-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1351 – Política e procedimentos de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Controle Gerenciado da Microsoft 1352 – Política e procedimentos de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Treinamento de resposta a incidentes

**ID**: NIST SP 800-53 R4 IR-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1353 – Treinamento de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Controle Gerenciado da Microsoft 1354 – Treinamento de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Controle Gerenciado da Microsoft 1355 – Treinamento de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Treinamento de resposta a incidentes | Eventos simulados

**ID**: NIST SP 800-53 R4 IR-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1356 – Treinamento de resposta a incidentes \| Eventos simulados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Treinamento de resposta a incidentes | Ambientes de treinamento automatizado

**ID**: NIST SP 800-53 R4 IR-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1357 – Treinamento de resposta a incidentes \| Ambientes de treinamento automatizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Teste de resposta a incidentes

**ID**: NIST SP 800-53 R4 IR-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1358 – Testes de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Teste de resposta a incidentes | Coordenação com planos relacionados

**ID**: NIST SP 800-53 R4 IR-3 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1359 – Testes de resposta a incidentes \| Coordenação com planos relacionados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Tratamento de incidentes

**ID**: NIST SP 800-53 R4 IR-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1360 – Tratamento de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Controle Gerenciado da Microsoft 1361 – Tratamento de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Controle Gerenciado da Microsoft 1362 – Tratamento de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Tratamento de incidentes | Processos automatizados de tratamento de incidentes

**ID**: NIST SP 800-53 R4 IR-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1363 – Tratamento de incidentes \| Processos automatizados de tratamento de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Tratamento de incidentes | Reconfiguração dinâmica

**ID**: NIST SP 800-53 R4 IR-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1364 – Tratamento de incidentes \| Reconfiguração dinâmica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Tratamento de incidentes | Continuidade das operações

**ID**: NIST SP 800-53 R4 IR-4 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1365 – Tratamento de incidentes \| Continuidade de operações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Tratamento de incidentes | Correlação de informações

**ID**: NIST SP 800-53 R4 IR-4 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1366 – Tratamento de incidentes \| Correlação de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Tratamento de incidentes | Ameaças internas - Capacidades específicas

**ID**: NIST SP 800-53 R4 IR-4 (6) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1367 – Tratamento de incidentes \| Ameaças internas – Capacidades específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Tratamento de incidentes | Correlação com organizações externas

**ID**: NIST SP 800-53 R4 IR-4 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1368 – Tratamento de incidentes \| Correlação com organizações externas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Monitoramento de incidentes

**ID**: NIST SP 800-53 R4 IR-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1369 – Monitoramento de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Monitoramento de incidentes | Acompanhamento automatizado / Coleta / Análise de Dados

**ID**: NIST SP 800-53 R4 IR-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1370 – Monitoramento de incidente \| Acompanhamento automatizado / Coleta de dados / Análise](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Relatório de incidentes

**ID**: NIST SP 800-53 R4 IR-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1371 – Relatórios de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Controle Gerenciado da Microsoft 1372 – Relatórios de incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Relatório de incidentes | Relatórios automatizados

**ID**: NIST SP 800-53 R4 IR-6 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1373 – Relatórios de incidentes \| Relatórios automatizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Assistência de resposta a incidentes

**ID**: NIST SP 800-53 R4 IR-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1374 – Assistência de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Assistência de resposta a incidentes | Suporte de automação para disponibilidade de informações / Suporte

**ID**: NIST SP 800-53 R4 IR-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1375 – Assistência de resposta a incidentes \| Suporte de automação para disponibilidade de informações / suporte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Assistência de resposta a incidentes | Coordenação com provedores externos

**ID**: NIST SP 800-53 R4 IR-7 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1376 – Assistência de resposta da incidentes \| Coordenação com provedores externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Controle Gerenciado da Microsoft 1377 – Assistência de resposta da incidentes \| Coordenação com provedores externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Plano de resposta a incidentes

**ID**: NIST SP 800-53 R4 IR-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1378 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Controle Gerenciado da Microsoft 1379 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Controle Gerenciado da Microsoft 1380 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Controle Gerenciado da Microsoft 1381 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Controle Gerenciado da Microsoft 1382 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Controle Gerenciado da Microsoft 1383 – Plano de resposta a incidentes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Resposta ao vazamento de informações

**ID**: NIST SP 800-53 R4 IR-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1384 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Controle Gerenciado da Microsoft 1385 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Controle Gerenciado da Microsoft 1386 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Controle Gerenciado da Microsoft 1387 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Controle Gerenciado da Microsoft 1388 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Controle Gerenciado da Microsoft 1389 – Resposta a vazamentos de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Resposta ao vazamento de informações | Pessoal responsável

**ID**: NIST SP 800-53 R4 IR-9 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1390 – Resposta a vazamentos de informações \| Pessoal responsável](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Resposta ao vazamento de informações | Treinamento

**ID**: NIST SP 800-53 R4 IR-9 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1391 – Resposta a vazamentos de informações \| Treinamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Resposta ao vazamentos de informações | Operações pós-vazamento

**ID**: NIST SP 800-53 R4 IR-9 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1392 – Resposta a vazamentos de informações \| Operações pós-vazamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Resposta ao vazamento de informações | Exposição ao pessoal não autorizado

**ID**: NIST SP 800-53 R4 IR-9 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1393 – Resposta a vazamentos de informações \| Exposição a pessoal não autorizado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |A Microsoft implementa esse controle de Resposta a Incidentes |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Manutenção

### <a name="system-maintenance-policy-and-procedures"></a>Política e procedimentos de manutenção do sistema

**ID**: NIST SP 800-53 R4 MA-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1394 – Política e procedimentos de manutenção do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Controle Gerenciado da Microsoft 1395 – Política e procedimentos de manutenção do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Manutenção controlada

**ID**: NIST SP 800-53 R4 MA-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1396 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Controle Gerenciado da Microsoft 1397 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Controle Gerenciado da Microsoft 1398 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Controle Gerenciado da Microsoft 1399 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Controle Gerenciado da Microsoft 1400 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Controle Gerenciado da Microsoft 1401 – Manutenção controlada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Manutenção Controlada | Atividades de Manutenção Automatizada

**ID**: NIST SP 800-53 R4 MA-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1402 – Manutenção controlada \| Atividades de manutenção automatizadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Controle Gerenciado da Microsoft 1403 – Manutenção controlada \| Atividades de manutenção automatizadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Ferramentas de manutenção

**ID**: NIST SP 800-53 R4 MA-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1404 – Ferramentas de manutenção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Ferramentas de manutenção | Ferramentas de inspeção

**ID**: NIST SP 800-53 R4 MA-3 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1405 – Ferramentas de manutenção \| Ferramentas de inspeção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Ferramentas de manutenção | Mídia de inspeção

**ID**: NIST SP 800-53 R4 MA-3 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1406 – Ferramentas de manutenção \| Inspecionar mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Ferramentas de manutenção | Evitar remoção não autorizada

**ID**: NIST SP 800-53 R4 MA-3 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1407 – Ferramentas de manutenção \| Impedir a remoção não autorizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Controle Gerenciado da Microsoft 1408 – Ferramentas de manutenção \| Impedir a remoção não autorizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Controle Gerenciado da Microsoft 1409 – Ferramentas de manutenção \| Impedir a remoção não autorizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Controle Gerenciado da Microsoft 1410 – Ferramentas de manutenção \| Impedir a remoção não autorizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Manutenção não local

**ID**: NIST SP 800-53 R4 MA-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1411 – Manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Controle Gerenciado da Microsoft 1412 – Manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Controle Gerenciado da Microsoft 1413 – Manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Controle Gerenciado da Microsoft 1414 – Manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Controle Gerenciado da Microsoft 1415 – Manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Manutenção não-local | Documentar manutenção não local

**ID**: NIST SP 800-53 R4 MA-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1416 – Manutenção não local \| Documentar a manutenção não local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Manutenção não-local | Segurança/limpeza comparável

**ID**: NIST SP 800-53 R4 MA-4 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1417 – Manutenção não local \| Segurança/limpeza comparável](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Controle Gerenciado da Microsoft 1418 – Manutenção não local \| Segurança/limpeza comparável](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Manutenção não-local | Proteção criptográfica

**ID**: NIST SP 800-53 R4 MA-4 (6) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1419 – Manutenção não local \| Proteção criptográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Equipe de manutenção

**ID**: NIST SP 800-53 R4 MA-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1420 – Pessoal de manutenção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Controle Gerenciado da Microsoft 1421 – Pessoal de manutenção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Controle Gerenciado da Microsoft 1422 – Pessoal de manutenção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Equipe de manutenção | Indivíduos sem acesso apropriado

**ID**: NIST SP 800-53 R4 MA-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1423 – Pessoal de manutenção \| Indivíduos sem acesso apropriado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Controle Gerenciado da Microsoft 1424 – Pessoal de manutenção \| Indivíduos sem acesso apropriado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Manutenção oportuna

**ID**: NIST SP 800-53 R4 MA-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1425 – Manutenção pontual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |A Microsoft implementa esse controle de Manutenção |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Proteção de Mídia

### <a name="media-protection-policy-and-procedures"></a>Procedimentos e política de proteção de mídia

**ID**: NIST SP 800-53 R4 MP-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1426 – Política e procedimentos de proteção de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Controle Gerenciado da Microsoft 1427 – Política e procedimentos de proteção de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Acesso à mídia

**ID**: NIST SP 800-53 R4 MP-2 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1428 – Acesso à mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Marcação de mídia

**ID**: NIST SP 800-53 R4 MP-3 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1429 – Marcação de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Controle Gerenciado da Microsoft 1430 – Marcação de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Armazenamento de mídia

**ID**: NIST SP 800-53 R4 MP-4 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1431 – Armazenamento de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Controle Gerenciado da Microsoft 1432 – Armazenamento de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Transporte de dados

**ID**: NIST SP 800-53 R4 MP-5 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1433 – Transporte de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Controle Gerenciado da Microsoft 1434 – Transporte de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Controle Gerenciado da Microsoft 1435 – Transporte de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Controle Gerenciado da Microsoft 1436 – Transporte de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Transporte de mídia | Proteção criptográfica

**ID**: NIST SP 800-53 R4 MP-5 (4) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1437 – Transporte de mídia \| Proteção criptográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Limpeza de mídia

**ID**: NIST SP 800-53 R4 MP-6 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1438 – Limpeza de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Controle Gerenciado da Microsoft 1439 – Limpeza de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Limpeza de mídia | Analisar / Aprovar / Acompanhar / Documentar / Verificar

**ID**: NIST SP 800-53 R4 MP-6 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1440 – Limpeza de mídia \| Examinar / aprovar / acompanhar / documentar / verificar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Limpeza de mídia | Teste de equipamentos

**ID**: NIST SP 800-53 R4 MP-6 (2) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1441 – Limpeza de mídia \| Testes de equipamentos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Limpeza de mídia | Técnicas não destrutivas

**ID**: NIST SP 800-53 R4 MP-6 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1442 – Limpeza de mídia \| Técnicas não destrutivas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Uso de mídia

**ID**: NIST SP 800-53 R4 MP-7 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1443 – Uso de mídia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Uso de mídia | Proibir o uso sem proprietário

**ID**: NIST SP 800-53 R4 MP-7 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1444 – Uso de mídia \| Proibir o uso sem proprietário](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |A Microsoft implementa esse controle de Proteção de Mídia |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Proteção Física e Ambiental

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Política e Procedimentos de Proteção Física e Ambiental

**ID**: NIST SP 800-53 R4 PE-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1445 – Política e procedimentos de proteção física e ambiental](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Controle Gerenciado da Microsoft 1446 – Política e procedimentos de proteção física e ambiental](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Autorizações de Acesso Físico

**ID**: NIST SP 800-53 R4 PE-2 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1447 – Autorizações de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Controle Gerenciado da Microsoft 1448 – Autorizações de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Controle Gerenciado da Microsoft 1449 – Autorizações de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Controle Gerenciado da Microsoft 1450 – Autorizações de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Controles de Acesso Físico

**ID**: NIST SP 800-53 R4 PE-3 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1451 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Controle Gerenciado da Microsoft 1452 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Controle Gerenciado da Microsoft 1453 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Controle Gerenciado da Microsoft 1454 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Controle Gerenciado da Microsoft 1455 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Controle Gerenciado da Microsoft 1456 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Controle Gerenciado da Microsoft 1457 – Controle de acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Controle de Acesso Físico | Acesso de Sistema de Informações

**ID**: NIST SP 800-53 R4 PE-3 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1458 – Controle de acesso físico \| Acesso do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Controle de Acesso de Média de Transmissão

**ID**: NIST SP 800-53 R4 PE-4 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1459 – Controle de acesso para mídia de transmissão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Controle de Acesso para Dispositivos de Saída

**ID**: NIST SP 800-53 R4 PE-5 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1460 – Controle de acesso para dispositivos de saída](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Monitorando Acesso Físico

**ID**: NIST SP 800-53 R4 PE-6 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1461 – Monitoramento do acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Controle Gerenciado da Microsoft 1462 – Monitoramento do acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Controle Gerenciado da Microsoft 1463 – Monitoramento do acesso físico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorando o Acesso Físico | Alarmes de Invasão / Equipamento de Vigilância

**ID**: NIST SP 800-53 R4 PE-6 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1464 – Monitoramento do acesso físico \| Alarmes de invasão / Equipamento de vigilância](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorando o Acesso Físico | Acesso Físico aos Sistemas de Informações de Monitoramento

**ID**: NIST SP 800-53 R4 PE-6 (4) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1465 – Monitoramento do acesso físico \| Monitoramento do acesso físico a sistemas de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Registros de Acesso de Visitante

**ID**: NIST SP 800-53 R4 PE-8 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1466 – Registros de acesso de visitante](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Controle Gerenciado da Microsoft 1467 – Registros de acesso de visitante](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Registros de Acesso de Visitante | Revisão / Manutenção de Registros Automatizada

**ID**: NIST SP 800-53 R4 PE-8 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1468 – Registros de acesso de visitante \| Manutenção / revisão de registros automatizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Equipamento de Energia e Cabeamento

**ID**: NIST SP 800-53 R4 PE-9 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1469 – Equipamento de energia e cabeamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Desligamento de Emergência

**ID**: NIST SP 800-53 R4 PE-10 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1470 – Desligamento de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Controle Gerenciado da Microsoft 1471 – Desligamento de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Controle Gerenciado da Microsoft 1472 – Desligamento de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Alimentação de Emergência

**ID**: NIST SP 800-53 R4 PE-11 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1473 – Energia de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Alimentação de Emergência | Fonte de Alimentação Alternativa a Longo Prazo - Recurso Operacional Mínimo

**ID**: NIST SP 800-53 R4 PE-11 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1474 – Emergency Power \| Fonte de energia alternativa de longo prazo – Capacidade operacional mínima](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Iluminação de Emergência

**ID**: NIST SP 800-53 R4 PE-12 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1475 – Iluminação de emergência](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Proteção ao Fogo

**ID**: NIST SP 800-53 R4 PE-13 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1476 – Proteção contra incêndio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Proteção contra Fogo | Sistemas / Dispositivos de Detecção

**ID**: NIST SP 800-53 R4 PE-13 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1477 – Proteção contra incêndio \| Dispositivos / sistemas de detecção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Proteção contra Fogo | Sistemas / Dispositivos de Supressão

**ID**: NIST SP 800-53 R4 PE-13 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1478 – Proteção contra incêndio \| Dispositivos / sistemas de supressão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Proteção contra Fogo | Supressão de Incêndio Automática

**ID**: NIST SP 800-53 R4 PE-13 (3) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1479 – Proteção contra incêndio \| Supressão de incêndio automática](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Controles de Umidade e Temperatura

**ID**: NIST SP 800-53 R4 PE-14 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1480 – Controles de temperatura e umidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Controle Gerenciado da Microsoft 1481 – Controles de temperatura e umidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Controles de Umidade e Temperatura | Monitoramento Com Alarmes / Notificações

**ID**: NIST SP 800-53 R4 PE-14 (2) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1482 – Controles de temperatura e umidade \| Monitoramento com alarmes / notificações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Proteção contra Danos de Água

**ID**: NIST SP 800-53 R4 PE-15 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1483 – Proteção contra danos causados pela água](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Proteção contra Danos de Água | Suporte de Automação

**ID**: NIST SP 800-53 R4 PE-15 (1) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1484 – Proteção contra danos causados pela água \| Suporte de automação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Entrega e Remoção

**ID**: NIST SP 800-53 R4 PE-16 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1485 – Entrega e remoção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Site de Trabalho Alternado

**ID**: NIST SP 800-53 R4 PE-17 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1486 – Site de trabalho alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Controle Gerenciado da Microsoft 1487 – Site de trabalho alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Controle Gerenciado da Microsoft 1488 – Site de trabalho alternativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Local dos Componentes do Sistema de Informações

**ID**: NIST SP 800-53 R4 PE-18 **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1489 – Localização dos componentes do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |A Microsoft implementa esse controle de Proteção Física e Ambiental |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Planejamento

### <a name="security-planning-policy-and-procedures"></a>Política e procedimentos de planejamento de segurança

**ID**: NIST SP 800-53 R4 PL-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1490 – Política e procedimentos de planejamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Controle Gerenciado da Microsoft 1491 – Política e procedimentos de planejamento de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Plano de segurança do sistema

**ID**: NIST SP 800-53 R4 PL-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1492 – Plano de segurança do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Controle Gerenciado da Microsoft 1493 – Plano de segurança do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Controle Gerenciado da Microsoft 1494 – Plano de segurança do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Controle Gerenciado da Microsoft 1495 – Plano de segurança do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Controle Gerenciado da Microsoft 1496 – Plano de segurança do sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Plano de segurança do sistema | Plano / Coordenar com outras entidades organizacionais

**ID**: NIST SP 800-53 R4 PL-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1497 – Plano de segurança do sistema \| Planejar / coordenar com outras entidades organizacionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Regras de comportamento

**ID**: NIST SP 800-53 R4 PL-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1498 – Regras de comportamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Controle Gerenciado da Microsoft 1499 – Regras de comportamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Controle Gerenciado da Microsoft 1500 – Regras de comportamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Controle Gerenciado da Microsoft 1501 – Regras de comportamento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Regras de comportamento | Restrições de redes e redes sociais

**ID**: NIST SP 800-53 R4 PL-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1502 – Regras de comportamento \| Restrições de rede e mídias sociais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Arquitetura de segurança da informação

**ID**: NIST SP 800-53 R4 PL-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1503 – Arquitetura de segurança de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Controle Gerenciado da Microsoft 1504 – Arquitetura de segurança de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Controle Gerenciado da Microsoft 1505 – Arquitetura de segurança de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |A Microsoft implementa esse controle de Planejamento |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Segurança de pessoal

### <a name="personnel-security-policy-and-procedures"></a>Política e procedimentos de segurança de pessoal

**ID**: NIST SP 800-53 R4 PS-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1506 – Política e procedimentos de segurança de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Controle Gerenciado da Microsoft 1507 – Política e procedimentos de segurança de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Designação de risco de posição

**ID**: NIST SP 800-53 R4 PS-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1508 – Designação de risco de posição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Controle Gerenciado da Microsoft 1509 – Designação de risco de posição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Controle Gerenciado da Microsoft 1510 – Designação de risco de posição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Equipe de triagem

**ID**: NIST SP 800-53 R4 PS-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1511 – Triagem de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Controle Gerenciado da Microsoft 1512 – Triagem de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Seleção de pessoal | Informações com medidas especiais de proteção

**ID**: NIST SP 800-53 R4 PS-3 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1513 – Triagem de pessoal \| Informações com medidas de proteção especiais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Controle Gerenciado da Microsoft 1514 – Triagem de pessoal \| Informações com medidas de proteção especiais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Encerramento de equipe

**ID**: NIST SP 800-53 R4 PS-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1515 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Controle Gerenciado da Microsoft 1516 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Controle Gerenciado da Microsoft 1517 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Controle Gerenciado da Microsoft 1518 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Controle Gerenciado da Microsoft 1519 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Controle Gerenciado da Microsoft 1520 – Término de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Encerramento de equipe | Notificação automatizada

**ID**: NIST SP 800-53 R4 PS-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1521 – Término de pessoal \| Notificação automatizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Transferência de pessoal

**ID**: NIST SP 800-53 R4 PS-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1522 – Transferência de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Controle Gerenciado da Microsoft 1523 – Transferência de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Controle Gerenciado da Microsoft 1524 – Transferência de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Controle Gerenciado da Microsoft 1525 – Transferência de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Contratos de acesso

**ID**: NIST SP 800-53 R4 PS-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1526 – Contratos de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Controle Gerenciado da Microsoft 1527 – Contratos de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Controle Gerenciado da Microsoft 1528 – Contratos de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Segurança de pessoal de terceiros

**ID**: NIST SP 800-53 R4 PS-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1529 – Segurança de pessoal de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Controle Gerenciado da Microsoft 1530 – Segurança de pessoal de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Controle Gerenciado da Microsoft 1531 – Segurança de pessoal de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Controle Gerenciado da Microsoft 1532 – Segurança de pessoal de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Controle Gerenciado da Microsoft 1533 – Segurança de pessoal de terceiros](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Sanções ao pessoal

**ID**: NIST SP 800-53 R4 PS-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1534 – Sanções de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Controle Gerenciado da Microsoft 1535 – Sanções de pessoal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |A Microsoft implementa esse controle de Segurança de Pessoal |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Avaliação de risco

### <a name="risk-assessment-policy-and-procedures"></a>Política e procedimentos de avaliação de risco

**ID**: NIST SP 800-53 R4 RA-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1536 – Política e procedimentos de avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Controle Gerenciado da Microsoft 1537 – Política e procedimentos de avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Categorização de segurança

**ID**: NIST SP 800-53 R4 RA-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1538 – Categorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Controle Gerenciado da Microsoft 1539 – Categorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Controle Gerenciado da Microsoft 1540 – Categorização de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Avaliação de risco

**ID**: NIST SP 800-53 R4 RA-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1541 – Avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Controle Gerenciado da Microsoft 1542 – Avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Controle Gerenciado da Microsoft 1543 – Avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Controle Gerenciado da Microsoft 1544 – Avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Controle Gerenciado da Microsoft 1545 – Avaliação de risco](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

**ID**: NIST SP 800-53 R4 RA-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita as máquinas virtuais para detectar se elas estão executando uma solução de avaliação de vulnerabilidade compatível. Um componente principal de cada programa de segurança e risco cibernético é a identificação e a análise das vulnerabilidades. O tipo de preço padrão da Central de Segurança do Azure inclui a verificação de vulnerabilidade para as máquinas virtuais sem custo adicional. Além disso, a Central de Segurança pode implantar essa ferramenta automaticamente para você. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Controle Gerenciado da Microsoft 1546 – Verificação de vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Controle Gerenciado da Microsoft 1547 – Verificação de vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Controle Gerenciado da Microsoft 1548 – Verificação de vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Controle Gerenciado da Microsoft 1549 – Verificação de vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Controle Gerenciado da Microsoft 1550 – Verificação de vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[As vulnerabilidades nos bancos de dados SQL devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Verificação de vulnerabilidade | Recurso da ferramenta de atualização

**ID**: NIST SP 800-53 R4 RA-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1551 – Verificação de vulnerabilidade \| Capacidade da ferramenta de atualização](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Verificação de vulnerabilidade | Atualizar por frequência / Antes de nova verificação / Quando identificado

**ID**: NIST SP 800-53 R4 RA-5 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1552 – Verificação de vulnerabilidade \| Atualizar por frequência / Antes de nova verificação / Quando identificado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Verificação de vulnerabilidade | Amplitude / Profundidade de cobertura

**ID**: NIST SP 800-53 R4 RA-5 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1553 – Verificação de vulnerabilidade \| Amplitude / profundidade de cobertura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Verificação de vulnerabilidade | Informações detectáveis

**ID**: NIST SP 800-53 R4 RA-5 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1554 – Verificação de vulnerabilidade \| Informações detectáveis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Verificação de vulnerabilidade | Acesso privilegiado

**ID**: NIST SP 800-53 R4 RA-5 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1555 – Verificação de vulnerabilidade \| Acesso privilegiado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Verificação de vulnerabilidade | Análises de tendência automatizada

**ID**: NIST SP 800-53 R4 RA-5 (6) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1556 – Verificação de vulnerabilidade \| Análises automatizadas de tendências](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Verificação de vulnerabilidade | Examinar logs de auditoria históricos

**ID**: NIST SP 800-53 R4 RA-5 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1557 – Verificação de vulnerabilidade \| Examinar logs de auditoria históricos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Verificação de vulnerabilidade | Correlacionar informações de verificação

**ID**: NIST SP 800-53 R4 RA-5 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1558 – Verificação de vulnerabilidade \| Correlacionar informações de verificação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |A Microsoft implementa esse controle de Avaliação de Risco |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>Aquisições do sistema e de serviços

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Política e procedimentos de aquisições do sistema e de serviços

**ID**: NIST SP 800-53 R4 SA-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1559 – Política e procedimentos de aquisição de sistema e serviços](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Controle Gerenciado da Microsoft 1560 – Política e procedimentos de aquisição de sistema e serviços](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Alocação de recursos

**ID**: NIST SP 800-53 R4 SA-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1561 – Alocação de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Controle Gerenciado da Microsoft 1562 – Alocação de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Controle Gerenciado da Microsoft 1563 – Alocação de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Ciclo de vida de desenvolvimento de sistemas

**ID**: NIST SP 800-53 R4 SA-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1564 – Ciclo de vida de desenvolvimento de sistemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Controle Gerenciado da Microsoft 1565 – Ciclo de vida de desenvolvimento de sistemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Controle Gerenciado da Microsoft 1566 – Ciclo de vida de desenvolvimento de sistemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Controle Gerenciado da Microsoft 1567 – Ciclo de vida de desenvolvimento de sistemas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Processo de aquisição

**ID**: NIST SP 800-53 R4 SA-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1568 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Controle Gerenciado da Microsoft 1569 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Controle Gerenciado da Microsoft 1570 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Controle Gerenciado da Microsoft 1571 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Controle Gerenciado da Microsoft 1572 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Controle Gerenciado da Microsoft 1573 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Controle Gerenciado da Microsoft 1574 – Processo de aquisição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Processo de aquisição | Propriedades funcionais de controles de segurança

**ID**: NIST SP 800-53 R4 SA-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1575 – Processo de aquisição \| Propriedades funcionais dos controles de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Processo de aquisição | Informações de design/implementação referentes aos controles de segurança

**ID**: NIST SP 800-53 R4 SA-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1576 – Processo de aquisição \| Informações de design/implementação referentes aos controles de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Processo de aquisição | Plano de monitoramento contínuo

**ID**: NIST SP 800-53 R4 SA-4 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1577 – Processo de aquisição \| Plano de monitoramento contínuo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Processo de aquisição | Funções/portas/protocolos/serviços em uso

**ID**: NIST SP 800-53 R4 SA-4 (9) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1578 – Processo de aquisição \| Funções/portas/protocolos/serviços em uso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Processo de aquisição | Uso de produtos de PIV aprovados

**ID**: NIST SP 800-53 R4 SA-4 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1579 – Processo de aquisição \| Uso de produtos de PIV aprovados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Documentação do sistema de informações

**ID**: NIST SP 800-53 R4 SA-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1580 – Documentação do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Controle Gerenciado da Microsoft 1581 – Documentação do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Controle Gerenciado da Microsoft 1582 – Documentação do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Controle Gerenciado da Microsoft 1583 – Documentação do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Controle Gerenciado da Microsoft 1584 – Documentação do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Princípios de engenharia de segurança

**ID**: NIST SP 800-53 R4 SA-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1585 – Princípios da engenharia de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Serviços do sistema de informações externas

**ID**: NIST SP 800-53 R4 SA-9 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1586 – Serviços do sistema de informações externo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Controle Gerenciado da Microsoft 1587 – Serviços do sistema de informações externo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Controle Gerenciado da Microsoft 1588 – Serviços do sistema de informações externo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>Serviços do sistema de informações externas | Avaliações de risco/Aprovações organizacionais

**ID**: NIST SP 800-53 R4 SA-9 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1589 – Serviços do sistema de informações externo \| Avaliações de risco / Aprovações organizacionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Controle Gerenciado da Microsoft 1590 – Serviços do sistema de informações externo \| Avaliações de risco / Aprovações organizacionais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>Serviços do sistema de informações externas | Identificação de funções/portas/ protocolos/serviços

**ID**: NIST SP 800-53 R4 SA-9 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1591 – Serviços do sistema de informações externo \| Ident. de funções/portas/protocolos/serviços](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>Serviços do sistema de informações externas | Interesses consistentes de consumidores e provedores

**ID**: NIST SP 800-53 R4 SA-9 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1592 – Serviços do sistema de informações externo \| Interesses consistentes de consumidores e provedores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>Serviços do sistema de informações externas | Local de processamento, armazenamento e manutenção

**ID**: NIST SP 800-53 R4 SA-9 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1593 – Serviços do sistema de informações externo \| Processamento, armazenamento e localização do serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Gerenciamento de configurações do desenvolvedor

**ID**: NIST SP 800-53 R4 SA-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1594 – Gerenciamento de configuração do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Controle Gerenciado da Microsoft 1595 – Gerenciamento de configuração do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Controle Gerenciado da Microsoft 1596 – Gerenciamento de configuração do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Controle Gerenciado da Microsoft 1597 – Gerenciamento de configuração do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Controle Gerenciado da Microsoft 1598 – Gerenciamento de configuração do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Gerenciamento de configurações do desenvolvedor | Verificação de integridade de software/firmware

**ID**: NIST SP 800-53 R4 SA-10 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1599 – Gerenciamento de configuração do desenvolvedor \| Verificação de integridade do software / firmware](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Testes e avaliações de segurança do desenvolvedor

**ID**: NIST SP 800-53 R4 SA-11 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1600 – Testes e avaliação de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Controle Gerenciado da Microsoft 1601 – Testes e avaliação de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Controle Gerenciado da Microsoft 1602 – Testes e avaliação de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Controle Gerenciado da Microsoft 1603 – Testes e avaliação de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Controle Gerenciado da Microsoft 1604 – Testes e avaliação de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Testes e avaliações de segurança do desenvolvedor |Análise de código estático

**ID**: NIST SP 800-53 R4 SA-11 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1605 – Testes e avaliação de segurança do desenvolvedor \| Análise de código estática](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Testes e avaliações de segurança do desenvolvedor | Análise de ameças e vulnerabilidades

**ID**: NIST SP 800-53 R4 SA-11 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1606 – Testes e avaliação de segurança do desenvolvedor \| Análises de ameaça e vulnerabilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Testes e avaliações de segurança do desenvolvedor |Análise de código estático

**ID**: NIST SP 800-53 R4 SA-11 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1607 – Testes e avaliação de segurança do desenvolvedor \| Análise de código dinâmica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Proteção da cadeia suprimentos

**ID**: NIST SP 800-53 R4 SA-12 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1608 – Proteção da cadeia suprimentos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Processo, padrões e ferramentas de desenvolvimento

**ID**: NIST SP 800-53 R4 SA-15 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1609 – Processo, padrões e ferramentas de desenvolvimento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Controle Gerenciado da Microsoft 1610 – Processo, padrões e ferramentas de desenvolvimento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Treinamento disponibilizado pelo desenvolvedor

**ID**: NIST SP 800-53 R4 SA-16 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1611 – Treinamento fornecido pelo desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Arquitetura e design de segurança do desenvolvedor

**ID**: NIST SP 800-53 R4 SA-17 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1612 – Arquitetura e design de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Controle Gerenciado da Microsoft 1613 – Arquitetura e design de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Controle Gerenciado da Microsoft 1614 – Arquitetura e design de segurança do desenvolvedor](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |A Microsoft implementa esse controle de Aquisição de Sistema e Serviços |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>Proteção do Sistema e das Comunicações

### <a name="system-and-communications-protection-policy-and-procedures"></a>Política e procedimentos de proteção do sistema e das comunicações

**ID**: NIST SP 800-53 R4 SC-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1615 – Política e procedimentos de proteção do sistema e das comunicações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Controle Gerenciado da Microsoft 1616 – Política e procedimentos de proteção do sistema e das comunicações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Particionamento do aplicativo

**ID**: NIST SP 800-53 R4 SC-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1617 – Particionamento de aplicativo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Isolamento de função de segurança

**ID**: NIST SP 800-53 R4 SC-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1618 – Isolamento de função de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Informações em recursos compartilhados

**ID**: NIST SP 800-53 R4 SC-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1619 – Informações em recursos compartilhados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Negação de prevenção de serviço

**ID**: NIST SP 800-53 R4 SC-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Proteção contra DDoS do Azure Standard deve estar habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |A proteção contra DDoS padrão deve ser habilitada para todas as redes virtuais com uma sub-rede que é parte de um gateway de aplicativo com um IP público. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Controle Gerenciado da Microsoft 1620 – Proteção contra negação de serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Disponibilidade de recursos

**ID**: NIST SP 800-53 R4 SC-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1621 – Disponibilidade de recurso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Proteção de limite

**ID**: NIST SP 800-53 R4 SC-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As recomendações da proteção de rede adaptável devem ser aplicadas nas máquinas virtuais para a Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |A Central de Segurança do Azure analisa os padrões de tráfego das máquinas virtuais para a Internet e fornece recomendações de regra do Grupo de Segurança de Rede que reduzem a possível superfície de ataque |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Todas as portas de rede devem ser restritas nos grupos de segurança de rede associados à sua máquina virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |A Central de Segurança do Azure identificou algumas das regras de entrada de seus grupos de segurança de rede como permissivas demais. As regras de entrada não devem permitir o acesso por meio de intervalos "Qualquer" ou "Internet". Isso tem o potencial de tornar seus recursos alvos de invasores. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Controle Gerenciado da Microsoft 1622 – Proteção de limite](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Controle Gerenciado da Microsoft 1623 – Proteção de limite](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Controle Gerenciado da Microsoft 1624 – Proteção de limite](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[As contas de armazenamento devem restringir o acesso da rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso da rede às contas de armazenamento deve ser restrito. Configure as regras de rede de tal forma que somente os aplicativos das redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da Internet, o acesso pode ser permitido para o tráfego de redes virtuais específicas do Azure ou para intervalos de endereços IP públicos da Internet |Audit, Deny, desabilitado |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Proteção de limite | Pontos de acesso

**ID**: NIST SP 800-53 R4 SC-7 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Controle Gerenciado da Microsoft 1625 – Proteção de limite \| Pontos de acesso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**ID**: NIST SP 800-53 R4 SC-7 (4) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |O possível acesso JIT (Just In Time) da rede será monitorado pela Central de Segurança do Azure como recomendação |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Controle Gerenciado da Microsoft 1626 – Proteção de limite \| Serviços de telecomunicações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Controle Gerenciado da Microsoft 1627 – Proteção de limite \| Serviços de telecomunicações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Controle Gerenciado da Microsoft 1628 – Proteção de limite \| Serviços de telecomunicações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Controle Gerenciado da Microsoft 1629 – Proteção de limite \| Serviços de telecomunicações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Controle Gerenciado da Microsoft 1630 – Proteção de limite \| Serviços de telecomunicações externos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Proteção de limite | Negar por padrão/permitir por exceção

**ID**: NIST SP 800-53 R4 SC-7 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1631 – Proteção de limite \| Negar por padrão / permitir por exceção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Proteção de limite | Impedir divisão de túnel para dispositivos remotos

**ID**: NIST SP 800-53 R4 SC-7 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1632 – Proteção de limite \| Impedir divisão de túnel para dispositivos remotos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Proteção de limite | Rotear o tráfego para os servidores proxy autenticados

**ID**: NIST SP 800-53 R4 SC-7 (8) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1633 – Proteção de limite \| Rotear o tráfego para os servidores proxy autenticados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Proteção de limite | Impedir o vazamento não autorizado

**ID**: NIST SP 800-53 R4 SC-7 (10) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1634 – Proteção de limite \| Impedir a exfiltração não autorizada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Proteção de limite | Proteção baseada em host

**ID**: NIST SP 800-53 R4 SC-7 (12) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1635 – Proteção de limite \| Proteção baseada em host](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Proteção de limite | Isolamento de ferramentas de segurança/mecanismos/componentes de suporte

**ID**: NIST SP 800-53 R4 SC-7 (13) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1636 – Proteção de limite \| Isolamento de componentes de suporte / mecanismos / ferramentas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Proteção de limite | Falha na segurança

**ID**: NIST SP 800-53 R4 SC-7 (18) **Propriedade**: Microsoft

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1637 – Proteção de limite \| Segurança contra falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Proteção de limite | Isolamento dinâmico/diferenciação

**ID**: NIST SP 800-53 R4 SC-7 (20) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1638 – Proteção de limite \| Isolamento / segregação dinâmica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Proteção de limite | Isolamento de componentes do sistema de informações

**ID**: NIST SP 800-53 R4 SC-7 (21) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1639 – Proteção de limite \| Isolamento de componentes do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>Confidencialidade e integridade de transmissão

**ID**: NIST SP 800-53 R4 SC-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1640 – Confidencialidade e integridade de transmissões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Confidencialidade e integridade de transmissão | Proteção física criptográfica ou alternativa

**ID**: NIST SP 800-53 R4 SC-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O aplicativo de API só deve estar acessível via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[O aplicativo de funções deve ser acessível apenas por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Controle Gerenciado da Microsoft 1641 – Confidencialidade e integridade de transmissões \| Proteção física criptográfica ou alternativa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria de habilitação de somente conexões via SSL ao Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito dos ataques de camada de rede, como man-in-the-middle, espionagem e sequestro de sessão |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Aplicativo Web deve ser acessível somente por HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |O uso do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Os servidores Web do Windows devem ser configurados para usar protocolos de comunicação segura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Para proteger a privacidade das informações comunicadas pela Internet, seus servidores Web devem usar a versão mais recente do protocolo de criptografia padrão do setor, o protocolo TLS. O TLS protege as comunicações em uma rede usando certificados de segurança para criptografar uma conexão entre computadores. O TLS 1.3 é mais rápido e seguro do que as versões anteriores: TLS 1.0 a 1.2 e SSL 2-3, que são considerados protocolos herdados. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>Desconexão da rede

**ID**: NIST SP 800-53 R4 SC-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1642 – Desconexão de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Estabelecimento de Chave de Criptografia e Gerenciamento

**ID**: NIST SP 800-53 R4 SC-12 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1643 – Estabelecimento e gerenciamento de chave de criptografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Disponibilidade

**ID**: NIST SP 800-53 R4 SC-12 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1644 – Estabelecimento e gerenciamento de chave de criptografia \| Disponibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Chaves simétricas

**ID**: NIST SP 800-53 R4 SC-12 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1645 – Estabelecimento e gerenciamento de chave de criptografia \| Chaves simétricas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Chaves assimétricas

**ID**: NIST SP 800-53 R4 SC-12 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1646 – Estabelecimento e gerenciamento de chave de criptografia \| Chaves assimétricas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Proteção criptográfica

**ID**: NIST SP 800-53 R4 SC-13 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1647 – Proteção criptográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>Dispositivos de computação colaborativos

**ID**: NIST SP 800-53 R4 SC-15 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1648 – Dispositivos de computação colaborativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Controle Gerenciado da Microsoft 1649 – Dispositivos de computação colaborativos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Certificados de infraestrutura de chave pública

**ID**: NIST SP 800-53 R4 SC-17 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1650 – Certificados de infraestrutura de chave pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Código móvel

**ID**: NIST SP 800-53 R4 SC-18 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1651 – Código móvel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Controle Gerenciado da Microsoft 1652 – Código móvel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Controle Gerenciado da Microsoft 1653 – Código móvel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Protocolo IP de Voice Over

**ID**: NIST SP 800-53 R4 SC-19 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1654 – Protocolo VoIP](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Controle Gerenciado da Microsoft 1655 – Protocolo VoIP](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Serviço de resolução de nome/endereço seguro (fonte autoritativa)

**ID**: NIST SP 800-53 R4 SC-20 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1656 – Serviço seguro de resolução de nome/endereço (fonte autoritativa)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Controle Gerenciado da Microsoft 1657 – Serviço seguro de resolução de nome/endereço (fonte autoritativa)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Serviço de resolução de nome/endereço seguro (Resolvedor recursivo ou de cache)

**ID**: NIST SP 800-53 R4 SC-21 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1658 – Serviço seguro de resolução de nome/endereço (Resolvedor recursivo ou de cache)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Arquitetura e o provisionamento para serviço de resolução de nome/endereço

**ID**: NIST SP 800-53 R4 SC-22 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1659 – Arquitetura e provisionamento para serviço de resolução de nome/endereço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Autenticidade de sessão

**ID**: NIST SP 800-53 R4 SC-23 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1660 – Autenticidade da sessão](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Autenticidade de sessão | Invalidar identificadores de sessão no logout

**ID**: NIST SP 800-53 R4 SC-23 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1661 – Autenticidade da sessão \| Invalidar identificadores da sessão no logoff](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Falha no estado conhecido

**ID**: NIST SP 800-53 R4 SC-24 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1662 – Falha em estado conhecido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Proteção de informações em repouso

**ID**: NIST SP 800-53 R4 SC-28 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1663 – Proteção de informações em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Proteção de informações de em repouso | Proteção de criptografia

**ID**: NIST SP 800-53 R4 SC-28 (1) **Propriedade**: Cliente

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[A criptografia de disco deve ser aplicada em Máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem uma criptografia de disco habilitada serão monitoradas pela Central de Segurança do Azure como recomendações. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Controle Gerenciado da Microsoft 1664 – Proteção de informações em repouso \| Proteção criptográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[A Transparent Data Encryption em bancos de dados SQL deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A Transparent Data Encryption deve ser habilitada para proteger os dados em repouso e atender aos requisitos de conformidade |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>Isolamento do processo

**ID**: NIST SP 800-53 R4 SC-39 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1665 – Isolamento de processo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |A Microsoft implementa esse controle de Proteção do Sistema e de Comunicações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>Integridade do Sistema e das Informações

### <a name="system-and-information-integrity-policy-and-procedures"></a>Política e procedimentos de integridade do sistema e das informações

**ID**: NIST SP 800-53 R4 SI-1 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1666 – Política e procedimentos de integridade do sistema e das informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Controle Gerenciado da Microsoft 1667 – Política e procedimentos de integridade do sistema e das informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Correção de falhas

**ID**: NIST SP 800-53 R4 SI-2 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita as máquinas virtuais para detectar se elas estão executando uma solução de avaliação de vulnerabilidade compatível. Um componente principal de cada programa de segurança e risco cibernético é a identificação e a análise das vulnerabilidades. O tipo de preço padrão da Central de Segurança do Azure inclui a verificação de vulnerabilidade para as máquinas virtuais sem custo adicional. Além disso, a Central de Segurança pode implantar essa ferramenta automaticamente para você. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Controle Gerenciado da Microsoft 1668 – Correção de falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Controle Gerenciado da Microsoft 1669 – Correção de falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Controle Gerenciado da Microsoft 1670 – Correção de falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Controle Gerenciado da Microsoft 1671 – Correção de falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Faça uma auditoria para verificar se faltam atualizações de segurança do sistema e atualizações críticas que devem ser instaladas para garantir que os seus conjuntos de dimensionamento de máquinas virtuais Windows e Linux estejam seguros. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[As atualizações do sistema devem ser instaladas em suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |A falta de atualizações do sistema de segurança em seus servidores será monitorada pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não atenderem à linha de base configurada serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Faça a auditoria das vulnerabilidades do SO nos seus conjuntos de dimensionamento de máquinas virtuais para protegê-los contra ataques. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[As vulnerabilidades nos bancos de dados SQL devem ser corrigidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore os resultados da verificação da avaliação de vulnerabilidade e as recomendações sobre como corrigir vulnerabilidades do banco de dados. |AuditIfNotExists, desabilitado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Correção de falhas | Gerenciamento central

**ID**: NIST SP 800-53 R4 SI-2 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1672 – Correção de falhas \| Gerenciamento central](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Correção de falhas | Status da correção automática de falhas

**ID**: NIST SP 800-53 R4 SI-2 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1673 – Correção de falhas \| Status da correção automática de falhas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correção de falhas | Tempo para corrigir falhas / Benchmarks para ações corretivas

**ID**: NIST SP 800-53 R4 SI-2 (3) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1674 – Correção de falhas \| Tempo para corrigir falhas / Benchmarks para ações corretivas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Controle Gerenciado da Microsoft 1675 – Correção de falhas \| Tempo para corrigir falhas / Benchmarks para ações corretivas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Proteção contra código mal-intencionado

**ID**: NIST SP 800-53 R4 SI-3 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Faça a auditoria da existência de uma solução de proteção de ponto de extremidade e da sua integridade nos seus conjuntos de dimensionamento de máquinas virtuais, para protegê-los contra ameaças e vulnerabilidades. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Controle Gerenciado da Microsoft 1676 – Proteção contra código malicioso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Controle Gerenciado da Microsoft 1677 – Proteção contra código malicioso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Controle Gerenciado da Microsoft 1678 – Proteção contra código malicioso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Controle Gerenciado da Microsoft 1679 – Proteção contra código malicioso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Monitorar o Endpoint Protection ausente na Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Proteção contra código mal-intencionado | Gerenciamento central

**ID**: NIST SP 800-53 R4 SI-3 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Faça a auditoria da existência de uma solução de proteção de ponto de extremidade e da sua integridade nos seus conjuntos de dimensionamento de máquinas virtuais, para protegê-los contra ameaças e vulnerabilidades. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Controle Gerenciado da Microsoft 1680 – Proteção contra código malicioso \| Gerenciamento central](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Monitorar o Endpoint Protection ausente na Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servidores sem um agente do Endpoint Protection instalado serão monitorados pela Central de Segurança do Azure como recomendações |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Proteção contra código mal-intencionado | Atualizações automáticas

**ID**: NIST SP 800-53 R4 SI-3 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1681 – Proteção contra código malicioso \| Atualizações automáticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Proteção contra código mal-intencionado | Detecção baseada em não assinatura

**ID**: NIST SP 800-53 R4 SI-3 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1682 – Proteção contra código malicioso \| Detecção baseada em não assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**ID**: NIST SP 800-53 R4 SI-4 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Segurança de dados avançada deve ser habilitada na Instância Gerenciada de SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada Instância Gerenciada de SQL sem a segurança de dados avançada. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A Segurança de Dados Avançada deve ser habilitada nos servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança de Dados Avançada |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Auditar o workspace do Log Analytics para a VM – Relatar incompatibilidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Relata as VMs como sem conformidade se elas não estão registrando no log do workspace do Log Analytics especificado na atribuição de política ou de iniciativa. |auditoria |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[O Agente do Log Analytics deverá ser habilitado para imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Relata máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[O agente do Log Analytics deverá ser habilitado em conjuntos de dimensionamento de máquinas virtuais para obter imagens das máquinas virtuais listadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Relata os conjuntos de dimensionamento de máquinas virtuais como não compatíveis, caso a imagem da máquina virtual não esteja na lista definida e o agente não esteja instalado. |AuditIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Controle Gerenciado da Microsoft 1683 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Controle Gerenciado da Microsoft 1684 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Controle Gerenciado da Microsoft 1685 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Controle Gerenciado da Microsoft 1686 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Controle Gerenciado da Microsoft 1687 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Controle Gerenciado da Microsoft 1688 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Controle Gerenciado da Microsoft 1689 – Monitoramento do sistema de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitoramento do sistema de informações | Sistema de detecção de intrusão em todo o sistema

**ID**: NIST SP 800-53 R4 SI-4 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1690 – Monitoramento do sistema de informações \| Sistema de detecção de intrusão em todo o sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitoramento do sistema de informações | Ferramentas automatizadas para análise em tempo real

**ID**: NIST SP 800-53 R4 SI-4 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1691 – Monitoramento do sistema de informações \| Ferramentas automatizadas para análise em tempo real](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitoramento do sistema de informações | Tráfego de comunicações de entrada e saída

**ID**: NIST SP 800-53 R4 SI-4 (4) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1692 – Monitoramento do sistema de informações \| Tráfego de comunicações de entrada e saída](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Monitoramento do sistema de informações | Alertas gerados pelo sistema

**ID**: NIST SP 800-53 R4 SI-4 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1693 – Monitoramento do sistema de informações \| Alertas gerados pelo sistema](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitoramento do sistema de informações | Analisar anomalias no tráfego de comunicações

**ID**: NIST SP 800-53 R4 SI-4 (11) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1694 – Monitoramento do sistema de informações \| Analisar anomalias no tráfego de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitoramento do sistema de informações | Detecção de intrusão sem fio

**ID**: NIST SP 800-53 R4 SI-4 (14) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1695 – Monitoramento do sistema de informações \| Detecção de intrusão sem fio](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitoramento do sistema de informações | Correlacionar as informações de monitoramento

**ID**: NIST SP 800-53 R4 SI-4 (16) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1696 – Monitoramento do sistema de informações \| Correlacionar as informações de monitoramento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitoramento do sistema de informações | Analisar o tráfego/vazamento oculto

**ID**: NIST SP 800-53 R4 SI-4 (18) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1697 – Monitoramento do sistema de informações \| Analisar o tráfego/exfiltração oculta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitoramento do sistema de informações | Indivíduos que apresentam um risco maior

**ID**: NIST SP 800-53 R4 SI-4 (19) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1698 – Monitoramento do sistema de informações \| Indivíduos que apresentam um risco maior](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Monitoramento do sistema de informações | Usuários privilegiados

**ID**: NIST SP 800-53 R4 SI-4 (20) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1699 – Monitoramento do sistema de informações \| Usuários privilegiados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitoramento do sistema de informações | Serviços de rede não autorizados

**ID**: NIST SP 800-53 R4 SI-4 (22) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1700 – Monitoramento do sistema de informações \| Serviços de rede não autorizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Monitoramento do sistema de informações | Dispositivos baseados em host

**ID**: NIST SP 800-53 R4 SI-4 (23) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1701 – Monitoramento do sistema de informações \| Dispositivos baseados em host](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitoramento do sistema de informações | Indicadores de comprometimento

**ID**: NIST SP 800-53 R4 SI-4 (24) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1702 – Monitoramento do sistema de informações \| Indicadores de comprometimento](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Alertas, avisos e diretivas de segurança

**ID**: NIST SP 800-53 R4 SI-5 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1703 – Alertas, avisos e diretivas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Controle Gerenciado da Microsoft 1704 – Alertas, avisos e diretivas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Controle Gerenciado da Microsoft 1705 – Alertas, avisos e diretivas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Controle Gerenciado da Microsoft 1706 – Alertas, avisos e diretivas de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alertas, avisos e diretivas de segurança | Alertas e avisos automatizados

**ID**: NIST SP 800-53 R4 SI-5 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1707 – Alertas, avisos e diretivas de segurança \| Alertas e avisos automatizados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Verificação de função de segurança

**ID**: NIST SP 800-53 R4 SI-6 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1708 – Verificação da função de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Controle Gerenciado da Microsoft 1709 – Verificação da função de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Controle Gerenciado da Microsoft 1710 – Verificação da função de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Controle Gerenciado da Microsoft 1711 – Verificação da função de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Integridade de software, firmware e informações

**ID**: NIST SP 800-53 R4 SI-7 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1712 – Integridade de software, firmware e informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integridade de software, firmware e informações | Verificações de integridade

**ID**: NIST SP 800-53 R4 SI-7 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1713 – Integridade de software, firmware e informações \| Verificações de integridade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integridade de software, firmware e informações | Notificações automatizadas de violações de integridade

**ID**: NIST SP 800-53 R4 SI-7 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1714 – Integridade de software, firmware e informações \| Notificações automatizadas de violações de integridade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integridade de software, firmware e informações | Resposta automatizada de violações de integridade

**ID**: NIST SP 800-53 R4 SI-7 (5) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1715 – Integridade de software, firmware e informações \| Resposta automatizada a violações de integridade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integridade de software, firmware e informações | Integração de detecção e resposta

**ID**: NIST SP 800-53 R4 SI-7 (7) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1716 – Integridade de software, firmware e informações \| Integração de detecção e resposta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integridade de software, firmware e informações | Código executável binário ou de máquina

**ID**: NIST SP 800-53 R4 SI-7 (14) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1717 – Integridade de software, firmware e informações \| Código executável binário ou de máquina](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Controle Gerenciado da Microsoft 1718 – Integridade de software, firmware e informações \| Código executável binário ou de máquina](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>Proteção contra spam

**ID**: NIST SP 800-53 R4 SI-8 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1719 – Proteção contra spam](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Controle Gerenciado da Microsoft 1720 – Proteção contra spam](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>Proteção contra spam | Gerenciamento central

**ID**: NIST SP 800-53 R4 SI-8 (1) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1721 – Proteção contra spam \| Gerenciamento central](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>Proteção contra spam | Atualizações automáticas

**ID**: NIST SP 800-53 R4 SI-8 (2) **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1722 – Proteção contra spam \| Atualizações automáticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Validação de entrada de informações

**ID**: NIST SP 800-53 R4 SI-10 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1723 – Validação de entrada de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Tratamento de erros

**ID**: NIST SP 800-53 R4 SI-11 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1724 – Tratamento de erro](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Controle Gerenciado da Microsoft 1725 – Tratamento de erro](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Manipulação e retenção de informações

**ID**: NIST SP 800-53 R4 SI-12 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1726 – Manipulação e retenção de informações](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Proteção de memória

**ID**: NIST SP 800-53 R4 SI-16 **Propriedade**: Compartilhado

|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle Gerenciado da Microsoft 1727 – Proteção de memória](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |A Microsoft implementa esse controle de Integridade do Sistema e de Informações |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> A disponibilidade de definições específicas do Azure Policy pode variar no Azure Governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

Artigos adicionais sobre o Azure Policy:

- Visão geral da [Conformidade Regulatória](../concepts/regulatory-compliance.md).
- Consulte a [estrutura de definição da iniciativa](../concepts/initiative-definition-structure.md).
- Veja outros exemplos em [Amostras do Azure Policy](./index.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
