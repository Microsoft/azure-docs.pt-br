---
title: Como examinar e publicar uma oferta no Microsoft Commercial Marketplace
description: Use o Partner Center para enviar sua oferta para visualização, Visualizar sua oferta e, em seguida, publicá-la no Microsoft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: f8ddcbef6d2ce4a9cba625374db9908335954424
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630887"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Como revisar e publicar uma oferta no Marketplace comercial

Este artigo mostra como usar o Partner Center para enviar sua oferta de publicação, Visualizar sua oferta e, em seguida, publicá-la no Marketplace comercial. Também abordaremos como verificar o status de publicação, pois ele prossegue pelas etapas de publicação. Você já deve ter criado uma oferta que deseja publicar.

## <a name="offer-status"></a>Status da oferta

Você pode revisar o status da sua oferta na guia **visão geral** do painel do Marketplace comercial no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Um dos seguintes indicadores de status é exibido na coluna **status** para cada oferta.

| Status | Descrição |
| ------------ | ------------- |
| Rascunho | A oferta foi criada, mas não está sendo publicada. |
| Publicação em andamento | A oferta está funcionando de forma por meio do processo de publicação. |
| Ação necessária | Descobrimos um problema crítico durante a certificação ou durante outra fase de publicação. |
| Versão Prévia | Certificamos a oferta, que agora aguarda uma verificação final pelo Publicador. Selecione **entrar em tempo real** para publicar a oferta ao vivo. |
| Ao vivo | A oferta está ativa no Marketplace e pode ser vista e adquirida pelos clientes. |
| Venda de parada pendente | O Publicador selecionou "parar venda" em uma oferta ou plano, mas a ação ainda não foi concluída. |
| Não disponível no Marketplace | Uma oferta publicada anteriormente no marketplace foi removida. |
|||

## <a name="validation-and-publishing-steps"></a>Etapas de validação e publicação

Quando estiver pronto para enviar uma oferta para publicação, selecione **revisar e publicar** no canto superior direito do Portal. A página **revisar e publicar** mostra o status de cada página para sua oferta, que pode ser uma das seguintes:

   - **Não iniciado** – a página está incompleta.
   - **Incompleto** – a página não tem informações necessárias ou tem erros que precisam ser corrigidos. Você precisará voltar para a página e atualizá-la.
   - **Concluída** – a página está concluída. Todos os dados necessários foram fornecidos e não há erros.

Se qualquer uma das páginas tiver um status diferente de **concluído**, você precisará corrigir o problema nessa página e retornar à página **revisar e publicar** para confirmar se o status agora aparece como **concluído**. Alguns tipos de oferta exigem testes. Nesse caso, você verá um campo **observações para a certificação** , em que você precisa fornecer instruções de teste à equipe de certificação e qualquer nota suplementar útil para entender seu aplicativo.

Depois que todas as páginas forem concluídas e você tiver inserido as notas de teste aplicáveis, selecione **publicar** para iniciar os processos de validação e publicação. As fases e a sequência geral podem variar dependendo do tipo de oferta que você está publicando. A tabela a seguir mostra um possível fluxo de publicação. Cada fase é explicada com mais detalhes nas seções a seguir.

