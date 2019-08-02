---
title: API de Tradução de Texto v2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de tradução de texto v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797812"
---
# <a name="translator-text-api-v20"></a>API de Tradução de Texto v2.0

> [!IMPORTANT]
> Esta versão da API de Tradução de Texto foi preterida. [Exiba a documentação para a versão 3 do API de tradução de texto](v3-0-reference.md).

A versão 2 da API de tradução de texto pode ser integrado perfeitamente seus aplicativos, sites, ferramentas ou outras soluções para fornecer experiências de usuário multilíngue. Você pode usá-lo em qualquer plataforma de hardware e com qualquer sistema operacional para realizar a conversão de idioma e outras tarefas relacionadas ao idioma, como detecção de idioma de texto e texto em fala, acordo com os padrões do setor. Para obter mais informações, consulte [API de tradução de texto](../translator-info-overview.md).

## <a name="getting-started"></a>Introdução
Para acessar a API de tradução de texto, você precisará [Inscreva-se no Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas para a API de tradução de texto exigem uma chave de assinatura para autenticação. A API dá suporte a três métodos de autenticação:

- Um token de acesso. Use a chave de assinatura mencionada na etapa 9 para criar um token de acesso fazendo uma solicitação POST para o serviço de autenticação. Veja a documentação do serviço de token para obter detalhes. Passar o token de acesso para o serviço de tradução usando o `Authorization` cabeçalho ou o `access_token` parâmetro de consulta. O token de acesso é válido por 10 minutos. Obter um novo token de acesso a cada 10 minutos e manter o usando o mesmo acesso token para solicitações repetidas durante 10 minutos.
- Uma chave de assinatura usada diretamente. Passe sua chave de assinatura como um valor no `Ocp-Apim-Subscription-Key` cabeçalho incluído com sua solicitação para a API de tradução de texto. Quando você usa a chave de assinatura diretamente, você não precisa chamar o serviço de autenticação de token para criar um token de acesso.
- Uma [assinatura de vários serviço do Azure dos serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/). Esse método permite que você use uma única chave secreta para autenticar solicitações para vários serviços.
Quando você usa uma chave secreta vários serviço, você precisa incluir dois cabeçalhos de autenticação com sua solicitação. O cabeçalho da primeira passa a chave secreta. O segundo cabeçalho Especifica a região associada à sua assinatura:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura de API de texto dos vários serviço. A região selecionada é a única região, que você pode usar para a tradução de texto quando você usa a chave de assinatura de vários serviços. Ele precisa ser da mesma região que você selecionou quando você se inscreveu para sua assinatura de vários serviço no portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, e `westus2`.

Sua chave de assinatura e o access token são segredos que devem ser escondidos do modo de exibição.

## <a name="profanity-handling"></a>Tratamento de vulgaridade
Normalmente, o serviço de tradução manterá profanação está presente na fonte. O grau de conteúdo ofensivo e o contexto que torna as palavras impróprias diferem de acordo com a cultura. Portanto, o grau de conteúdo ofensivo no idioma de destino pode ser aumentado ou reduzido.

Se você quiser impedir que o conteúdo ofensivo na tradução mesmo quando ele é no texto de origem, você pode usar o conteúdo ofensivo filtragem de opção para os métodos que dão suporte a ele. A opção permite que você escolha se deseja ver conteúdo ofensivo excluído ou marcadas com marcas apropriadas, ou se você deseja permitir que o conteúdo ofensivo no destino. Os valores aceitos `ProfanityAction` estão `NoAction` (padrão), `Marked`, e `Deleted`.


|ProfanityAction    |Action |Fonte de exemplo (japonês)  |Tradução de exemplo (em inglês)  |
|:--|:--|:--|:--|
|NoAction   |Padrão. O mesmo que não configurar a opção. Vulgaridades passarão da origem para o destino.        |彼はジャッカスです。     |Ele é um babaca.   |
|Marked     |Palavras impróprias ficará cercadas por marcas XML \<profanação > e \</profanity >.       |彼はジャッカスです。 |Ele é um \<profanidade > jackass\</profanity >.  |
|Deleted    |Palavras vulgares serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo conteúdo da tradução
Quando você converter o conteúdo com marcas, como o HTML (`contentType=text/html`), às vezes é útil excluir o conteúdo específico da tradução. É possível usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer no idioma original. No exemplo a seguir, o conteúdo na primeira `div` elemento não será convertido, mas o conteúdo no segundo `div` elemento será convertido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de caracteres de texto de um idioma para outro.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor retornado:** Uma cadeia de caracteres que representa o texto traduzido.

Se você usou anteriormente `AddTranslation` ou `AddTranslationArray` inserir uma tradução com uma classificação de 5 ou superior para a mesma sentença de origem, `Translate` retorna apenas a primeira escolha que está disponível para seu sistema. "Sentença de mesma origem" significa exatamente o mesmo (100% correspondente), exceto para o uso de maiusculas, espaço em branco, valores de marca e pontuação no final de uma sentença. Se nenhuma classificação for armazenada com uma classificação de 5 ou superior, o resultado retornado será a tradução automática pelo Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO    |Tipo de parâmetro|tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o texto a traduzir. O texto não pode conter mais de 10.000 caracteres.|query|cadeia de caracteres|
|from|(vazio)   |Opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo convertido. Por exemplo, en para inglês.|query|cadeia de caracteres|
|para|(vazio) |Obrigatório. Uma cadeia de caracteres que representa o código do idioma para traduzir o texto em.|query|cadeia de caracteres|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Formatos com suporte são `text/plain` (padrão) e `text/html`. Qualquer elemento HTML precisa ser bem formados e completas de elementos.|query|cadeia de caracteres|
|category|(vazio)   |Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. O padrão é `general`.|query|cadeia de caracteres|
|Autorização|(vazio)  |É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)  |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|


### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera as traduções para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Aqui está o formato do corpo da solicitação:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Esses elementos estão em `TranslateArrayRequest`:


* `AppId`: Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `From`: Opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo convertido. Se esse campo for deixado vazio, a resposta incluirá o resultado da detecção automática de idioma.
* `Options`: Opcional. Um `Options` objeto que contém os valores a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - `Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. O padrão é `general`.
    - `ContentType`: O formato do texto que está sendo traduzido. Os formatos com suporte são `text/plain` (padrão), `text/xml`, e `text/html`. Qualquer elemento HTML precisa ser bem formados e completas de elementos.
    - `ProfanityAction`: Especifica como profanities são tratadas, conforme explicado anteriormente. Os valores aceitos são `NoAction` (padrão), `Marked`, e `Deleted`.
    - `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será retornado na resposta.
    - `Uri`: Filtre os resultados por esse URI. Padrão: `all`.
    - `User`: Filtre os resultados por esse usuário. Padrão: `all`.
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos da matriz é 2.000.
* `To`: Obrigatório. Uma cadeia de caracteres que representa o código do idioma para traduzir o texto em.

Você pode omitir elementos opcionais. Direcionam de elementos que são filhos do `TranslateArrayRequest` devem ser listados em ordem alfabética.

O `TranslateArray` método aceita `application/xml` ou `text/xml` para `Content-Type`.

**Valor retornado:** Uma matriz `TranslateArrayResponse`. Cada `TranslateArrayResponse` tenha estes elementos:

