---
title: Mover Azure App recursos do serviço
description: Use Azure Resource Manager para mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531365"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover diretrizes para recursos do serviço de aplicativo

Este artigo descreve as etapas para mover os recursos do serviço de aplicativo. Há requisitos específicos para mover os recursos do serviço de aplicativo para uma nova assinatura.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover um aplicativo Web entre assinaturas, as seguintes diretrizes se aplicam:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados TLS/SSL importados ou carregados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos.
- Os ambientes do serviço de aplicativo não podem ser movidos para um novo grupo de recursos ou assinatura. No entanto, você pode mover um aplicativo Web e um plano do serviço de aplicativo para uma nova assinatura sem mover o Ambiente do Serviço de Aplicativo. Após a movimentação, o aplicativo Web não é mais hospedado no Ambiente do Serviço de Aplicativo.
- Você pode mover um certificado associado a uma Web sem excluir as associações de TLS, desde que o certificado seja movido com todos os outros recursos no grupo de recursos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do serviço de aplicativo não estiver mais em seu grupo de recursos original, mova-o de volta para seu grupo de recursos original. Em seguida, mova o recurso entre assinaturas.

Se você não se lembrar do grupo de recursos original, poderá encontrá-lo por meio de diagnóstico. Para seu aplicativo Web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gerenciamento**.

![Selecionar diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de migração**.

![Selecionar opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter as etapas recomendadas para mover o aplicativo Web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a serem executadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo Web.

![Captura de tela mostra as etapas recomendadas para mover recursos da Web do Microsoft dot.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Suporte de movimentação

Para determinar quais recursos do serviço de aplicativo podem ser movidos, consulte mover status de suporte para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
