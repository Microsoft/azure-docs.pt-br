---
title: Corrigir recomendações na central de segurança do Azure | Microsoft Docs
description: Este artigo explica como responder às recomendações na central de segurança do Azure para proteger seus recursos e satisfazer as políticas de segurança.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438260"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de correção na Central de Segurança do Azure

As recomendações fornecem sugestões sobre como proteger melhor seus recursos. Implemente uma recomendação seguindo as etapas de correção fornecidas na recomendação.

## <a name="remediation-steps"></a>Etapas de correção <a name="remediation-steps"></a>

Depois de examinar todas as recomendações, decida qual delas corrigir primeiro. Recomendamos que você priorize os controles de segurança com o maior potencial para aumentar sua pontuação segura.

1. Na lista, selecione uma recomendação.

1. Siga as instruções na seção **etapas de correção** . Cada recomendação tem seu próprio conjunto de instruções. A captura de tela a seguir mostra as etapas de correção para configurar aplicativos para permitir somente o tráfego por HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Etapas de correção manual para uma recomendação" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Depois de concluído, uma notificação é exibida informando se o problema foi resolvido.

## <a name="quick-fix-remediation"></a>Correção rápida

Para simplificar a correção e melhorar a segurança do seu ambiente (e aumentar sua pontuação segura), muitas recomendações incluem uma opção de correção rápida.

A correção rápida ajuda a corrigir rapidamente uma recomendação em vários recursos.

> [!TIP]
> As soluções de correção rápida só estão disponíveis para recomendações específicas. Para encontrar as recomendações que têm uma correção rápida disponível, use o filtro **ações de resposta** para obter a lista de recomendações:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Use os filtros acima da lista de recomendações para encontrar recomendações que tenham a opção correção rápida":::

Para implementar uma solução de correção rápida:

1. Na lista de recomendações que têm a **correção rápida!** rótulo, selecione uma recomendação.

    [![Selecione correção rápida!](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Na guia **recursos não íntegros** , selecione os recursos nos quais você deseja implementar a recomendação e selecione **corrigir**.

    > [!NOTE]
    > Alguns dos recursos listados podem estar desabilitados, pois você não tem as permissões apropriadas para modificá-los.

1. Na caixa de confirmação, leia os detalhes e as implicações de correção.

    ![Correção rápida](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > As implicações são listadas na caixa cinza na janela **corrigir recursos** que é aberta depois de clicar em **corrigir**. Eles listam as alterações que ocorrem ao prosseguir com a correção de correção rápida.

1. Insira os parâmetros relevantes, se necessário, e aprove a correção.

    > [!NOTE]
    > Pode levar vários minutos após a correção ser concluída para ver os recursos na guia **recursos íntegros** . Para exibir as ações de correção, verifique o [log de atividades](#activity-log).

1. Depois de concluído, uma notificação será exibida informando se a correção foi bem-sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Log de correções de correção rápida no log de atividades <a name="activity-log"></a>

A operação de correção usa uma implantação de modelo ou chamada à API de PATCH REST para aplicar a configuração no recurso. Essas operações são registradas no [log de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Próximas etapas

Neste documento, você mostrou como corrigir recomendações na central de segurança. Para saber mais sobre a Central de Segurança, confira as páginas seguintes:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md) -saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure
* [O que são políticas, iniciativas e recomendações de segurança?](security-policy-concept.md)