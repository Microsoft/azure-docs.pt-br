---
title: API de texto do tradutor V2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do tradutor V2.0.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: 961dd277034db7e5406e671233f26b4fd8fe5f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467149"
---
# <a name="translator-text-api-v20"></a>API de Tradução de Texto v2.0

> [!IMPORTANT]
> Esta versão da API de Tradução de Texto foi preterida. [Exiba a documentação para v3 da API de Tradução de Texto](v3-0-reference.md).

A Translator Text API V2 pode ser perfeitamente integrada em seus aplicativos, sites, ferramentas ou outras soluções para fornecer experiências de vários usuários. Aproveitando os padrões do setor, pode ser usado em qualquer plataforma de hardware e com qualquer sistema operacional para executar a tradução de idioma e outras operações relacionadas a idioma, como detecção de idioma de texto ou conversão de texto em fala. Clique aqui para obter mais informações sobre a API do Microsoft Translator.

## <a name="getting-started"></a>Introdução
Para acessar a API de texto do tradutor, você precisará [se inscrever no Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autorização
Todas as chamadas para a API de texto do tradutor exigem uma chave de assinatura para autenticar. A API é compatível com dois modos de autenticação:

* Usando um token de acesso. Use a chave de assinatura mencionada na **etapa** 9 para gerar um token de acesso fazendo uma solicitação POST para o serviço de autorização. Veja a documentação do serviço de token para obter detalhes. Passe o token de acesso para o serviço do Tradutor usando o cabeçalho de Autorização ou o parâmetro de consulta access_token. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue usando o mesmo token de acesso para solicitações repetidas dentro desses 10 minutos.

* Usando uma chave de assinatura diretamente. Passe sua chave de assinatura como um valor no cabeçalho `Ocp-Apim-Subscription-Key` incluído com a sua solicitação à API do Tradutor. Nesse modo, você não precisa chamar o serviço de token de autenticação para gerar um token de acesso.

Considere a sua chave de assinatura e o token de acesso como segredos que devem ficar ocultos da exibição.

## <a name="profanity-handling"></a>Tratamento de vulgaridade
Normalmente o serviço de tradução reterá a linguagem vulgar presente no texto de origem da tradução. O grau de vulgaridade e o contexto que torna palavras vulgar diferem entre as culturas, assim, o grau de vulgaridade no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar obter linguagem vulgar na tradução, independentemente da presença de linguagem vulgar no texto de origem, poderá usar a opção de filtragem de linguagem vulgar para métodos compatíveis com isso. A opção permite que você escolha se deseja ver a linguagem vulgar excluída ou marcada com marcas apropriadas, ou se não deseja que nenhuma ação seja realizada. Os valores aceitos de `ProfanityAction` são `NoAction` (padrão), Marcado e `Deleted`.


|ProfanityAction    |Ação |Origem de exemplo (japonês)  |Tradução de exemplo (inglês)  |
|:--|:--|:--|:--|
|NoAction   |Padrão. O mesmo que não configurar a opção. Vulgaridades passarão da origem para o destino.        |彼はジャッカスです。     |Ele é um babaca.   |
|Marked     |Palavras impróprias ficará cercadas por marcas XML \<profanação > e \</profanity >.       |彼はジャッカスです。 |Ele é um \<profanidade > jackass\</profanity >.  |
|Deleted    |Palavras vulgares serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo conteúdo da tradução
Ao traduzir conteúdo com marcas como HTML (`contentType=text/html`), às vezes é útil excluir conteúdo específico da tradução. Você pode usar o atributo `class=notranslate` para especificar o conteúdo deve permanecer no idioma original. No exemplo a seguir, o conteúdo dentro do primeiro elemento `div` não será traduzido, enquanto o conteúdo do segundo elemento `div` será traduzido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de caracteres de texto de um idioma para outro.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor retornado:** Uma cadeia de caracteres representando o texto traduzido.

Se você tiver usado `AddTranslation` ou `AddTranslationArray` anteriormente para inserir uma tradução com uma classificação de 5 ou mais para a mesma frase de origem, `Translate` retornará apenas a opção superior que está disponível para seu sistema. A "frase de mesma origem" significa exatamente igual (correspondência de 100%), com exceção de maiúsculas e minúsculas, espaço em branco, valores de marcação e pontuação no fim de uma frase. Se nenhuma classificação for armazenada com uma classificação de 5 ou mais, o resultado retornado será a tradução automática feita pelo Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)

