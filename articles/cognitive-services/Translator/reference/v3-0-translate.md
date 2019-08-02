---
title: Método de Tradução de API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Usar o método de Tradução de API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 2b08ddb4241a9af7aee31bb51d75dd82ff8255d2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839630"
---
# <a name="translator-text-api-30-translate"></a>API de Tradução de Texto 3.0: Translate

Traduz texto.

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>api-version</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Versão da API solicitada pelo cliente. O valor precisa ser <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>from</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o idioma do texto de entrada. Localize quais idiomas estão disponíveis para tradução, consultando <a href="./v3-0-languages.md">idiomas com suporte</a> usando o escopo <code>translation</code>. Se o parâmetro <code>from</code> não for especificado, a detecção automática de idioma será aplicada para determinar o idioma de origem. <br/><br/>Você deve usar o <code>from</code> parâmetro, em vez da detecção automática ao usar o <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dicionário dinâmico</a> recurso.</td>
  </tr>
  <tr>
    <td>para</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos <a href="./v3-0-languages.md">idiomas com suporte</a> incluídos no escopo <code>translation</code>. Por exemplo, use <code>to=de</code> para traduzir para alemão.<br/>É possível traduzir para vários idiomas simultaneamente, repetindo o parâmetro na cadeia de caracteres de consulta. Por exemplo, use <code>to=de&to=it</code> para traduzir para alemão e italiano.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Parâmetro opcional</em>.<br/>Define se o texto que está sendo traduzido é texto sem formatação ou texto HTML. Qualquer HTML precisa ser um elemento bem formado e completo. Os valores possíveis são: <code>plain</code> (padrão) ou <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Parâmetro opcional</em>.<br/>Uma cadeia de caracteres especificando a categoria (domínio) da tradução. Esse parâmetro é usado para obter traduções de um sistema personalizado compilado com <a href="../customization.md">Tradutor Personalizado</a>. Adicione a ID da categoria do seu conversor personalizado <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">detalhes do projeto</a> para esse parâmetro para usar seu implantado personalizada sistema. O valor padrão é: <code>general</code>.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica como os conteúdos ofensivos devem ser tratados nas traduções. Os valores possíveis são: <code>NoAction</code> (padrão), <code>Marked</code> ou <code>Deleted</code>. Para reconhecer maneiras de tratar conteúdo ofensivo, consulte <a href="#handle-profanity">Tratamento de conteúdo ofensivo</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica como os conteúdos ofensivos devem ser marcados nas traduções. Os valores possíveis são: <code>Asterisk</code> (padrão) ou <code>Tag</code>. Para reconhecer maneiras de tratar conteúdo ofensivo, consulte <a href="#handle-profanity">Tratamento de conteúdo ofensivo</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica se deve incluir a projeção de alinhamento do texto de origem para texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica se deve incluir limites de sentença para o texto de entrada e o texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica um idioma de fallback se o idioma do texto de entrada não pode ser identificado. A detecção automática de idioma é aplicada quando o parâmetro <code>from</code> é omitido. Se a detecção falhar, será assumido o idioma <code>suggestedFrom</code>.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o script do texto de entrada.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o script do texto traduzido.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica que o serviço tem permissão para fazer fallback para um sistema geral, quando um sistema personalizado não existir. Os valores possíveis são: <code>true</code> (padrão) ou <code>false</code>.<br/><br/><code>allowFallback=false</code> especifica que a tradução deve usar somente sistemas treinados para o <code>category</code> especificado pela solicitação. Se uma tradução do idioma X para a o idioma Y requer encadeamento por meio de um idioma dinâmico E, em seguida, todos os sistemas da cadeia (X -> E e E -> Y) precisarão ser personalizado e ter a mesma categoria. Se nenhum sistema for encontrado com a categoria específica, a solicitação retornará um código de 400 status. <code>allowFallback=true</code> especifica que o serviço tem permissão para fazer fallback para um sistema geral, quando um sistema personalizado não existir.
