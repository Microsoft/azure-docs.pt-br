---
title: Atualizar uma oferta existente – marketplace comercial da Microsoft
description: Como fazer atualizações em uma oferta ou um plano do marketplace comercial existente, sincronizar públicos-alvo privados e remover uma oferta.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: trkeya
ms.author: trkeya
ms.date: 10/27/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c28ab557f6aac94fa9acde78c7433b850ecba0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097239"
---
# <a name="update-existing-offers-in-the-commercial-marketplace"></a>Atualizar ofertas existentes no marketplace comercial

Este artigo explica como fazer atualizações em planos e ofertas existentes e como remover uma oferta do marketplace comercial. Veja suas ofertas na guia **Visão geral** do [portal do marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

## <a name="update-a-published-offer"></a>Atualizar uma oferta publicada

Use estas etapas para atualizar uma oferta que foi publicada com êxito no estado Versão Prévia ou Ativa.

1. Selecione o nome da oferta que deseja atualizar. O status da oferta pode ser listado como **Versão Prévia**, **Ativa**, **Publicação em andamento**, **Rascunho**, **Atenção necessária** ou **Não disponível** (se você optou anteriormente por parar de vender a oferta). Depois que a oferta for selecionada, a página **Visão geral da oferta** será aberta.
1. Escolha a página da oferta que deseja atualizar, como **Propriedades**, **Listagem da oferta** ou **Versão Prévia** (ou escolha **Atualizar** no cartão aplicável na página **Visão geral da oferta**).
1. Faça as alterações e selecione **Salvar rascunho**. Repita esse processo até que todas as alterações sejam concluídas.
1. Examine as alterações na página **[Comparar](#compare-changes-to-your-offer)** .
1. Quando estiver pronto para publicar sua oferta atualizada, selecione **Examinar e publicar** em qualquer página. A página **Examinar e publicar** será aberta. Nessa página, você verá o status de conclusão das seções da oferta que atualizou: 
    - **Alterações não publicadas**: a seção foi atualizada e está concluída. Todos os dados necessários foram fornecidos e não foram apresentados erros nas atualizações.
    - **Incompleta**: as atualizações feitas na seção apresentaram erros que precisam ser corrigidos ou exigem que mais informações sejam fornecidas.
2. Selecione **Publicar** para enviar a oferta atualizada para publicação. Depois, sua oferta passará pelas [etapas de validação e publicação](../review-publish-offer.md#validation-and-publishing-steps) padrão.

## <a name="changing-offer-type"></a>Como alterar o tipo de oferta

[!INCLUDE [change-offer-type](../includes/change-offer-type.md)]

> [!IMPORTANT]
> Você precisará examinar a versão prévia da oferta quando ela estiver disponível e selecionar **Ativar** para publicar a oferta atualizada para o seu público-alvo (público ou privado).

## <a name="add-a-plan-to-an-existing-offer"></a>Adicionar um plano a uma oferta existente

Conclua estas etapas para adicionar um novo plano a uma oferta já publicada.

1. Com a página **Visão geral da oferta** da oferta existente aberta, acesse a página **Visão geral do plano** e escolha **Criar plano**.
1. Crie um plano de acordo com as [diretrizes](../plans-pricing.md) usando o **modelo de preços de planos existentes**.
1. Selecione **Salvar rascunho** depois de alterar o nome do plano.
1. Escolha **Publicar** quando estiver pronto para publicar as atualizações. A página **[Examinar e publicar](../review-publish-offer.md)** será aberta e fornecerá um status de conclusão para as atualizações.

## <a name="update-a-plan-for-an-existing-offer"></a>Atualizar um plano para uma oferta existente

Conclua estas etapas para fazer alterações em um plano para uma oferta já publicada.

1. Com a página **Visão geral da oferta** da oferta existente aberta, escolha o plano que deseja alterar. Se o plano não estiver acessível na lista **Visão geral do plano**, selecione **Ver todos os planos**.
1. Selecione o **Nome**, o **Modelo de preço** ou a **Disponibilidade** do plano. *Atualmente, os planos só estão disponíveis em inglês (Estados Unidos)* .
1. Selecione **Salvar rascunho** depois de fazer alterações no nome do plano, na descrição ou na disponibilidade do público-alvo.
1. Escolha **Examinar e publicar** quando estiver pronto para publicar as atualizações. A página **[Examinar e publicar](../review-publish-offer.md)** será aberta e fornecerá um status de conclusão para as atualizações.
1. Selecione **Publicar** para enviar o plano atualizado para publicação. Enviaremos um email a você quando uma versão prévia da oferta estiver disponível para revisão e aprovação.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Oferecer um plano de máquina virtual com um novo preço

Depois que um plano de máquina virtual é publicado, o preço dele não pode ser alterado. Para oferecer o mesmo plano com um preço diferente, você precisará ocultar o plano e criar outro com o preço atualizado. Primeiro, oculte o plano com o preço que deseja alterar:

1. Com a página **Visão geral da oferta** da oferta existente aberta, escolha o plano que deseja alterar. Se o plano não estiver acessível na lista **Visão geral do plano**, selecione **Ver todos os planos**.
1. Marque a caixa de seleção **Ocultar plano**. Selecione o rascunho antes de continuar.

Agora que você ocultou o plano com o preço antigo, crie uma cópia desse plano com o preço atualizado:

1. No Partner Center, volte à **Visão geral do plano**.
2. Selecione **Criar novo plano**. Insira uma **ID de Plano** e um **Nome de plano** e escolha **Criar**.
1. Para reutilizar a configuração técnica do plano ocultado, marque a caixa de seleção **Reutilizar configuração técnica**. Leia [Criar planos para uma oferta de VM](../azure-vm-create-plans.md) para saber mais.
    > [!IMPORTANT]
    > Se você selecionar **Este plano reutiliza a configuração técnica de outro plano**, não poderá parar de vender o plano pai posteriormente. Não use essa opção se deseja parar de vender o plano pai.
3. Conclua todas as seções necessárias do novo plano, incluindo o novo preço.
1. Selecione **Salvar rascunho**.
1. Depois de concluir todas as seções necessárias do novo plano, selecione **Examinar e publicar**. Isso enviará sua oferta para revisão e publicação. Leia [Examinar e publicar uma oferta no marketplace comercial](../review-publish-offer.md) para obter mais detalhes.

## <a name="sync-private-plan-audiences"></a>Sincronizar públicos-alvo do plano privado

Se a sua oferta incluir um ou mais planos configurados para serem disponibilizados somente para um público-alvo restrito privado, será possível atualizar somente o público-alvo que pode acessar esse plano privado sem publicar outras alterações na oferta. 

Para atualizar e sincronizar o público-alvo privado dos seus planos:

- Modifique o público-alvo em um ou mais planos privados usando o botão + **Adicionar ID** ou **Importar clientes (CSV)** e salve as alterações.
- Selecione **Sincronizar público-alvo privado** na página **Visão geral do plano**.

A opção **Sincronizar público-alvo privado** publicará somente as alterações nos públicos-alvo privados, sem publicar outras atualizações que você tenha feito na oferta de rascunho.

## <a name="compare-changes-to-your-offer"></a>Comparar as alterações com a sua oferta

Antes de publicar atualizações na sua oferta ativa ou de [versão prévia](#compare-changes-to-a-preview-offer), audite as alterações salvas na página **Comparar**. Acesse a página **Comparar** no canto superior direito de qualquer página da oferta, como a página **Propriedades** ou **Listagem da oferta**. A página **Comparar** mostra as versões lado a lado das alterações salvas dessa oferta e a oferta do marketplace publicada.

- Você pode usar **Comparar** a qualquer momento durante o processo de edição.
- Selecione um campo na página **Comparar** para procurar o valor que deseja modificar.
- Para ver os valores de todos os campos, até mesmo os campos não atualizados, selecione o filtro **Todos os campos**. Você pode modificar filtros nesses campos selecionando **Campos modificados** e escolhendo um dos filtros abaixo:
    - O filtro **Valores removidos** exibe os campos que você publicou e agora está removendo por completo.
    - O filtro **Valores adicionados** exibe os campos que você não publicou originalmente e agora está adicionando.
    - O filtro **Valores editados** exibe os campos que foram publicados, mas cujo conteúdo agora foi atualizado.

      >[!NOTE]
      > Se um desses filtros não estiver disponível, isso indicará que você não fez uma atualização desse tipo.

- Para ver apenas os valores que não foram atualizados, selecione o filtro **Campos inalterados**. Os valores de campo mostrados para a versão publicada e de rascunho serão os mesmos.

  ![Filtros para comparar atualizações na sua oferta publicada ou de versão prévia](./media/compare-changes-marketplace.png)

>[!NOTE]
> Atualmente, as seguintes páginas não dão suporte ao recurso **Comparar**:
>- Público-alvo de Revendedores CSP
>- Test Drive da Configuração Técnica
>- Test Drive da Listagem do Marketplace
>- Venda conjunta
>- Arquivos complementares

Lembre-se de publicar novamente sua oferta depois de fazer as atualizações para que elas entrem em vigor.

### <a name="compare-changes-to-a-preview-offer"></a>Comparar as alterações com uma oferta de versão prévia

Se você tiver alterações na versão prévia que não estão ativas, compare novas alterações com a oferta de versão prévia do marketplace.

1. Selecione **Comparar** na barra de comandos da página.
2. Selecione a lista suspensa **Com** e altere-a de **Oferta ativa** para **Oferta de versão prévia**. Se a sua oferta ainda não tiver sido ativada, você não verá a opção **Oferta ativa**.
3. A página **Comparar** fornece versões lado a lado que mostram as alterações.

Lembre-se de publicar novamente sua oferta depois de fazer as atualizações para que elas entrem em vigor.

## <a name="stop-selling-an-offer-or-plan"></a>Parar de vender uma oferta ou um plano

Você pode remover as listagens de ofertas e planos do marketplace comercial da Microsoft, o que impedirá novos clientes de encontrá-los e comprá-los. Os clientes que já adquiriram a oferta ou o plano ainda poderão usá-lo e poderão baixá-lo novamente, se necessário. No entanto, eles não receberão atualizações se você decidir publicar novamente a oferta ou o plano mais tarde.

- Para parar de vender uma oferta depois de publicá-la, selecione **Parar de vender** na página **Visão geral da oferta**. Após algumas horas de confirmação, a oferta não estará mais visível no marketplace comercial.

- Para parar de vender um plano, selecione **Parar de vender** na página **Visão geral do plano**. A opção de parar de vender um plano só estará disponível se você tiver mais de um plano na oferta. Você pode optar por parar de vender um plano sem afetar outros planos na oferta.
     >[!NOTE]
     > Depois de confirmar que deseja parar de vender o plano, você precisará publicar novamente a oferta para que a alteração entre em vigor.

Depois de parar de vender uma oferta ou um plano, você ainda o verá no Partner Center com o status **Não disponível**. Se você decidir listar ou vender essa oferta ou esse plano novamente, siga as instruções para [atualizar uma oferta publicada](#update-a-published-offer). Não se esqueça de que será necessário **publicar** a oferta ou o plano novamente depois de fazer as alterações.

## <a name="remove-offers-from-existing-customers"></a>Remover ofertas de clientes existentes

Para remover ofertas de clientes existentes, [registre uma solicitação de suporte](https://aka.ms/marketplacepublishersupport). Na lista de tópicos de suporte, selecione **Marketplace Comercial** > **Exclusão, Remoção ou Encerramento de Ofertas e Aplicativos** e envie a solicitação. A equipe de suporte orientará você pelo processo de remoção da oferta.

## <a name="next-steps"></a>Próximas etapas

- [Verificar o status de publicação da sua oferta no marketplace comercial](../review-publish-offer.md)
