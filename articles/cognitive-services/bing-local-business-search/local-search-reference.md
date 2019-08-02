---
title: Referência do Bing local da API de pesquisa de negócios v7
titleSuffix: Azure Cognitive Services
description: Descreve os elementos de programação da API de pesquisa do Bing Local comercial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 36d87ee9db68c47ee96519cb6b04ff24478427d6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423412"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Referência da API v7 da Pesquisa de empresa local do Bing

A API de pesquisa de empresa local envia uma consulta de pesquisa ao Bing para obter resultados que incluam restaurantes, hotéis ou outras empresas locais. A API de pesquisa de empresa local envia uma consulta de pesquisa ao Bing para obter resultados que incluam restaurantes, hotéis ou outras empresas locais. Os resultados de entidade incluem pessoas, lugares ou coisas. O local neste contexto é entidades de negócios, Estados, países/regiões, etc.  

Esta seção fornece detalhes técnicos sobre os objetos de resposta e os parâmetros e cabeçalhos de consulta que afetam os resultados da pesquisa. Para exemplos que mostram como fazer solicitações, consulte [Início rápido da Pesquisa C# de empresa local](quickstarts/local-quickstart.md) ou [Início rápido de Java da Pesquisa de empresa local](quickstarts/local-search-java-quickstart.md). 
  
Para obter informações sobre os cabeçalhos que devem ser incluídos nas solicitações, confira [Cabeçalhos](#headers).  
  
Para obter informações sobre os parâmetros de consulta que devem ser incluídos nas solicitações, confira [Parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre os objetos JSON incluídos na resposta, confira [Objetos de resposta](#response-objects).

Para obter informações sobre o uso permitido e a exibição de resultados, confira [Requisitos de uso e exibição](use-display-requirements.md).


  
## <a name="endpoint"></a>Ponto de extremidade  
Para solicitar resultados de negócios locais, envie uma solicitação GET para: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
A solicitação deve usar o protocolo HTTPS.  
  
> [!NOTE]
> O tamanho máximo da URL é de 2.048 caracteres. Para garantir que o tamanho da URL não exceda o limite, o tamanho máximo dos parâmetros de consulta deve ser inferior a 1.500 caracteres. Se a URL excede 2.048 caracteres, o servidor retorna 404 Não encontrado.  
  
  
## <a name="headers"></a>Cabeçalhos  
Veja a seguir os cabeçalhos que podem ser incluídos em uma solicitação e uma resposta.  
  
|Cabeçalho|Descrição|  
|------------|-----------------|  
|Aceitar|Cabeçalho de solicitação opcional.<br /><br /> O tipo de mídia padrão é application/json. Para especificar que a resposta usará [JSON-LD](https://json-ld.org/), defina o cabeçalho Accept como application/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Cabeçalho de solicitação opcional.<br /><br /> Uma lista delimitada por vírgula de idiomas a serem usados para as cadeias de caracteres de interface do usuário. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, confira [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Esse cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos – não especifique ambos.<br /><br /> Se você definir esse cabeçalho, também deverá especificar o parâmetro de consulta cc. Para determinar o mercado para o qual retornar os resultados, o Bing usa o primeiro idioma compatível suporte encontrado na lista e combina-o com o valor de parâmetro `cc`. Se a lista não inclui um idioma compatível, o Bing encontra o idioma e o mercado mais próximos que dão suporte à solicitação ou usa um mercado padrão ou agregado para os resultados. Para determinar o mercado usado pelo Bing, confira o cabeçalho BingAPIs-Market.<br /><br /> Use esse cabeçalho e o parâmetro de consulta `cc` somente se você especificar vários idiomas. Caso contrário, use os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> Uma cadeia de caracteres de interface do usuário é uma cadeia de caracteres que é usada como um rótulo em uma interface do usuário. Há poucas cadeias de caracteres de interface do usuário nos objetos de resposta JSON. Todos os links para as propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="market" />BingAPIs-Market|Cabeçalho de resposta.<br /><br /> O mercado usado pela solicitação. O formulário é \<languageCode\>-\<countryCode\>. Por exemplo, en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Cabeçalho de resposta.<br /><br /> A ID da entrada de log que contém os detalhes da solicitação. Quando ocorrer um erro, capture essa ID. Se você não conseguir determinar e resolver o problema, inclua essa ID juntamente com outras informações fornecidas à equipe de Suporte.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de solicitação obrigatório.<br /><br /> A chave de assinatura que você recebeu quando se inscreveu nesse serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Cabeçalho de solicitação opcional<br /><br /> Por padrão, o Bing retorna o conteúdo armazenado em cache se disponível. Para impedir que o Bing retorne o conteúdo armazenado em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent" />User-Agent|Cabeçalho de solicitação opcional.<br /><br /> O agente do usuário que originou a solicitação. O Bing usa o agente do usuário para fornecer uma experiência otimizada aos usuários móveis. Embora isso seja opcional, você é incentivado a sempre especificar esse cabeçalho.<br /><br /> O agente do usuário deve ter a mesma cadeia de caracteres enviada por qualquer navegador geralmente usado. Para obter informações sobre agentes do usuário, confira [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Veja a seguir exemplos de cadeias de caracteres de agente do usuário.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatível; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; como Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 como Mac OS X) AppleWebKit/536.26 (KHTML; como Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) como Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 como Mac OS X) AppleWebKit/537.51.1 (KHTML, como Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de solicitação e resposta opcional.<br /><br /> O Bing usa esse cabeçalho para fornecer aos usuários um comportamento consistente nas chamadas à API do Bing. Em geral, o Bing faz o pré-lançamento de novos recursos e novas melhorias e usa a ID do cliente como uma chave para atribuição de tráfego em versões de pré-lançamento diferentes. Se você não usar a mesma ID do cliente para um usuário em várias solicitações, o Bing poderá atribuir o usuário a várias versões de pré-lançamento conflitantes. A atribuição a várias versões de pré-lançamento conflitantes pode levar a uma experiência do usuário inconsistente. Por exemplo, se a segunda solicitação tem uma atribuição de versão de pré-lançamento diferente da primeira, a experiência pode ser inesperada. Além disso, o Bing pode usar a ID do cliente para adaptar os resultados da Web ao histórico de pesquisa dessa ID do cliente, fornecendo uma experiência mais rica para o usuário.<br /><br /> O Bing também usa esse cabeçalho para ajudar a melhorar as classificações de resultado pela análise da atividade gerada por uma ID do cliente. As melhorias de relevância ajudam com uma melhor qualidade dos resultados fornecidos pelas APIs do Bing e, por sua vez, permitem taxas de clickthrough mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** Embora opcional, você deverá considerar esse cabeçalho como obrigatório. A persistência da ID do cliente em várias solicitações para a mesma combinação de usuário final e dispositivo permite 1) ao consumidor da API receber uma experiência do usuário consistente e 2) taxas de clickthrough mais altas por meio de uma melhor qualidade dos resultados das APIs do Bing.<br /><br /> Veja a seguir as regras de uso básicas que se aplicam a esse cabeçalho.<br /><ul><li>Cada usuário que usa o aplicativo no dispositivo precisa ter uma ID do cliente exclusiva gerada pelo Bing.<br /><br/>Se você não incluir esse cabeçalho na solicitação, o Bing gerará uma ID e a retornará no cabeçalho de resposta X-MSEdge-ClientID. A única vez que você NÃO deve incluir esse cabeçalho em uma solicitação é a primeira vez que o usuário usa o aplicativo nesse dispositivo.<br /><br/></li><li>Use a ID do cliente para cada solicitação da API do Bing feita pelo aplicativo para esse usuário no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Garanta que essa ID do Cliente não seja vinculável aos dados da conta de usuário autenticável.</li><br/><li>Persista a ID do cliente. Para persistir a ID em um aplicativo de navegador, use um cookie HTTP persistente para garantir que a ID seja usada em todas as sessões. Não use um cookie de sessão. Para outros aplicativos, como aplicativos móveis, use o armazenamento persistente do dispositivo para persistir a ID.<br /><br/>Na próxima vez que o usuário usar o aplicativo no dispositivo, obtenha a ID do cliente persistente.</li></ul><br /> **OBSERVAÇÃO:** As respostas do Bing podem ou não incluir esse cabeçalho. Se a resposta incluir esse cabeçalho, capture a ID do cliente e use-a para todas as solicitações seguintes do Bing para o usuário no dispositivo.<br /><br /> **OBSERVAÇÃO:** Se você incluir o X-MSEdge-ClientID, não deverá incluir cookies na solicitação.|  
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de solicitação opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é usado para descobrir o local do usuário. O Bing usa as informações de local para determinar o comportamento da pesquisa segura.<br /><br /> **OBSERVAÇÃO:** Embora opcional, é recomendável sempre especificar esse cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não oculte o endereço (por exemplo, alterando o último octeto para 0). Se você ocultar o endereço, isso fará com que o local não fique em nenhum lugar próximo ao local real do dispositivo, o que poderá resultar no fornecimento de resultados incorretos pelo Bing.|  
|<a name="location" />X-Search-Location|Cabeçalho de solicitação opcional.<br /><br /> Uma lista delimitada por ponto-e-vírgula de pares chave/valor que descrevem a localização geográfica do cliente. O Bing usa as informações de local para determinar o comportamento da pesquisa segura e retornar o conteúdo local relevante. Especifique o par chave/valor como \<key\>:\<value\>. Veja a seguir as chaves usadas para especificar o local do usuário.<br /><br /><ul><li>lat – a latitude do local do cliente, em graus. A latitude precisa ser superior ou igual a -90,0 e inferior ou igual a +90,0. Valores negativos indicam latitudes sul e valores positivos indicam latitudes norte.<br /><br /></li><li>long – a longitude do local do cliente, em graus. A longitude precisa ser superior ou igual a -180,0 e inferior ou igual a +180,0. Valores negativos indicam longitudes oeste e valores positivos indicam longitudes leste.<br /><br /></li><li>re – o raio, em metros, que especifica a precisão horizontal das coordenadas. Passe o valor retornado pelo serviço de local do dispositivo. Os valores típicos podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de torres de celular e 18.000 m para a pesquisa inversa de IP.<br /><br /></li><li>ts – o carimbo de data/hora UTC UNIX do período em que o cliente estava no local. (O carimbo de data/hora UNIX é o número de segundos desde 1º de janeiro de 1970.)<br /><br /></li><li>head – opcional. A direção ou o curso da viagem relativo do cliente. Especifique a direção da viagem como graus de 0 a 360, no sentido horário em relação ao norte verdadeiro. Especifique essa chave somente se a chave `sp` for diferente de zero.<br /><br /></li><li>sp – a velocidade horizontal, em metros por segundo, na qual o dispositivo cliente está viajando.<br /><br /></li><li>alt – a altitude do dispositivo cliente, em metros.<br /><br /></li><li>are – opcional. O raio, em metros, que especifica a precisão vertical das coordenadas. O raio usa como padrão 50 quilômetros. Especifique essa chave somente se você especificar a chave `alt`.<br /><br /></li></ul> **OBSERVAÇÃO:** Embora essas chaves sejam opcionais, quanto mais informações você fornecer, mais precisos serão os resultados de localização.<br /><br /> **OBSERVAÇÃO:** Você é incentivado a sempre especificar a localização geográfica do usuário. O fornecimento do local é especialmente importante se o endereço IP do cliente não reflete precisamente o local físico do usuário (por exemplo, se o cliente usa VPN). Para obter melhores resultados, você deve incluir esse cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas, no mínimo, você deve incluir esse cabeçalho.|

> [!NOTE] 
> Lembre-se de que os Termos de Uso exigem conformidade com todas as leis aplicáveis, incluindo o uso desses cabeçalhos. Por exemplo, em algumas jurisdições, como na Europa, há requisitos para obter o consentimento do usuário antes de colocar determinados dispositivos de monitoramento em dispositivos do usuário.
  

## <a name="query-parameters"></a>Parâmetros de consulta  
A solicitação pode incluir os parâmetros de consulta a seguir. Confira a coluna Obrigatório para obter os parâmetros necessários. É necessário codificar os parâmetros de consulta em URL.  
  
  
|Nome|Valor|Tipo|Necessário|  
|----------|-----------|----------|--------------|
|<a name="count" />Contagem|O número de resultados a serem retornados, começando com o índice especificado pelo `offset` parâmetro.|Cadeia|Não|   
|<a name="localCategories" />localCategories|Lista de opções que definem a pesquisa por categoria de negócio.  Consulte [categorias de negócios locais de pesquisa](local-categories.md)|Cadeia|Não|  
|<a name="mkt" />mkt|O mercado do qual os resultados são obtidos. <br /><br />Para obter uma lista dos possíveis valores de mercado, confira Códigos de mercado.<br /><br /> **OBSERVAÇÃO:** Atualmente, a API de Pesquisa de Empresas Locais dá suporte apenas ao mercado e ao idioma en-us.<br /><br />|Cadeia|Sim|
|<a name="offset"/>deslocamento|O índice para iniciar os resultados especificados pelo parâmetro `count`.|Inteiro|Não|  
|<a name="query" />q|Termo de pesquisa do usuário.|Cadeia|Não|  
|<a name="responseformat" />responseFormat|O tipo de mídia a ser usado para a resposta. Veja a seguir os possíveis valores que não diferenciam maiúsculas de minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON contidos na resposta, confira [Objetos de resposta](#response-objects).<br /><br />  Se você especificar JsonLd, o corpo da resposta incluirá objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, confira [JSON-LD](https://json-ld.org/).|Cadeia|Não|  
|<a name="safesearch" />Pesquisa Segura|Um filtro usado para filtrar o conteúdo para adulto. Veja a seguir os possíveis valores de filtro que não diferenciam maiúsculas de minúsculas.<br /><ul><li>Desativado – retorna páginas da Web com texto, imagens ou vídeos para adulto.<br /><br/></li><li>Moderado – retorna páginas da Web com texto para adulto, mas não imagens nem vídeos para adulto.<br /><br/></li><li>Estrito – não retorna páginas da Web com texto, imagens ou vídeos para adulto.</li></ul><br /> O padrão é Moderado.<br /><br /> **OBSERVAÇÃO:** Se a solicitação for proveniente de um mercado cuja política de conteúdo para adulto do Bing exija que `safeSearch` seja definido como Estrito, o Bing ignorará o valor `safeSearch` e usará Estrito.<br/><br/>**OBSERVAÇÃO:** Se você usar o operador de consulta `site:`, haverá a possibilidade da resposta poder conter conteúdo adulto, independentemente de como o parâmetro de consulta `safeSearch` está definido. Só use `site:` se estiver ciente do conteúdo do site e se o cenário der suporte à possibilidade de conteúdo para adulto. |Cadeia|Não|  
|<a name="setlang" />setLang|O idioma a ser usado para cadeias de caracteres de interface do usuário. Especifique o idioma usando o código de idioma ISO 639-1 de 2 letras. Por exemplo, o código de idioma para o inglês é EN. O padrão é EN (inglês).<br /><br /> Embora isso seja opcional, você sempre deve especificar o idioma. Normalmente, você define `setLang` com o mesmo idioma especificado por `mkt`, a menos que o usuário deseje exibir as cadeias de caracteres de interface do usuário em outro idioma.<br /><br /> Esse parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos – não especifique ambos.<br /><br /> Uma cadeia de caracteres de interface do usuário é uma cadeia de caracteres que é usada como um rótulo em uma interface do usuário. Há poucas cadeias de caracteres de interface do usuário nos objetos de resposta JSON. Além disso, todos os links para as propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|Cadeia|Não| 


## <a name="response-objects"></a>Objetos de resposta  
A seguir estão os objetos de resposta JSON que a resposta pode incluir. Se a solicitação for bem-sucedida, o objeto de nível superior na resposta é o objeto [SearchResponse](#searchresponse). Se a solicitação falha, o objeto de nível superior é o objeto [ErrorResponse](#errorresponse).


|Objeto|Descrição|  
|------------|-----------------|  
|[Local](#place)|Define informações sobre uma empresa local, como um restaurante ou hotel.|  

  
### <a name="error"></a>Erro  
Define o erro ocorrido.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|O código de erro que identifica a categoria de erro. Para obter uma lista dos possíveis códigos, confira [Códigos de erro](#error-codes).|Cadeia|  
|<a name="error-message" />message|Uma descrição do erro.|Cadeia|  
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|Cadeia|  
|<a name="error-parameter" />parameter|O parâmetro de consulta na solicitação que causou o erro.|Cadeia|  
|<a name="error-subcode" />subCode|O código de erro que identifica o erro. Por exemplo, se `code` é InvalidRequest, `subCode` pode ser ParameterInvalid ou ParameterInvalidValue. |Cadeia|  
|<a name="error-value" />value|O valor do parâmetro de consulta inválido.|Cadeia|  
  

### <a name="errorresponse"></a>ErrorResponse  
O objeto de nível superior incluído pela resposta quando a solicitação falha.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia|  
|<a name="errors" />errors|Uma lista de erros que descreve os motivos pelos quais a solicitação falhou.|[Error](#error)[]|  

  
  
### <a name="license"></a>Licença  
Define a licença sob a qual a foto ou o texto pode ser usado.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|name|O nome da licença.|Cadeia|  
|url|A URL para um site em que o usuário pode obter mais informações sobre a licença.<br /><br /> Use o nome e a URL para criar um hiperlink.|Cadeia|  


### <a name="link"></a>Link  
Define os componentes de um hiperlink.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia|  
|texto|O texto de exibição.|Cadeia|  
|url|Uma URL. Use a URL e exiba o texto para criar um hiperlink.|Cadeia|  
  


  
### <a name="organization"></a>Organização  
Define um editor.  
  
Observe que um editor pode fornecer seu nome, seu site ou ambos.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|name|O nome do editor.|Cadeia|  
|url|A URL para o site do editor.<br /><br /> Observe que o editor pode não fornecer um site.|Cadeia|  
  
  

### <a name="place"></a>Local  
Define informações sobre uma empresa local, como um restaurante ou hotel.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Digite hint, que pode ser definido como um dos seguintes:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurante</ul><li>|Cadeia|  
|endereço|O endereço postal em que a entidade está localizada.|PostalAddress|  
|entityPresentationInfo|Informações adicionais sobre a entidade, como dicas que você pode usar para determinar o tipo da entidade. Por exemplo, seja um restaurante ou hotel. O campo `entityScenario` está definido como ListItem.|EntityPresentationInfo|  
|name|O nome da entidade.|Cadeia|  
|telefone|Número de telefone da entidade.|Cadeia|  
|url|A URL para o site da entidade.<br /><br /> Use este URL junto com o nome da entidade para criar um hiperlink que, quando clicado, leva o usuário ao site da entidade.|Cadeia|  
|webSearchUrl|A URL para o resultado da pesquisa do Bing para esse local.|Cadeia| 
  
  
### <a name="querycontext"></a>QueryContext  
Define o contexto de consulta usado pelo Bing para a solicitação.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|adultIntent|Um valor booliano que indica se a consulta especificada é direcionada para adultos. O valor é **true** se a consulta é direcionada para adultos; caso contrário, **false**.|Boolean|  
|alterationOverrideQuery|A cadeia de caracteres de consulta a ser usada para forçar o Bing a usar a cadeia de caracteres original. Por exemplo, se a cadeia de caracteres de consulta for *velejando na direção do vento*, a cadeia de caracteres de consulta de substituição será *+velejando na direção do vento*. Lembre-se de codificar a cadeia de caracteres de consulta que resulta em *%2Bvelejando+na direção do vento*.<br /><br /> Esse campo é incluído somente se a cadeia de caracteres de consulta original contém um erro de ortografia.|Cadeia|  
|alteredQuery|A cadeia de caracteres de consulta usada pelo Bing para executar a consulta. O Bing usa a cadeia de caracteres de consulta alterada se a cadeia de caracteres de consulta original contém erros de ortografia. Por exemplo, se a cadeia de caracteres de consulta for `saling downwind`, a cadeia de caracteres de consulta alterada será `sailing downwind`.<br /><br /> Esse campo é incluído somente se a cadeia de caracteres de consulta original contém um erro de ortografia.|Cadeia|  
|askUserForLocation|Um valor booliano que indica se o Bing exige o local do usuário para fornecer resultados precisos. Se você especificou o local do usuário usando os cabeçalhos [X-MSEdge-ClientIP](#clientip) e [X-Search-Location](#location), ignore esse campo.<br /><br /> Para consultas com reconhecimento de local, como "clima de hoje" ou "restaurantes próximos ao meu local" que precisam do local do usuário para fornecer resultados precisos, esse campo é definido como **true**.<br /><br /> Para consultas com reconhecimento de local que incluem o local (por exemplo, "clima de Seattle"), esse campo é definido como **false**. Esse campo também é definido como **false** para consultas sem reconhecimento de local, como "campeões de venda".|Boolean|  
|originalQuery|A cadeia de caracteres de consulta especificada na solicitação.|Cadeia|  

### <a name="identifiable"></a>Identifiable

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|id|Um identificador de recurso|Cadeia|
 
### <a name="rankinggroup"></a>RankingGroup
Define um grupo de resultados da pesquisa, como linha principal.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|items|Uma lista de resultados da pesquisa a serem exibidos no grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado da pesquisa a ser exibido.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|resultIndex|Um índice baseado em zero do item na resposta a ser exibido. Se o item não incluir esse campo, exiba todos os itens na resposta. Por exemplo, exiba todos os artigos de notícias na resposta Notícias.|Inteiro|
|answerType|A resposta que contém o item a ser exibido. Por exemplo, Notícias.<br /><br />Use o tipo para encontrar a resposta no objeto SearchResponse. O tipo é o nome de um campo SearchResponse.<br /><br /> No entanto, use o tipo de resposta somente se esse objeto incluir o campo de valor; caso contrário, ignore-o.|Cadeia|
|textualIndex|O índice da resposta em textualAnswers a ser exibido.| Inteiro sem sinal|
|value|A ID que identifica uma resposta a ser exibida ou um item de uma resposta a ser exibido. Se a ID identificar uma resposta, exiba todos os itens da resposta.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Define o local em que o conteúdo da página de resultados da pesquisa deve ser colocado e em qual ordem.  
  
|Nome|Valor|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Os resultados da pesquisa a serem exibidos na linha principal.|  
|<a name="ranking-pole" />pole|Os resultados da pesquisa que devem receber o tratamento mais visível (por exemplo, exibidos acima da linha principal e da barra lateral).|  
|<a name="ranking-sidebar" />sidebar|Os resultados da pesquisa a serem exibidos na barra lateral.| 

### <a name="searchresponse"></a>SearchResponse  
Define o objeto de nível superior incluído pela resposta quando a solicitação é bem-sucedida.  
  
Observe que se o serviço suspeitar de um ataque de negação de serviço, a solicitação será bem-sucedida (o código de status HTTP é 200 OK); no entanto, o corpo da resposta estará vazio.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo, que é definida como SearchResponse.|Cadeia|  
|Locais|Uma lista de entidades que são relevantes para a consulta de pesquisa.|Objeto JSON|  
|queryContext|Um objeto que contém a cadeia de caracteres de consulta Bing usado para a solicitação.<br /><br /> Este objeto contém a cadeia de caracteres de consulta, conforme inserido pelo usuário. Ele também pode conter uma cadeia de caracteres de consulta alterada Bing usado para a consulta se a cadeia de caracteres de consulta continha um erro de ortografia.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Códigos de erro

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.  
  
|Código de Status|Descrição|  
|-----------------|-----------------|  
|200|Êxito.|  
|400|Um dos parâmetros de consulta está ausente ou é inválido.|  
|401|A chave de assinatura está ausente ou é inválida.|  
|403|O usuário foi autenticado (por exemplo, ele usou uma chave de assinatura válida), mas não tem permissão para acessar o recurso solicitado.<br /><br /> O Bing também pode retornar esse status se o chamador excedeu suas consultas em relação à cota do mês.|  
|410|A solicitação usou HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo compatível.|  
|429|O chamador excedeu suas consultas em relação à cota de segundos.|  
|500|Erro de servidor inesperado.|

Se a solicitação falha, a resposta contém um objeto [ErrorResponse](#errorresponse), que contém uma lista de objetos [Error](#error) que descrevem a causa do erro. Se o erro está relacionado a um parâmetro, o campo `parameter` identifica o parâmetro que causa o problema. Além disso, se o erro está relacionado a um valor de parâmetro, o campo `value` identifica o valor que é inválido.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Veja a seguir os possíveis valores de código de erro e de código de suberro.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O código de status HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retornará InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se você usar o protocolo HTTP em vez de HTTPS, o Bing retornará HttpNotAllowed, e o código de status HTTP será 410.
|RateLimitExceeded|Nenhum subcódigo|O Bing retorna RateLimitExceeded sempre que você excede a cota de QPS (consultas por segundo) ou QPM (consultas por mês).<br/><br/>Se você exceder o QPS, o Bing retornará o código de status HTTP 429 e, se você exceder o QPM, o Bing retornará 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

## <a name="next-steps"></a>Próximas etapas
- [ Início Rápido da Pesquisa de empresa local ](quickstarts/local-quickstart.md)
- [Início rápido de negócios pesquisa Java local](quickstarts/local-search-java-quickstart.md)
- [Início rápido de negócios o nó de pesquisa local](quickstarts/local-search-node-quickstart.md)
- [Início rápido do Python para negócios de pesquisa local](quickstarts/local-search-python-quickstart.md)
