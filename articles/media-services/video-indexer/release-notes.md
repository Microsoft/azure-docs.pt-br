---
title: Notas de versão do Video Indexer de serviços de mídia do Azure | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os serviços de mídia do Azure Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 02/01/2021
ms.author: juliako
ms.openlocfilehash: c0d332c00635444ab4d694e46486a498b43137f2
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427644"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de versão do Video Indexer de serviços de mídia do Azure

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` no leitor de feed de RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="january-2021"></a>Janeiro de 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer é implantado na nuvem do governo dos EUA 

Agora você pode criar uma conta paga Video Indexer na nuvem do governo dos EUA nas regiões Virgínia e Arizona. Video Indexer oferta de avaliação gratuita não está disponível na região mencionada. Para obter mais informações, acesse Video Indexer documentação. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer implantados na região da Índia central 

Agora você pode criar uma conta paga Video Indexer na região da Índia central. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Novo modo escuro para a experiência de Video Indexer site

O Video Indexer experiências do site agora está disponível no modo escuro. Para habilitar o modo escuro, abra o painel configurações e alterne a opção **modo escuro** . 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Configuração de modo escuro":::

## <a name="december-2020"></a>Dezembro de 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer implantados no Oeste da Suíça e Norte da Suíça

Agora você pode criar uma conta paga Video Indexer nas regiões Oeste da Suíça e Norte da Suíça.

## <a name="october-2020"></a>Outubro de 2020

### <a name="animated-character-identification-improvements"></a>Aprimoramentos na identificação de caracteres animados  

Video Indexer dá suporte à detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a visão personalizada dos serviços cognitivas. Adicionamos um aprimoramento importante a esse algoritmo de ia na detecção e no reconhecimento de caracteres, pois a precisão de insights e os caracteres identificados são significativamente melhorados.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Alterações de authenticatication de site Video Indexer planejadas

A partir de 1º de março de 2021, você não poderá mais se inscrever e entrar no [portal do desenvolvedor](video-indexer-use-apis.md) do [site video indexer](https://www.videoindexer.ai/) usando o Facebook ou o LinkedIn.

Você poderá se inscrever e entrar usando um destes provedores: Azure AD, Microsoft e Google.

> [!NOTE]
> As contas de Video Indexer conectadas ao LinkedIn e ao Facebook não estarão acessíveis após 1º de março de 2021. 
> 
> Você deve [convidar](invite-users.md) um email do Azure AD, Microsoft ou Google para a conta de video indexer para que você ainda terá acesso.<br/>
> Como alternativa, você pode criar uma conta paga e migrar os dados.

## <a name="august-2020"></a>Agosto de 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Design móvel para o site Video Indexer

O Video Indexer experiência do site agora é compatível com dispositivos móveis. A experiência do usuário é responsiva para se adaptar ao seu tamanho de tela móvel (excluindo UIs de personalização). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Aprimoramentos de acessibilidade e correções de bugs 

Como parte da WCAG (diretrizes de acessibilidade de conteúdo da Web), o Video Indexer experiências do site é alinhado com a série C, como parte dos padrões de acessibilidade da Microsoft. Vários bugs e aprimoramentos relacionados à navegação por teclado, acesso programático e leitor de tela foram resolvidos. 

## <a name="july-2020"></a>Julho de 2020

### <a name="ga-for-multi-language-identification"></a>GA para identificação em vários idiomas

A identificação em vários idiomas é movida de visualização para GA e pronta para uso produtivo.

Não há impacto de preço relacionado à transição de "visualização para GA".

### <a name="video-indexer-website-improvements"></a>Aprimoramentos no site Video Indexer

#### <a name="adjustments-in-the-video-gallery"></a>Ajustes na Galeria de vídeos

Foi adicionada uma nova barra de pesquisa para a pesquisa detalhada com recursos de filtragem adicionais. Os resultados da pesquisa também foram aprimorados.

Novo modo de exibição de lista com a capacidade de classificar e gerenciar o arquivo de vídeo com vários arquivos.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Novo painel para fácil seleção e configuração

Painel lateral para fácil seleção e configuração de usuário foi adicionada, permitindo a criação e o compartilhamento de contas simples e rápidas, bem como configuração de configuração.

O painel lateral também é usado para preferências e ajuda do usuário.

## <a name="june-2020"></a>Junho de 2020

### <a name="search-by-topics"></a>Pesquisar por tópicos

Agora você pode usar a API de pesquisa para procurar vídeos com tópicos específicos (somente API).

Os tópicos são adicionados como parte do `textScope` (parâmetro opcional). Consulte [API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Search-Videos) para obter detalhes.  

### <a name="labels-enhancement"></a>Aprimoramento de rótulos

O rotulador de rótulo foi atualizado e agora inclui mais rótulos visuais que podem ser identificados.

## <a name="may-2020"></a>Maio de 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer implantado no leste dos EUA

Agora você pode criar uma conta paga Video Indexer na região leste dos EUA.
 
### <a name="video-indexer-url"></a>URL de Video Indexer

Video Indexer pontos de extremidade regionais eram todos unificados para iniciar somente com www. Nenhum item de ação é necessário.

De agora em diante, você chega a www.videoindexer.ai se é para inserir widgets ou fazer logon em Video Indexer aplicativos Web.

O wus.videoindexer.ai também seria Redirecionado para www. Mais informações estão disponíveis em [Video indexer widgets de inserção em seus aplicativos](video-indexer-embed-widgets.md).

## <a name="april-2020"></a>Abril de 2020

### <a name="new-widget-parameters-capabilities"></a>Novos recursos de parâmetros de widget

O widget **insights** inclui novos parâmetros: `language` e `control` .

O widget **Player** tem um novo `locale` parâmetro. Os `locale` `language` parâmetros e são controlados pelo idioma do jogador.

Para obter mais informações, consulte a seção [tipos de widget](video-indexer-embed-widgets.md#widget-types) . 

### <a name="new-player-skin"></a>Nova aparência do jogador

Uma nova aparência do jogador iniciada com o design atualizado.

### <a name="prepare-for-upcoming-changes"></a>Preparar para alterações futuras

* Hoje, as seguintes APIs retornam um objeto de conta:

    * [Criar conta paga](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Obter conta](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Get-accounts-Authorization](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-accounts-com-token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    O objeto de conta tem um `Url` campo que aponta para o local do [Video indexer site](https://www.videoindexer.ai/).
Para contas pagas `Url` , o campo está atualmente apontando para uma URL interna em vez do site público.
Nas próximas semanas, iremos alterá-la e retornar o Video Indexer URL do [site](https://www.videoindexer.ai/) para todas as contas (avaliação e paga).

    Não use as URLs internas, você deve estar usando as [APIs públicas do video indexer](https://api-portal.videoindexer.ai/).
* Se você estiver inserindo Video Indexer URLs em seus aplicativos e as URLs não estiverem apontando para o [site do video indexer](https://www.videoindexer.ai/) ou o ponto de extremidade da API do video indexer ( `https://api.videoindexer.ai` ), mas sim para um ponto de extremidade regional (por exemplo, `https://wus2.videoindexer.ai` ), gere novamente as URLs.

   Você pode fazer isso por meio de:

    * Substituindo a URL por uma URL apontando para as APIs do widget de Video Indexer (por exemplo, o [widget insights](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget))
    * Usando o site Video Indexer para gerar uma nova URL inserida:
         
         Pressione **reproduzir** para ir para a página do vídeo-> clique no botão **&lt; / &gt; Inserir** – > copiar a URL para seu aplicativo:
   
    As URLs regionais não têm suporte e serão bloqueadas nas próximas semanas.

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Suporte a idiomas personalizados para idiomas adicionais

