---
title: Atualizar uma oferta existente-Microsoft Commercial Marketplace
description: Como fazer atualizações em uma oferta ou plano do Marketplace comercial existente, sincronizar públicos privados e remover uma oferta.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: trkeya
ms.author: trkeya
ms.date: 10/27/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 26ceee828bae7ea3fe2d78ec1393f308556b8401
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033350"
---
# <a name="update-existing-offers-in-the-commercial-marketplace"></a>Atualizar ofertas existentes no Marketplace comercial

Este artigo explica como fazer atualizações em planos e ofertas existentes e também como remover uma oferta do Marketplace comercial. Você pode exibir suas ofertas na guia **visão geral** do [portal do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

## <a name="update-a-published-offer"></a>Atualizar uma oferta publicada

Use estas etapas para atualizar uma oferta que foi publicada com êxito no estado de visualização ou ao vivo.

1. Selecione o nome da oferta que você deseja atualizar. O status da oferta pode ser listado como **Visualização**, **dinâmico**, **publicação em andamento**, **rascunho**, **atenção necessária** ou **não disponível** (se você optou anteriormente por parar de vender a oferta). Depois de selecionada, a página **visão geral da oferta** para essa oferta será aberta.
1. Selecione a página de oferta que você deseja atualizar, como **Propriedades**, **listagem de oferta** ou **Visualização** (ou selecione **Atualizar** no cartão aplicável na página **visão geral da oferta** ).
1. Faça suas alterações e selecione **salvar rascunho**. Repita esse processo até que todas as suas alterações sejam concluídas.
1. Examine as alterações na página **[comparar](#compare-changes-to-your-offer)** .
1. Quando estiver pronto para publicar sua oferta atualizada, selecione **revisar e publicar** em qualquer página. A página **revisar e publicar** será aberta. Nesta página, você verá o status de conclusão das seções da oferta que você atualizou: 
    - **Alterações não publicadas**: a seção foi atualizada e está concluída. Todos os dados necessários foram fornecidos e não houve erros introduzidos nas atualizações.
    - **Incompleto**: as atualizações feitas na seção introduziram erros que precisam ser corrigidos ou exigem mais informações a serem fornecidas.
2. Selecione **publicar** para enviar a oferta atualizada para publicação. Sua oferta passará pelas [etapas de validação e publicação](../review-publish-offer.md#validation-and-publishing-steps)padrão.

> [!IMPORTANT]
> Você deve revisar a versão prévia da oferta quando ela estiver disponível e selecionar **Go-Live** para publicar sua oferta atualizada em seu público-alvo (público ou privado).

## <a name="add-a-plan-to-an-existing-offer"></a>Adicionar um plano a uma oferta existente

Conclua estas etapas para adicionar um novo plano a uma oferta que você já publicou.

1. Com a página **visão geral da oferta** para sua oferta existente aberta, vá para a página **visão geral do plano** e, em seguida, selecione **criar novo plano**.
1. Crie um novo plano de acordo com as [diretrizes](../plans-pricing.md) usando o **modelo de preços de planos existentes**.
1. Selecione **salvar rascunho** depois de alterar o nome do plano.
1. Selecione **publicar** quando estiver pronto para publicar suas atualizações. A página **[revisar e publicar](../review-publish-offer.md)** é aberta e fornece um status de conclusão para suas atualizações.

## <a name="update-a-plan-for-an-existing-offer"></a>Atualizar um plano para uma oferta existente

Conclua estas etapas para fazer alterações em um plano para uma oferta que você já publicou.

1. Com a página **visão geral da oferta** para sua oferta existente aberta, escolha o plano que você deseja alterar. Se o plano não estiver acessível na lista **visão geral do plano** , selecione **Ver todos os planos**.
1. Selecione o **nome** do plano, o **modelo de preços** ou a **disponibilidade**. *Atualmente, os planos estão disponíveis apenas em inglês (Estados Unidos)*.
1. Selecione **salvar rascunho** depois de fazer alterações no nome do plano, na descrição ou na disponibilidade do público.
1. Selecione **revisar e publicar** quando estiver pronto para publicar suas atualizações. A página **[revisar e publicar](../review-publish-offer.md)** é aberta e fornece um status de conclusão para suas atualizações.
1. Selecione **publicar** para enviar o plano atualizado para publicação. Enviaremos um email para você quando uma versão de visualização da oferta atualizada estiver disponível para revisão e aprovação.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Oferecer um plano de máquina virtual a um novo preço

Depois que um plano de máquina virtual é publicado, seu preço não pode ser alterado. Para oferecer o mesmo plano a um preço diferente, você deve ocultar o plano e criar um novo com o preço atualizado. Primeiro, oculte o plano com o preço que você deseja alterar:

1. Com a página **visão geral da oferta** para sua oferta existente aberta, escolha o plano que você deseja alterar. Se o plano não estiver acessível na lista **visão geral do plano** , selecione **Ver todos os planos**.
1. Marque a caixa de seleção **ocultar plano** . Salve o rascunho antes de continuar.

Agora que você ocultou o plano com o preço antigo, crie uma cópia desse plano com o preço atualizado:

1. No Partner Center, volte para o **plano visão geral**.
2. Selecione **Criar novo plano**. Insira uma **ID de plano** e um **nome de plano** e, em seguida, selecione **criar**.
1. Para reutilizar a configuração técnica do plano que você ocultou, marque a caixa de seleção **reutilizar a configuração técnica** . Leia [criar planos para uma oferta de VM](../azure-vm-create-plans.md) para saber mais.
    > [!IMPORTANT]
    > Se você selecionar **este plano reutiliza a configuração técnica de outro plano**, não será possível parar de vender o plano pai posteriormente. Não use essa opção se desejar parar de vender o plano pai.
3. Conclua todas as seções necessárias para o novo plano, incluindo o novo preço.
1. Selecione **Salvar rascunho**.
1. Depois de concluir todas as seções necessárias para o novo plano, selecione **revisar e publicar**. Isso enviará sua oferta para revisão e publicação. Leia [revisar e publicar uma oferta no Marketplace comercial](../review-publish-offer.md) para obter mais detalhes.

## <a name="sync-private-plan-audiences"></a>Sincronizar audiências do plano privado

Se sua oferta incluir um ou mais planos configurados para serem disponibilizados somente para um público restrito privado, será possível atualizar somente o público que pode acessar esse plano privado sem publicar outras alterações na oferta. 

Para atualizar e sincronizar o público privado para seus planos:

- Modifique o público em um ou mais planos privados usando o botão + **Adicionar ID** ou **importar clientes (CSV)** e, em seguida, salve as alterações.
- Selecione **sincronizar público privado** na página **visão geral do plano** .

**Sincronizar público privado** publicará somente as alterações em seus públicos privados, sem publicar outras atualizações que você tenha feito na oferta de rascunho.

## <a name="compare-changes-to-your-offer"></a>Comparar alterações à sua oferta

Antes de publicar atualizações em sua oferta ao vivo ou [Visualização](#compare-changes-to-a-preview-offer) , você pode auditar as alterações salvas na página **comparar** . Você pode acessar a página **comparar** no canto superior direito de qualquer página de oferta, como a página de listagem **Propriedades** ou **oferta** . A página **comparar** mostra as versões lado a lado das alterações salvas dessa oferta e a oferta do Marketplace publicada.

- Você pode usar **comparar** a qualquer momento durante o processo de edição.
- Selecione um campo na página **comparar** para navegar até o valor que você deseja modificar.
- Para ver os valores de todos os campos, até mesmo os campos não atualizados, selecione o filtro **todos os campos** . Você pode modificar filtros dentro desses campos selecionando **campos modificados** e, em seguida, selecionando um dos filtros abaixo:
    - O filtro de **valores removidos exibe os** campos que você publicou e agora está completamente removendo.
    - O filtro **valores adicionados exibe os** campos que você não publicou originalmente e que agora estão sendo adicionados.
    - O filtro de **valores editados** exibe os campos que foram publicados, mas agora você atualizou o conteúdo.

      >[!NOTE]
      > Se um desses filtros não estiver disponível, isso indica que você não fez uma atualização desse tipo.

- Para ver apenas os valores que não foram atualizados, selecione o filtro **campos inalterados** . Os valores de campo mostrados para a versão publicada e de rascunho serão os mesmos.

  ![Filtros para comparar atualizações à sua oferta publicada ou de visualização](./media/compare-changes-marketplace.png)

>[!NOTE]
> Atualmente, as páginas a seguir não dão suporte a **comparação**:
>- Público do revendedor CSP
>- Testar a configuração técnica
>- Lista do test drive do Marketplace
>- Venda conjunta
>- Arquivos complementares

Lembre-se de republicar sua oferta depois de fazer as atualizações para que as alterações entrem em vigor.

### <a name="compare-changes-to-a-preview-offer"></a>Comparar alterações a uma oferta de visualização

Se você tiver alterações na visualização que não estão dinâmicas, você poderá comparar novas alterações com a oferta de versão prévia do Marketplace.

1. Selecione **comparar** na barra de comandos da página.
2. Selecione a lista suspensa **com** e altere-a da **oferta dinâmica** para a versão **prévia**. Se sua oferta ainda não tiver sido ativada, você não verá a opção de **oferta dinâmica** .
3. A página **comparar** fornece versões lado a lado que mostram as alterações.

Lembre-se de republicar sua oferta depois de fazer as atualizações para que as alterações entrem em vigor.

## <a name="stop-selling-an-offer-or-plan"></a>Pare de vender uma oferta ou um plano

Você pode remover as listagens de ofertas e planos do Microsoft Commercial Marketplace, o que impedirá novos clientes de encontrá-los e adquiri-los. Todos os clientes que já adquiriram a oferta ou o plano ainda poderão usá-lo e poderão baixá-lo novamente, se necessário. No entanto, eles não receberão atualizações se você decidir republicar a oferta ou o plano posteriormente.

- Para parar de vender uma oferta depois de publicá-la, selecione **parar de vender** na página **visão geral da oferta** . Em algumas horas de confirmação, a oferta não estará mais visível no Marketplace comercial.

- Para parar de vender um plano, selecione **parar de vender** na página **visão geral do plano** . A opção de parar de vender um plano só estará disponível se você tiver mais de um plano na oferta. Você pode optar por parar de vender um plano sem afetar outros planos dentro de sua oferta.
     >[!NOTE]
     > Depois de confirmar que deseja parar de vender o plano, você deve republicar a oferta para que a alteração entre em vigor.

Depois de deixar de vender uma oferta ou um plano, você ainda o verá no Partner Center com um status **não disponível** . Se você decidir listar ou vender esta oferta ou planejar novamente, siga as instruções para [atualizar uma oferta publicada](#update-a-published-offer). Não se esqueça de que será necessário **publicar** a oferta ou o plano novamente após fazer as alterações.

## <a name="remove-offers-from-existing-customers"></a>Remover ofertas de clientes existentes

Para remover ofertas de clientes existentes, [registre uma solicitação de suporte](https://aka.ms/marketplacepublishersupport). Na lista tópico de suporte, selecione oferta de **Marketplace comercial**  >  **ou deslistação de aplicativos, remoção ou rescisão** e envie a solicitação. A equipe de suporte orientará você pelo processo de remoção da oferta.

## <a name="next-steps"></a>Próximas etapas

- [Verificar o status de publicação da sua oferta do Marketplace comercial](../review-publish-offer.md)
