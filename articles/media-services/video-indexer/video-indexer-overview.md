---
title: O que é o Video Indexer?
titlesuffix: Azure Media Services
description: Este tópico oferece uma visão geral do serviço de Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: a182b9ec0fb945b4c2ffddd7a977df8ad9a8d250
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559835"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer do Azure é um aplicativo de nuvem criado com base na Análise de Mídia do Azure, no Azure Search, nos Serviços Cognitivos (como a API de Detecção Facial, o Microsoft Translator, a API da Pesquisa Visual Computacional e o Serviço de Fala Personalizado). Ele permite que você extraia os insights de seus vídeos usando os modelos de áudio e vídeo do Video Indexer descritos abaixo:
  
## <a name="video-insights"></a>Insights para vídeos

- **Detecção facial**: Detecta e agrupa rostos que aparecem no vídeo.
- **Identificação de celebridade**: Indexador de vídeo identifica automaticamente a mais de 1 milhão de celebridades – como líderes mundiais, atores, atrizes, atletas, pesquisadores, negócios e líderes de tecnologia em todo o mundo. Os dados sobre esses celebridades também podem ser encontrados em diversos sites famosos, assim como IMDB e Wikipédia.
- **Identificação de face baseada em conta**: O Video Indexer treina um modelo para uma conta específica. Em seguida, reconhece faces no vídeo com base no modelo treinado. Para obter mais informações, consulte [personalizar um modelo de pessoa do site do Video Indexer](customize-person-model-with-website.md) e [personalizar um modelo de pessoa com a API do indexador de vídeo](customize-person-model-with-api.md).
- **Extração em miniatura para rosto** ("melhor rosto"): Identifica automaticamente o melhor rosto capturado em cada grupo de rostos (com base na qualidade, no tamanho e na posição frontal) e o extrai como um ativo de imagem.
- **Reconhecimento de texto visual** (OCR): Extrai o texto que é exibido visualmente no vídeo.
- **Moderação de conteúdo visual**: Detecta visuais somente para adultos e/ou eróticos.
- **Identificação de rótulos**: Identifica objetos visuais e ações exibidas.
- **Segmentação de cena**: determina quando as alterações de uma cena no vídeo com base em indicações visuais. Uma cena representa um único evento, e ele é composto por uma série de capturas consecutivas, que são semanticamente relacionados. 
- **Captura de detecção**: determina quando as alterações de uma captura de vídeo com base em indicações visuais. Uma captura é uma série de quadros obtido da câmera motion picture mesmo. Para obter mais informações, consulte [cenas, capturas e quadros-chave](scenes-shots-keyframes.md).
- **Detecção de quadro preto**: Identifica quadros pretos apresentados no vídeo.
- **Extração de quadro-chave**: Detecta quadros-chave estáveis em um vídeo.
- **Sem interrupção créditos**: identificar o início e término dos créditos sem interrupção no final do programa de TV e filmes.

## <a name="audio-insights"></a>Insights de áudio

- **Detecção automática de idioma**: Identifica automaticamente o idioma falado dominante. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português (brasileiro). Fará fallback para o inglês quando o idioma não puder ser detectado.
- **Transcrição de áudio**: Conversão de fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo e português (brasileiro), hindi e coreano.
- **Legendagem oculta**: Cria legendas ocultas em três formatos: VTT, TTML, SRT.
- **Processamento de dois canais**: Detecta automaticamente, separa a transcrição e mescla em uma única linha do tempo.
- **Redução de ruído**: Limpa gravações com ruídos ou áudio com telefonia (com base nos filtros do Skype).
- **Personalização de transcrição** (CRIS): Treina fala personalizado para modelos de texto para criar as transcrições de específicos do setor. Para obter mais informações, consulte [personalizar um modelo de idioma do site do Video Indexer](customize-language-model-with-website.md) e [personalizar um modelo de linguagem com as APIs do indexador de vídeo](customize-language-model-with-api.md).
- **Enumeração de alto-falante**: Mapeia e entende qual locutor disse quais palavras e quando.
- **Estatísticas de alto-falante**: Fornece estatísticas das taxas de fala dos alto-falantes.
- **Moderação de conteúdo visual**: Detecta texto explícito na transcrição de áudio.
- **Efeitos de áudio**: Identifica efeitos de áudio como palmas, fala e silêncio.
- **Detecção de emoções**: Identifica emoções com base em indicações de fala e de áudio. A emoção pode ser: alegria, tristeza, raiva ou medo.
- **Tradução**: Cria traduções da transcrição de áudio em 54 idiomas diferentes.

