---
title: Referência da pesquisa de resposta do projeto
titlesuffix: Azure Cognitive Services
description: Referência do ponto de extremidade de Pesquisa de Resposta de Projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 4384bf658024f89664c5202ba10d793d7ad734e0
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592922"
---
# <a name="project-answer-search-v7-reference"></a>Referência da Pesquisa de Resposta de Projeto v7

A SearchAPI de Resposta do Bing usa um parâmetro de consulta e retorna uma `searchResponse` com `answerType`: `facts` ou `entities`. 

Os aplicativos que usam a API de Pesquisa de Resposta enviam as solicitações para o ponto de extremidade com uma URL a ser visualizada em um parâmetro de consulta.  A solicitação precisa incluir o parâmetro `q=searchTerm` e o cabeçalho *Ocp-Apim-Subscription-Key*.   

A resposta JSON pode ser analisada quanto aos fatos e às entidades que contêm detalhes sobre o objeto da pesquisa.

## <a name="endpoint"></a>Ponto de extremidade
Para solicitar os resultados da Pesquisa de Resposta, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir mais especificações.

Ponto de extremidade GET: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

A solicitação precisa usar o protocolo HTTPS e incluir o seguinte parâmetro de consulta:
-  `q=<URL>` -A consulta que identifica o objeto de pesquisa

