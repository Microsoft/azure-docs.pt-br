---
title: Perguntas frequentes sobre a transição do Portal do Cloud Partner para o Partner Center – Microsoft Commercial Marketplace
description: Respostas para perguntas frequentes sobre a transição de ofertas de Portal do Cloud Partner para o Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 091feacceb510038786ae487c0895f9ff1e47aba
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880331"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Perguntas frequentes sobre a transição do Portal do Cloud Partner para o Partner Center

O Portal do Cloud Partner fez a transição para o Partner Center. O Partner Center oferece uma experiência simplificada e integrada para a publicação de novas ofertas no [Microsoft AppSource](https://appsource.microsoft.com/) ou no [Azure Marketplace](https://azuremarketplace.microsoft.com/) e para o gerenciamento de ofertas existentes que foram migradas do portal do Cloud Partner. Todas as funções do Portal do Cloud Partner estão disponíveis no Partner Center. Este artigo aborda as perguntas mais frequentes sobre isso.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>O que a transição para o Partner Center significa para mim?

Você pode continuar fazendo negócios no Partner Center:

| Área<img src="" width=200px> | Alterações |
| --- | --- |
| Conta | Não é necessário criar uma nova conta do Partner Center; Você pode usar suas credenciais de Portal do Cloud Partner existentes para fazer logon no Partner Center, em que agora você gerenciará sua conta, os usuários, as permissões e a cobrança. As informações do contrato de publicação e do perfil da empresa são migradas para sua nova conta do Partner Center, juntamente com quaisquer informações de perfil de pagamento, contas de usuário e permissões e ofertas ativas. Saiba mais em [gerenciar sua conta do Marketplace comercial no Partner Center](partner-center-portal/manage-account.md). |
| Ofereça experiência de gerenciamento de publicação e oferta | Nós movemos seus dados de oferta do Portal do Cloud Partner para o Partner Center. Agora você vai acessar suas ofertas no Partner Center, o que oferece uma experiência de usuário aprimorada e uma interface intuitiva. Saiba como [atualizar uma oferta existente no Marketplace comercial](partner-center-portal/update-existing-offer.md). |
| Disponibilidade de suas ofertas no Marketplace comercial | Sem alterações. Se sua oferta estiver ativa no Marketplace comercial, ela continuará a ser dinâmica. |
| Novas compras e implantações | Sem alterações. Seus clientes podem continuar comprando e implantando suas ofertas sem interrupções. |
| Pagamentos | Todas as compras e implantações continuarão a ser pagas normalmente. Saiba mais sobre como ser [pago no mercado comercial](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| Integrações de API com APIs existentes de [Portal do Cloud Partner ](cloud-partner-portal-api-overview.md) | As APIs existentes do Portal do Cloud Partner ainda têm suporte e suas integrações existentes ainda funcionam. Saiba mais em [as APIs REST do portal do Cloud Partner terão suporte?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Análise | Você pode continuar a monitorar as vendas, avaliar o desempenho e otimizar suas ofertas no Marketplace comercial exibindo análise no Partner Center. Há diferenças entre o modo como os relatórios de análise são exibidos no CPP e no Partner Center. Por exemplo, as **informações do vendedor** em cpp têm uma guia **Orders & Usage** que exibe dados para ofertas baseadas em uso e ofertas não baseadas em uso, enquanto no Partner Center a página **pedidos** tem uma guia separada para ofertas de SaaS. Saiba mais em [relatórios analíticos do Access para o mercado comercial no Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>É necessário criar uma nova conta para gerenciar minhas ofertas no Partner Center?

Não, sua conta será preservada. Isso significa que, se você for um parceiro existente, poderá usar suas credenciais de conta de Portal do Cloud Partner existentes para entrar no Partner Center. Não crie uma nova conta ou quaisquer ofertas criadas no Portal do Cloud Partner e movidas para o Partner Center não serão associadas a ela.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quais páginas no Partner Center correspondem às páginas que usei na Portal do Cloud Partner?

A seguir estão os links do Partner Center para páginas comumente usadas no Portal do Cloud Partner. Se você salvou os links de Portal do Cloud Partner como indicadores, convém atualizá-los.

| Página do Portal do Cloud Partner <img src="" width=100px>| Link da página Portal do Cloud Partner | Link da página do Partner Center |
| --- | --- | --- |
| Página de todas as ofertas | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Página de todos os publicadores | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Perfil do editor | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Página Usuários | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Página de histórico | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | O recurso de histórico ainda não tem suporte no Partner Center. |
| Painel de informações | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Relatórios de pagamento | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Diferenças no relatório de pagamento

Essas são as diferenças no relatório de pagamento entre o Portal do Cloud Partner desativado e o Partner Center atual:

| Portal de Parceiros de Nuvem | Partner Center |
| --- | --- |
| **Link**: https://cloudpartner.azure.com/ | **Link**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navegação**: Relatório de pagamento fornecido no Pagamento de Insights | **Navegação**: Relatório de pagamento fornecido no Partner Center - Ícone de Pagamento |
| **Escopo**:<ul><li>A transação por item de linha está visível para coleta em andamento, coletados e pagos.</li><li>Relatório - mostra todos os itens de linha após a criação da ordem de compra, incluindo a coleta em andamento e a cobrança em andamento, bem como o status de coleta e os itens de linha que ainda não estão qualificados para serem pagos.</li></ul> | **Escopo**:<ul><li>Mostra os itens de linha depois que eles são considerados ganhos qualificados.</li><li>Os clientes pagam para a Microsoft primeiro e depois os ISVs podem ver a inicialização do relatório de pagamento.</li><li>O relatório de pagamento não mostra a coleção em andamento e a cobrança em andamento.</li></ul> |
| **A transação não está pronta para o pagamento**: Cobrança em andamento | **A transação não está pronta para o pagamento**: Próximo pagamento estimado: O status de pagamento está no estado não processado. |
| **Status de pagamento**: n/a | **Status de pagamento**:<ul><li>Não processado: O ganho está qualificado para pagamento.</li><li>Próximos: O ganho será enviado ao editor no próximo pagamento mensal.</li><li>Enviado: O pagamento foi enviado ao seu banco.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>E quanto às ofertas que publiquei na Portal do Cloud Partner?

As ofertas foram movidas para o Partner Center e poderão ser acessadas depois que você entrar no Partner Center, com exceção do serviço gerenciado do Dynamics NAV e das ofertas de Cortana Intelligence. Se sua oferta esteve ativa no Marketplace comercial, ela continuará a ser dinâmica e seus clientes continuarão a ser capazes de comprá-lo e implantá-lo sem interrupções. Veja a próxima pergunta, **quais ofertas foram movidas para o Partner Center?** para obter mais detalhes.

## <a name="what-offers-were-moved-to-partner-center"></a>Quais ofertas foram movidas para o Partner Center?

Todos os tipos de oferta com suporte anteriormente na Portal do Cloud Partner têm suporte no Partner Center, com exceção do serviço gerenciado do Dynamics NAV e das ofertas de Cortana Intelligence.

Para os tipos de oferta com suporte no Partner Center, todas as ofertas foram movidas independentemente de seu status; as ofertas de rascunho, de lista de reversão e de visualização também são movidas.

| Tipo de oferta <img src="" width=150px>| Mudou para o Partner Center? <img src="" width=100px>| Próximas etapas |
| --- | --- | --- |
| SaaS | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [planejar uma oferta de SaaS para o Marketplace comercial](plan-saas-offer.md). |
| Máquina Virtual | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [planejar uma oferta de máquina virtual](marketplace-virtual-machines.md). |
| Aplicativo do Azure | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta de aplicativo do Azure](create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta do Dynamics 365 Business central](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 para compromisso com o cliente & PowerApps | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar um Dynamics 365 para o envolvimento do cliente & oferta do PowerApps](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta do Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md). |
| Aplicativo do Power BI | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar um aplicativo Power bi para AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| Módulo do IoT Edge | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar, configurar e publicar uma oferta de módulo IOT Edge no Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Contêiner | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta de contêiner do Azure](./create-azure-container-offer.md). |
| Serviço de consultoria | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta de serviço de consultoria](./create-consulting-service-offer.md). |
| Serviço gerenciado | Sim | Entre no Partner Center para criar novas ofertas e gerenciar ofertas que foram criadas no Portal do Cloud Partner. Saiba mais em [criar uma oferta de serviço gerenciado](./plan-managed-service-offer.md). |
| Serviço gerenciado do Dynamics NAV | Não | A Microsoft desenvolveu o serviço gerenciado do Dynamics NAV no [dynamics 365 Business central](/dynamics365/business-central/)e, portanto, listamos as ofertas dinâmicas de serviço gerenciado do Dynamics NAV do AppSource. Essas ofertas não são mais detectáveis pelos clientes e não foram movidas para o Partner Center. Para disponibilizar suas ofertas no AppSource, adapte-as às ofertas do Dynamics 365 Business central e envie-as no [Partner Center](https://partner.microsoft.com/). Saiba mais em [criar uma oferta do Dynamics 365 Business central](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Não | A Microsoft evoluiu o mapa de estrada do produto para Cortana Intelligence, portanto, nós a listamos Cortana Intelligence ofertas ao vivo da AppSource. Essas ofertas não são mais detectáveis pelos clientes e não foram movidas para o Partner Center. Para disponibilizar suas ofertas no mercado comercial, adapte suas ofertas para ofertas de software como serviço (SaaS) e envie-as para o [Partner Center](https://partner.microsoft.com/). Saiba mais na [lista de verificação de criação de oferta de SaaS no Partner Center](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Não consigo encontrar minhas ofertas de Portal do Cloud Partner no Partner Center

O que você vê no Partner Center depende dos programas nos quais você está inscrito, das contas às quais você pertence e das funções e permissões de usuário atribuídas. Há muitos programas do Partner Center disponíveis e você pode estar inscrito em vários programas. Você também pode ter acesso a várias contas com as mesmas credenciais de usuário.

As ofertas que você criou no Portal do Cloud Partner estão disponíveis no Partner Center no programa do **Marketplace comercial** e na conta usada para criar as ofertas. Para verificar se você está exibindo o programa certo e a conta certa, siga as etapas abaixo. Para obter outras dicas de solução de problemas, consulte [gerenciar sua conta do Partner Center](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Acesse o programa certo no Partner Center

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) com as mesmas credenciais usadas para entrar no portal do Cloud Partner. O painel de navegação à esquerda exibe as opções associadas aos programas nos quais você está inscrito.

    Exemplo: Suponha que você tenha acesso a três programas: o programa MPN, o programa de referências e o programa do Marketplace comercial. Ao entrar no Partner Center, você verá esses três programas no painel de navegação.

2. Selecione   >  **visão geral** do Marketplace comercial para acessar suas ofertas.

    Se você não vir o programa comercial do Marketplace no painel de navegação à esquerda, você poderá estar na conta errada. Siga as etapas na próxima seção para acessar a conta correta.

    [![Captura de tela que mostra o menu visão geral do Partner Center](media/cpp-pc-faq/overview-menu.png "Mostra o menu de visão geral do Partner Center")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Acesse a conta certa no Partner Center

Se você fizer parte de várias contas, no Partner Center você verá um botão de seletor de conta marcado por duas setas no menu de navegação à esquerda. Selecione o botão selecionador de conta para exibir uma lista de todas as contas às quais você pertence. Selecione qualquer conta na lista para alternar para ela e ver todos os programas e informações pertencentes a essa conta. Se você não vir um botão de seletor de conta no menu de navegação, será membro de uma única conta.

[![Captura de tela mostra o botão do seletor de conta do Partner Center.](media/cpp-pc-faq/picker-button.png "Mostra o botão do seletor de conta do Partner Center")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Como fazer criar novas ofertas?

Acesse o programa do Marketplace comercial no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para criar novas ofertas. Na página Visão geral, selecione **+ nova oferta**.

[![Captura de tela mostra o menu visão geral do Partner Center.](media/cpp-pc-faq/new-offer.png "Mostra o menu de visão geral do Partner Center")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Não consigo entrar e preciso abrir um tíquete de suporte

Se não for possível entrar em sua conta, você poderá abrir um [tíquete de suporte](https://partner.microsoft.com/support/v2/?stage=1) aqui.

## <a name="where-are-instructions-for-using-partner-center"></a>Onde estão as instruções para usar o Partner Center?

Vá para a [documentação do Marketplace comercial.](index.yml)em seguida, expanda o **portal do Marketplace comercial no Partner Center**. Para ver os artigos de ajuda para a criação de ofertas no Partner Center, expanda **criar uma nova oferta**.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Quais são as diferenças de gerenciamento de publicação e oferta?

Aqui estão algumas diferenças entre o Portal do Cloud Partner e o Partner Center.

### <a name="modular-publishing-capabilities"></a>Recursos de publicação modulares

O Partner Center fornece uma opção de publicação modular que permite selecionar as alterações que você deseja publicar em vez de sempre publicar todas as atualizações ao mesmo tempo. Por exemplo, a tela a seguir mostra que as únicas alterações selecionadas a serem publicadas são as alterações nas **Propriedades** e na  **listagem de ofertas**. As alterações feitas na página visualização não serão publicadas.

[![Captura de tela mostra a página de revisão e de publicação do Partner Center.](media/cpp-pc-faq/review-page.png "Mostra a página revisar e publicar do Partner Center")](media/cpp-pc-faq/review-page.png#lightbox)

As atualizações que você não publica são salvas como rascunhos. Continue a usar sua visualização de oferta para verificar sua oferta antes de torná-la dinâmica para o público.

### <a name="enhanced-preview-options"></a>Opções de visualização avançada

O Partner Center inclui um recurso [comparar](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) com opções de filtragem aprimoradas. Isso lhe dá a capacidade de comparar com as versões de visualização e dinâmica da oferta.

[![Captura de tela mostra o recurso de comparação do Partner Center.](media/cpp-pc-faq/compare.png "Mostra o recurso de comparação do Partner Center")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Alterações de identidade visual e navegação

Você observará algumas alterações de identidade visual. Por exemplo, as *SKUs* são marcadas como *planos* no Partner Center:

[![Captura de tela mostra a página planos do Partner Center.](media/cpp-pc-faq/plans.png "Mostra a página de planos do Partner Center")](media/cpp-pc-faq/plans.png#lightbox)

Além disso, as informações fornecidas anteriormente nas páginas de detalhes do **Marketplace** ou da **vitrine**  (serviço de consultoria, Power BI aplicativo) na portal do Cloud Partner agora são coletadas na página de **listagem de ofertas** no Partner Center:

[! [Captura de tela mostra a página de listagem da oferta do Partner Center.] (mídia/cpp-PC-perguntas frequentes/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

As informações fornecidas anteriormente para SKUs em uma única página no Portal do Cloud Partner podem agora ser coletadas em várias páginas no Partner Center:

- Página de configuração do plano
- Página Listagem de planos
- Página de disponibilidade do plano
- Página planejar configuração técnica, conforme mostrado aqui:

[![Mostra a página de configuração técnica do Partner Center.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Sua ID de oferta agora é mostrada na barra de navegação à esquerda da oferta:

![Mostra o local da ID da oferta do Partner Center](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Parar de vender a oferta

Você pode solicitar para [parar de vender uma oferta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) no Marketplace diretamente do portal do Partner Center. A opção está disponível na página **Visão geral da oferta** para sua oferta.

[![Captura de tela mostra a página do centro de parceiros para parar de vender uma oferta.](media/cpp-pc-faq/stop-sell.png "Mostra a página do centro de parceiros para parar de vender uma oferta")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>As APIs REST do Portal do Cloud Partner ainda têm suporte?

As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão a funcionar. A transição para o Partner Center apresenta pequenas alterações. Examine a tabela abaixo para garantir que seu código continue a funcionar no Partner Center.

| API <img src="" width=100px>| Descrição das alterações | Impacto |
| --- | --- | --- |
| POSTAR publicação, GoLive, cancelar | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o status da operação. | Ao enviar qualquer uma das solicitações POST correspondentes para uma oferta, o cabeçalho Location terá um dos dois formatos, dependendo do status de migração da oferta: <ul><li>Ofertas não migradas: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operação GET | Para ofertas que anteriormente eram compatíveis com um campo ' Notification-email ' na resposta, esse campo será preterido e não retornará mais para ofertas migradas. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nas solicitações. Em vez disso, o serviço de API será alinhado com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações de andamento da operação serão enviadas para o endereço de email definido na seção informações de contato do vendedor de suas configurações de conta no Partner Center.<br><br>Verifique se o endereço de email definido na seção informações de contato do vendedor nas [configurações da conta](https://partner.microsoft.com/dashboard/account/management) no Partner Center está correto para receber notificações. |
|||