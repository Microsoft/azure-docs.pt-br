---
title: Obtenha suporte para o programa do Marketplace comercial no Partner Center
description: Saiba mais sobre as opções de suporte para o programa comercial do Marketplace no Partner Center, incluindo como arquivar uma solicitação de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879234"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Suporte para o programa do Marketplace comercial no Partner Center

A Microsoft fornece suporte para uma ampla variedade de produtos e serviços. Encontrar a equipe de suporte correta é importante para garantir uma resposta apropriada e oportuna. Considere os seguintes cenários, que podem ajudá-lo a rotear sua consulta para a equipe apropriada:

- Se você for um Publicador e tiver uma pergunta de um cliente, peça ao cliente para solicitar suporte usando os links de suporte no [portal do Azure](https://portal.azure.com/).
- Se você for um Publicador e tiver detectado um problema de segurança com um aplicativo em execução no Azure, consulte [como registrar um tíquete de suporte de evento de segurança](../security/fundamentals/event-support-ticket.md). Os editores devem relatar eventos de segurança suspeitos, incluindo incidentes e vulnerabilidades de segurança das ofertas de serviço e de software do Microsoft Azure Marketplace na primeira oportunidade.
- Se você for um Publicador e tiver uma pergunta relacionada ao seu aplicativo ou serviço, examine as opções de suporte a seguir.

## <a name="get-help-or-open-a-support-ticket"></a>Obter ajuda ou abrir um tíquete de suporte

1. Entre com as informações da sua conta. Se você ainda não tiver feito isso, será necessário [criar uma conta do Partner Center](partner-center-portal/create-account.md).

1. No menu no canto superior direito da página, selecione o ícone de **suporte** . O painel **ajuda e suporte** é exibido no lado direito da página.

1. Para obter ajuda com o Marketplace comercial, selecione **Marketplace comercial**.

   ![Menu suspenso de suporte](./media/support/commercial-marketplace-support-pane.png)

1. Na caixa **Resumo do problema** , insira uma breve descrição do problema.

1. Na caixa **tipo de problema** , siga um destes procedimentos:

    - **Opção 1**: insira palavras-chave como: Marketplace, aplicativo do Azure, oferta de SaaS, gerenciamento de conta, gerenciamento de Lead, problema de implantação, pagamento ou migração de oferta de covenda. Em seguida, selecione um tipo de problema na lista recomendada que aparece.

    - **Opção 2**: selecione **Procurar tópicos** na lista **categoria** e, em seguida, selecione **Marketplace comercial**. Em seguida, selecione o **tópico** e o **subtópico** apropriados.

1. Depois de encontrar o tópico de sua escolha, selecione **examinar soluções**.

    ![Próxima etapa](./media/support/next-step.png)

As seguintes opções são mostradas:

- Para selecionar um tópico diferente, clique em **selecionar um problema diferente**.
- Para ajudar a resolver o problema, examine as etapas e os documentos recomendados, se disponíveis.

    ![Soluções recomendadas](./media/support/recommended-solutions.png)

Se você não conseguir encontrar sua resposta na autoajuda, selecione **fornecer detalhes do problema**. Preencha todos os campos obrigatórios para acelerar o processo de resolução e selecione **Enviar**.

>[!Note]
>Se você não tiver entrado no Partner Center, talvez seja necessário entrar antes de criar um tíquete.

## <a name="track-your-existing-support-requests"></a>Acompanhe suas solicitações de suporte existentes

Para examinar os tíquetes abertos e fechados, no menu de navegação esquerdo, selecione suporte do **Marketplace comercial**  >  .

## <a name="record-issue-details-with-a-har-file"></a>Registrar detalhes do problema com um arquivo HAR

Para ajudar os agentes de suporte a solucionar o problema, considere anexar um arquivo HAR (formato de arquivo HTTP) ao seu tíquete de suporte. Os arquivos HAR são logs de solicitações de rede em um navegador da Web.

> [!WARNING]
> Os arquivos HAR podem registrar dados confidenciais sobre sua conta do Partner Center.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge e Google Chrome

Para gerar um arquivo HAR usando **o Microsoft Edge ou o** **Google Chrome**:

1. Vá para a página da Web onde você está enfrentando o problema.
2. No canto superior direito da janela, selecione o ícone de reticências e, **em seguida, ferramentas de**  >  **desenvolvedor** ferramentas. Você pode pressionar F12 como um atalho.
3. No painel Ferramentas de desenvolvedor, selecione a guia **rede** .
4. Selecione **parar a gravação do log de rede** e **desmarque** para remover os logs existentes. O ícone de registro ficará cinza.

    ![Como remover os logs existentes no Microsoft Edge ou no Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selecione **registrar log de rede** para iniciar a gravação. Quando você iniciar a gravação, o ícone de registro ficará vermelho.

    ![Como iniciar a gravação no Microsoft Edge ou no Google Chrome](media/support/chromium-start-session.png)

6. Reproduza o problema que você deseja solucionar.
7. Depois de reproduzir o problema, selecione **parar de gravar o log de rede**.
8. Selecione **Exportar Har**, marcado com um ícone de seta para baixo e salve o arquivo.

    ![Como exportar um arquivo HAR no Microsoft Edge ou no Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Para gerar um arquivo HAR usando o **Mozilla Firefox**:

1. Vá para a página da Web onde você está enfrentando o problema.
1. No canto superior direito da janela, selecione o ícone de reticências e, em seguida, ferramentas de alternância de **desenvolvedor da Web**  >  . Você pode pressionar F12 como um atalho.
1. Selecione a guia **rede** e, em seguida, selecione **limpar** para remover os logs existentes.

    ![Como remover os logs existentes no Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reproduza o problema que você deseja solucionar.
1. Depois de reproduzir o problema, selecione **Har exportar/importar**  >  **salvar tudo como Har**.

    ![Como exportar um arquivo HAR no Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Para gerar um arquivo HAR usando o **Safari**:

1. Habilitar as ferramentas de desenvolvedor no Safari: selecione Preferências do **Safari**  >  . Vá para a guia **avançado** e, em seguida, selecione **Mostrar menu desenvolver na barra de menus**.
1. Vá para a página da Web onde você está enfrentando o problema.
1. Selecione **Desenvolver** e, em seguida, selecione **Mostrar Inspetor da Web**.
1. Selecione a guia **rede** e, em seguida, selecione **limpar itens de rede** para remover os logs existentes.

    ![Como remover os logs existentes no Safari](media/support/safari-clear-session.png)

1. Reproduza o problema que você deseja solucionar.
1. Depois de reproduzir o problema, selecione **Exportar** e salve o arquivo.

    ![Como exportar um arquivo HAR no Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](partner-center-portal/update-existing-offer.md)