string

Tipo de Conteúdo da Resposta: aplicativo/xml 

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO    |Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Obrigatório. Se o cabeçalho Autorização ou Ocp-Apim-Subscription-Key for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo "Bearer" + " " + "access_token".|query|string|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho do texto não deve exceder dez mil caracteres.|query|string|
|de|(vazio)   |Opcional. Uma cadeia de caracteres representando o código de idioma do texto da tradução. Por exemplo, en para inglês.|query|string|
|para|(vazio) |Obrigatório. Uma cadeia de caracteres representando o código de idioma para o qual traduzir o texto.|query|string|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Os formatos compatíveis são text/plain (padrão) e text/html. Qualquer HTML precisa ser um elemento bem formado e completo.|query|string|
|category|(vazio)   |Opcional. Uma cadeia de caracteres contendo a categoria (domínio) da tradução. Usa geral como padrão.|query|string|
|Autorização|(vazio)  |Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + " " + "access_token".|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Obrigatório se o campo de appid ou cabeçalho de Autorização não for especificado.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Use o método `TranslateArray` para recuperar traduções para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

O formato do corpo da solicitação deve ser o seguinte:

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

Elementos dentro de `TranslateArrayRequest` são:


* `appid`: Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.
* `from`: Opcional. Uma cadeia de caracteres representando o código de idioma do qual traduzir o texto. Se deixados em branco, a resposta incluirá o resultado da detecção automática de idioma.
* `options`: Opcional. Um objeto `Options` que contém os valores listados abaixo. Eles são todos opcionais e usam como padrão as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - `Category`: Uma cadeia de caracteres contendo a categoria (domínio) da tradução. Usa `general` como padrão.
    - `ContentType`: O formato do texto que está sendo traduzido. Os formatos compatíveis são `text/plain` (padrão), `text/xml` e `text/html`. Qualquer HTML precisa ser um elemento bem formado e completo.
    - `ProfanityAction`: Especifica como vulgaridades serão tratadas, conforme explicado acima. Valores aceitos de `ProfanityAction` são `NoAction` (padrão), `Marked` e `Deleted`.
    - `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Os mesmos conteúdos serão retornados na resposta.
    - `Uri`: Filtre os resultados por esse URI. Padrão: `all`.
    - `User`: Filtre os resultados por esse usuário. Padrão: `all`.
* `texts`: Obrigatório. Uma matriz contendo os textos para tradução. Todas as cadeias de caracteres devem ser do mesmo idioma. O total de todos os textos a serem convertidos não deve exceder dez mil caracteres. O número máximo de elementos de matriz é dois mil.
* `to`: Obrigatório. Uma cadeia de caracteres representando o código de idioma para o qual traduzir o texto.

Os elementos opcionais podem ser omitidos. Elementos que são filhos diretos de TranslateArrayRequest devem ser listados em ordem alfabética.

O método TranslateArray aceita `application/xml` ou `text/xml` para `Content-Type`.

**Valor retornado:** Uma matriz `TranslateArrayResponse`. Cada `TranslateArrayResponse` tem os seguintes elementos:

* `Error`: Indicará um erro, se algum tiver ocorrido. Caso contrário, definido como nulo.
* `OriginalSentenceLengths`: Uma matriz de inteiros indicando o tamanho de cada frase no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros indicando o tamanho de cada frase no texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Retorna o mesmo conteúdo que o da solicitação.

O formato do corpo da resposta é o seguinte.

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

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Uma resposta bem-sucedida contém uma matriz de `TranslateArrayResponse` no formato descrito acima.

string

Tipo de Conteúdo da Resposta: aplicativo/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)) |Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + " " + "access_token".|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo de appid ou cabeçalho de Autorização não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP   |Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada. Os erros comuns incluem: <ul><li>O elemento Array não pode estar vazio</li><li>Categoria inválida</li><li>O idioma de Origem é inválido</li><li>O idioma de Destino é inválido</li><li>A solicitação contém elementos demais</li><li>Não há suporte para o idioma de Origem</li><li>Não há suporte para o idioma de Destino</li><li>A Solicitação de Tradução tem dados demais</li><li>O HTML não está no formato correto</li><li>Foram passadas cadeia de caracteres demais na Solicitação de Tradução</li></ul>|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera nomes amigáveis para os idiomas passados como o parâmetro `languageCodes` e localizados usando o idioma da localidade passado.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo da solicitação inclui uma matriz de cadeia de caracteres que representa os códigos de idioma ISO 639-1 para os quais recuperar os nomes amigáveis. Por exemplo: 

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor retornado:** Uma matriz de cadeia de caracteres contendo nomes de idiomas compatíveis com o Serviço de Tradutor, localizados no idioma solicitado.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Uma matriz de cadeia de caracteres contendo nomes de idiomas compatíveis com o Serviço de Tradutor, localizados no idioma solicitado.

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|localidade|(vazio) |Obrigatório. Uma cadeia de caracteres representando uma combinação de um código de cultura de duas letras minúsculas ISO 639 associados a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 para localizar os nomes de idioma ou um código de cultura em letras minúsculas ISO 639 sozinho.|query|string|
|Autorização|(vazio)  |Obrigatório se o campo aapid ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Obrigatório se o campo aapid ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtenha uma lista de códigos de idioma representando os idiomas compatíveis com o Serviço de Tradução.  `Translate` e `TranslateArray` podem traduzir entre qualquer um desses idiomas.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor retornado:** Uma matriz de cadeia de caracteres contendo os códigos de idioma compatíveis com os Serviços de Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Uma matriz de cadeia de caracteres contendo os códigos de idioma compatíveis com os Serviços de Tradutor.

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)  |Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera os idiomas disponíveis para a sintetização de voz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor retornado:** Uma matriz de cadeia de caracteres contendo os códigos de idioma compatíveis com síntese de fala no Serviço de Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Uma matriz de cadeia de caracteres contendo os códigos de idioma compatíveis com síntese de fala no Serviço de Tradutor.

string

Tipo de Conteúdo da Resposta: aplicativo/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Notas de implementação
Retorna um fluxo de wave ou mp3 do texto passado que está sendo falado no idioma desejado.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor retornado:** Um fluxo de wave ou mp3 do texto passado que está sendo falado no idioma desejado.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)

binary

Tipo de Conteúdo da Resposta: aplicativo/xml 

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres contendo uma ou mais frases do idioma especificado a ser falada para o fluxo de wave. O tamanho do texto para falar não deve exceder dois mil caracteres.|query|string|
|Linguagem|(vazio)   |Obrigatório. Uma cadeia de caracteres representando o código de idioma compatível no qual falar o texto. O código deve estar presente na lista de códigos retornados do método `GetLanguagesForSpeak`.|query|string|
|formato|(vazio)|Opcional. Uma cadeia de caracteres especificando a ID do tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor padrão é `audio/wav`.|query|string|
|options|(vazio)    |<ul><li>Opcional. Uma cadeia de caracteres especificando propriedades da fala sintetizada:<li>`MaxQuality` e `MinSize` estão disponíveis para especificar a qualidade dos sinais de áudio. Com `MaxQuality`, você pode obter as vozes com a qualidade mais alta e, com `MinSize`, você pode obter as vozes com o menor tamanho. O padrão é `MinSize`.</li><li>`female` e `male` estão disponíveis para especificar o gênero desejado da voz. O padrão é `female`. Use a barra vertical <code>\|</code> para incluir várias opções. Por exemplo `MaxQuality|Male`.</li></li></ul> |query|string|
|Autorização|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Use o método `Detect` para identificar o idioma de uma parte selecionada do texto.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor retornado:** Uma cadeia de caracteres contendo um código de Idioma de dois caracteres para o texto especificado. .

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)

string

Tipo de Conteúdo da Resposta: aplicativo/xml

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|text|(vazio)|Obrigatório. Uma cadeia de caracteres contendo algum texto cujo idioma deve ser identificado. O tamanho do texto não deve exceder dez mil caracteres.|query| string|
|Autorização|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key  |(vazio)    |Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Notas de implementação
Use o método `DetectArray` para identificar o idioma de uma matriz de cadeia de caracteres de uma só vez. Executa uma detecção independente de cada elemento da matriz individual e retorna um resultado para cada linha da matriz.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

O formato do corpo da solicitação deve ser o seguinte.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O tamanho do texto não deve exceder dez mil caracteres.

**Valor retornado:** Uma matriz de cadeia de caracteres contendo códigos de Idioma de dois caracteres para cada linha da matriz de entrada.

O formato do corpo da resposta é o seguinte.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
DetectArray foi bem-sucedida. Retorna uma matriz de cadeia de caracteres contendo códigos de Idioma de dois caracteres para cada linha da matriz de entrada.

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho de Autorização não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **OBSERVAÇÃO DE SUBSTITUIÇÃO:** Após 31 de janeiro de 2018, esse método não aceitará novos envios de frase e você receberá uma mensagem de erro. Consulte este comunicado sobre alterações às Funções de Tradução Colaborativa.

Adiciona uma tradução à memória de tradução.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)

string

Tipo de Conteúdo da Resposta: aplicativo: xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(vazio)|Obrigatório. Uma cadeia de caracteres contendo o texto a traduzir. A cadeia de caracteres tem um tamanho máximo de mil caracteres.|query|string|
|translatedText|(vazio) |Obrigatório. Uma cadeia de caracteres contendo o texto traduzido no idioma de destino. A cadeia de caracteres tem um tamanho máximo de dois mil caracteres.|query|string|
|de|(vazio)   |Obrigatório. Uma cadeia de caracteres representando o código de idioma do texto da tradução. en = inglês, de = alemão, etc...|query|string|
|para|(vazio)|Obrigatório. Uma cadeia de caracteres representando o código de idioma para o qual traduzir o texto.|query|string|
|classificação|(vazio) |Opcional. Um inteiro representando a classificação de qualidade para essa cadeia de caracteres. Valor entre -10 e 10. O valor padrão é 1.|query|inteiro|
|contentType|(vazio)    |Opcional. O formato do texto que está sendo traduzido. Os formatos compatíveis são "text/plain" e "text/html". Qualquer HTML precisa ser um elemento bem formado e completo.   |query|string|
|category|(vazio)|Opcional. Uma cadeia de caracteres contendo a categoria (domínio) da tradução. Usa geral como padrão.|query|string|
|usuário|(vazio)|Obrigatório. Uma cadeia de caracteres usada para rastrear o originador do envio.|query|string|
|uri|(vazio)|Opcional. Uma cadeia de caracteres contendo o local do conteúdo dessa tradução.|query|string|
|Autorização|(vazio)|Obrigatório se o campo aapid ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.    |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|410|Não há mais suporte para AddTranslation.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **OBSERVAÇÃO DE SUBSTITUIÇÃO:** Após 31 de janeiro de 2018, esse método não aceitará novos envios de frase e você receberá uma mensagem de erro. Consulte este comunicado sobre alterações às Funções de Tradução Colaborativa.

Adiciona uma matriz de traduções para adicionar memória de tradução. Esta é uma versão de matriz de `AddTranslation`.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

O formato do corpo da solicitação é o seguinte.

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

Elementos dentro do elemento AddtranslationsRequest são:

* `AppId`: Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres contendo o código de idioma referente ao idioma de origem. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `To`: Obrigatório. Uma cadeia de caracteres contendo o código de idioma referente ao idioma de destino. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `Translations`: Obrigatório. Uma matriz de traduções para adicionar à memória de tradução. Cada tradução deve conter: originalText, translatedText e rating. O tamanho de cada originalText e translatedText é limitado a mil caracteres. O total de todos os originalText(s) e translatedText(s) não deve exceder dez mil caracteres. O número máximo de elementos de matriz é 100.
* `Options`: Obrigatório. Um conjunto de opções, incluindo Category, ContentType, Uri e User. O usuário é obrigatório. Category, ContentType e Uri são opcionais. Os elementos especificado devem estar listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
O método AddTranslationArray foi bem-sucedido. Após 31 de janeiro de 2018, envios de frase não serão aceitos. O serviço responderá com o código de erro 410.

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + " " + "access_token".|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo de appid ou cabeçalho de Autorização não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|410    |Não há mais suporte para AddTranslation.|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Divide um trecho de texto em frases e retorna uma matriz contendo os tamanhos em cada frase.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor retornado:** Uma matriz de inteiros representando os tamanhos das frases. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Uma matriz de inteiros representando os tamanhos das frases. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença.

inteiro

Tipo de Conteúdo da Resposta: aplicativo/xml 

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se o cabeçalho Autorização ou Ocp-Apim-Subscription-Key for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo "Bearer" + " " + "access_token".|query| string|
|text|(vazio)   |Obrigatório. Uma cadeia de caracteres representando o texto a ser dividido em frases. O tamanho do texto não deve exceder dez mil caracteres.|query|string|
|Linguagem   |(vazio)    |Obrigatório. Uma cadeia de caracteres representando o código de idioma do texto de entrada.|query|string|
|Autorização|(vazio)|Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + " " + "access_token".    |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o campo de appid ou cabeçalho de Autorização não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas|
|500|Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta X-MS-Trans-Info.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma matriz de traduções para um dado par de idiomas do armazenamento e do mecanismo de MT. GetTranslations difere de Traduzir uma vez que retorna todas as traduções disponíveis.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo da solicitação inclui o objeto TranslationOptions opcional, que tem o formato a seguir.

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

O objeto `TranslateOptions` contém os valores listados abaixo. Eles são todos opcionais e usam como padrão as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.

* `Category`: Uma cadeia de caracteres contendo a categoria (domínio) da tradução. Usa geral como padrão.
* `ContentType`: A única opção compatível é "text/plain", que é a opção padrão.
* `IncludeMultipleMTAlternatives`: sinalizador booliano para determinar se mais de uma alternativa deve ser retornada do mecanismo de MT. Os valores válidos são true e false (diferencia maiúsculas de minúsculas). O padrão é false e inclui somente uma alternativa. Definir o sinalizador como true permite gerar alternativas artificiais na tradução, totalmente integradas com CTF (estrutura de traduções colaborativas). O recurso permite retornar frases alternativas que não têm nenhuma alternativa na CTF adicionando alternativas artificiais da lista de n melhores do decodificador.
    - Classificações As classificações são aplicadas da seguinte maneira: 1) A melhor tradução automática tem uma classificação de 5. 2) As alternativas da CTF refletem a autoridade do revisor, de -10 a +10. 3) As alternativas de tradução (n melhores) geradas automaticamente têm uma classificação 0 e um grau de correspondência 100.
    - Número de alternativas O número de alternativas retornadas é até maxTranslations, mas pode ser menor.
    - Pares de idiomas Essa funcionalidade não está disponível para traduções entre chinês simplificado e tradicional em ambos os sentidos. Está disponível para todos os outros pares de idiomas compatíveis com o Microsoft Translator.
* `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Os mesmos conteúdos serão retornados na resposta.
* `Uri`: Filtre os resultados por esse URI. Se nenhum valor estiver definido, o padrão será todos.
* `User`: Filtre os resultados por esse usuário. Se nenhum valor estiver definido, o padrão será todos.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor retornado:** O formato do corpo da resposta é como mostrado a seguir.

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

