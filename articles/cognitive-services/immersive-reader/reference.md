---
title: Referência do SDK do leitor de imersão
titleSuffix: Azure Cognitive Services
description: O SDK do leitor de imersão contém uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636520"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Referência do SDK do JavaScript do leitor de imersão (v 1.1)

O SDK do leitor de imersão contém uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Inicia o leitor de imersão dentro de um `iframe` em seu aplicativo Web. Observe que o tamanho do seu conteúdo é limitado a um máximo de 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>Parâmetros de launchAsync

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `token` | string | O token de autenticação do Azure AD. Consulte [como criar um recurso de leitura de imersão](./how-to-create-immersive-reader.md) para obter mais detalhes. |
| `subdomain` | string | O subdomínio personalizado do seu recurso de leitor de imersão no Azure. Consulte [como criar um recurso de leitura de imersão](./how-to-create-immersive-reader.md) para obter mais detalhes. |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a ser mostrado no leitor de imersão. |
| `options` | [Opções](#options) | Opções para configurar determinados comportamentos do leitor de imersão. Opcional. |

#### <a name="returns"></a>Retornos

Retorna um `Promise<LaunchResponse>` , que resolve quando o leitor de imersão é carregado. O é `Promise` resolvido para um [`LaunchResponse`](#launchresponse) objeto.

#### <a name="exceptions"></a>Exceções

O retornado `Promise` será rejeitado com um [`Error`](#error) objeto se o leitor de imersão não for carregado. Para obter mais informações, consulte os [códigos de erro](#error-codes).

<br>

## <a name="close"></a>close

Fecha o leitor de imersão.

Um exemplo de caso de uso para essa função é se o botão sair estiver oculto pela configuração ```hideExitButton: true``` em [Opções](#options). Em seguida, um botão diferente (por exemplo, seta para voltar do cabeçalho móvel) pode chamar essa ```close``` função quando ele é clicado.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Botão Iniciar do leitor de imersão

O SDK fornece o estilo padrão para o botão iniciar o leitor de imersão. Use o `immersive-reader-button` atributo Class para habilitar esse estilo. Consulte [como personalizar o botão leitor de imersão](./how-to-customize-launch-button.md) para obter mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Atributos opcionais

Use os atributos a seguir para configurar a aparência do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text` ou `iconAndText`. Assume o padrão de `icon`. |
| `data-locale` | Define a localidade. Por exemplo, `en-US` ou `fr-FR`. O padrão é inglês `en` . |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. O padrão é 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

A ```renderButtons``` função não será necessária se você estiver usando o guia de instruções [do botão leitor de imersão](./how-to-customize-launch-button.md) .

Essa função define e atualiza os elementos do botão de leitura imersiva do documento. Se ```options.elements``` for fornecido, os botões serão renderizados dentro de cada elemento fornecido em ```options.elements``` . O uso do ```options.elements``` parâmetro é útil quando você tem várias seções em seu documento para iniciar o leitor de imersão e deseja uma maneira simplificada de renderizar vários botões com o mesmo estilo, ou deseja renderizar os botões com um padrão de design simples e consistente. Para usar essa função com o parâmetro [renderButtons Options](#renderbuttons-options) , chame ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` no carregamento da página, conforme demonstrado no trecho de código abaixo. Caso contrário, os botões serão renderizados dentro dos elementos do documento que têm a classe ```immersive-reader-button``` , conforme mostrado em [como personalizar o botão do leitor de imersão](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Consulte os [atributos opcionais](#optional-attributes) acima para obter mais opções de renderização. Para usar essas opções, adicione qualquer um dos atributos de opção a cada ```HTMLDivElement``` em seu HTML de página.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>Parâmetros de renderButtons

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `options` | [opções de renderButtons](#renderbuttons-options) | Opções para configurar determinados comportamentos da função renderButtons. Opcional. |

### <a name="renderbuttons-options"></a>Opções de renderButtons

Opções para renderizar os botões de leitura imersiva.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Parâmetros de opções de renderButtons

| Configuração | Type | Descrição |
| ------- | ---- | ----------- |
| elementos | HTMLDivElement[] | Elementos para renderizar os botões de leitura de imersão no. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Contém a resposta da chamada para `ImmersiveReader.launchAsync` . Observe que uma referência ao `iframe` que contém o leitor de imersão pode ser acessada via `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>Parâmetros de LaunchResponse

| Configuração | Type | Descrição |
| ------- | ---- | ----------- |
| contêiner | HTMLDivElement | Elemento HTML que contém o iframe do leitor de imersão. |
| sessionID | String | Identificador global exclusivo para esta sessão, usado para depuração. |
 
## <a name="error"></a>Erro

Contém informações sobre um erro.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Parâmetros de erro

| Configuração | Type | Descrição |
| ------- | ---- | ----------- |
| code | String | Um de um conjunto de códigos de erro. Confira [Códigos de erro](#error-codes). |
| message | String | Representação do erro legível por humanos. |

#### <a name="error-codes"></a>Códigos do Erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, consulte `message` o parâmetro do [erro](#error). |
| Tempo limite | Falha ao carregar o leitor de imersão no tempo limite especificado. |
| TokenExpired | O token fornecido expirou. |
| Acelerado | O limite de taxa de chamada foi excedido. |

<br>

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no leitor de imersão.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Parâmetros de conteúdo

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| título | String | Texto do título mostrado na parte superior do leitor de imersão (opcional) |
| partes | [Parte []](#chunk) | Matriz de partes |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

Um único bloco de dados, que será passado para o conteúdo do leitor de imersão.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Parâmetros de bloco

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| conteúdo | String | A cadeia de caracteres que contém o conteúdo enviado ao leitor de imersão. |
| lang | String | Idioma do texto, o valor está no formato de marca de idioma IETF BCP 47, por exemplo, en, es-ES. O idioma será detectado automaticamente se não for especificado. Confira os [Idiomas compatíveis](#supported-languages). |
| Tipo MIME | string | Há suporte para formatos de texto sem formatação, MathML e HTML & Microsoft Word DOCX. Consulte [tipos de MIME com suporte](#supported-mime-types) para obter mais detalhes. |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Tipos MIME com suporte

| Tipo MIME | Descrição |
| --------- | ----------- |
| texto/sem formatação | Texto sem formatação. |
| texto/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicativo/MathML + XML | MathML (matematica Markup Language). [Saiba mais](./how-to/display-math.md).
| aplicativo/vnd.openxmlformats-officedocument.wordprocessingml.document | Documento de formato Microsoft Word. docx.


<br>

## <a name="options"></a>Opções

Contém propriedades que configuram determinados comportamentos do leitor de imersão.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Parâmetros de opções

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| uiLang | String | Idioma da interface do usuário, o valor está no formato de marca de idioma IETF BCP 47, por exemplo, en, es-ES. O padrão é o idioma do navegador, se não for especificado. |
| tempo limite | Número | Duração (em milissegundos) antes de [launchAsync](#launchasync) falhar com um erro de tempo limite (o padrão é 15000 MS). Esse tempo limite só se aplica à inicialização inicial da página do leitor, onde o sucesso é observado quando a página do leitor é aberta e o controle giratório é iniciado. O ajuste do tempo limite não deve ser necessário. |
| uiZIndex | Número | Z-índice do iframe que será criado (o padrão é 1000). |
| useWebview | Booliano| Use uma marca WebView em vez de um iframe, para compatibilidade com aplicativos Chrome (o padrão é false). |
| onsair | Função | É executado quando o leitor de imersão é encerrado. |
| allowFullscreen | Booliano | A capacidade de alternar a tela inteira (o padrão é true). |
| hideExitButton | Booliano | Se deseja ou não ocultar a seta do botão de saída do leitor de imersão (o padrão é false). Isso só deve ser verdadeiro se houver um mecanismo alternativo fornecido para sair do leitor de imersão (por exemplo, uma seta para voltar da barra de ferramentas móvel). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Configuração para o uso do cookie do leitor de imersão (o padrão é *CookiePolicy. Disable* ). É responsabilidade do aplicativo host obter qualquer consentimento do usuário necessário de acordo com a política de conformidade do cookie da UE. Consulte [Opções de política de cookie](#cookiepolicy-options). |
| disableFirstRun | Booliano | Desabilite a primeira experiência de execução. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Opções para configurar a leitura em voz alta. |
| conversãooptions | [Conversãooptions](#translationoptions) | Opções para configurar a tradução. |
| displayOptions | [DisplayOptions](#displayoptions) | Opções para configurar o tamanho do texto, a fonte, etc. |
| das | String | Cadeia de caracteres retornada por preferencchanged que representa as preferências do usuário no leitor de imersão. Consulte [parâmetros de configurações](#settings-parameters) e [como armazenar as preferências do usuário](./how-to-store-user-preferences.md) para obter mais informações. |
| preferencchanged | Função | É executado quando as preferências do usuário são alteradas. Consulte [como armazenar as preferências do usuário](./how-to-store-user-preferences.md) para obter mais informações. |
| customDomain | String | Reservado para uso interno. Domínio personalizado no qual o webapp do leitor de imersão está hospedado (o padrão é NULL). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Importante** Não tente alterar programaticamente os valores da `-preferences` cadeia de caracteres enviada de e para o aplicativo de leitor de imersão, pois isso pode causar um comportamento inesperado, resultando em uma experiência de usuário degradada para seus clientes. Os aplicativos host nunca devem atribuir um valor personalizado ou manipular a `-preferences` cadeia de caracteres. Ao usar a `-preferences` opção de cadeia de caracteres, use apenas o valor exato que foi retornado da `-onPreferencesChanged` opção de retorno de chamada.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>Parâmetros de ReadAloudOptions

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| voice | String | Voz, "fêmea" ou "masculino". Observe que nem todos os idiomas dão suporte a ambos os gêneros. |
| velocidade | Número | Velocidade de reprodução, deve estar entre 0,5 e 2,5, inclusive. |
| autoPlay | Booliano | Iniciar automaticamente leitura em voz alta quando o leitor de imersão for carregado. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> Devido a limitações do navegador, a reprodução automática não tem suporte no Safari.

<br>

## <a name="translationoptions"></a>Conversãooptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>Parâmetros de translationoptions

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| Linguagem | String | Define o idioma de tradução, o valor está no formato de marca de idioma IETF BCP 47, por exemplo: fr-FR, es-MX, zh-Hans-CN. Necessário para habilitar automaticamente a tradução de palavras ou documentos. |
| autoEnableDocumentTranslation | Booliano | Traduza automaticamente o documento inteiro. |
| autoEnableWordTranslation | Booliano | Habilitar automaticamente a tradução automática. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>Parâmetros de displayoptions

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| textSize | Número | Define o tamanho do texto escolhido. |
| increaseSpacing | Booliano | Define se o espaçamento de texto será alternado ou desativado. |
| fontFamily | String | Define a fonte escolhida ("Calibri", "ComicSans" ou "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>Opções de CookiePolicy

```typescript
enum CookiePolicy { Disable, Enable }
```

**As configurações listadas abaixo são apenas para fins informativos** . O leitor de imersão armazena suas configurações, ou preferências do usuário, em cookies. Essa *cookiePolicy* opção cookiePolicy **desabilita** o uso de cookies por padrão para obedecer às leis de conformidade do cookie da UE. Se você quiser reabilitar os cookies e restaurar a funcionalidade padrão para as preferências de usuário do leitor de imersão, será necessário garantir que seu site ou aplicativo obtenha o consentimento adequado do usuário para habilitar cookies. Em seguida, para reabilitar os cookies no leitor de imersão, você deve definir explicitamente a opção *cookiePolicy* como *cookiePolicy. Enable* ao iniciar o leitor de imersão. A tabela a seguir descreve quais configurações o leitor de imersão armazena em seu cookie quando a opção *cookiePolicy* está habilitada.

#### <a name="settings-parameters"></a>Parâmetros de configurações

| Configuração | Type | Descrição |
| ------- | ---- | ----------- |
| textSize | Número | Define o tamanho do texto escolhido. |
| fontFamily | String | Define a fonte escolhida ("Calibri", "ComicSans" ou "Sitka"). |
| Espaçamento de linhas | Número | Define se o espaçamento de texto será alternado ou desativado. |
| formattingEnabled | Booliano | Define se a formatação HTML é ativada ou desativada. |
| - | String | Define o tema escolhido (por exemplo, "Light", "escuro"...). |
| syllabificationEnabled | Booliano | Define se o syllabification foi alternado ou desativado. |
| nounHighlightingEnabled | Booliano | Isso define se o realce de substantivo é alternado ou desativado. |
| nounHighlightingColor | String | Define a cor de realce do substantivo escolhido. |
| verbHighlightingEnabled | Booliano | Define se o realce de verbo é alternado ou desativado. |
| verbHighlightingColor | String | Define a cor de realce do verbo escolhido. |
| adjectiveHighlightingEnabled | Booliano | Define se o realce de adjetivo é alternado ou desativado. |
| adjectiveHighlightingColor | String | Define a cor de realce do adjetivo escolhido. |
| adverbHighlightingEnabled | Booliano | Define se o realce de advérbio é alternado ou desativado. |
| adverbHighlightingColor | String | Define a cor de realce advérbio escolhida. |
| pictureDictionaryEnabled | Booliano | Define se o dicionário de imagem é alternado ou desativado. |
| posLabelsEnabled | Booliano | Define se o rótulo de texto sobrescrito de cada parte realçada da fala é alternado ou desativado.  |

<br>

## <a name="supported-languages"></a>Idiomas compatíveis

O recurso de tradução do leitor de imersão dá suporte a vários idiomas. Consulte [Suporte ao idioma](./language-support.md) para obter mais informações.

<br>

## <a name="html-support"></a>Suporte a HTML

Quando a formatação estiver habilitada, o conteúdo a seguir será renderizado como HTML no leitor de imersão.

| HTML | Conteúdo com suporte |
| --------- | ----------- |
| Estilos de fonte | Negrito, itálico, sublinhado, código, tachado, sobrescrito, subscrito |
| Listas não ordenadas | Disco, círculo, quadrado |
| Listas ordenadas | Decimal, Upper-Alpha, minúscula-alfa, Upper-Roman Lower-Roman |

Marcas sem suporte serão renderizadas comparativamente. Não há suporte para imagens e tabelas no momento.

<br>

## <a name="browser-support"></a>Suporte ao navegador

Use as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o leitor de imersão.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Próximas etapas

* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Início rápido: criar um aplicativo Web que inicia o leitor de imersão (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
