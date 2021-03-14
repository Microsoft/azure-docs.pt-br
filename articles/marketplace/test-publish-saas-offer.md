---
title: Como testar e publicar uma oferta de SaaS no Microsoft Commercial Marketplace
description: Use o Partner Center para enviar sua oferta de SaaS para visualizar, Visualizar sua oferta, testar e, em seguida, publicá-la no Microsoft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ea647591194dfd6ba0bf818f4ce4d3a2005f0bc4
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630768"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Como testar e publicar uma oferta de SaaS no Marketplace comercial

Este artigo explica como usar o Partner Center para enviar sua oferta de SaaS para publicar, Visualizar sua oferta, testá-la e, em seguida, publicá-la ao mercado comercial. Você já deve ter criado uma oferta que deseja publicar.

## <a name="submit-your-offer-for-publishing"></a>Envie sua oferta para publicação

1. Entre no painel do Marketplace comercial no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Na página **visão geral** , selecione a oferta que você deseja publicar.
1. No canto superior direito do portal, selecione **revisar e publicar**.
2. Verifique se a coluna **status** de cada página diz **concluída**. Os três status possíveis são os seguintes:

   - **Não iniciado** – a página está incompleta.
   - **Incompleto** – a página não tem informações necessárias ou tem erros que precisam ser corrigidos. Você precisará voltar para a página e atualizá-la.
   - **Concluída** – a página está concluída. Todos os dados necessários foram fornecidos e não há erros.

1. Se qualquer uma das páginas tiver um status diferente de **concluído**, selecione o nome da página, corrija o problema, salve a página e, em seguida, selecione **revisar e publicar** novamente para retornar a esta página.
1. Depois que todas as páginas forem concluídas, na caixa **notas de certificação** , forneça instruções de teste para a equipe de certificação para garantir que seu aplicativo seja testado corretamente. Forneça notas suplementares úteis para entender seu aplicativo.
1. Para iniciar o processo de publicação para sua oferta, selecione **publicar**. A página **visão geral da oferta** é exibida e mostra o **status de publicação** da oferta.

O status de publicação de sua oferta será alterado conforme ele se mover pelo processo de publicação. Para obter informações detalhadas sobre esse processo, consulte [etapas de validação e publicação](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Visualize e teste sua oferta

Quando a oferta estiver pronta para sua aprovação, enviaremos um email para solicitar que você examine e aprove sua versão prévia da oferta. Você também pode atualizar a página **visão geral da oferta** em seu navegador para ver se sua oferta atingiu a fase de aprovação do Publicador. Se tiver, o botão **Go Live** e os links de visualização estarão disponíveis. Haverá um link para Microsoft AppSource visualização, Azure Marketplace Preview ou ambos, dependendo das opções que você escolheu ao criar sua oferta. Se você optar por vender sua oferta pela Microsoft, qualquer pessoa que tenha sido adicionada ao público de visualização poderá testar a aquisição e a implantação de sua oferta para garantir que ela atenda às suas necessidades durante esse estágio.

A captura de tela a seguir mostra a página **visão geral da oferta** para uma oferta de SaaS, com dois links de visualização sob o botão **Go Live** . As etapas de validação que você verá nessa página variam de acordo com as seleções feitas quando você criou a oferta.

![Ilustra a página Visão geral da oferta para uma oferta no Partner Center. O botão Go Live e os links de visualização são mostrados. O link Exibir relatório de validação também é mostrado em validação automatizada.](./media/review-publish-offer/publish-status-saas.png)

Use as etapas a seguir para visualizar sua oferta.

1. Na página **visão geral da oferta** , selecione um link de visualização no botão **ir ao vivo** .

1. Para validar o fluxo de compra e configuração de ponta a ponta, compre sua oferta enquanto ela estiver em versão prévia. Primeiro, notifique a Microsoft com um [tíquete de suporte](https://aka.ms/marketplacesupport) para garantir que não processaremos uma cobrança.

1. Se sua oferta de SaaS oferecer suporte à [cobrança limitada usando o serviço de medição do Marketplace comercial](./partner-center-portal/saas-metered-billing.md), examine e siga as práticas recomendadas de teste detalhadas em [APIs de cobrança limitadas do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Examine e siga as instruções de teste em [APIs de preenchimento de SaaS versão 2 no Microsoft Commercial Marketplace](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) para garantir que sua oferta seja integrada com êxito às APIs antes de publicar sua oferta ao vivo.

1. Se a etapa de validação da oferta resultar em avisos, um link **Exibir relatório de validação** aparecerá na página **visão geral da oferta** . Certifique-se de examinar o relatório e resolver os problemas antes de selecionar o botão **ir ao vivo** . Caso contrário, a certificação provavelmente falhará e atrasará a sua oferta de entrar em funcionamento.

1. Se você precisar fazer alterações depois de Visualizar e testar a oferta, poderá editar e reenviar para publicar uma nova visualização. Para obter mais informações, consulte [atualizar uma oferta existente no Marketplace comercial](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publicar sua oferta em tempo real

Depois de concluir todos os testes em sua versão prévia, selecione **entrar em tempo real** para publicar sua oferta ao vivo no Marketplace comercial.

   > [!TIP]
   > Se sua oferta já estiver ativa no Marketplace comercial, todas as atualizações feitas não entrarão em operação até que você selecione **entrar em ativação**.

Agora que você optou por disponibilizar sua oferta no Marketplace comercial, realizamos uma série de verificações de validação finais para garantir que a oferta ao vivo seja configurada como a versão de visualização da oferta. Para obter detalhes sobre essas verificações de validação, consulte [fase de publicação](review-publish-offer.md#publish-phase).

Depois que essas verificações de validação forem concluídas, sua oferta estará ativa no Marketplace.

## <a name="next-steps"></a>Próximas etapas

- [Acessar relatórios analíticos para o mercado comercial no Partner Center](./partner-center-portal/analytics.md)
