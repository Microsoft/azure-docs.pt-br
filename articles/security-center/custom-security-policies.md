---
title: Criar políticas de segurança personalizadas na Central de Segurança do Azure | Microsoft Docs
description: Definições de política personalizada do Azure monitoradas na Central de Segurança do Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a901e71da640f8413e5714ad59073324f582c1b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441050"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Criar iniciativas e políticas de segurança personalizadas

Para ajudar a proteger seus sistemas e ambientes, a Central de Segurança do Azure gera recomendações de segurança. Essas recomendações são baseadas nas melhores práticas do setor, que são incorporadas à política de segurança padrão genérica fornecida para todos os clientes. Elas também podem vir do conhecimento da Central de Segurança de padrões do setor e regulatórios.

Com esse recurso, você pode adicionar suas *iniciativas* personalizadas. Em seguida, você receberá recomendações se o seu ambiente não seguir as políticas que você criar. As iniciativas personalizadas que você criar aparecerão junto com as iniciativas internas do painel de conformidade regulatória, conforme descrito no tutorial [Aprimorar a conformidade regulatória](security-center-compliance-dashboard.md).

Conforme discutido na [documentação do Azure Policy](../governance/policy/concepts/definition-structure.md#definition-location), quando você especifica uma localização para sua iniciativa personalizada, ele deve ser um grupo de gerenciamento ou uma assinatura. 

> [!TIP]
> Para obter uma visão geral dos principais conceitos desta página, consulte [o que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua assinatura 

1. Na barra lateral da Central de Segurança, abra a página **Política de segurança**.

1. Selecione uma assinatura ou grupo de gerenciamento ao qual você gostaria de adicionar uma iniciativa personalizada.

    [![Como selecionar uma assinatura para a qual você criará sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Você deve adicionar padrões personalizados no nível da assinatura (ou superior) para que eles sejam avaliados e exibidos na Central de Segurança. 
    >
    > Quando você adiciona um padrão personalizado, ele atribui uma *iniciativa* a esse escopo. Portanto, recomendamos que você selecione o escopo mais amplo necessário para essa atribuição.

1. Na página Política de segurança, em Suas iniciativas personalizadas, clique em **Adicionar uma iniciativa personalizada**.

    [![Clique em adicionar uma iniciativa personalizada](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A seguinte página é exibida:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página Adicionar iniciativas personalizadas, examine a lista de políticas personalizadas já criadas em sua organização. Se você vir uma que deseja atribuir à sua assinatura, clique em **Adicionar**. Se não houver uma iniciativa na lista que atenda às suas necessidades, pule esta etapa.

1. Para criar uma exibição personalizada:

    1. Clique em **Criar**.
    1. Insira a localização e o nome da definição.
    1. Selecione as políticas a serem incluídas e clique em **Adicionar**.
    1. Insira os parâmetros desejados.
    1. Clique em **Save** (Salvar).
    1. Na página Adicionar iniciativas personalizadas, clique em Atualizar. Sua nova iniciativa será mostrada como disponível.
    1. Clique em **Adicionar** e atribua-a à sua assinatura.

    > [!NOTE]
    > A criação de iniciativas requer credenciais de proprietário da assinatura. Para obter mais informações sobre as funções do Azure, confira [Permissões na Central de Segurança do Azure](security-center-permissions.md).

    Sua nova iniciativa entra em vigor e você pode ver o impacto das duas seguintes maneiras:

    * Na barra lateral da Central de Segurança, em Política e Conformidade, escolha **Conformidade regulatória**. O painel de conformidade é aberto para mostrar sua nova iniciativa personalizada junto com as iniciativas internas.
    
    * Você começará a receber recomendações se o seu ambiente não seguir as políticas que você definiu.

1. Para ver as recomendações resultantes para sua política, clique em **Recomendações** na barra lateral para abrir a página de recomendações. As recomendações serão exibidas com um rótulo "Personalizado" e estarão disponíveis em aproximadamente uma hora.

    [![Recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configurar uma política de segurança no Azure Policy usando a API REST

Como parte da integração nativa com o Azure Policy, a Central de Segurança do Azure permite que você use a API REST do Azure Policy para criar atribuições de política. As instruções a seguir orientam você a criar as atribuições da política e personalizar atribuições existentes. 

Conceitos importantes no Azure Policy: 

- Uma **definição de política** é uma regra 

- Uma **iniciativa** é uma coleção de definições de política (regras) 

- Uma **atribuição** é um aplicativo de uma iniciativa ou uma política para um escopo específico (grupo de gerenciamento, assinatura, etc.) 

A central de segurança tem uma iniciativa interna, o benchmark de segurança do Azure, que inclui todas as suas políticas de segurança. Para avaliar as políticas da central de segurança em seus recursos do Azure, você deve criar uma atribuição no grupo de gerenciamento ou assinatura que deseja avaliar.

A iniciativa interna tem todas as políticas da Central de Segurança habilitadas por padrão. Você pode optar por desabilitar determinadas políticas da iniciativa interna. Por exemplo, para aplicar todas as políticas da central de segurança, exceto o **Firewall do aplicativo Web**, altere o valor do parâmetro de efeito da política para **desabilitado**.

## <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua essas variáveis:

- **{Scope}** Insira o nome do grupo de gerenciamento ou da assinatura à qual você está aplicando a política
- **{policyAssignmentName}** Insira o nome da atribuição de política relevante
- **{Name}** insira seu nome ou o nome do administrador que aprovou a alteração da política

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

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Aprimore suas recomendações personalizadas com informações detalhadas

As recomendações internas fornecidas com a Central de Segurança do Azure incluem detalhes como níveis de severidade e instruções de correção. Se você quiser adicionar esse tipo de informação às suas recomendações personalizadas para que elas apareçam no portal do Azure ou onde quer que você acesse suas recomendações, precisará usar a API REST. 

Os dois tipos de informações que você pode adicionar são:

- **RemediationDescription** – cadeia de caracteres
- **Severidade** – enumeração [baixa, média, alta]

Os metadados devem ser adicionados à definição de política para uma política que faz parte da iniciativa personalizada. Ela deve estar na propriedade ‘securityCenter’, conforme mostrado:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Abaixo está um exemplo de uma política personalizada, incluindo a propriedade metadados/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Para obter outro exemplo de como usar a propriedade securityCenter, confira [esta seção da documentação da API REST](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar políticas de segurança personalizadas. 

Para obter outros materiais relacionados, confira os seguintes artigos: 

- [Visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança internas](./policy-reference.md)