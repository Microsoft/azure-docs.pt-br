---
title: Mover recursos de serviço de aplicativo do Azure para um novo grupo de recursos ou assinatura
description: Use o Azure Resource Manager para mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723565"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover a orientação para recursos do serviço de aplicativo

As etapas para mover os recursos de serviço de aplicativo diferem com base em se você estiver movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

## <a name="move-in-same-subscription"></a>Mover na mesma assinatura

Ao mover um aplicativo Web _dentro da mesma assinatura_, você não pode mover os certificados SSL de terceiros. No entanto, você pode mover um aplicativo Web para o novo grupo de recursos sem mover o respectivo certificado SSL de terceiros, mantendo a funcionalidade SSL do aplicativo.

Se você deseja mover o certificado SSL com o aplicativo Web, siga estas etapas:

1. Excluir o certificado de terceiros do aplicativo Web, mas manter uma cópia do seu certificado
2. Mova o aplicativo Web.
3. Carregue o certificado de terceiros para o aplicativo Web movido.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover um aplicativo Web _entre assinaturas_, as seguintes limitações se aplicam:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados SSL importados ou carregados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do serviço de aplicativo não está mais no seu grupo de recursos original, mova-o novamente para seu grupo de recursos original. Em seguida, mova o recurso entre assinaturas.

Se você não lembrar o grupo de recursos original, você pode achar por meio de diagnóstico. Para o seu aplicativo web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gerenciamento**.

![Selecione o diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **opções de migração**.

![Selecione as opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para as etapas recomendadas mover o aplicativo web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você ver as ações recomendadas a serem executadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Mover o certificado de serviço de aplicativo

Você pode mover seu Certificado do Serviço de Aplicativo para um novo grupo de recursos ou assinatura. Se seu Certificado do Serviço de Aplicativo estiver associado a um aplicativo Web, você deverá executar algumas etapas antes de mover os recursos para uma nova assinatura. Exclua a associação de SSL e o certificado particular do aplicativo Web antes de mover os recursos. O Certificado do Serviço de Aplicativo não precisa ser excluído, apenas o certificado particular no aplicativo Web.

## <a name="move-support"></a>Suporte de movimentação

Para determinar quais recursos do serviço de aplicativo podem ser movidos, consulte Mover o status de suporte de:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
