---
title: O que é o Video Indexer dos Serviços de Mídia do Azure?
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do serviço Video Indexer dos Serviços de Mídia do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/05/2021
ms.author: juliako
ms.openlocfilehash: 12d23ec471329bd4e0ecb502750198e946e58872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530222"
---
# <a name="what-is-azure-media-services-video-indexer"></a>O que é o Video Indexer dos Serviços de Mídia do Azure?

[!INCLUDE [regulation](./includes/regulation.md)]

O Video Indexer (VI) é a solução de IA dos Serviços de Mídia do Azure e faz parte da marca Serviços Cognitivos da Azure. O Video Indexer oferece a capacidade de extrair insights aprofundados (sem necessidade de análise de dados ou habilidades de codificação) usando modelos de machine learning baseados em vários canais (voz, vocais, visuais). Você pode personalizar e treinar os modelos ainda mais. O serviço permite pesquisa avançada, reduz custos operacionais, oferece novas oportunidades de monetização e cria novas experiências de usuário em grandes arquivos de vídeos (com poucas barreiras de entrada).

Para começar a extrair informações com o Video Indexer, você precisa criar uma conta e carregar vídeos. Ao carregar seus vídeos para o Video Indexer, ele executa modelos de IA diferentes para analisar os elementos visuais e de áudio. À medida que o Video Indexer analisa o vídeo, modelos de IA extraem os insights.

Quando você cria uma conta de Video Indexer e a conecta aos serviços de mídia, os arquivos de mídia e metadados são armazenados na conta de armazenamento do Azure associada a essa conta dos serviços de mídia. Para obter mais informações, consulte [criar uma conta de video indexer conectada ao Azure](connect-to-azure.md).

O diagrama a seguir é ilustrativo e não uma explicação técnica do funcionamento de back-end do Video Indexer.

