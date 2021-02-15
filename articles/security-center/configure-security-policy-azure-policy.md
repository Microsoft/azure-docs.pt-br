---
title: Criar e editar políticas de segurança de Azure Policy usando a API REST
description: Saiba mais sobre o gerenciamento de políticas de Azure Policy por meio de uma API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fcfed585aece4fd57c085e0c9f6fdcd6cbeae74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448356"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configurar uma política de segurança no Azure Policy usando a API REST

Como parte da integração nativa com o Azure Policy, a Central de Segurança do Azure permite que você use a API REST do Azure Policy para criar atribuições de política. As instruções a seguir orientam você a criar as atribuições da política e personalizar atribuições existentes. 

Conceitos importantes no Azure Policy: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é um aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura, etc.) 

A Central de Segurança tem uma iniciativa interna que inclui todas as suas políticas de segurança. Para avaliar as políticas da central de segurança em seus recursos do Azure, você deve criar uma atribuição no grupo de gerenciamento ou assinatura que deseja avaliar.

A iniciativa interna tem todas as políticas da Central de Segurança habilitadas por padrão. Você pode optar por desabilitar determinadas políticas da iniciativa interna. Por exemplo, para aplicar todas as políticas da central de segurança, exceto o **Firewall do aplicativo Web**, altere o valor do parâmetro de efeito da política para **desabilitado**. 

## <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua essas variáveis:

- **{Scope}** Insira o nome do grupo de gerenciamento ou da assinatura à qual você está aplicando a política.
- **{policyAssignmentName}** insira o [nome da atribuição de política relevante](#policy-names).
- **{name}** insira seu nome ou o nome do administrador que aprovou a alteração da política.

Este exemplo mostra como atribuir a iniciativa interna da Central de Segurança a uma assinatura ou um grupo de gerenciamento
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Este exemplo mostra como atribuir a iniciativa interna da Central de Segurança a uma assinatura, com as seguintes políticas desabilitadas: 

- Atualizações do sistema (“systemUpdatesMonitoringEffect”) 

- Configurações de segurança ("systemConfigurationsMonitoringEffect") 

- Proteção do ponto de extremidade ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Este exemplo mostra como remover uma atribuição:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Referência dos nomes de políticas <a name="policy-names"></a>

|Nome da política na Central de Segurança|Nome da política exibido no Azure Policy |Nome do parâmetro de efeito da política|
|----|----|----|
|Criptografia do SQL |monitora o banco de dados SQL não criptografado na Central de Segurança do Azure |sqlEncryptionMonitoringEffect| 
|Auditoria do SQL |monitora o banco de dados SQL não auditado na Central de Segurança do Azure |sqlAuditingMonitoringEffect|
|Atualizações do sistema |monitora atualizações de sistemas ausentes na Central de Segurança do Azure |systemUpdatesMonitoringEffect|
|Criptografia do armazenamento |Auditar a criptografia de blob ausente para contas de armazenamento |storageEncryptionMonitoringEffect|
|Acesso à rede JIT |Monitorar um possível acesso JIT (just-in-time) de rede na central de segurança do Azure |jitNetworkAccessMonitoringEffect |
|Controles de aplicativo adaptáveis |Monitorar possíveis listas de permissões de aplicativo na central de segurança do Azure |adaptiveApplicationControlsMonitoringEffect|
|Grupos de segurança de rede |monitora o acesso permissivo à rede na Central de Segurança do Azure |networkSecurityGroupsMonitoringEffect| 
|Configurações de segurança |monitora a vulnerabilidades do sistema operacional na Central de Segurança do Azure |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |monitora o Endpoint Protection ausente na Central de Segurança do Azure |endpointProtectionMonitoringEffect |
|Criptografia do disco |monitora discos não criptografados da VM na Central de Segurança do Azure |diskEncryptionMonitoringEffect|
|Avaliação de vulnerabilidade |monitora vulnerabilidades de máquinas virtuais na Central de Segurança do Azure |vulnerabilityAssessmentMonitoringEffect|
|Firewall do aplicativo Web |monitora o aplicativo Web desprotegido na Central de Segurança do Azure |webApplicationFirewallMonitoringEffect |
|Firewall da próxima geração |monitora pontos de extremidade de rede desprotegidos na Central de Segurança do Azure| |


## <a name="next-steps"></a>Próximas etapas

Para obter outros materiais relacionados, confira os seguintes artigos: 

- [Políticas de segurança personalizadas](custom-security-policies.md)
- [Visão geral da política de segurança](tutorial-security-policy.md)