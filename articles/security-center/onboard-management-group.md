---
title: Integrar um grupo de gerenciamento na Central de Segurança do Azure
description: Saiba como usar uma definição fornecida do Azure Policy para habilitar a Central de Segurança do Azure para todas as assinaturas de um grupo de gerenciamento.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016552"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Habilitar a Central de Segurança em todas as assinaturas de um grupo de gerenciamento

Você pode usar o Azure Policy para habilitar a Central de Segurança do Azure em todas as assinaturas do Azure do mesmo MG (grupo de gerenciamento). Isso é mais conveniente do que acessá-las individualmente no portal e funciona mesmo que as assinaturas pertençam a proprietários diferentes. 

Para integrar um grupo de gerenciamento e todas as respectivas assinaturas:

1. Como um usuário com permissões de **Administrador de Segurança**, abra o Azure Policy e pesquise a definição **Habilitar a Central de Segurança do Azure na sua assinatura**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="A definição do Azure Policy Habilitar a Central de Segurança do Azure na sua assinatura":::

1. Selecione **Atribuir** e defina o escopo para o nível do MG.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Atribuindo a definição Habilitar a Central de Segurança do Azure na sua assinatura":::

    > [!TIP]
    > Além do escopo, não há nenhum parâmetro obrigatório.

1. Selecione **Criar uma tarefa de correção** para garantir que todas as assinaturas existentes que não têm a Central de Segurança habilitada sejam integradas.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Criando uma tarefa de correção para a definição do Azure Policy Habilitar a Central de Segurança do Azure na sua assinatura":::

1. Quando for atribuída, a definição:

    1. Detectará todas as assinaturas no MG que ainda não estão registradas na Central de Segurança.
    1. Marcará essas assinaturas como "não compatível".
    1. Marcará como "em conformidade" todas as assinaturas registradas (independentemente de terem o Azure Defender ativado ou desativado).

    A tarefa de correção habilitará a Central de Segurança gratuitamente nas assinaturas não compatíveis.

> [!IMPORTANT]
> A definição de política habilitará a Central de Segurança apenas em assinaturas **existentes**. Para registrar as assinaturas recém-criadas, abra a guia de conformidade, selecione as assinaturas não compatíveis pertinentes e crie uma tarefa de correção. Repita essa etapa quando você tiver uma ou mais assinaturas novas que desejar monitorar com a Central de Segurança.

## <a name="optional-modifications"></a>Modificações opcionais

Há várias opções para você modificar a definição do Azure Policy: 

- **Definir a conformidade de maneira diferente** – a política fornecida classifica todas as assinaturas do MG que ainda não estão registradas na Central de Segurança como "não compatíveis". Você pode optar por defini-la para todas as assinaturas sem o Azure Defender.

    A definição fornecida, define *uma das* configurações de "preço" abaixo como em conformidade. Isso significa que uma assinatura definida como "Standard" ou "Gratuita" está em conformidade.

    > [!TIP]
    > Quando um plano do Azure Defender for habilitado, ele será descrito em uma definição de política como se estivesse na configuração 'Standard'. Quando é desabilitado, ele é "Gratuito". Para saber mais sobre as diferenças entre esses planos, confira [Central de Segurança gratuita vs. Azure Defender habilitado](security-center-pricing.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Se você alterá-lo conforme exibido abaixo, somente as assinaturas definidas como "Standard" serão classificadas como em conformidade:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definir alguns planos do Azure Defender a serem aplicados ao habilitar a Central de Segurança** – a política fornecida habilita a Central de Segurança sem qualquer um dos planos opcionais do Azure Defender. Você pode habilitar uma ou mais dessas opções.

    A seção `deployment` da definição fornecida tem um parâmetro `pricingTier`. Por padrão, ele é definido como `free`, mas você pode modificá-lo. 


## <a name="next-steps"></a>Próximas etapas:

Agora que você integrou um grupo de gerenciamento inteiro, habilite as proteções avançadas do Azure Defender. 

> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](enable-azure-defender.md)