* `Error`: Indica um erro se um ocorrer. Caso contrário, definido como nulo.
* `OriginalSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase do texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Retorna o mesmo conteúdo que a solicitação.

Aqui está o formato do corpo da resposta:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma resposta bem-sucedida inclui uma matriz de `TranslateArrayResponse` matrizes no formato descrito anteriormente.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)  |É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP   |Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada. Os erros comuns incluem: <ul><li>Elemento de matriz não pode ficar vazio.</li><li>Categoria inválida.</li><li>De idioma é inválido.</li><li>A linguagem é inválida.</li><li>A solicitação contém muitos elementos.</li><li>Não há suporte para o idioma de From.</li><li>Não há suporte para a linguagem.</li><li>Traduzir solicitação tem muitos dados.</li><li>HTML não está em um formato correto.</li><li>Número excessivo de cadeias de caracteres foram passadas na solicitação traduzir.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera a nomes amigáveis para os idiomas passado como o parâmetro `languageCodes`, localizada no passado `locale` idioma.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo da solicitação inclui uma matriz de cadeia de caracteres que representa os códigos de idioma 639-1 ISO para o qual recuperar os nomes amigáveis. Veja um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor retornado:** Uma matriz de cadeia de caracteres que contém os nomes de idiomas com suporte no serviço de tradução, localizado para o idioma solicitado.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os nomes de idiomas com suporte pelo serviço de tradução, localizado para o idioma solicitado.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|localidade|(vazio) |Obrigatório. Uma cadeia de caracteres que representa um dos seguintes, usado para localizar os nomes de idiomas: <ul><li>A combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiusculas ISO 3166. <li>Um código de cultura em minúsculas ISO 639 por si só.|query|cadeia de caracteres|
|Autorização|(vazio)  |É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)  |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista dos códigos de idioma que representam os idiomas com suporte pelo serviço de tradução.  `Translate` e `TranslateArray` podem traduzir entre qualquer um desses idiomas.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor retornado:** Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte no serviço de tradução.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte no serviço de tradução.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|Autorização|(vazio)  |É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera os idiomas disponíveis para a sintetização de voz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor retornado:** Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte para síntese de fala no serviço de tradução.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte para síntese de fala no serviço de tradução.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Notas de implementação
Retorna um fluxo WAV ou MP3 do passados em texto falado no idioma desejado.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor retornado:** Um fluxo WAV ou MP3 do passados em texto falado no idioma desejado.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

binary

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres que contém um ou mais sentenças a ser falado para o fluxo, no idioma especificado. O texto não deve exceder a 2.000 caracteres.|query|cadeia de caracteres|
|language|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o código de idioma com suporte do idioma no qual falar o texto. O código deve ser um dos códigos retornados pelo método `GetLanguagesForSpeak`.|query|cadeia de caracteres|
|format|(vazio)|Opcional. Uma cadeia de caracteres que especifica a ID do tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor padrão é `audio/wav`.|query|cadeia de caracteres|
|options|(vazio)    |Opcional. Uma cadeia de caracteres que especifica as propriedades da voz sintetizada:<ul><li>`MaxQuality` e `MinSize` especificar a qualidade do sinal de áudio. `MaxQuality` fornece a melhor qualidade. `MinSize` fornece o menor tamanho de arquivo. O padrão é `MinSize`.</li><li>`female` e `male` especificar o gênero desejado da voz. O padrão é `female`. Use a barra vertical (<code>\|</code>) para incluir várias opções. Por exemplo,  `MaxQuality|Male`.</li></li></ul>  |query|cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)  |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Identifica o idioma de uma seção de texto.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor retornado:** Uma cadeia de caracteres que contém um código de idioma de dois caracteres para o texto.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|text|(vazio)|Obrigatório. Uma cadeia de caracteres que contém o texto cujo idioma deve ser identificado. O texto não deve exceder 10.000 caracteres.|query|  cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key  |(vazio)    |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica os idiomas em uma matriz de cadeias de caracteres. Modo independente detecta o idioma de cada elemento da matriz individuais e retorna um resultado para cada linha da matriz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Aqui está o formato do corpo da solicitação:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode exceder 10.000 caracteres.

**Valor retornado:** Uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha na matriz de entrada.

Aqui está o formato do corpo da resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`DetectArray` foi bem-sucedida. Retorna uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha da matriz de entrada.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Observação de substituição:** Depois de 31 de janeiro de 2018, esse método não aceitará novos envios de frase. Você obterá uma mensagem de erro. Consulte o comunicado sobre alterações para o Framework de tradução colaborativa (CTF).

Adiciona uma tradução à memória de tradução.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

cadeia de caracteres

Tipo de conteúdo de resposta: aplicativo: xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|originalText|(vazio)|Obrigatório. Uma cadeia de caracteres que contém o texto a traduzir. O comprimento máximo da cadeia de caracteres é de 1.000 caracteres.|query|cadeia de caracteres|
|translatedText|(vazio) |Obrigatório. Uma cadeia de caracteres que contém o texto traduzido em idioma de destino. O comprimento máximo da cadeia de caracteres é 2.000 caracteres.|query|cadeia de caracteres|
|from|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o código de idioma do idioma do texto original. Por exemplo, en para inglês e de para alemão.|query|cadeia de caracteres|
|para|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.|query|cadeia de caracteres|
|classificação|(vazio) |Opcional. Um inteiro que representa a classificação de qualidade para a cadeia de caracteres. O valor está entre -10 e 10. O padrão é 1.|query|integer|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Os formatos com suporte são `text/plain` e `text/html`. Qualquer elemento HTML precisa ser bem formados e completas de elementos.    |query|cadeia de caracteres|
|category|(vazio)|Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. O padrão é `general`.|query|cadeia de caracteres|
|usuário|(vazio)|Obrigatório. Uma cadeia de caracteres que é usada para controlar o originador do envio.|query|cadeia de caracteres|
|uri|(vazio)|Opcional. Uma cadeia de caracteres que contém o local do conteúdo da tradução.|query|cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.  |cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410|`AddTranslation` não é mais suportada.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Observação de substituição:** Depois de 31 de janeiro de 2018, esse método não aceitará novos envios de frase. Você obterá uma mensagem de erro. Consulte o comunicado sobre alterações para o Framework de tradução colaborativa (CTF).

Adiciona uma matriz de traduções para a memória de tradução. Esse método é uma versão de matriz de `AddTranslation`.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Aqui está o formato do corpo da solicitação:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Esses elementos estão em `AddtranslationsRequest`:

* `AppId`: Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres que contém o código de idioma do idioma de origem. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `To`: Obrigatório. Uma cadeia de caracteres que contém o código de idioma do idioma de destino. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `Translations`: Obrigatório. Uma matriz de traduções para adicionar à memória de tradução. Cada tradução deve conter `OriginalText`, `TranslatedText`, e `Rating`. O tamanho máximo de cada `OriginalText` e `TranslatedText` é de 1.000 caracteres. O total de todos os `OriginalText` e `TranslatedText` elementos não podem exceder 10.000 caracteres. O número máximo de elementos de matriz é 100.
* `Options`: Obrigatório. Um conjunto de opções, incluindo `Category`, `ContentType`, `Uri`, e `User`. `User` é necessário. `Category`, `ContentType`, e `Uri` são opcionais. Os elementos especificado devem estar listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`AddTranslationArray` Método foi bem-sucedido. 

Depois de 31 de janeiro de 2018, envios de frase não será aceita. O serviço responderá com o código de erro 410.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410    |`AddTranslation` não é mais suportada.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Divide uma seção de texto em frases e retorna uma matriz que contém os comprimentos de cada frase.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor retornado:** Uma matriz de inteiros que representa os comprimentos das sentenças. O tamanho da matriz representa o número de frases. Os valores representam o tamanho de cada frase.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de inteiros que representa os comprimentos das sentenças. O tamanho da matriz representa o número de frases. Os valores representam o tamanho de cada frase.

integer

Tipo de conteúdo de resposta: application/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query| cadeia de caracteres|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o texto a ser dividido em sentenças. O tamanho máximo do texto é 10.000 caracteres.|query|cadeia de caracteres|
|language   |(vazio)    |Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto de entrada.|query|cadeia de caracteres|
|Autorização|(vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.   |cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)|É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500|Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma matriz de traduções para um dado par de idiomas do armazenamento e do mecanismo de MT. `GetTranslations` é diferente do `Translate` em que ele retorna todas as traduções disponíveis.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo da solicitação inclui opcional `TranslationOptions` objeto, que tem este formato:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

O `TranslateOptions` objeto contém os valores na lista a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.

* `Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. O padrão é `general`.
* `ContentType`: A única opção com suporte e o padrão é `text/plain`.
* `IncludeMultipleMTAlternatives`: Um sinalizador booliano para especificar se mais de uma alternativa deve ser retornada do mecanismo de MT. Os valores válidos são `true` e `false` (diferencia maiusculas de minúsculas). O padrão é `false`, que retorna apenas uma alternativa. Definir o sinalizador para `true` permite a criação de alternativas artificiais, totalmente integrado com o Framework de tradução colaborativa (CTF). O recurso de retornadas alternativas para as frases que não têm nenhuma tradução em CTF adicionando artificiais alternativas do permite que o *n*-lista recomendadas do decodificador.
    - Classificações. As classificações são aplicadas como este: 
         - A melhor tradução automática tem uma classificação de 5.
       - As alternativas de CTF refletem a autoridade do revisor. Eles variam de -10 a + 10.
       - Gerado automaticamente (*n*-melhor) alternativas de conversão têm uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas retornados pode ser tão alto quanto o valor especificado em `maxTranslations`, mas pode ser inferior.
    - Pares de idiomas. Essa funcionalidade não está disponível para conversões entre o chinês simplificado e chinês tradicional, em qualquer direção. Ele está disponível para todos os outros pares de idiomas com suporte pelo Microsoft Translator.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será retornado na resposta.