</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>Cabeçalhos de autenticação</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>Veja <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: <code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>O tamanho do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcional</em>.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. É possível omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta nomeado <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo da solicitação

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia nomeada `Text`, que representa a cadeia de caracteres a ser traduzida.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* Todo o texto incluído na solicitação não pode exceder 5.000 caracteres incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `detectedLanguage`: um objeto que descreve o idioma detectado por meio das seguintes propriedades:

      * `language`: uma cadeia de caracteres que representa o código do idioma detectado.

      * `score`: um valor flutuante indicando a confiança no resultado. A pontuação é entre zero e um, e uma pontuação baixa indica uma baixa confiança.

    A propriedade `detectedLanguage` somente está presente no objeto de resultado quando a detecção automática de idioma é solicitada.

  * `translations`: uma matriz de resultados de tradução. O tamanho da matriz corresponde ao número de idiomas de destino especificados por meio do parâmetro de consulta `to`. Cada elemento na matriz inclui:

    * `to`: uma cadeia de caracteres representando o código de idioma do idioma de destino.

    * `text`: uma cadeia de caracteres dando o texto traduzido.

    * `transliteration`: um objeto que fornece o texto traduzido no script especificado pelo parâmetro `toScript`.

      * `script`: uma cadeia de caracteres especificando o script de destino.   

      * `text`: uma cadeia de caracteres que fornece o texto traduzido no script de destino.

    O objeto `transliteration` não será incluído se a transliteração não ocorrer.

    * `alignment`: um objeto com uma única propriedade de cadeia de caracteres nomeada `proj`, que mapeia o texto de entrada para o texto traduzido. As informações de alinhamento serão fornecidas somente quando o parâmetro de solicitação `includeAlignment` for `true`. O alinhamento é retornado como um valor de cadeia de caracteres do seguinte formato: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Os dois-pontos separam o índice inicial e final, o traço separa os idiomas e o espaço separa as palavras. Uma palavra pode alinhar com zero, uma, ou várias palavras em outro idioma e, as palavras alinhadas podem ser não contíguas. Quando nenhuma informação de alinhamento estiver disponível, o elemento de alinhamento ficará vazio. Consulte [Obter informações de alinhamento](#obtain-alignment-information) para um exemplo e restrições.

    * `sentLen`: um objeto retornando limites de sentença nos textos de entrada e saída.

      * `srcSentLen`: uma matriz de inteiros representando os comprimentos das sentenças no texto de entrada. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

      * `transSentLen`:  uma matriz de inteiros representando os comprimentos das sentenças no texto traduzido. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

    Limites de sentença serão incluídos somente quando o parâmetro de solicitação `includeSentenceLength` for `true`.

  * `sourceText`: um objeto com uma única propriedade de cadeia de caracteres nomeada `text`, que fornece o texto de entrada no script padrão do idioma de origem. `sourceText` a propriedade está presente apenas quando a entrada é expressa em um script que não é o script usual para o idioma. Por exemplo, se a entrada fosse árabe gravada em script de latim, `sourceText.text` seria o mesmo texto árabe convertido em script de árabe.

Exemplos de respostas JSON são fornecidos na seção [exemplos](#examples).

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
    <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar a solicitação. É usado para fins de solução de problemas.</td>
  </tr>
  <tr>
    <td>Sistema-MT-X</td>
    <td>Especifica o tipo de sistema que foi usado para tradução para cada idioma 'para' solicitado para conversão. O valor é uma lista separada por vírgulas de cadeia de caracteres. Cada cadeia de caracteres indica um tipo:<br/><ul><li>Personalizado - a solicitação inclui um sistema personalizado e, no mínimo, um sistema personalizado foi usado durante a tradução.</li><li>Equipe - todas as outras solicitações</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação. 

<table width="100%">
  <th width="20%">Código de status</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>200</td>
    <td>Sucesso.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou é inválido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A solicitação não pôde ser autenticada. Verifique se as credenciais estão especificadas e são válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A solicitação não foi autorizada. Verifique os detalhes da mensagem de erro. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram consumidas.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A solicitação não pôde ser atendida porque um recurso está ausente. Verifique os detalhes da mensagem de erro. Ao usar um <code>category</code> personalizado, isso geralmente indica que o sistema de conversão personalizada ainda não está disponível para atender às solicitações. A solicitação deve ser repetida após um período de espera (por exemplo, 1 minuto).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Erro inesperado. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta <code>X-RequestId</code> e identificador do cliente do cabeçalho de solicitação <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente não disponível. Tente novamente a solicitação. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta <code>X-RequestId</code> e identificador do cliente do cabeçalho de solicitação <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns que podem ser encontrados na [página de referência da API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

### <a name="translate-a-single-input"></a>Traduzir uma única entrada

Este exemplo mostra como traduzir uma única sentença de inglês para chinês simplificado.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A matriz `translations` inclui um elemento, que fornece a tradução da única parte do texto na entrada.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Traduzir uma entrada única com detecção automática de idioma

Este exemplo mostra como traduzir uma única sentença de inglês para chinês simplificado. A solicitação não especifica o idioma de entrada. Em vez disso, é usada a detecção automática do idioma de origem.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
A resposta é semelhante à resposta do exemplo anterior. Como a detecção automática de idioma foi solicitada, a resposta também incluirá informações sobre o idioma detectado para o texto de entrada. 

### <a name="translate-with-transliteration"></a>Traduzir com transliteração

Vamos estender o exemplo anterior, adicionando transliteração. A solicitação a seguir pede uma tradução de chinês gravada em script de latim.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

O resultado da tradução agora inclui uma propriedade `transliteration` que fornece o texto traduzido usando caracteres de latim.

### <a name="translate-multiple-pieces-of-text"></a>Traduzir várias partes de texto

Traduzir várias cadeias de caracteres de uma vez é simplesmente uma questão de especificar uma matriz de cadeia de caracteres no corpo da solicitação.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Traduzir para vários idiomas

Este exemplo mostra como traduzir a mesma entrada para vários idiomas em uma solicitação.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Tratar conteúdo ofensivo

Normalmente, o serviço do Translator reterá a linguagem vulgar presente no texto de origem da tradução. O grau de vulgaridade e o contexto que torna palavras vulgar diferem entre as culturas, assim, o grau de vulgaridade no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar conteúdo ofensivo na tradução, independentemente da presença de linguagem vulgar no texto de origem, poderá usar a opção de filtragem de linguagem vulgar. A opção permite escolher se você quer ver o conteúdo ofensivo excluído, se quer marcar conteúdos ofensivos com marcas apropriadas (dando a opção de adicionar seu próprio pós-processamento), ou se não quer executar nenhuma ação. Os valores aceitos de `ProfanityAction` são `Deleted`, `Marked` e `NoAction` (padrão).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Action</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Esse é o comportamento padrão. O conteúdo ofensivo passará da origem para o destino.<br/><br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um babaca.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Palavras ofensivas serão removidas da saída sem substituição.<br/><br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Palavras ofensivas são substituídas por um marcador na saída. O marcador depende do parâmetro <code>ProfanityMarker</code>.<br/><br/>
Para <code>ProfanityMarker=Asterisk</code>, palavras ofensivas são substituídas por <code>***</code>:<br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um \*\*\*.<br/><br/>
Para <code>ProfanityMarker=Tag</code>, palavras ofensivas são circundadas por marcas XML &lt;profanity&gt; e &lt;/profanity&gt;:<br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um &lt;profanity&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Por exemplo:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

Isso retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Compara com:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

Essa última solicitação retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduzir conteúdo com marcação e decidir o que é traduzido

É comum traduzir conteúdo que inclui marcação, como conteúdo de uma página HTML ou conteúdo de um documento XML. Inclua o parâmetro de consulta `textType=html` ao traduzir conteúdo com marcas. Além disso, às vezes é útil excluir conteúdo específico da tradução. É possível usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer no idioma original. No exemplo a seguir, o conteúdo dentro do primeiro elemento `div` não será traduzido, enquanto o conteúdo do segundo elemento `div` será traduzido.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aqui, está uma solicitação de exemplo para ilustrar.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Obter informações de alinhamento

Para receber informações de alinhamento, especifique `includeAlignment=true` na cadeia de caracteres de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

As informações de alinhamento iniciam com `0:2-0:1`, significando que os três primeiros caracteres no texto de origem (`The`) são mapeados para os dois primeiros caracteres no texto traduzido (`La`).

Observe as seguintes restrições:

* O alinhamento é retornado apenas para um subconjunto dos pares de idiomas:
  - do inglês para qualquer outro idioma;
  - de qualquer outro idioma para o inglês, exceto chinês simplificado, chinês tradicional e letão para inglês;
  - do japonês para o coreano ou do coreano para o japonês.
* Você não receberá alinhamento se a sentença for uma tradução predefinida. Exemplo de uma tradução predefinida é "Isso é um teste", "eu te amo", e outras frases de alta frequência.

### <a name="obtain-sentence-boundaries"></a>Obter limites de sentença

Para receber informações sobre o comprimento da sentença no texto de origem e no texto traduzido, especifique `includeSentenceLength=true` na cadeia de caracteres de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Traduzir com dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico é seguro somente para substantivos compostos como nomes próprios e nomes de produtos.

A marcação para fornecer usa a seguinte sintaxe.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Por exemplo, considere a frase em inglês "A palavra wordomatic é uma entrada de dicionário." Para preservar a palavra _wordomatic_ na tradução, envie a solicitação:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

O resultado é:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Esse recurso funciona da mesma maneira com `textType=text` ou com `textType=html`. O recurso deve ser usado com moderação. A maneira apropriada e muito melhor de personalizar a tradução é usando o Tradutor Personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. [Saiba mais sobre o Tradutor Personalizado](../customization.md).
 





