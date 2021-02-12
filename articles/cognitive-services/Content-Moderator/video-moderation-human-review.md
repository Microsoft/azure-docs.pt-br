---
title: Moderação de vídeo com a ferramenta de revisão – Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a moderação de vídeo assistida por computador e a ferramenta de revisão para conteúdo moderado inadequado
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853491"
---
# <a name="video-moderation-with-the-review-tool"></a>Moderação de vídeo com a ferramenta de revisão

Use a [ferramenta de análise](Review-Tool-User-Guide/human-in-the-loop.md) e [moderação de vídeo](video-moderation-api.md) assistida por computador Content moderator para vídeos moderados e transcrições para conteúdo adulto (explícito) e erótico (Sugestor) para obter os melhores resultados para seus negócios.

## <a name="view-videos-under-review"></a>Exibir vídeos em revisão

No painel, selecione qualquer uma das filas de revisão no tipo de conteúdo vídeo. Isso iniciará uma revisão e abrirá a página de moderação de conteúdo do vídeo.

> [!div class="mx-imgBorder"]
> ![Em Content Moderator (visualização), o controle deslizante é realçado e definido como 4 revisões. As opções desfoque All e preto e branco são realçadas e ambas são definidas.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Contagem de revisão

Use o controle deslizante no canto superior direito para definir o número de revisões que você gostaria de exibir na página.

### <a name="view-type"></a>Tipo de exibição

Você pode exibir as diferentes entradas de conteúdo como blocos ou em uma exibição detalhada. A exibição de **detalhes** permitirá que você veja quadros-chave e outras informações sobre o vídeo selecionado. 

> [!NOTE]
> Em vez de emitir quadros em intervalos regulares, o serviço de moderação de vídeo identifica e emite apenas os quadros potencialmente completos (bons). Esse recurso permite uma geração de quadros eficiente para análise de adulto e erótico no nível do quadro.

O modo de exibição **xadrez** mostrará cada vídeo como um único bloco. Selecione o botão expandir acima de um quadro de vídeo para ampliar o vídeo e ocultar os outros.

### <a name="content-obscuring-effects"></a>Efeitos de disfarce de conteúdo

Use as alternâncias **borrar tudo** e **preto e branco** para definir esses efeitos de disfarce de conteúdo. Eles são ativados por padrão. No modo de exibição por **lado** , você pode alternar os efeitos individualmente para cada vídeo.

## <a name="check-video-details"></a>Verificar detalhes do vídeo

No modo de exibição de **detalhes** , o painel direito mostrará várias guias que fornecem detalhes sobre o vídeo.

* Selecione a guia **observações** para adicionar anotações personalizadas a vídeos.
* Selecione a guia **transcrição** para ver a transcrição do vídeo que &mdash; o serviço extrai automaticamente uma transcrição de qualquer fala no vídeo. Quando você seleciona uma seção de texto, o player de vídeo saltará para essa parte do vídeo.
* Selecione a guia **metadados** para exibir os metadados do arquivo de vídeo.
* Selecione a guia **histórico** para ver o histórico da revisão, como quando ela foi criada e como ela foi modificada.

> [!div class="mx-imgBorder"]
> ![O painel direito é realçado e a guia observações é selecionada. Há uma área de teste rotulada adicionar uma anotação.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Aplicar marcas de moderação

A principal tarefa de uma revisão de vídeo é aplicar ou remover marcas de moderação em vídeos ou em partes de vídeos.

### <a name="bulk-tagging"></a>Marcação em massa

A barra de ferramentas de **marcas em massa** permite que você adicione marcas a vários vídeos selecionados de uma vez. Selecione um ou mais vídeos e, em seguida, selecione as marcas que você deseja aplicar e clique em **Enviar**. 

> [!div class="mx-imgBorder"]
> ![O botão + é realçado no painel marcas em massa.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Marcação de quadro-chave

Você também pode adicionar marcas de moderação a quadros-chave específicos. Selecione os quadros no painel do bloco do quadro chave e selecione **marcas de quadro** -chave + para aplicar as marcas desejadas.

> [!NOTE]
> Se o serviço não pôde extrair os quadros-chave, o painel do bloco do quadro chave não mostrará **nenhum quadro disponível** e a opção para selecionar quadros-chave ficará esmaecida. Nesse caso, você só pode aplicar marcas ao vídeo como um todo (usando as marcas de **vídeo +** botão).

> [!div class="mx-imgBorder"]
> ![O painel de bloco, player de vídeo, painel de marcas de quadro-chave e painéis de marcas de vídeo são mostrados. As marcas de quadro-chave + e marcas de vídeo + botões são realçadas.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Refazer uma revisão em espera

O botão **manter** na parte inferior do painel de vídeo permite que você coloque uma revisão em espera para que possa recuperá-la e concluí-la mais tarde. Você pode fazer isso para uma revisão que exige uma consulta de outro membro da equipe ou gerente que não está disponível no momento. 

Você pode exibir os vídeos em espera clicando no botão **manter** na parte superior da tela. O painel de espera aparece à direita. A partir daqui, você pode selecionar várias revisões em espera e liberá-las de volta na fila ou definir o tempo de expiração. Após o período pré-configurado, as revisões em espera são liberadas de volta para a fila. Selecione **salvar** para começar a contar a partir da hora de expiração selecionada no momento.

> [!div class="mx-imgBorder"]
> ![No painel vídeo, o botão manter é realçado. Na parte inferior do painel, uma caixa de combinação de tempo de espera é realçada, juntamente com os botões liberar e salvar.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Enviar uma revisão

Depois de aplicar as marcas, selecione o botão **Enviar** na parte inferior do painel de vídeo. Se você marcou vários vídeos, poderá enviá-los em uma única revisão ou como revisões separadas.

## <a name="limbo-state"></a>Estado limbo

Depois de enviar uma revisão, o vídeo é movido para o estado **limbo** , que pode ser exibido selecionando o botão **limbo** na parte superior da tela. Os vídeos permanecem no estado limbo para um período de tempo pré-configurado (que você pode alterar no menu na parte inferior) ou até que eles sejam revisados novamente ou enviados manualmente.

Depois que os vídeos expirarem de limbo, suas revisões serão marcadas como concluídas.

## <a name="next-steps"></a>Próximas etapas

- Introdução com o [Início rápido de moderação de vídeo](video-moderation-api.md).
- Saiba como gerar [análises de vídeos](video-reviews-quickstart-dotnet.md) para seus revisores humanos do seu resultado moderado.
- Adicione [análises de transcrição de vídeo](video-transcript-reviews-quickstart-dotnet.md) às suas análises de vídeo.