![Fluxograma do Video Indexer dos Serviços de Mídia do Azure](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você precisará respeitar todas as leis aplicáveis em seu uso do Video Indexer e não poderá usar o Video Indexer ou qualquer serviço do Azure de uma maneira que viole os direitos de outras pessoas ou que possa ser prejudicial a outras pessoas.

Antes de carregar qualquer vídeo/imagem no Video Indexer, você precisará ter todos os direitos adequados para usar o vídeo/a imagem, incluindo, quando exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/na imagem, para uso, processamento e armazenamento dos respectivos dados no Video Indexer e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a coleta, o processamento online e o armazenamento de determinadas categorias de dados, como dados biométricos. Antes de usar o Video Indexer e o Azure para o processamento e o armazenamento de dados sujeitos a requisitos legais especiais, você precisará garantir a conformidade com os requisitos legais que possam se aplicar a você.

Para saber mais sobre conformidade, privacidade e segurança no Video Indexer, acesse a [Central de Confiabilidade](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) da Microsoft. Para saber mais sobre as obrigações de privacidade, as práticas de tratamento e retenção de dados pela Microsoft, incluindo como excluir seus dados, examine a [Política de Privacidade](https://privacy.microsoft.com/PrivacyStatement), o “OST” [(Termos do Online Services)](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) e o “DPA” [(Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Ao usar o Video Indexer, você concorda em vincular-se ao OST, ao DPA e à política de privacidade.

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Video Indexer?

As informações do Video Indexer podem ser aplicadas a vários cenários, entre eles:

* *Pesquisa avançada*: Use os insights extraídos do vídeo para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação das palavras faladas e faces pode habilitar a experiência de pesquisa para localizar momentos em um vídeo em que pessoa falou certas palavras ou quando duas pessoas foram vistas junto. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, instituições de ensino, emissoras, proprietários de conteúdo de entretenimento, aplicativos de linha de negócios e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os usuários precisam pesquisar.
* *Criação de conteúdo*: Crie trailers, melhores momentos, conteúdo de redes sociais ou clipes de notícias com base nos insights que o Video Indexer extrai do seu conteúdo. Quadros-chave, marcadores de cenas e carimbos de data/hora para as pessoas e aparências de rótulo tornam o processo de criação muito mais tranquilo e fácil e permite obter as partes do vídeo que você precisa para o conteúdo que está sendo criado.
* *Acessibilidade*: Se quiser disponibilizar seu conteúdo para pessoas com deficiências ou se quiser que seu conteúdo seja distribuído em regiões diferentes com outros idiomas, você poderá usar a transcrição e a tradução fornecidas pelo Video Indexer em vários idiomas.
* *Monetização*: O Video Indexer pode ajudar a aumentar o valor dos vídeos. Por exemplo, setores que dependem da receita de anúncios (mídia de notícias, redes sociais e outros) podem fornecer anúncios relevantes usando os insights extraídos como sinais adicionais para o servidor de anúncios.
* *Moderação de conteúdo*: Use modelos de moderação de conteúdo visual e textual para manter seus usuários protegidos contra conteúdo inadequado e validar que o conteúdo publicado por você corresponde aos valores da sua organização. Você pode bloquear automaticamente determinados vídeos ou alertar os usuários sobre o conteúdo.
* *Recomendações:* Os insights do vídeo podem ser usados para melhorar a participação do usuário destacando os momentos do vídeo relevantes para os usuários. Ao marcar cada vídeo com metadados adicionais, você poderá recomendar os vídeos mais relevantes e realçar as partes do vídeo que corresponderão às necessidades dos seus usuários.

## <a name="features"></a>Recursos

A lista a seguir mostra os insights que você pode recuperar dos vídeos usando os modelos de vídeo e áudio do Video Indexer:

### <a name="video-insights"></a>Insights para vídeos

* **Detecção facial**: Detecta e agrupa rostos que aparecem no vídeo.
* **Identificação de celebridade**: o Video Indexer identifica automaticamente mais de 1 milhão de celebridades, como líderes mundiais, atores e atrizes, atletas, pesquisadores, líderes dos negócios e do setor de tecnologia em todo o mundo. Os dados sobre esses celebridades também podem ser encontrados em vários sites (IMDB, Wikipédia etc.).
* **Identificação de face baseada em conta**: O Video Indexer treina um modelo para uma conta específica. Em seguida, ele reconhece os rostos no vídeo com base no modelo treinado. Para obter mais informações, [Personalizar um modelo de pessoa com o site do Video Indexer](customize-person-model-with-website.md) e [Personalizar um modelo de Pessoa com a API do Video Indexer](customize-person-model-with-api.md).
* **Extração em miniatura para rosto** ("melhor rosto"): Identifica automaticamente o melhor rosto capturado em cada grupo de rostos (com base na qualidade, no tamanho e na posição frontal) e o extrai como um ativo de imagem.
* **Reconhecimento de texto visual** (OCR): Extrai o texto que é exibido visualmente no vídeo.
* **Moderação de conteúdo visual**: Detecta visuais somente para adultos e/ou eróticos.
* **Identificação de rótulos**: Identifica objetos visuais e ações exibidas.
* **Segmentação de cena**: Determina quando a cena muda em um vídeo com base em indicações visuais. Uma cena representa um único evento e é composta por uma série de capturas consecutivas, que estão relacionadas semanticamente.
* **Detecção de captura**: Determina quando a captura muda em um vídeo com base em indicações visuais. Uma captura é uma série de quadros tirados da mesma câmera de imagem de movimento. Para obter mais informações, consulte [Cenas, capturas e quadros-chave](scenes-shots-keyframes.md).
* **Detecção de quadro preto**: Identifica quadros pretos apresentados no vídeo.
* **Extração de quadro-chave**: Detecta quadros-chave estáveis em um vídeo.
* **Créditos em movimento**: Identifica o início e o fim dos créditos em movimento no final de filmes e programas de TV.
* **Detecção de caracteres animados** (versão prévia): Detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Para obter mais informações, consulte [Detecção de caracteres animados](animated-characters-recognition.md).
* **Detecção de tipo de captura de editorial**: Marcação de capturas com base no tipo (como plano aberto, plano média, close up, close up extremo, duas capturas, várias pessoas, externa e interna, etc.). Para obter mais informações, consulte [Detecção do tipo de captura do editorial](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Insights de áudio

* **Transcrição de áudio**: Conversão de fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (mandarim), japonês, árabe, russo e português, hindi e coreano.
* **Detecção automática de idioma**: Identifica automaticamente o idioma falado dominante. Os idiomas com suporte incluem inglês, espanhol, francês, alemão, italiano, chinês (mandarim), japonês, russo e português. Se o idioma não for identificado claramente, o Video Indexer suporá que o idioma falado é o inglês. Para obter mais informações, consulte [Modelo de identificação de idioma](language-identification-model.md).
* **Identificação e transcrição de fala em vários idiomas**: identifica automaticamente o idioma falado em segmentos diferentes do áudio. Ele envia cada segmento do arquivo de mídia a ser transcrito e, em seguida, combina a transcrição em uma transcrição unificada. Para obter mais informações, consulte [Identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* **Legendagem oculta**: Cria legendas ocultas em três formatos: VTT, TTML, SRT.
* **Processamento de dois canais**: Detecta automaticamente, separa a transcrição e mescla em uma única linha do tempo.
* **Redução de ruído**: Limpa gravações com ruídos ou áudio com telefonia (com base nos filtros do Skype).
* **Personalização de transcrição** (CRIS): Treina modelos de conversão de fala em texto para transcrições específicas do setor. Para obter mais informações, [Personalizar um modelo de linguagem com o site do Video Indexer](customize-language-model-with-website.md) e [Personalizar um modelo de linguagem com a API do Video Indexer](customize-language-model-with-api.md).
* **Enumeração de alto-falante**: Mapeia e entende qual locutor disse quais palavras e quando. Dezesseis alto-falantes podem ser detectados em um único arquivo de áudio.
* **Estatísticas de alto-falante**: Fornece estatísticas das taxas de fala dos alto-falantes.
* **Moderação de conteúdo visual**: Detecta texto explícito na transcrição de áudio.
* **Efeitos de áudio** (visualização pública): detecta os seguintes efeitos de áudio nos segmentos de não fala do conteúdo: gunshot, cortar de vidro, alarme, Siren, explosão, cachorro latido, incontestável, risadas, reações de torcida (com muita paz, Clapping e booing) e silêncio. Observação: o conjunto completo de eventos está disponível somente ao escolher ' análise de áudio avançada ' na predefinição de carregamento; caso contrário, apenas ' silêncio ' e ' reação a '
* **Detecção de emoções**: Identifica emoções com base na fala (o que está sendo dito) e no tom de voz (como está sendo dito). A emoção pode ser alegria, tristeza, raiva ou medo.
* **Tradução**: Cria traduções da transcrição de áudio em 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (vários canais)

Ao fazer a indexação por um canal, o resultado parcial desses modelos estará disponível.

* **Extração de palavra-chave**: Extrai palavras-chave de falas e de textos visuais.
* **Extração de entidades nomeadas**: Extrai marcas, locais e pessoas de texto visual e de fala por meio de processamento natural de linguagem (NLP).
* **Inferência do tópico**: Faz inferências dos principais tópicos das transcrições. A taxonomia de IPTC de 2º nível está incluída.
* **Artefatos**: Extrai um conjunto avançado de artefatos com o "próximo nível de detalhes" para cada um dos modelos.
* **Análise de Sentimento**: Identifica sentimentos positivos, negativos e neutros de falas e de textos visuais.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar a utilizar o Video Indexer?

Você pode acessar os recursos do Video Indexer de três maneiras:

* Portal do Video Indexer: Uma solução fácil de usar que permite avaliar o produto, gerenciar a conta e personalizar modelos.

    Para obter mais informações sobre o portal, consulte [Introdução ao site do Video Indexer](video-indexer-get-started.md).  

* Integração da API: Todos os recursos do Video Indexer estão disponíveis por meio de uma API REST, que permite integrar a solução em seus aplicativos e sua infraestrutura.

    Para começar como desenvolvedor, consulte [Usar a API REST do Video Indexer](video-indexer-use-apis.md).

* Widget incorporável: Permite incorporar as experiências de insights, player e editor do Video Indexer em seu aplicativo.

    Para obter mais informações, consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Se você estiver usando o site, os insights serão adicionados como metadados e estarão visíveis no portal. Se estiver usando APIs, as informações estarão disponíveis como um arquivo JSON.

## <a name="supported-browsers"></a>Navegadores com suporte

A lista a seguir mostra os navegadores com suporte que você pode usar para o site Video Indexer e para seus aplicativos que incorporam os widgets. A lista também mostra a versão mínima do navegador com suporte:

- Borda, versão: 16
- Firefox, versão: 54
- Chrome, versão: 58
- Safari, versão: 11
- Opera, versão: 44
- Opera Mobile, versão: 59
- Navegador Android, versão: 81
- Navegador Samsung, versão: 7
- Chrome para Android, versão: 87
- Firefox para Android, versão: 83

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao site do Video Indexer](video-indexer-get-started.md).
- [Processar o conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md).
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).
