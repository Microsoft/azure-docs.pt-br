---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
Título: transmitir conteúdo com integração de CDN: descrição dos serviços de mídia do Azure: Saiba mais sobre o streaming de conteúdo com a integração da CDN, bem como pré-busca e Origin-Assist a pré-busca de CDN.
serviços: Media-Services documentationcenter: ' ' autor: IngridAtMicrosoft gerente: femila editor: ' ' MS. Service: Media-Services MS. Workload: MS. tópico: conceptual MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>Transmitir conteúdo com integração CDN

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo.  

A CDN armazena em cache o conteúdo transmitido de um ponto de extremidade de streaming dos serviços de mídia [(origem)](streaming-endpoint-concept.md) por codec, por protocolo de streaming, por taxa de bits, por formato de contêiner e por criptografia/DRM. Para cada combinação de codec-Streaming Protocol-Encryption Format-taxa de bits de criptografia, haverá um cache CDN separado.

O conteúdo popular será servido diretamente do cache da CDN, desde que o fragmento de vídeo seja armazenado em cache. É provável que o conteúdo ao vivo seja armazenado em cache, porque normalmente há muitas pessoas assistindo exatamente o mesmo conteúdo. O conteúdo sob demanda pode ser um pouco mais complicado porque você pode ter algum conteúdo que seja popular e outros não. Se você tem milhões de ativos de vídeo em que nenhum deles é popular (apenas um ou dois visualizadores por semana), mas você tem milhares de pessoas assistindo a todos os vídeos diferentes, a CDN se torna muito menos eficiente.

Também é necessário considerar como o streaming adaptável funciona. Cada fragmento de vídeo individual é armazenado em cache como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é observado. Se o visualizador ignorar apenas alguns segundos aqui e houver, somente os fragmentos de vídeo associados ao que a pessoa observou serão armazenados em cache na CDN. Com o streaming adaptável, você normalmente tem de 5 a 7 taxas de bits diferentes de vídeo. Se uma pessoa estiver assistindo a uma taxa de bits e outra pessoa estiver assistindo a uma taxa de bits diferente, ela será cada uma armazenada em cache separadamente na CDN. Mesmo que duas pessoas estejam assistindo a mesma taxa de bits, elas podem ser transmitidas por protocolos diferentes. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é armazenado em cache separadamente. Portanto, cada taxa de bits e protocolo são armazenados em cache separadamente e apenas os fragmentos de vídeo que foram solicitados são armazenados em cache.

Exceto para o ambiente de teste, recomendamos que a CDN seja habilitada para os pontos de extremidade de streaming Standard e Premium. Cada tipo de ponto de extremidade de streaming tem um limite de taxa de transferência com suporte diferente.
É difícil fazer um cálculo preciso para o número máximo de fluxos simultâneos com suporte por um ponto de extremidade de streaming, pois há vários fatores a serem levados em conta. Estão incluídos:

- Taxas de bits máximas usadas para streaming
- Comportamento de pré-buffer e alternância do Player. Os jogadores tentam disparar segmentos de uma origem e usam a velocidade de carga para calcular a alternância de taxa de bits adaptável. Se um ponto de extremidade de streaming ficar perto da saturação, os tempos de resposta poderão variar e os jogadores começarão a mudar para a qualidade inferior. Como isso está reduzindo a carga nos players de ponto de extremidade de streaming, dimensione de volta para a qualidade mais alta criando gatilhos de troca
Em geral, é seguro estimar o máximo de fluxos simultâneos por meio da taxa de transferência máxima do ponto de extremidade de streaming e dividir isso pela taxa de bits máxima (supondo que todos os jogadores usem a taxa de bits mais alta). Por exemplo, você pode ter um ponto de extremidade de streaming padrão que é limitado a 600 Mbps e a taxa de bits mais alta de 3Mbp. Nesse caso, aproximadamente 200 fluxos simultâneos têm suporte na taxa de bits superior. Lembre-se também de considerar os requisitos de largura de banda de áudio. Embora um fluxo de áudio só possa ser transmitido em 128 KPS, o total de streaming aumenta rapidamente quando você o multiplica pelo número de fluxos simultâneos.