Isso inclui um elemento `GetTranslationsResponse` contendo os seguintes valores:

* `Translations`: Uma matriz de correspondências encontradas, armazenadas em objetos TranslationMatch (veja abaixo). As traduções podem incluir pequenas variações do texto original (correspondência difusa). As traduções serão classificadas: Correspondências de 100% primeiro, correspondências difusas abaixo.
* `From`: Se o método não tiver especificado um idioma de Origem, ele será o resultado da detecção automática do idioma. Caso contrário, será o idioma de Origem informado.
* `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Contém o mesmo valor especificado no parâmetro TranslateOptions.

O objeto TranslationMatch consiste no seguinte:

* `Error`: Se tiver ocorrido um erro para uma cadeia de caracteres de entrada específica, o código de erro será armazenado. Caso contrário, o campo ficará vazio.
* `MatchDegree`: O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas.  MatchDegree indica o grau de correspondência do texto de entrada com relação ao texto original encontrado no armazenamento. O valor retornado vai de 0 a 100, em que 0 é nenhuma similaridade e 100 é uma correspondência exata que diferencia de maiúsculas e minúsculas.
MatchedOriginalText: O texto original correspondente para esse resultado. Retornado somente se o texto original da correspondência for diferente do texto de entrada. Usado para retornar o texto de origem de uma correspondência difusa. Não é retornado para resultados do Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Os resultados da Tradução Automática terão uma classificação de 5. Traduções fornecidas de modo anônimo geralmente terão uma classificação de 1 a 4, enquanto traduções fornecidas com autoridade geralmente terão uma classificação de 6 a 10.
* `Count`: O número de vezes que essa tradução com essa classificação foi selecionada. O valor será 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Um objeto `GetTranslationsResponse` no formato descrito acima.

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.|query|string|
|text|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho do texto não deve exceder dez mil caracteres.|query|string|
|de|(vazio)|Obrigatório. Uma cadeia de caracteres representando o código de idioma do texto da tradução.|query|string|
|para |(vazio)    |Obrigatório. Uma cadeia de caracteres representando o código de idioma para o qual traduzir o texto.|query|string|
|maxTranslations|(vazio)|Obrigatório. Um inteiro que representa o número máximo de traduções a retornar.|query|inteiro|
|Autorização| (vazio)|Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|string| cabeçalho|
|Ocp-Apim-Subscription-Key|(vazio)  |Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Use o método `GetTranslationsArray` para recuperar vários candidatos de tradução para vários textos de origem.

O URI da solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

O formato do corpo da solicitação é o seguinte.

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

`GetTranslationsArrayRequest` inclui os seguintes elementos:

* `AppId`: Obrigatório. Se o cabeçalho de Autorização for usado, deixe o campo appid vazio ou inclua uma cadeia de caracteres contendo `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres representando o código de idioma do texto da tradução.
* `MaxTranslations`: Obrigatório. Um inteiro que representa o número máximo de traduções a retornar.
* `Options`: Opcional. Um objeto Opções que contém os valores listados abaixo. Eles são todos opcionais e usam como padrão as configurações mais comuns. Os elementos especificado devem estar listados em ordem alfabética.
    - Categoria: Uma cadeia de caracteres contendo a categoria (domínio) da tradução. Usa geral como padrão.
    - `ContentType`: A única opção compatível é "text/plain", que é a opção padrão.
    - `IncludeMultipleMTAlternatives`: sinalizador booliano para determinar se mais de uma alternativa deve ser retornada do mecanismo de MT. Os valores válidos são true e false (diferencia maiúsculas de minúsculas). O padrão é false e inclui somente uma alternativa. Definir o sinalizador como true permite gerar alternativas artificiais na tradução, totalmente integradas com CTF (estrutura de traduções colaborativas). O recurso permite retornar frases alternativas que não têm nenhuma alternativa na CTF adicionando alternativas artificiais da lista de n melhores do decodificador.
        - Classificações As classificações são aplicadas da seguinte maneira: 1) A melhor tradução automática tem uma classificação de 5. 2) As alternativas da CTF refletem a autoridade do revisor, de -10 a +10. 3) As alternativas de tradução (n melhores) geradas automaticamente têm uma classificação 0 e um grau de correspondência 100.
        - Número de alternativas O número de alternativas retornadas é até maxTranslations, mas pode ser menor.
        - Pares de idiomas Essa funcionalidade não está disponível para traduções entre chinês simplificado e tradicional em ambos os sentidos. Está disponível para todos os outros pares de idiomas compatíveis com o Microsoft Translator.