Para obter exemplos que mostram como fazer solicitações, confira [Início Rápido do C#](c-sharp-quickstart.md) ou [Início Rápido do Java](java-quickstart.md). 

As seções a seguir fornecem detalhes técnicos sobre os objetos de resposta, os parâmetros de consulta e os cabeçalhos que afetam os resultados da pesquisa. 
  
Para obter informações sobre os cabeçalhos que devem ser incluídos nas solicitações, confira [Cabeçalhos](#headers).  
  
Para obter informações sobre os parâmetros de consulta que devem ser incluídos nas solicitações, confira [Parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre os objetos JSON incluídos na resposta, confira [Objetos de resposta](#response-objects).

O tamanho máximo da URL de consulta é de 2.048 caracteres. Para garantir que o tamanho da URL não exceda o limite, o tamanho máximo dos parâmetros de consulta deve ser inferior a 1.500 caracteres. Se a URL excede 2.048 caracteres, o servidor retorna 404 Não encontrado.  

Para obter informações sobre o uso permitido e a exibição de resultados, confira [Requisitos de uso e exibição](use-display-requirements.md). 

> [!NOTE]
> Alguns cabeçalhos de solicitação significativos para outras APIs de pesquisa não afetam a Visualização de URL
> - Pragma – o chamador não tem controle sobre se o cache é usado pela Visualização de URL
> - Cache-Control – o chamador não tem controle sobre se o cache é usado pela Visualização de URL
> - User-Agent
> 
> Além disso, alguns parâmetros não são atualmente significativos para a API de Visualização de URL, mas podem ser usados no futuro para uma melhor globalização. 
 
## <a name="headers"></a>Cabeçalhos  
Veja a seguir os cabeçalhos que podem ser incluídos em uma solicitação e uma resposta.  
  
|Cabeçalho|DESCRIÇÃO|  
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
  
  
|NOME|Value|Type|Obrigatório|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|O mercado do qual os resultados são obtidos. <br /><br />Para obter uma lista dos possíveis valores de mercado, confira Códigos de mercado.<br /><br /> **OBSERVAÇÃO:** Atualmente, a API de Visualização de URL dá suporte apenas ao mercado e ao idioma en-us.<br /><br />|String|Sim|  
|<a name="query" />q|A URL a ser visualizada|String|Sim|  
|<a name="responseformat" />responseFormat|O tipo de mídia a ser usado para a resposta. Veja a seguir os possíveis valores que não diferenciam maiúsculas de minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON contidos na resposta, confira [Objetos de resposta](#response-objects).<br /><br />  Se você especificar JsonLd, o corpo da resposta incluirá objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, confira [JSON-LD](https://json-ld.org/).|Cadeia de caracteres|Não|  
|<a name="safesearch" />Pesquisa Segura|Um filtro usado para filtrar o conteúdo para adulto. Veja a seguir os possíveis valores de filtro que não diferenciam maiúsculas de minúsculas.<br /><ul><li>Desativado – retorna páginas da Web com texto, imagens ou vídeos para adulto.<br /><br/></li><li>Moderado – retorna páginas da Web com texto para adulto, mas não imagens nem vídeos para adulto.<br /><br/></li><li>Estrito – não retorna páginas da Web com texto, imagens ou vídeos para adulto.</li></ul><br /> O padrão é Moderado.<br /><br /> **OBSERVAÇÃO:** Se a solicitação for proveniente de um mercado cuja política de conteúdo para adulto do Bing exija que `safeSearch` seja definido como Estrito, o Bing ignorará o valor `safeSearch` e usará Estrito.<br/><br/>**OBSERVAÇÃO:** Se você usar o operador de consulta `site:`, haverá a possibilidade da resposta poder conter conteúdo adulto, independentemente de como o parâmetro de consulta `safeSearch` está definido. Só use `site:` se estiver ciente do conteúdo do site e se o cenário der suporte à possibilidade de conteúdo para adulto. |Cadeia de caracteres|Não|  
|<a name="setlang" />setLang|O idioma a ser usado para cadeias de caracteres de interface do usuário. Especifique o idioma usando o código de idioma ISO 639-1 de 2 letras. Por exemplo, o código de idioma para o inglês é EN. O padrão é EN (inglês).<br /><br /> Embora isso seja opcional, você sempre deve especificar o idioma. Normalmente, você define `setLang` com o mesmo idioma especificado por `mkt`, a menos que o usuário deseje exibir as cadeias de caracteres de interface do usuário em outro idioma.<br /><br /> Esse parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos – não especifique ambos.<br /><br /> Uma cadeia de caracteres de interface do usuário é uma cadeia de caracteres que é usada como um rótulo em uma interface do usuário. Há poucas cadeias de caracteres de interface do usuário nos objetos de resposta JSON. Além disso, todos os links para as propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|String|Não| 


## <a name="response-objects"></a>Objetos de resposta  
O esquema de resposta é uma [WebPage] ou uma ErrorResponse, como na API de Pesquisa na Web. Se a solicitação falha, o objeto de nível superior é o objeto [ErrorResponse](#errorresponse).


|Object|DESCRIÇÃO|  
|------------|-----------------|  
|[WebPage]|O objeto JSON de nível superior que contém atributos de visualização.|  
|[Fact]|O objeto JSON de nível superior que contém os fatos.| 
|[Entities]|O objeto JSON de nível superior que contém os detalhes da entidade.| 

  
### <a name="error"></a>Erro  
Define o erro ocorrido.  
  
|Elemento|DESCRIÇÃO|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|O código de erro que identifica a categoria de erro. Para obter uma lista dos possíveis códigos, confira [Códigos de erro](#error-codes).|Cadeia de caracteres|  
|<a name="error-message" />message|Uma descrição do erro.|Cadeia de caracteres|  
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|String|  
|<a name="error-parameter" />parameter|O parâmetro de consulta na solicitação que causou o erro.|Cadeia de caracteres|  
|<a name="error-subcode" />subCode|O código de erro que identifica o erro. Por exemplo, se `code` é InvalidRequest, `subCode` pode ser ParameterInvalid ou ParameterInvalidValue. |Cadeia de caracteres|  
|<a name="error-value" />value|O valor do parâmetro de consulta inválido.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
O objeto de nível superior incluído pela resposta quando a solicitação falha.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia de caracteres|  
|<a name="errors" />errors|Uma lista de erros que descreve os motivos pelos quais a solicitação falhou.|[Erro](#error)|  

  
  
### <a name="license"></a>Licença  
Define a licença sob a qual a foto ou o texto pode ser usado.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|name|O nome da licença.|String|  
|url|A URL para um site em que o usuário pode obter mais informações sobre a licença.<br /><br /> Use o nome e a URL para criar um hiperlink.|Cadeia de caracteres|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Define uma regra contratual para atribuição de licença.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Uma dica de tipo, que é definida como LicenseAttribution.|String|  
|license|A licença sob a qual o conteúdo pode ser usado.|[Licença](#license)|  
|licenseNotice|A licença é exibida ao lado do campo de destino. Por exemplo, "Texto sob a licença CC-BY-SA".<br /><br /> Use o nome da licença e a URL no campo `license` para criar um hiperlink para o site que descreve os detalhes da licença. Em seguida, substitua o nome da licença na cadeia de caracteres `licenseNotice` (por exemplo, CC-BY-SA) com o hiperlink recém-criado.|Cadeia de caracteres|  
|mustBeCloseToContent|Um valor booliano que determina se o conteúdo da regra precisa ser colocado nas proximidades do campo ao qual a regra se aplica. Se for **true**, o conteúdo precisará ser colocado nas proximidades. Se for **false** ou se esse campo não existir, o conteúdo poderá ser colocado em qualquer lugar, a critério do chamador.|Boolean|  
|targetPropertyName|O nome do campo ao qual a regra se aplica.|String|  
  

### <a name="link"></a>Link  
Define os componentes de um hiperlink.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia de caracteres|  
|text|O texto de exibição.|Cadeia de caracteres|  
|url|Uma URL. Use a URL e exiba o texto para criar um hiperlink.|Cadeia de caracteres|  
  

### <a name="linkattribution"></a>LinkAttribution  
Define uma regra contratual para atribuição de link.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Uma dica de tipo, que é definida como LinkAttribution.|String|  
|mustBeCloseToContent|Um valor booliano que determina se o conteúdo da regra precisa ser colocado nas proximidades do campo ao qual a regra se aplica. Se for **true**, o conteúdo precisará ser colocado nas proximidades. Se for **false** ou se esse campo não existir, o conteúdo poderá ser colocado em qualquer lugar, a critério do chamador.|Boolean|  
|targetPropertyName|O nome do campo ao qual a regra se aplica.<br /><br /> Se um destino não é especificado, a atribuição se aplica à entidade como um todo e deve ser exibida imediatamente após a apresentação da entidade. Se houver várias regras de atribuição de texto e de link que não especificam um destino, você deverá concatená-las e exibi-las usando um rótulo "Dados de:". Por exemplo, "Dados de <provider name1\> &#124; <provider name2\>".|Cadeia de caracteres|  
|text|O texto de atribuição.|Cadeia de caracteres|  
|url|A URL para o site do provedor. Use `text` e a URL para criação do hiperlink.|Cadeia de caracteres|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Define uma regra contratual para atribuição de mídia.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Uma dica de tipo, que é definida como MediaAttribution.|Cadeia de caracteres|  
|mustBeCloseToContent|Um valor booliano que determina se o conteúdo da regra precisa ser colocado nas proximidades do campo ao qual a regra se aplica. Se for **true**, o conteúdo precisará ser colocado nas proximidades. Se for **false** ou se esse campo não existir, o conteúdo poderá ser colocado em qualquer lugar, a critério do chamador.|Boolean|  
|targetPropertyName|O nome do campo ao qual a regra se aplica.|Cadeia de caracteres|  
|url|A URL usada para criação do hiperlink do conteúdo de mídia. Por exemplo, se o destino é uma imagem, use a URL para tornar a imagem clicável.|Cadeia de caracteres|  
  
  
  
### <a name="organization"></a>Organização  
Define um editor.  
  
Observe que um editor pode fornecer seu nome, seu site ou ambos.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|name|O nome do editor.|Cadeia de caracteres|  
|url|A URL para o site do editor.<br /><br /> Observe que o editor pode não fornecer um site.|Cadeia de caracteres|  
  
  

### <a name="webpage"></a>WebPage  
Define as informações sobre uma página da Web na visualização.  
  
|NOME|Value|Type|  
|----------|-----------|----------|
|name|O título de página, não necessariamente o título HTML|Cadeia de caracteres|
|url|A URL que foi realmente rastreada (a solicitação pode ter seguido os redirecionamentos)|Cadeia de caracteres|  
|description|Breve descrição da página e do conteúdo|String|  
|isFamilyFriendly|Mais preciso para itens no índice da Web; os fetches em tempo real fazem essa detecção baseada somente na URL e não no conteúdo da página|boolean|
|primaryImageOfPage/contentUrl|A URL de uma imagem representativa a ser incluída na visualização|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Define o contexto de consulta usado pelo Bing para a solicitação.  
  
|Elemento|DESCRIÇÃO|Type|  
|-------------|-----------------|----------|  
|adultIntent|Um valor booliano que indica se a consulta especificada é direcionada para adultos. O valor é **true** se a consulta é direcionada para adultos; caso contrário, **false**.|Boolean|  
|alterationOverrideQuery|A cadeia de caracteres de consulta a ser usada para forçar o Bing a usar a cadeia de caracteres original. Por exemplo, se a cadeia de caracteres de consulta for *velejando na direção do vento*, a cadeia de caracteres de consulta de substituição será *+velejando na direção do vento*. Lembre-se de codificar a cadeia de caracteres de consulta que resulta em *%2Bvelejando+na direção do vento*.<br /><br /> Esse campo é incluído somente se a cadeia de caracteres de consulta original contém um erro de ortografia.|String|  
|alteredQuery|A cadeia de caracteres de consulta usada pelo Bing para executar a consulta. O Bing usa a cadeia de caracteres de consulta alterada se a cadeia de caracteres de consulta original contém erros de ortografia. Por exemplo, se a cadeia de caracteres de consulta for `saling downwind`, a cadeia de caracteres de consulta alterada será `sailing downwind`.<br /><br /> Esse campo é incluído somente se a cadeia de caracteres de consulta original contém um erro de ortografia.|String|  
|askUserForLocation|Um valor booliano que indica se o Bing exige o local do usuário para fornecer resultados precisos. Se você especificou o local do usuário usando os cabeçalhos [X-MSEdge-ClientIP](#clientip) e [X-Search-Location](#location), ignore esse campo.<br /><br /> Para consultas com reconhecimento de local, como "clima de hoje" ou "restaurantes próximos ao meu local" que precisam do local do usuário para fornecer resultados precisos, esse campo é definido como **true**.<br /><br /> Para consultas com reconhecimento de local que incluem o local (por exemplo, "clima de Seattle"), esse campo é definido como **false**. Esse campo também é definido como **false** para consultas sem reconhecimento de local, como "campeões de venda".|Boolean|  
|originalQuery|A cadeia de caracteres de consulta especificada na solicitação.|String|  

### <a name="identifiable"></a>Identifiable

|NOME|Value|Type|  
|-------------|-----------------|----------|
|id|Um identificador de recurso|String|
 
### <a name="rankinggroup"></a>RankingGroup
Define um grupo de resultados da pesquisa, como linha principal.

|NOME|Value|Type|  
|-------------|-----------------|----------|
|items|Uma lista de resultados da pesquisa a serem exibidos no grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado da pesquisa a ser exibido.

|NOME|Value|Type|  
|-------------|-----------------|----------|
|resultIndex|Um índice baseado em zero do item na resposta a ser exibido. Se o item não incluir esse campo, exiba todos os itens na resposta. Por exemplo, exiba todos os artigos de notícias na resposta Notícias.|Número inteiro|
|answerType|A resposta que contém o item a ser exibido. Por exemplo, Notícias.<br /><br />Use o tipo para encontrar a resposta no objeto SearchResponse. O tipo é o nome de um campo SearchResponse.<br /><br /> No entanto, use o tipo de resposta somente se esse objeto incluir o campo de valor; caso contrário, ignore-o.|Cadeia de caracteres|
|textualIndex|O índice da resposta em textualAnswers a ser exibido.| Inteiro sem sinal|
|value|A ID que identifica uma resposta a ser exibida ou um item de uma resposta a ser exibido. Se a ID identificar uma resposta, exiba todos os itens da resposta.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
Define o local em que o conteúdo da página de resultados da pesquisa deve ser colocado e em qual ordem.  
  
|NOME|Value|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Os resultados da pesquisa a serem exibidos na linha principal.|  
|<a name="ranking-pole" />pole|Os resultados da pesquisa que devem receber o tratamento mais visível (por exemplo, exibidos acima da linha principal e da barra lateral).|  
|<a name="ranking-sidebar" />sidebar|Os resultados da pesquisa a serem exibidos na barra lateral.| 


### <a name="searchresponse"></a>SearchResponse  
Define o objeto de nível superior incluído pela resposta quando a solicitação é bem-sucedida.  
  
Observe que se o serviço suspeitar de um ataque de negação de serviço, a solicitação será bem-sucedida (o código de status HTTP é 200 OK); no entanto, o corpo da resposta estará vazio.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo, que é definida como SearchResponse.|String|  
|WebPage|Um objeto JSON que define a visualização|string|  
  
  
### <a name="textattribution"></a>TextAttribution  
Define uma regra contratual para a atribuição de texto sem formatação.  
  
|NOME|Value|Type|  
|----------|-----------|----------|  
|_type|Uma dica de tipo, que é definida como TextAttribution.|Cadeia de caracteres|  
|text|O texto de atribuição.<br /><br /> A atribuição de texto se aplica à entidade como um todo e deve ser exibida imediatamente após a apresentação da entidade. Se houver várias regras de atribuição de texto ou de link que não especificam um destino, você deverá concatená-las e exibi-las usando um rótulo "Dados de:".|Cadeia de caracteres| 


## <a name="error-codes"></a>Códigos do Erro

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|200|Sucesso.|  
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

|Código|Subcódigo|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O código de status HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retornará InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se você usar o protocolo HTTP em vez de HTTPS, o Bing retornará HttpNotAllowed, e o código de status HTTP será 410.
|RateLimitExceeded|Nenhum subcódigo|O Bing retorna RateLimitExceeded sempre que você excede a cota de QPS (consultas por segundo) ou QPM (consultas por mês).<br/><br/>Se você exceder o QPS, o Bing retornará o código de status HTTP 429 e, se você exceder o QPM, o Bing retornará 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)