* `Uri`: Filtre os resultados por esse URI. Se nenhum valor for definido, o padrão é `all`.
* `User`: Filtre os resultados por esse usuário. Se nenhum valor for definido, o padrão é `all`.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor retornado:** Aqui está o formato da resposta:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Essa resposta inclui um `GetTranslationsResponse` elemento que contém os seguintes valores:

* `Translations`: Uma matriz de correspondências encontradas, armazenado no `TranslationMatch` objetos (descritos na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde ao primeiro, correspondências difusas em seguida.
* `From`: Se o método não especifica um `From` linguagem, esse valor será proveniente de detecção automática de idioma. Caso contrário, será especificado `From` idioma.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto consiste em um desses valores:

* `Error`: O código de erro, se ocorrer um erro para uma cadeia de caracteres de entrada específica. Caso contrário, esse campo está vazio.
* `MatchDegree`: Indica como o texto de entrada se aproxima o texto original encontrado no repositório. O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas. O valor retornou intervalos de 0 a 100, onde 0 não corresponde a nenhuma similaridade e 100 é uma correspondência exata de maiusculas e minúsculas.
* `MatchedOriginalText`: O texto original correspondente para esse resultado. Esse valor é retornado somente se o texto original correspondente for diferente do texto de entrada. Ele é usado para retornar o texto de origem de uma correspondência difusa. Esse valor não é retornado para resultados do Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Resultados da tradução automática tem uma classificação de 5. Traduções anonimamente fornecidas geralmente têm uma classificação de 1 a 4. As traduções fornecidas com autoridade geralmente terá uma classificação de 6 a 10.
* `Count`: O número de vezes que essa tradução com essa classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Um `GetTranslationsResponse` objeto no formato descrito anteriormente.

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|query|cadeia de caracteres|
|text|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o texto a traduzir. O tamanho máximo do texto é 10.000 caracteres.|query|cadeia de caracteres|
|from|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo convertido.|query|cadeia de caracteres|
|para |(vazio)    |Obrigatório. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.|query|cadeia de caracteres|
|maxTranslations|(vazio)|Obrigatório. Um inteiro que representa o número máximo de traduções para retornar.|query|integer|
|Autorização| (vazio)|É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cadeia de caracteres|  cabeçalho|
|Ocp-Apim-Subscription-Key|(vazio)  |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos com a data aproximada e a hora da solicitação e com a ID de solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera vários candidatos de tradução para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Aqui está o formato do corpo da solicitação:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` inclui estes elementos:

* `AppId`: Obrigatório. Se o `Authorization` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, inclua uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo convertido.
* `MaxTranslations`: Obrigatório. Um inteiro que representa o número máximo de traduções para retornar.
* `Options`: Opcional. Um `Options` objeto que contém os valores a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - `Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. O padrão é `general`.
    - `ContentType`: A única opção com suporte e o padrão é `text/plain`.
    - `IncludeMultipleMTAlternatives`: Um sinalizador booliano para especificar se mais de uma alternativa deve ser retornada do mecanismo de MT. Os valores válidos são `true` e `false` (diferencia maiusculas de minúsculas). O padrão é `false`, que retorna apenas uma alternativa. Definir o sinalizador para `true` habilita a geração de alternativas artificiais na conversão, totalmente integrado com o Framework de traduções colaborativa (CTF). O recurso de retornadas alternativas para as frases que não têm nenhum alternativas em CTF adicionando artificiais alternativas do permite que o *n*-lista recomendadas do decodificador.
        - Classificações as classificações são aplicadas como este:
          - A melhor tradução automática tem uma classificação de 5.
          - As alternativas de CTF refletem a autoridade do revisor. Eles variam de -10 a + 10.
          - Gerado automaticamente (*n*-melhor) alternativas de conversão têm uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas retornados pode ser tão alto quanto o valor especificado em `maxTranslations`, mas pode ser inferior.
        - Pares de idiomas. Essa funcionalidade não está disponível para conversões entre o chinês simplificado e chinês tradicional, em qualquer direção. Ele está disponível para todos os outros pares de idiomas com suporte pelo Microsoft Translator.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será retornado na resposta.
* `Uri`: Filtre os resultados por esse URI. Se nenhum valor for definido, o padrão é `all`.
* `User`: Filtre os resultados por esse usuário. Se nenhum valor for definido, o padrão é `all`.
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é dez.
* `To`: Obrigatório. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.

Você pode omitir elementos opcionais. Direcionam de elementos que são filhos do `GetTranslationsArrayRequest` devem ser listados em ordem alfabética.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor retornado:** Aqui está o formato da resposta:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Cada `GetTranslationsResponse` elemento contém estes valores:

* `Translations`: Uma matriz de correspondências encontradas, armazenado no `TranslationMatch` objetos (descritos na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde ao primeiro, correspondências difusas em seguida.
* `From`: Se o método não especifica um `From` linguagem, esse valor será proveniente de detecção automática de idioma. Caso contrário, será especificado `From` idioma.
* `State`: Estado do usuário para ajudar a correlacionar a solicitação e resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto contém os seguintes valores:
* `Error`: O código de erro, se ocorrer um erro para uma cadeia de caracteres de entrada específica. Caso contrário, esse campo está vazio.
* `MatchDegree`: Indica como o texto de entrada se aproxima o texto original encontrado no repositório. O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas. O valor retornou intervalos de 0 a 100, onde 0 não corresponde a nenhuma similaridade e 100 é uma correspondência exata de maiusculas e minúsculas.
* `MatchedOriginalText`: O texto original correspondente para esse resultado. Esse valor é retornado somente se o texto original correspondente for diferente do texto de entrada. Ele é usado para retornar o texto de origem de uma correspondência difusa. Esse valor não é retornado para resultados do Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Resultados da tradução automática tem uma classificação de 5. Traduções anonimamente fornecidas geralmente têm uma classificação de 1 a 4. As traduções fornecidas com autoridade geralmente têm uma classificação de 6 a 10.
* `Count`: O número de vezes que essa tradução com essa classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (status 200)

cadeia de caracteres

Tipo de conteúdo de resposta: application/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Valor|DESCRIÇÃO|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |É necessário se os dois os `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|cadeia de caracteres|
|Ocp-Apim-Subscription-Key|(vazio)  |É necessário se os dois os `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar para a API de tradução de texto v3](../migrate-to-v3.md)


