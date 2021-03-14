---
title: Migre soluções de televenda do OCP GTM para o Partner Center para Microsoft AppSource
description: Saiba como migrar soluções de venda conjunta de OCP GTM para o Partner Center para Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 3/04/2021
ms.openlocfilehash: 92702f38b6143c5dac2111c57fadeba7d9142992
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181050"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migração de soluções de venda conjuntas de OCP GTM para o mercado comercial

A Microsoft está migrando a experiência de publicação. O [Marketplace comercial](overview.md) fornece publicação simplificada para a venda por meio dos três canais da Microsoft, centralizando a criação e o gerenciamento da oferta no Partner Center, onde você já está gerenciando sua relação com a Microsoft.

Como um parceiro da Microsoft inscrito no mercado comercial, você pode:

- Publique suas ofertas de forma centralizada e covenda em canais diretos de clientes, parceiros e vendedores da Microsoft.
- Verifique se suas ofertas estão na loja online correta —[Microsoft AppSource](https://appsource.microsoft.com) ou [Azure Marketplace](https://azure.microsoft.com)— para alcançar milhões de clientes em nuvem que se alinham com os recursos da sua oferta.
- Conduza sua própria experiência de publicação para comercializar com as ofertas que se alinham com suas metas de negócios.
- Alinhe sua publicação de oferta no Partner Center, onde você já está gerenciando seu relacionamento da Microsoft e as oportunidades de venda conjunta.
- Desbloquear [recompensas do Marketplace](partner-center-portal/marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Pré-requisitos para continuar a vender com a Microsoft

Verifique se você tem uma associação do Active Microsoft Partner Network e está inscrita no Marketplace comercial no Partner Center.

- Junte-se ao Microsoft Partner Network [sem custo](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership). Como parceiro, você terá acesso a recursos, programas, ferramentas e conexões exclusivos para aumentar seus negócios.
- Se você não tiver uma conta no Marketplace comercial, registre-se [agora](partner-center-portal/create-account.md) para continuar a vender com a Microsoft e acessar a experiência de publicação completa.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Publicando atualizações para atingir o status pronto para venda

Para que sua solução seja detectável para os vendedores e parceiros da Microsoft, ela deve atender aos [requisitos prontos para venda](marketplace-co-sell.md). Para que um vendedor da Microsoft seja incentivados, sua solução deve atender aos [requisitos qualificados do incentivo](marketplace-co-sell.md). Conclua esses requisitos na guia co-venda no Partner Center (consulte [esta imagem](#cosell-tab) mais adiante neste artigo).

> [!NOTE]
> No mercado comercial, suas soluções são conhecidas como "ofertas" em toda a experiência de publicação.

Depois de inscrever-se no Marketplace comercial, prepare-se para migrar suas soluções do OCP GTM.

Siga estas etapas antes de importar suas soluções do OCP GTM:

1. Visite a lista de [editores](https://partner.microsoft.com/dashboard/account/v3/publishers/list)da sua empresa. Ele inclui o proprietário da conta, os gerentes e os desenvolvedores que têm acesso de publicação. Saiba mais sobre as [funções de usuário do Partner Center](./partner-center-portal/manage-account.md#define-user-roles-and-permissions).
2. Peça a um dos contatos listados para [Adicionar usuários](https://partner.microsoft.com/dashboard/account/usermanagement) ao Marketplace comercial como *gerentes* ou *desenvolvedores*, já que apenas essas funções podem editar e publicar soluções.
3. Trabalhe com seus desenvolvedores para mover suas soluções de sua conta do GTM OCP para o mercado comercial.
4. Decida qual das opções a seguir você deseja fazer:
    1. Mescle essa solução com uma oferta semelhante no Marketplace comercial.
    1. Migre essa solução de OCP GTM para o mercado comercial.
    1. Descartar esta solução.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Migrar suas soluções do OCP GTM

1. Comece a migração [aqui](https://partner.microsoft.com/solutions/migration#).
2. Selecione a página **visão geral** e **clique aqui para** começar.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Página de visão geral do Partner Center, guia Visão geral.":::

3. Para começar a migrar, selecione a guia **soluções** , que exibe todas as soluções associadas às suas IDs de MPN.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Página de visão geral do Partner Center, guia soluções.":::

    > [!NOTE]
    > Esta guia notará se não houver soluções pendentes para migrar para o Marketplace comercial. Para continuar a comercializar com a Microsoft, verifique se as soluções migradas foram publicadas no mercado comercial.

    Saiba mais sobre o status da solução revisando as dicas de ferramenta. Todas as ações com as soluções pendentes são listadas em **ação**.<a name="beginmigration"></a>

4. Selecione **Iniciar migração** (consulte a imagem acima) para a solução que você deseja migrar e, em seguida, selecione uma das seguintes opções:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="As três opções de migração.":::

### <a name="merge-solution-with-a-similar-offer"></a>Mesclar solução com uma oferta semelhante

Selecione esta opção se a solução já estiver publicada no mercado comercial e as duas devem ser mescladas em uma única oferta. Isso evitará a criação de ofertas duplicadas.

1. Identifique a oferta existente.
    1. Selecione **desejo mesclar esta solução com uma oferta semelhante no Marketplace comercial** (consulte a imagem **necessária** da ação [acima](#beginmigration)).
    1. A guia **ação 1** mostra as ofertas de Marketplace comercial ao vivo às quais sua solução OCP GTM pode ser associada. Selecione a oferta ao vivo na lista, se você tiver uma. Se não houver nenhuma lista de ofertas para escolher, insira o endereço voltado para o cliente (URL) do Microsoft AppSource ou do Azure Marketplace.
        [![A guia ação 1 do processo de mesclagem.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Selecione **Continuar**.
1. Solicite a mesclagem.
    1. A guia **ação 2** mostra instruções para solicitar a mesclagem da solução GTM OCP com aquela que você identificou. Para solicitar a mesclagem, selecione **salvar & contatar o suporte**, que abre uma página de suporte do parceiro em um navegador.
    1. Selecione **fornecer detalhes do problema** e insira o seguinte: [ ![ a guia ação 2 do processo de mesclagem.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Selecione **Enviar**. A equipe de suporte ao parceiro entrará em contato com você dentro de dois dias úteis.
    1. O suporte a parceiros funcionará com você para garantir uma mesclagem bem-sucedida dessa oferta para que ela seja publicada como uma oferta dinâmica.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Migrar esta solução de OCP GTM

Selecione esta opção quando você pode ter uma solução OCP GTM que ainda não tem uma oferta correspondente publicada no Marketplace comercial. Você precisará publicar essa solução no mercado comercial para continuar vendendo com a Microsoft e a migração dessa solução economizará tempo retendo as informações e a lista de materiais de OCP GTM. Essa opção exige que você selecione um tipo de oferta.

1. Selecione **desejo migrar esta solução de OCP GTM para o Marketplace comercial** (consulte a imagem necessária para a **ação** [acima](#beginmigration)) e **continue**.
1. Na guia **ação 1** , selecione o [tipo de oferta](publisher-guide-by-offer-type.md)e **continue**.

    [![A guia Action 1 do processo de migração.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. Na guia **ação 2** , selecione o [perfil do Publicador](partner-center-portal/create-account.md) na lista fornecida. Se você não tiver uma conta de editor, crie uma no [Partner Center](https://partner.microsoft.com/solutions/migration), selecione-a aqui.

    [![A guia ação 2 do processo de migração.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. Selecione **criar uma oferta de rascunho** para migrar sua solução para o Marketplace comercial como um rascunho. Ele ainda não estará ativo.
1. Continue o processo de publicação no Partner Center. Para obter assistência com a publicação no Partner Center, consulte [tornar sua oferta ativa no Marketplace comercial](#make-your-offer-live-in-the-marketplace) abaixo.

### <a name="discard-this-solution"></a>Descartar esta solução

Selecione esta opção quando uma solução em GTM soluções de OCP não for mais relevante. Você será solicitado a confirmar a descartar e também poderá desfazê-la mais tarde.

1. Selecione **desejo descartar esta solução** (consulte a imagem **necessária da ação** [acima](#beginmigration)) e **continue**.
2. Selecione **descartar**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Confirme a descartar.":::

3. Para desfazer o descarte, selecione **desfazer descartar**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="O link desfazer descartar.":::

4. Se precisar de ajuda adicional, selecione a guia **obter ajuda** para entrar em contato com a equipe de suporte do parceiro.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="O link de suporte na guia obter ajuda.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Torne sua oferta ativa no Marketplace

Se você tiver selecionado para migrar sua oferta para o Marketplace comercial, ela será recebida como um rascunho. Você ainda precisa publicar sua oferta para torná-la ativa no Marketplace comercial; Isso reterá seu status de venda, incentivos e pipeline de referência.

Para obter instruções detalhadas sobre as informações que você precisa fornecer antes que sua oferta possa ser publicada, leia o [Guia de publicação](publisher-guide-by-offer-type.md)apropriado. Para obter um resumo, leia abaixo.

1. Na página **visão geral** no Partner Center, selecione o nome da oferta de rascunho.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="A página Visão geral da oferta no Partner Center.":::

<a name="cosell-tab"></a>

2. Conclua as informações necessárias em cada guia. Opcionalmente, conclua a página **revender por meio de CSPs** (no menu de navegação à esquerda abaixo) para revender por meio do programa CSP (provedor de soluções na nuvem). Os links e dicas de ferramentas de **saiba mais** guiarão você pelos requisitos e detalhes.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="As guias da instalação visão geral da oferta no Partner Center.":::

3. Alguns dos detalhes voltados para o vendedor da Microsoft foram copiados da solução OCP GTM. Conclua as informações necessárias restantes na guia **co-venda com a Microsoft** para tornar sua oferta ainda mais fácil de vender. Ao concluir, selecione **revisar e publicar**. Para obter mais informações, consulte [Configurar a televenda para uma oferta de Marketplace comercial](commercial-marketplace-co-sell.md).

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="A guia oferta de co-venda no Partner Center.":::

4. Depois de examinar todas as informações enviadas, selecione **publicar** para enviar sua oferta de rascunho para revisão de certificação.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="A guia revisão da oferta e o botão publicar no Partner Center.":::

5. Acompanhe o status do seu envio na guia **visão geral** .

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="A barra de status de publicação da oferta na guia Visão geral no Partner Center.":::

6. Você será notificado quando nossa análise de certificação for concluída. Se fornecermos comentários acionáveis, resolva-o e selecione **publicar** para iniciar uma recertificação.
7. Depois que sua oferta passar na certificação, visualize a oferta com o link fornecido e faça os ajustes finais desejados. Quando estiver pronto, selecione **entrar no ar** (consulte o botão acima) para publicar sua oferta em vitrines comerciais relevantes do Marketplace. Uma vez ao vivo, sua oferta manterá seu status de venda conjunta da solução GTM OCP original.

## <a name="next-steps"></a>Próximas etapas

- [Revenda por meio de Parceiros do CSP](cloud-solution-providers.md)
- [Configurar a televenda para uma oferta de Marketplace comercial](commercial-marketplace-co-sell.md)
- Exibir essas [perguntas frequentes](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