Este tópico discute a habilitação da [integração da CDN](#enable-azure-cdn-integration). Ele também explica a pré-busca (cache ativo) e o conceito de [CDN-prefetch de origem de assistência](#origin-assist-cdn-prefetch) .

## <a name="considerations"></a>Considerações

- O [ponto de extremidade de streaming](streaming-endpoint-concept.md) `hostname` e a URL de streaming permanecem os mesmos, independentemente de você habilitar ou não a CDN.
- Se você precisar da capacidade de testar seu conteúdo com ou sem CDN, crie outro ponto de extremidade de streaming que não esteja habilitado para CDN.

## <a name="enable-azure-cdn-integration"></a>Habilitar a integração da CDN do Azure

> [!IMPORTANT]
> Não é possível habilitar a CDN para contas de avaliação ou de aluno do Azure.
>
> A integração da CDN é habilitada em todos os data centers do Azure, exceto nas regiões federais governamentais e China.

Depois que um ponto de extremidade de streaming é provisionado com a CDN habilitada, há um tempo de espera definido nos serviços de mídia antes que a atualização de DNS seja feita para mapear o ponto de extremidade de streaming para o ponto de extremidade CDN.

Se quiser desabilitar/habilitar a CDN depois, o ponto de extremidade de streaming deverá estar no estado **interrompido**. Depois que o ponto de extremidade de streaming é iniciado, pode levar até quatro horas para a integração da CDN do Azure ser habilitada e para que as alterações estejam ativas em todos os POPs da CDN. No entanto, você pode iniciar seu ponto de extremidade de streaming e transmitir sem interrupções do ponto de extremidade de streaming. Depois que a integração for concluída, o fluxo será entregue da CDN. Durante o período de provisionamento, o ponto de extremidade de streaming estará no estado **inicial** e você poderá observar o desempenho degradado.

Quando o ponto de extremidade de streaming padrão é criado, ele é configurado por padrão com a Verizon padrão. Você pode configurar provedores da Verizon Premium ou Standard Akamai usando APIs REST.

A integração dos Serviços de Mídia do Azure à CDN do Azure é implementada da **Verizon na CDN do Azure** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preço e provedores da CDN do Azure**.

> [!NOTE]
> Para obter detalhes sobre a CDN do Azure, consulte a [visão geral da CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Determinar se uma alteração de DNS foi feita

Você pode determinar se a alteração de DNS foi feita em um ponto de extremidade de streaming (o tráfego está sendo direcionado para a CDN do Azure) usando <https://www.digwebinterface.com> . Se você vir os nomes de domínio azureedge.net nos resultados, o tráfego agora está sendo apontado para a CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

O cache da CDN é um processo reativo. Se a CDN puder prever o que o próximo objeto será solicitado, a CDN poderá solicitar e armazenar em cache o próximo objeto de forma proativa. Com esse processo, você pode obter um pressionamento de cache para todos (ou a maior parte) dos objetos, o que melhora o desempenho.

O conceito de pré-busca se esforça para posicionar objetos na "borda da Internet" na previsão de que eles serão solicitados pelo jogador de forma iminente, reduzindo assim o tempo de entrega desse objeto ao jogador.

Para atingir essa meta, um ponto de extremidade de streaming (origem) e a CDN precisam trabalhar lado a lado de duas maneiras:

- A origem dos serviços de mídia precisa ter a "inteligência" (assistência de origem) para informar à CDN o próximo objeto a prefetch.
- A CDN faz a pré-busca e o cache (parte da pré-busca da CDN). A CDN também precisa ter a "inteligência" para informar a origem se é uma pré-busca ou uma busca regular, manipular as respostas de 404 e uma maneira de evitar um loop de pré-busca infinito.

### <a name="benefits"></a>Benefícios

Os benefícios do recurso de *ajuda da CDN-Assist de origem* incluem:

- A pré-busca melhora a qualidade da reprodução de vídeo ao posicionar previamente os segmentos de vídeo previstos na borda durante a reprodução, reduzindo a latência para o visualizador e melhorando os tempos de download do segmento de vídeo. Isso resulta em um tempo de inicialização de vídeo mais rápido e em menos ocorrências de rearmazenamento em buffer.
- Esse conceito é aplicável ao cenário de origem da CDN geral e não é limitado à mídia.
- A Akamai adicionou esse recurso ao [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Este recurso ainda não é aplicável à CDN da Akamai integrada com o ponto de extremidade de streaming dos serviços de mídia. No entanto, ele está disponível para os clientes dos serviços de mídia que têm um contrato Akamai já existente e exigem integração personalizada entre o Akamai CDN e a origem dos serviços de mídia.

### <a name="how-it-works"></a>Como ele funciona

O suporte da CDN para os `Origin-Assist CDN-Prefetch` cabeçalhos (para streaming ao vivo e de vídeo sob demanda) está disponível para clientes que têm contrato direto com a AKAMAI CDN. O recurso envolve as seguintes trocas de cabeçalho HTTP entre o Akamai CDN e a origem dos serviços de mídia:

|Cabeçalho HTTP|Valores|Remetente|Receptor|Finalidade|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (padrão) ou 0 |CDN|Origem|Para indicar que a CDN está habilitada para pré-busca.|
|`CDN-Origin-Assist-Prefetch-Path`| Exemplo: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|Origem|CDN|Para fornecer o caminho de pré-busca para a CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (solicitação de pré-busca) ou 0 (solicitação regular)|CDN|Origem|Para indicar que a solicitação da CDN é uma pré-busca.|

Para ver parte da troca de cabeçalho em ação, você pode tentar as seguintes etapas:

1. Use o postmaster ou a ondulação para emitir uma solicitação para a origem dos serviços de mídia para um segmento ou fragmento de áudio ou vídeo. Certifique-se de adicionar o cabeçalho `CDN-Origin-Assist-Prefetch-Enabled: 1` na solicitação.
2. Na resposta, você deve ver o cabeçalho `CDN-Origin-Assist-Prefetch-Path` com um caminho relativo como seu valor.

### <a name="supported-streaming-protocols"></a>Protocolos de streaming com suporte

O `Origin-Assist CDN-Prefetch` recurso dá suporte aos seguintes protocolos de streaming para streaming ao vivo e sob demanda:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Streaming suave

### <a name="faqs"></a>Perguntas frequentes

* E se uma URL de caminho de pré-busca for inválida para que a pré-busca da CDN receba um 404?

    A CDN armazenará em cache apenas uma resposta de 404 por 10 segundos (ou outro valor configurado).

* Suponha que você tenha um vídeo sob demanda. Se a CDN-prefetch estiver habilitada, esse recurso implicará que, uma vez que um cliente solicite o primeiro segmento de vídeo, a pré-busca iniciará um loop para prefetch de todos os segmentos de vídeo subsequentes na mesma taxa de bits?

    Não, CDN-prefetch é feito somente após uma solicitação/resposta iniciada pelo cliente. CDN-a pré-busca nunca é disparada por uma pré-busca, para evitar um loop de pré-busca.

* Origin-Assist está CDN-Prefetch recurso AlwaysOn? Como é possível ativar/desativar?

    Esse recurso está desativado por padrão. Os clientes precisam ativá-lo por meio da API da Akamai.

* Para transmissão ao vivo, o que aconteceria Origin-Assist se o próximo segmento ou fragmento ainda não estiver disponível?

    Nesse caso, a origem dos serviços de mídia não fornecerá `CDN-Origin-Assist-Prefetch-Path` cabeçalho e a CDN-prefetch não ocorrerá.

* Como `Origin-Assist CDN-Prefetch` funciona com filtros de manifesto dinâmico?

    Esse recurso funciona independentemente do filtro de manifesto. Quando o próximo fragmento estiver fora de uma janela de filtro, sua URL ainda estará localizada olhando para o manifesto do cliente bruto e, em seguida, retornado como cabeçalho de resposta da pré-busca da CDN. Portanto, a CDN obterá a URL de um fragmento filtrado do manifesto de traço/HLS/suave. No entanto, o Player nunca fará uma solicitação GET para a CDN para buscar esse fragmento, pois esse fragmento não está incluído no manifesto DASH/HLS/suave mantido pelo Player (o Player não sabe que a existência do fragmento).

* O traço MPD/HLS playlist/Smooth manifest pode ser previamente buscado?

    Não, DASH MPD, HLS mestre playlist, HLS Variant playlist ou a URL de manifesto suave não é adicionada ao cabeçalho prefetch.

* As URLs de pré-busca são relativas ou absolutas?

    Embora o Akamai CDN permita ambos, a origem dos serviços de mídia fornece apenas URLs relativas para o caminho de pré-busca porque não há nenhum benefício aparente no uso de URLs absolutas.

* Este recurso funciona com conteúdo protegido por DRM?

    Sim, como esse recurso funciona no nível de HTTP, ele não decodifica nem analisa nenhum segmento/fragmento. Não importa se o conteúdo está criptografado ou não.

* Esse recurso funciona com a inserção AD do lado do servidor (SSAI)?
    
    Ele faz o conteúdo original/principal (o conteúdo de vídeo original antes da inserção do AD) funciona, já que SSAI não altera o carimbo de data/hora do conteúdo de origem da origem dos serviços de mídia. Se esse recurso funciona com o conteúdo do AD depende se a origem do AD dá suporte ao auxiliar de origem. Por exemplo, se o conteúdo do AD também estiver hospedado nos serviços de mídia do Azure (a mesma origem ou a outra), o conteúdo do AD também será pré-busca.

* Este recurso funciona com o conteúdo de UHD/HEVC?

    Sim.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* Certifique-se de examinar o documento de [ponto de extremidade de streaming (origem)](streaming-endpoint-concept.md) .
* O exemplo [neste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto de extremidade de streaming padrão com .NET.
