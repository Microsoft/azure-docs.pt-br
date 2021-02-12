---
title: Criar políticas de segurança personalizadas na Central de Segurança do Azure | Microsoft Docs
description: Definições de política personalizada do Azure monitoradas na Central de Segurança do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/03/2020
ms.author: memildin
ms.openlocfilehash: 8d2b43ab57ea7a3b1dc1d13bcdea9932ccecb9dc
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559024"
---
# <a name="using-custom-security-policies"></a>Como usar políticas de segurança personalizadas

Para ajudar a proteger seus sistemas e ambientes, a Central de Segurança do Azure gera recomendações de segurança. Essas recomendações são baseadas nas melhores práticas do setor, que são incorporadas à política de segurança padrão genérica fornecida para todos os clientes. Elas também podem vir do conhecimento da Central de Segurança de padrões do setor e regulatórios.

Com esse recurso, você pode adicionar suas *iniciativas* personalizadas. Em seguida, você receberá recomendações se o seu ambiente não seguir as políticas que você criar. As iniciativas personalizadas que você criar aparecerão junto com as iniciativas internas do painel de conformidade regulatória, conforme descrito no tutorial [Aprimorar a conformidade regulatória](security-center-compliance-dashboard.md).

Conforme discutido na [documentação do Azure Policy](../governance/policy/concepts/definition-structure.md#definition-location), quando você especifica uma localização para sua iniciativa personalizada, ele deve ser um grupo de gerenciamento ou uma assinatura. 

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