* `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Os mesmos conteúdos serão retornados na resposta.
* `Uri`: Filtre os resultados por esse URI. Se nenhum valor estiver definido, o padrão será todos.
* `User`: Filtre os resultados por esse usuário. Se nenhum valor estiver definido, o padrão será todos.
* `Texts`: Obrigatório. Uma matriz contendo os textos para tradução. Todas as cadeias de caracteres devem ser do mesmo idioma. O total de todos os textos a serem convertidos não deve exceder dez mil caracteres. O número máximo de elementos de matriz é dez.
* `To`: Obrigatório. Uma cadeia de caracteres representando o código de idioma para o qual traduzir o texto.

Os elementos opcionais podem ser omitidos. Elementos que são filhos diretos de `GetTranslationsArrayRequest` devem ser listados em ordem alfabética.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor retornado:** O formato do corpo da resposta é como mostrado a seguir.

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

Cada elemento `GetTranslationsResponse` contém os seguintes valores:

* `Translations`: Uma matriz de correspondências encontradas, armazenadas em objetos `TranslationMatch` (veja abaixo). As traduções podem incluir pequenas variações do texto original (correspondência difusa). As traduções serão classificadas: Correspondências de 100% primeiro, correspondências difusas abaixo.
* `From`: Se o método não tiver especificado um idioma `From`, ele será o resultado da detecção automática do idioma. Caso contrário, será o idioma de Origem informado.
* `State`: O estado do usuário para ajudar a correlacionar solicitação e resposta. Contém o mesmo valor dado no parâmetro `TranslateOptions`.

O objeto `TranslationMatch` consiste no seguinte:
* `Error`: Se tiver ocorrido um erro para uma cadeia de caracteres de entrada específica, o código de erro será armazenado. Caso contrário, o campo ficará vazio.
* `MatchDegree`: O sistema corresponde as frases de entrada com o repositório, incluindo correspondências inexatas.  `MatchDegree` indica o grau de correspondência do texto de entrada com relação ao texto original encontrado no armazenamento. O valor retornado vai de 0 a 100, em que 0 é nenhuma similaridade e 100 é uma correspondência exata que diferencia de maiúsculas e minúsculas.
* `MatchedOriginalText`: O texto original correspondente para esse resultado. Retornado somente se o texto original da correspondência for diferente do texto de entrada. Usado para retornar o texto de origem de uma correspondência difusa. Não é retornado para resultados do Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa que está tomando a decisão referente à qualidade. Os resultados da Tradução Automática terão uma classificação de 5. Traduções fornecidas de modo anônimo geralmente terão uma classificação de 1 a 4, enquanto traduções fornecidas com autoridade geralmente terão uma classificação de 6 a 10.
* `Count`: O número de vezes que essa tradução com essa classificação foi selecionada. O valor será 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (Status 200)

string

Tipo de Conteúdo da Resposta: aplicativo/xml
 
### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Obrigatório se o campo `appid` ou o cabeçalho `Ocp-Apim-Subscription-Key` não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Obrigatório se o campo `appid` ou o cabeçalho `Authorization` não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400    |Solicitação inválida. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Informe-nos se o erro persistir. Forneça-nos a data e a hora aproximadas da solicitação e a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente não disponível. Tente novamente e informe-nos se o erro persistir.|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar para o API de Tradução de Texto v3](../migrate-to-v3.md)