O Video Indexer agora dá suporte a modelos de linguagem personalizados para `ar-SY` , `en-UK` e `en-AU` (somente API).
 
### <a name="delete-account-timeframe-action-update"></a>Excluir a atualização da ação do período de tempo da conta

A ação Excluir conta agora exclui a conta em 90 dias, em vez de 48 horas.
 
### <a name="new-video-indexer-github-repository"></a>Novo repositório GitHub Video Indexer

Um novo Video Indexer GitHub com projetos diferentes, guias de introdução e exemplos de código agora está disponível: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Atualização do Swagger

Video Indexer **autenticações** e **operações** unificadas em uma única [especificação de openapi Video indexer (Swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Os desenvolvedores podem encontrar as APIs em [Video indexer portal do desenvolvedor](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Dezembro de 2019

### <a name="update-transcript-with-the-new-api"></a>Atualizar transcrição com a nova API

Atualize uma seção específica na transcrição usando a API [Update-Video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Corrigir a configuração da conta no portal de Video Indexer

Agora você pode atualizar a configuração de conexão dos serviços de mídia para autoajudar com problemas como: 

* recurso incorreto dos serviços de mídia do Azure
* alterações de senha
* Os recursos dos serviços de mídia foram movidos entre assinaturas  

Para corrigir a configuração da conta, no portal do Video Indexer, navegue até configurações > guia conta (como proprietário).

### <a name="configure-the-custom-vision-account"></a>Configurar a conta de visão personalizada

Configurar a conta de visão personalizada em contas pagas usando o portal de Video Indexer (anteriormente, isso só era suportado pela API). Para fazer isso, entre no portal de Video Indexer, escolha personalização de modelo > caracteres animados > configurar. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Cenas, capturas e quadros-chave – agora em um painel de insights

Os bastidores, as capturas e os quadros-chave agora são mesclados em uma percepção para facilitar o consumo e a navegação. Ao selecionar a cena desejada, você pode ver quais capturas e quadros-chave são consistentes. 

### <a name="notification-about-a-long-video-name"></a>Notificação sobre um nome de vídeo longo

Quando um nome de vídeo tem mais de 80 caracteres, Video Indexer mostra um erro descritivo no carregamento.

### <a name="streaming-endpoint-is-disabled-notification"></a>A notificação de ponto de extremidade de streaming está desabilitada

Quando o ponto de extremidade de streaming estiver desabilitado, Video Indexer mostrará um erro descritivo na página do Player.

### <a name="error-handling-improvement"></a>Aprimoramento do tratamento de erros

O código de status 409 agora será retornado de [reindexação de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) e atualização de APIs de [índice de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) caso um vídeo esteja ativamente indexado, para evitar a substituição das alterações de reindexação atuais por acidente.

## <a name="november-2019"></a>Novembro de 2019
 
* Suporte aos modelos de idioma personalizado coreano

    O video indexer agora dá suporte a modelos de linguagem personalizados em coreano ( `ko-KR` ) tanto na API quanto no Portal. 
* Novos idiomas com suporte para conversão de fala em texto (STT)

    Video Indexer APIs agora dão suporte a STT em árabe Levantine (ar-SY), dialeto inglês do Reino Unido (en-GB) e dialeto australiano inglês (en-AU).
    
    Para carregamento de vídeo, substituimos zh-HANS a zh-CN, ambos têm suporte, mas o zh-CN é recomendado e mais preciso.
    
## <a name="october-2019"></a>Outubro de 2019
 
* Pesquisar caracteres animados na Galeria

    Ao indexar caracteres animados, agora você pode pesquisá-los na galé de vídeo da conta. Para obter mais informações, consulte [manuscrito de caracteres animados](animated-characters-recognition.md).

## <a name="september-2019"></a>Setembro de 2019
 
Vários avanços anunciados em IBC 2019:
 
* Reconhecimento de caracteres animados (visualização pública)

    Capacidade de detectar os caracteres do grupo do AD Recognize no conteúdo animado, por meio da integração com visão personalizada. Para obter mais informações, consulte [Detecção de caracteres animados](animated-characters-recognition.md).
* Identificação de vários idiomas (visualização pública)

    Detecte segmentos em vários idiomas na faixa de áudio e crie uma transcrição multilíngue com base neles. Suporte inicial: Inglês, espanhol, alemão e francês. Para obter mais informações, consulte [Identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* Extração de entidade nomeada para pessoas e local

    Extrai marcas, locais e pessoas de fala e texto visual por meio do NLP (processamento de idioma natural).
* Classificação do tipo editorial shot

    Marcação de capturas com tipos editoriais como close up, Medium, two shot, interno, externamente, etc. Para obter mais informações, consulte [detecção do tipo editorial shot](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Aprimoramento do tópico inferência – agora cobrindo o nível 2
    
    O tópico inferência Model Agora dá suporte à granularidade mais profunda da taxonomia IPTC. Leia os detalhes completos nos [serviços de mídia do Azure nova inovação com alimentação de ia](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Agosto de 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer implantado no Sul do Reino Unido

Agora você pode criar uma conta paga Video Indexer na região do Sul do Reino Unido.

### <a name="new-editorial-shot-type-insights-available"></a>Novas informações de tipo de captura editorial disponíveis

As novas marcas adicionadas às capturas de vídeo fornecem "tipos de captura" editoriais para identificá-las com as frases editoriais comuns usadas no fluxo de trabalho de criação de conteúdo, como: Extreme closeup, closeup, Wide, Medium, two shot, externamente, interno, face esquerda e face direita (disponível no JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extração de entidades de novas pessoas e locais disponíveis

Video Indexer identifica locais nomeados e pessoas por meio do NLP (processamento de idioma natural) do OCR e da transcrição do vídeo. Video Indexer usa o algoritmo de aprendizado de máquina para reconhecer quando locais específicos (por exemplo, a torre de Eiffel) ou pessoas (por exemplo, João da Silva) estão sendo chamados em um vídeo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extração de quadros-chave na resolução nativa

Os quadros-chave extraídos por Video Indexer estão disponíveis na resolução original do vídeo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA para treinamento de modelos de face personalizados de imagens

Rostos de treinamento de imagens movidas do modo de visualização para GA (disponível via API e no Portal).

> [!NOTE]
> Não há impacto de preço relacionado à transição de "visualização para GA".

### <a name="hide-gallery-toggle-option"></a>Ocultar opção de alternância da Galeria

O usuário pode optar por ocultar a guia Galeria do portal (semelhante a ocultar a guia amostras).
 
### <a name="maximum-url-size-increased"></a>Tamanho máximo de URL aumentado

Suporte para cadeia de caracteres de consulta de URL 4096 (em vez de 2048) na indexação de um vídeo.
 
### <a name="support-for-multi-lingual-projects"></a>Suporte para projetos multilíngues

Agora, os projetos podem ser criados com base em vídeos indexados em diferentes idiomas (somente API).

## <a name="july-2019"></a>Julho de 2019

### <a name="editor-as-a-widget"></a>Editor como um widget

O Video Indexer AI-Editor agora está disponível como um widget a ser inserido nos aplicativos do cliente.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda oculta no portal

Os clientes podem fornecer os formatos de arquivo VTT, SRT e TTML como entrada para modelos de linguagem na página personalização do Portal.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer implantado no leste do Japão

Agora você pode criar uma conta paga Video Indexer na região leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar a API da conta (versão prévia)

Adicionada uma nova API que permite que você [atualize a chave ou o ponto de extremidade de conexão do serviço de mídia do Azure](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o tratamento de erros ao carregar 

Uma mensagem descritiva é retornada em caso de configuração incorreta da conta subjacente dos serviços de mídia do Azure.

### <a name="player-timeline-keyframes-preview"></a>Linha do tempo do Player-visualização de quadros principais 

Agora você pode ver uma visualização de imagem para cada vez na linha do tempo do jogador.

### <a name="editor-semi-select"></a>Seleção semi do editor

Agora você pode ver uma visualização de todas as informações que são selecionadas como resultado da escolha de um período de insight específico no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda oculta

[Criar um modelo de linguagem personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e atualizar as APIs de [modelos de linguagem personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) agora dão suporte aos formatos de arquivo VTT, Srt e TTML como entrada para modelos de linguagem.

Ao chamar a [API Update transcrição de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), a transcrição é adicionada automaticamente. O modelo de treinamento associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar seus modelos de idioma, consulte [Personalizar um modelo de linguagem com Video indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download – TXT e CSV

Além do formato de legenda oculta já suportado (SRT, VTT e TTML), Video Indexer agora dá suporte ao download da transcrição nos formatos TXT e CSV.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](video-indexer-overview.md)