## <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (vários canais)

Ao fazer a indexação por um canal, o resultado parcial desses modelos estará disponível

- **Extração de palavra-chave**: Extrai palavras-chave de falas e de textos visuais.
- **Extração de marcas**: Extrai marcas de falas e de textos visuais.
- **Inferência do tópico**: Faz inferências dos principais tópicos das transcrições. A taxonomia de IPTC de 1º nível está incluída.
- **Artefatos**: Extrai um conjunto avançado de artefatos com o "próximo nível de detalhes" para cada um dos modelos.
- **Análise de Sentimento**: Identifica sentimentos positivos, negativos e neutros de falas e de textos visuais.
 
Depois que o Video Indexer terminar de processar e analisar, você poderá examinar, coletar, pesquisar e publicar as informações do vídeo.

Se sua função for um gerenciador de conteúdo ou um desenvolvedor, o serviço do Video Indexer poderá atender às suas necessidades. Os gerenciadores de conteúdo podem usar o portal da Web do Video Indexer para consumir o serviço sem escrever uma única linha de código; confira [Começar a usar o site do Video Indexer](video-indexer-get-started.md). Os desenvolvedores podem aproveitar as APIs para processar o conteúdo em grande escala; consulte [Usar API REST do Video Indexer](video-indexer-use-apis.md). O serviço também permite que os clientes usem widgets para publicar transmissões de vídeo e informações extraídas em seus próprios aplicativos; consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Você pode se inscrever no serviço usando a conta existente do AAD, LinkedIn, Facebook, Google ou MSA. Para obter mais informações, consulte o [guia de introdução](video-indexer-get-started.md).

## <a name="scenarios"></a>Cenários

Abaixo estão alguns cenários em que o Video Indexer pode ser útil

- Pesquisa: as informações extraídas do vídeo podem ser usadas para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação das palavras faladas e faces pode habilitar a experiência de pesquisa para localizar momentos em um vídeo em que determinada pessoa falou certas palavras ou quando duas pessoas foram vistas junto. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, instituições de ensino, emissoras, proprietários de conteúdo de entretenimento, aplicativos de linha de negócios e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os usuários precisam pesquisar.
- Criação de conteúdo – insights extraído dos vídeos e ajudar a criar efetivamente conteúdo como notícias, conteúdo de mídia social, trailers clipes etc. do conteúdo existente no arquivo morto de organização 
- Monetização: o Video Indexer pode ajudar a melhorar o valor dos vídeos. Por exemplo, setores que contam com a receita de anúncios (como a mídia de notícias, mídias sociais etc.) podem entregar anúncios mais relevantes usando as informações extraídas como sinais adicionais para o servidor do anúncio (apresentar um anúncio de sapatos esportivos é mais relevante durante uma partida de futebol do que em uma competição de natação).
- Participação do usuário: as informações do vídeo podem ser usadas para melhorar a participação do usuário posicionando os momentos do vídeo relevantes para os usuários. Por exemplo, considere um vídeo educativo que explica as esferas nos primeiros 30 minutos e as pirâmides nos próximos 30 minutos. Um aluno que lê sobre as pirâmides aproveitaria mais se o vídeo fosse posicionado para começar a partir do marcador de 30 minutos.

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao site do Video Indexer](video-indexer-get-started.md)
- [Processar o conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)