| Fase | O que acontece |
| ------------ | ------------- | ------------- |
| [Validação automatizada](#automated-validation-phase) | Processamos um conjunto de validações automatizadas. |
| [Certificação](#certification-phase) | Realizamos validações manuais. |
| [Criação de visualização](#preview-creation-phase) | A página de listagem de sua oferta de visualização está disponível para qualquer pessoa que tenha o link de visualização. Se sua oferta for vendida pela Microsoft (transformada), somente o público especificado na página **público de visualização** da sua oferta poderá comprar e acessar a oferta para teste. |
| [Aprovação do publicador](#publisher-sign-off-phase) | Enviamos um email com uma solicitação para você visualizar e aprovar sua oferta. |
| [Publicar](#publish-phase) | Executamos uma série de etapas para verificar se a oferta de visualização está publicada em tempo real no mercado comercial. |
|||

## <a name="automated-validation-phase"></a>Fase de validação automatizada

A primeira etapa no processo de publicação é um conjunto de validações automatizadas. Cada etapa de validação corresponde a um recurso escolhido quando você criou a oferta. Cada verificação de validação deve ser concluída antes que a oferta possa avançar para a próxima etapa no processo de publicação.

- **Instalação do fluxo de compra da oferta** (<10 min)

   Garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.

- **Validação de dados do Test Drive** (aproximadamente 5 min)

   Validamos os dados fornecidos na página de configuração técnica da oferta. Testamos e aprovamos test drive funcionalidade. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Provisionamento de drive de teste** (~ 30 min)

    Depois de validar os dados e a funcionalidade do seu test drive na etapa anterior, implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Validação e registro de gerenciamento de leads** (<15 min)

    Confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos na página de **configuração da oferta** . Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

- **Validação da oferta** (<30 min)

    Somente para ofertas de SaaS, executamos validações rápidas para conteúdo e configurações técnicas. A criação da visualização não será bloqueada em problemas de validação detectados nesta etapa. Se encontrarmos quaisquer problemas que possam falhar na certificação, um link **Exibir relatório de validação** aparecerá na página **visão geral da oferta** . O relatório conterá descrições de quaisquer violações de política.

    > [!IMPORTANT]
    > Certifique-se de examinar e endereçar todos os avisos da etapa de validação da oferta antes de selecionar o botão **ir ao vivo** . Caso contrário, você correrá o risco de realizar uma falha na certificação que atrasará a sua oferta.

## <a name="certification-phase"></a>Fase de certificação

As ofertas enviadas ao Marketplace comercial devem ser certificadas antes de serem publicadas. As ofertas passam por testes rigorosos, alguns manuais automatizados e outros. Para saber mais, confira [políticas de certificação do Marketplace comercial](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Há três níveis de validação incluídos no processo de certificação para cada oferta enviada.

- Elegibilidade de negócios do Publicador
- Validação de conteúdo
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade de negócios do Publicador

Cada tipo de oferta verifica um conjunto de critérios de qualificação base necessários. Esses critérios podem incluir o status de MPN do Publicador, as competências mantidas, os níveis de competência e assim por diante.

#### <a name="content-validation"></a>Validação de conteúdo

As informações inseridas quando você criou sua oferta são verificadas quanto à qualidade e relevância. Essas verificações revisarão suas entradas para o Marketplace listar detalhes, preços, disponibilidade, planos associados e assim por diante. Para atender aos critérios de listagem do Microsoft AppSource e do Azure Marketplace, validaremos que sua oferta inclui:

- Um título que descreve com precisão a oferta
- Descrições bem escritas que fornecem uma visão geral completa e uma proposta de valor
- Capturas de tela e vídeos de qualidade
- Uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo lendo as [políticas de listagem geral](/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (pacote ou binário) passa pelas verificações a seguir.

- Verificação de malware
- Chamadas de rede monitoradas
- Pacote analisado
- Verificação completa da funcionalidade da oferta

A oferta é testada em várias plataformas e versões para garantir que ela seja robusta.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Se sua oferta falhar em qualquer lista, técnica ou verificação de política, ou se você não estiver qualificado para enviar uma oferta desse tipo, enviaremos um relatório de falha de certificação por email para você.

Este relatório contém descrições de todas as políticas que falharam, juntamente com as notas de revisão. Examine este relatório de email, resolva quaisquer problemas, faça atualizações em sua oferta quando necessário e reenvie a oferta usando o [portal do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. Você pode reenviar a oferta quantas vezes forem necessárias até passar a certificação.

## <a name="preview-creation-phase"></a>Fase de criação da versão prévia

Durante a fase de criação de versão prévia, criamos uma versão de sua oferta que será acessível somente para o público especificado na página do **público de visualização** de sua oferta, se houver. A versão de visualização da sua oferta não estará disponível para ninguém fora do público de visualização até que você publique a oferta ao vivo.

> [!NOTE]
> Não use o público de visualização para dar às pessoas fora da visibilidade da sua organização uma oferta. Em vez disso, use a opção oferta privada. Neste ponto, sua oferta não foi totalmente testada e validada e não está pronta para distribuição externa.

## <a name="cancel-publishing"></a>Cancelar publicação

Para cancelar uma oferta com o status **publicação em andamento** :

1. Selecione o nome da oferta para abrir a página **visão geral da oferta** .
1. Selecione **Cancelar publicação** no canto superior direito da página.
1. Confirme que você deseja impedir que a oferta seja publicada.

Se você quiser publicar a oferta em um momento posterior, será necessário iniciar o processo de publicação.

> [!NOTE]
> Você pode interromper a publicação de uma oferta somente se a oferta ainda não tiver progredido para a etapa de aprovação do Publicador. Depois de selecionar **entrar em ativação**, você não terá a opção de cancelar a publicação mais.

## <a name="publisher-sign-off-phase"></a>Fase de aprovação do Publicador

Quando a oferta estiver pronta para você revisar e ser desligada, enviaremos um email para solicitar que você examine e aprove sua versão prévia da oferta. Você também pode atualizar a página **visão geral da oferta** em seu navegador para ver se sua oferta atingiu a fase de aprovação do Publicador. Se tiver, o botão **Go Live** e os links de visualização estarão disponíveis.

A captura de tela a seguir mostra a página **visão geral da oferta** no programa do Marketplace comercial no Partner Center. As etapas de validação que você verá nessa página variam de acordo com o tipo de oferta e as seleções feitas quando você criou a oferta. Observe que a fase de certificação pode ocorrer durante o envio para visualização ou envio para o Live, dependendo do tipo de oferta.

![Ilustra a página Visão geral da oferta para uma oferta no Partner Center. O botão Go Live e os links de visualização são mostrados.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Visualizando e aprovando sua oferta

> [!IMPORTANT]
> Para validar o fluxo de compra e configuração de ponta a ponta, compre sua oferta enquanto ela estiver em versão prévia. Primeiro Notifique a Microsoft com um [tíquete de suporte](https://aka.ms/marketplacesupport) para garantir que não processaremos uma cobrança. Se sua oferta for uma listagem _entre em contato comigo_ , teste se um cliente potencial foi criado conforme o esperado fornecendo os detalhes de contato comigo durante a visualização.

Na página **visão geral da oferta** , você verá links de visualização no botão **ir ao vivo** . Haverá um link para a visualização do AppSource, visualização do Azure Marketplace ou ambos, dependendo das opções escolhidas ao criar sua oferta. Se você optar por vender sua oferta pela Microsoft, qualquer pessoa que tenha sido adicionada ao público de visualização poderá testar a aquisição e a implantação de sua oferta para garantir que ela atenda às suas necessidades durante esse estágio.

> [!IMPORTANT]
> Certifique-se de examinar e endereçar todos os avisos da etapa de _validação da oferta_ antes de selecionar o botão **ir ao vivo** . Caso contrário, você correrá o risco de realizar uma falha na certificação que atrasará a sua oferta.

Depois de aprovar sua versão prévia, selecione **entrar em tempo real** para publicar sua oferta ao vivo no Marketplace comercial. 

Se você quiser fazer alterações depois de visualizar a oferta, poderá editar e reenviar a solicitação de publicação. Se sua oferta já estiver ativa e disponível para o público no Marketplace, as atualizações feitas não entrarão em funcionamento até que você selecione **entrar em tempo real*. Para obter mais informações, consulte [atualizar uma oferta existente no Marketplace comercial](partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Fase de publicação

Agora que você optou por entrar em operação com sua oferta, que a torna disponível no mercado comercial, realizamos uma série de verificações finais de validação para garantir que a oferta ao vivo seja configurada da mesma forma que a versão prévia da oferta.

- **Instalação do fluxo de compra da oferta** (>10 min)

    Garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.

- **Validação de dados do Test Drive** (aproximadamente 5 min)

    Validamos os dados fornecidos na página de configuração técnica da oferta. Testamos e aprovamos test drive funcionalidade. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Provisionamento de drive de teste** (~ 30 min)

    Implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Validação e registro de gerenciamento de leads** (>15 min)

    Confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos em sua página de **configuração de oferta** . Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

- **Publicação final (>30 minutos)**

    Garantimos que sua oferta se torne publicamente disponível no Marketplace.

Depois que essas verificações de validação forem concluídas, sua oferta estará ativa no Marketplace.

## <a name="publishing-history"></a>Histórico de publicação

A página **histórico** no Partner Center mostra os eventos de publicação para suas ofertas do Marketplace comercial. Para cada evento, a página exibe o usuário que iniciou a ação, o tipo de evento e a data e hora do evento. As [etapas de validação e publicação](#validation-and-publishing-steps) são listadas com a data e a hora de conclusão.

Para exibir o histórico de sua oferta:

1.    Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2.    No menu de navegação à esquerda, selecione   >  **visão geral** do Marketplace comercial.
3.    Selecione sua oferta.
4.    Selecione a guia **histórico** no menu de navegação à esquerda. A página fornece detalhes para os seguintes eventos, conforme aplicável à sua oferta:

|Evento    |Descrição    |
|---------|---------------|
|Oferta criada    |A oferta foi criada no Partner Center. Um usuário selecionou o tipo de oferta, a ID da oferta e o alias de oferta na  >  **visão geral** do Marketplace comercial.    |
|Plano criado: *nome do plano*    |Um usuário criou um novo plano inserindo a ID do plano e o nome do plano na guia **visão geral do plano** .</br>*Esse evento se aplica somente aos tipos de oferta que dão suporte a planos*.    |
|Plano excluído    |Um usuário excluiu um plano de rascunho que não foi publicado selecionando **excluir rascunho** na página **visão geral do plano** .</br>*Esse evento se aplica somente aos tipos de oferta que dão suporte a planos*.    |
|Reinício do plano iniciado venda: *nome do plano*    |Um usuário iniciou um plano de parada-venda selecionando **parar de vender** na página **visão geral do plano** .</br>*Esse evento se aplica somente aos tipos de oferta que dão suporte a planos*.    |
|Desfazer plano parar venda: *nome do plano*    |Um usuário cancelou um plano de parada-venda selecionando **desfazer parar de vender** na página **visão geral do plano** .</br>*Esse evento se aplica somente aos tipos de oferta que dão suporte a planos*.    |
|Oferta enviada para visualização    |Um usuário enviou a oferta para visualização selecionando **publicar** na página **revisar e publicar** .    |
|Envio iniciado para visualizar cancelamento    |Um usuário solicitou a cancelamento da publicação da oferta, selecionando **Cancelar publicação** na página **visão geral da oferta** após o envio para visualização.</br>*Esse evento é exibido à medida que a solicitação de cancelamento está sendo processada*.    |
|Envio cancelado para visualização    |Um usuário cancelou a publicação da oferta para visualização selecionando **Cancelar publicação** na página **visão geral da oferta** após o envio para visualização.</br>*Esse evento é exibido depois que a solicitação de cancelamento é processada com êxito*.    |
|Sair para entrar no ar    |Um usuário publicou a oferta no Marketplace comercial selecionando **Go Live** na página **visão geral da oferta** .    |
|Cancelamento de publicar no Marketplace iniciado    |Um usuário solicitou a cancelamento da publicação da oferta selecionando **Cancelar publicação** na página **visão geral da oferta** após a aprovação para entrar em tempo real.</br>*Esse evento é exibido à medida que a solicitação de cancelamento está sendo processada*.    |
|Publicação cancelada no Marketplace comercial    |Um usuário cancelou a publicação da oferta selecionando **Cancelar publicação** na página **visão geral da oferta** após a aprovação para entrar em funcionamento.</br>*Esse evento é exibido depois que a solicitação de cancelamento é processada com êxito*.    |
|Sincronizar público privado    |Um usuário atualizou e sincronizou o público privado selecionando **sincronizar audiência privada** na página **visão geral do plano** ou na página de **disponibilidade do plano &** .</br>*Esse evento se aplica somente a tipos de oferta que dão suporte a planos privados*.    |
|Parar a oferta de venda    |Um usuário parou de vender a oferta selecionando **parar de vender** na página **visão geral da oferta** .    |

> [!NOTE]
> A página histórico não diz quando um rascunho de oferta foi salvo.

### <a name="filter-options"></a>Opções de filtro

Você pode usar filtros para restringir o histórico completo de sua oferta a eventos de publicação específicos:

1.    Selecione o botão filtrar no canto superior direito da página.
2.    Escolha um filtro e, em seguida, selecione **aplicar** para ver quais eventos de histórico correspondem aos critérios selecionados.
3.    Selecione **limpar filtros** para retornar ao histórico completo de sua oferta.

Há quatro filtros:
* Eventos
* Usuários
* Data
* Pages (Páginas)

Ao escolher o filtro de **páginas** , você pode selecionar qualquer uma das páginas do Partner Center que são aplicáveis ao seu tipo de oferta. Quando aplicado, o filtro **páginas** mostra todas as **ofertas enviadas para visualizar** eventos com alterações na página selecionada.

* Para todas as ofertas, a página de **instalação da oferta** é incluída por padrão para cada evento de envio.
* Para ofertas que dão suporte a planos, a página **visão geral do plano** é incluída para cada evento de envio.
* Para ofertas que dão suporte a test drive, a página **Test Drive** é incluída para cada evento de envio.

### <a name="users"></a>Usuários

Se um evento foi iniciado por um usuário, a página de histórico mostrará o usuário de acordo com os seguintes cenários:

#### <a name="the-event-was-initiated-by-the-publisher"></a>O evento foi iniciado pelo Publicador

Os usuários com permissões de publicação para uma oferta terão seu nome exibido para os eventos de publicação que iniciarem.

[![A página histórico exibe o nome dos usuários com permissões de publicação.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>O evento foi iniciado pela Microsoft

Você pode dar permissões aos administradores da Microsoft para iniciar ações em seu nome ou tomar ações corretivas após um erro de sistema inesperado. O nome e o logotipo da Microsoft são exibidos para publicar eventos iniciados pela Microsoft em nome de sua conta.

[![Exemplo de como a página de histórico exibe eventos iniciados pela Microsoft.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>O evento foi iniciado por um usuário não identificado

Os usuários que não estiverem mais associados a uma conta terão seu nome removido da coluna **iniciado por** depois que suas permissões de publicação forem retiradas.

[![Exemplo de como a página de histórico exibe eventos iniciados por usuários não identificados.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Etapas de validação e publicação

Nenhum usuário é mostrado para processos do sistema que correspondem às [etapas de validação e publicação](#validation-and-publishing-steps). Esses eventos são codificados por cores de acordo com o status de conclusão do evento.

[![Exemplo de como a página de histórico exibe etapas de validação e publicação.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

[Acessar relatórios analíticos para o mercado comercial no Partner Center](partner-center-portal/analytics.md)
