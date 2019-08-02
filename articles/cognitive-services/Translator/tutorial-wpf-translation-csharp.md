---
title: 'Tutorial: Criar um aplicativo de tradução com o WPF em C# – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo do WPF (Windows Presentation Foundation) que usa APIs dos Serviços Cognitivos para tradução de texto, detecção de idioma e verificação ortográfica com uma chave de assinatura única. Este exercício mostrará como usar recursos da API de Tradução de Texto e a API de Verificação Ortográfica do Bing.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: b929d0c0da2a812a1c8595536f09931e4edd0fd9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594925"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Tutorial: Criar um aplicativo de tradução com o WPF

Neste tutorial, você criará um aplicativo do [WPF (Windows Presentation Foundation)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) que usa os Serviços Cognitivos do Azure para tradução de texto, detecção de idioma e verificação ortográfica com uma chave de assinatura única. Especificamente, seu aplicativo chamará APIs de Tradução de Texto e da [Verificação Ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

O que é o WPF? É uma estrutura de interface do usuário que cria aplicativos de cliente da área de trabalho. A plataforma de desenvolvimento do WPF é compatível com um amplo conjunto de recursos de desenvolvimento de aplicativo, incluindo um modelo de aplicativo, recursos, controles, elementos gráficos, layout, vinculação de dados, documentos e segurança. Ela é um subconjunto do .NET Framework, portanto, se você já tiver criado aplicativos com o .NET Framework usando ASP.NET ou Windows Forms, a experiência de programação deverá ser familiar. O WPF usa linguagem XAML para fornecer um modelo declarativo para programação de aplicativo que vamos analisar nas próximas seções.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto do WPF no Visual Studio
> * Adicionar assemblies e pacotes NuGet ao seu projeto
> * Criar a interface do usuário do seu aplicativo com XAML
> * Usar a API de Tradução de Texto para obter idiomas, traduzir texto e detectar o idioma de origem
> * Usar a API de Verificação Ortográfica do Bing para validar sua entrada e aumentar a precisão da tradução
> * Executar seu aplicativo do WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Serviços Cognitivos usados neste tutorial

Essa lista inclui os Serviços Cognitivos usados neste tutorial. Siga o link para navegar até a referência de API de cada recurso.

| Serviço | Recurso | DESCRIÇÃO |
|---------|---------|-------------|
| Conversor de Texto | [Obter Idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Recupera uma lista completa dos idiomas com suporte para tradução de texto. |
| Conversor de Texto | [Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Traduz textos para mais de 60 idiomas. |
| Conversor de Texto | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Detecta o idioma do texto de entrada. Inclui a pontuação de confiança para detecção. |
| Verificação Ortográfica do Bing | [Verificação Ortográfica](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Corrige erros de ortografia para melhorar a precisão da tradução. |

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você precisará do seguinte:

* Assinatura dos Serviços Cognitivos do Azure. [Obter uma chave dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-resource).
* Um computador Windows
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) – Community ou Enterprise

> [!NOTE]
> É recomendável criar a assinatura na região Oeste dos EUA para este tutorial. Caso contrário, você precisará alterar os pontos de extremidade e regiões no código ao trabalhar neste exercício.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Criar um aplicativo do WPF no Visual Studio

A primeira coisa que precisamos fazer é configurar nosso projeto no Visual Studio.

1. Abra o Visual Studio. Em seguida, selecione **Arquivo > Novo > Projeto**.
2. No painel esquerdo, localize e selecione **Visual C#** . Em seguida, selecione **Aplicativo do WPF (.NET Framework)** no painel central.
   ![Criar um aplicativo do WPF no Visual Studio](media/create-wpf-project-visual-studio.png)
3. Nomeie o projeto `MSTranslatorTextDemo`, defina a versão da estrutura como **.NET Framework 4.5.2 ou posterior** e, em seguida, clique em **OK**.
4. Seu projeto foi criado. Você observará duas guias abertas: `MainWindow.xaml` e `MainWindow.xaml.cs`. Neste tutorial adicionaremos código a esses dois arquivos. O primeiro para a interface do usuário do aplicativo; o segundo para as chamadas à Tradução de Texto e à Verificação Ortográfica do Bing.
   ![Examinar o ambiente](media/blank-wpf-project.png)

Na próxima seção, vamos adicionar um pacote NuGet e assemblies ao nosso projeto para obter outras funcionalidades, como análise de JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Adicionar referências e pacotes NuGet ao seu projeto

Nosso projeto requer alguns assemblies do .NET Framework e o NewtonSoft.Json, que instalaremos usando o gerenciador de pacotes NuGet.

### <a name="add-net-framework-assemblies"></a>Adicionar assemblies do .NET Framework

Vamos adicionar assemblies ao nosso projeto para serializar e desserializar objetos e para gerenciar solicitações e respostas HTTP.

1. Localize seu projeto no Gerenciador de Soluções do Visual Studio (painel direito). Clique com o botão direito do mouse no projeto e selecione **Adicionar > Referência...** , abrindo o **Gerenciador de Referências**.
   ![Adicionar referências de assembly](media/add-assemblies-sample.png)
2. A guia de assemblies lista todos os assemblies do .NET Framework disponíveis para fazer referência. Use a barra de pesquisa no canto superior direito da tela para pesquisar essas referências e adicioná-las ao seu projeto:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
3. Depois de adicionar essas referências ao projeto, clique em **OK** para fechar o **Gerenciador de Referências**.

> [!NOTE]
> Se você quiser saber mais sobre referências de assembly, confira [Como: adicionar ou remover referência usando o Gerenciador de Referências](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017).

### <a name="install-newtonsoftjson"></a>Instalar o NewtonSoft.Json

Nosso aplicativo usará o NewtonSoft.Json para desserializar objetos JSON. Siga estas instruções para instalar o pacote.

1. Localize seu projeto no Gerenciador de Soluções do Visual Studio e clique com o botão direito do mouse no projeto. Selecione **Gerenciar Pacotes NuGet...** .
2. Localize e selecione a guia **Procurar**.
3. Digite [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) na barra de pesquisa.
   ![Localizar e instalar o NewtonSoft.Json](media/add-nuget-packages.png)
4. Selecione o pacote e clique em **Instalar**.
5. Quando a instalação estiver concluída, feche a guia.

## <a name="create-a-wpf-form-using-xaml"></a>Criar um formulário do WPF usando XAML

Para usar o aplicativo, você precisará de uma interface do usuário. Usando XAML, vamos criar um formulário que permite aos usuários selecionar idiomas de entrada e de tradução, inserir texto para tradução e exibir a saída da tradução.

Vamos dar uma olhada no que estamos criando.

![Interface do usuário XAML do WPF](media/translator-text-csharp-xaml.png)

A interface do usuário inclui os seguintes componentes:

| NOME | Type | DESCRIÇÃO |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Exibe uma lista dos idiomas compatíveis com o Microsoft Translator para a tradução de texto. O usuário seleciona o idioma a partir do qual traduzir. |
| `ToLanguageComboBox` | ComboBox | Exibe a mesma lista de idiomas de `FromComboBox`, mas é usada para selecionar o idioma para o qual usuário está traduzindo. |
| `TextToTranslate` | TextBox | Permite que o usuário insira o texto a ser traduzido. |
| `TranslateButton` | Botão | Use esse botão para traduzir o texto. |
| `TranslatedTextLabel` | Rótulo | Exibe a tradução. |
| `DetectedLanguageLabel` | Rótulo | Exibe o idioma detectado do texto a ser traduzido (`TextToTranslate`). |

> [!NOTE]
> Estamos criando este formulário usando código-fonte XAML, no entanto, você pode criar o formulário com o editor do Visual Studio.

Vamos adicionar o código ao nosso projeto.

1. No Visual Studio, selecione a guia para `MainWindow.xaml`.
2. Copie este código em seu projeto e salve.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
3. Agora você verá uma versão prévia da interface do usuário do aplicativo no Visual Studio. Deve ser semelhante à imagem acima.

Isso é tudo, o formulário está pronto. Agora vamos escrever código para usar a Tradução de Texto e a Verificação Ortográfica do Bing.

> [!NOTE]
> Fique à vontade para ajustar esse formulário ou criar seu próprio formulário.

## <a name="create-your-app"></a>Criar o aplicativo

`MainWindow.xaml.cs` contém o código que controla o nosso aplicativo. Nas próximas seções, vamos adicionar código para preencher os menus suspensos e chamar algumas APIs expostas pela Tradução de Texto e a Verificação Ortográfica do Bing.

* Quando o programa for iniciado e `MainWindow` instanciada, o método `Languages` da API de Tradução de Texto será chamado para recuperar e preencher os menus suspensos de idioma que selecionamos. Isso ocorre uma vez no início de cada sessão.
* Quando o botão **Traduzir** é clicado, a seleção de idioma e o texto do usuário são recuperados, a verificação ortográfica é executada na entrada e a tradução e o idioma detectado são exibidos para o usuário.
  * O método `Translate` da API de Tradução de Texto é chamado para traduzir texto de `TextToTranslate`. Essa chamada também inclui os idiomas `to` e `from` selecionados com os menus suspensos.
  * O método `Detect` da API de Tradução de Texto é chamado para determinar o idioma de texto de `TextToTranslate`.
  * A Verificação Ortográfica do Bing é usada para validar `TextToTranslate` e ajustar os erros de ortografia.

Todo o nosso projeto é encapsulado na classe `MainWindow : Window`. Vamos começar adicionando código para definir a chave de assinatura, declarar pontos de extremidade para a Tradução de Texto e a Verificação Ortográfica do Bing e inicializar o aplicativo.

1. No Visual Studio, selecione a guia para `MainWindow.xaml.cs`.
2. Substitua as instruções `using` pré-populadas pelo seguinte.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
3. Localize a classe `MainWindow : Window` e a substitua por este código:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
   1. Adicione sua chave de assinatura dos Serviços Cognitivos e salve.

Neste bloco de código, declaramos duas variáveis membro que contêm informações sobre os idiomas disponíveis para tradução:

| Variável | Type | DESCRIÇÃO |
|----------|------|-------------|
|`languageCodes` | Matriz de cadeias de caracteres |Armazena em cache os códigos do idioma. O serviço Translator usa códigos curtos, como `en` para português, para identificar os idiomas. |
|`languageCodesAndTitles` | Dicionário classificado | Mapeia os nomes "amigáveis" na interface do usuário de volta para os códigos curtos usados na API. A classificação é em ordem alfabética, sem diferenciar as letras maiúsculas e minúsculas. |

Então, dentro do construtor `MainWindow`, adicionamos tratamento de erro com `HandleExceptions`. Isso garantirá que um alerta seja fornecido se uma exceção não for tratada. Em seguida, uma verificação é executada para confirmar se a chave de assinatura fornecida tem 32 caracteres de comprimento. Um erro será lançado se a chave tiver menos ou mais do que 32 caracteres.

Se houver chaves com o comprimento correto, a chamada `InitializeComponent()` manterá a interface do usuário em execução localizando, carregando e instanciando a descrição XAML da janela principal do aplicativo.

Por último, adicionamos código para chamar métodos para recuperar os idiomas para tradução e preencher os menus suspensos para a interface do usuário do nosso aplicativo. Não se preocupe, falaremos sobre o código por trás dessas chamadas em breve.

## <a name="get-supported-languages"></a>Obter idiomas com suporte

A API de Tradução de Texto atualmente é compatível com mais de 60 idiomas. Como a compatibilidade com novos idiomas será adicionada com o passar do tempo, é recomendável chamar o recurso de Idiomas exposto pela Tradução de Texto em vez de embutir a lista de idiomas em seu aplicativo.

Nesta seção, vamos criar uma solicitação `GET` ao recurso de Idiomas, especificando que queremos uma lista de idiomas disponíveis para a tradução.

> [!NOTE]
> O recurso de Idiomas permite que você filtre o suporte de idiomas com os seguintes parâmetros de consulta: transliteração, dicionário e tradução. Para obter mais informações, confira [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Antes de continuarmos, vamos dar uma olhada em um exemplo de saída de uma chamada ao recurso de Idiomas:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Nessa saída, podemos pode extrair o código do idioma e o `name` de um idioma específico. Nosso aplicativo usa NewtonSoft.Json para desserializar o objeto JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Continuando de onde paramos na última seção, vamos adicionar um método para obter os idiomas compatíveis para o nosso aplicativo.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao seu projeto:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

O método `GetLanguagesForTranslate()` cria uma solicitação HTTP GET e usa o parâmetro de cadeia de caracteres de consulta `scope=translation` para limitar o escopo da solicitação aos idiomas compatíveis para tradução. O cabeçalho `Accept-Language` com o valor `en` é adicionado para que os idiomas com suporte sejam retornados em inglês.

A resposta JSON é analisada e convertida em um dicionário. Depois, os códigos de idioma são adicionados à variável de membro `languageCodes`. Os pares de chave/valor que contêm os códigos do idioma e os nomes amigáveis circulam e são adicionados à variável de membro `languageCodesAndTitles`. Os menus suspensos no formulário exibem os nomes amigáveis, mas os códigos são necessários para solicitar a tradução.

## <a name="populate-language-drop-down-menus"></a>Preencher os menus suspensos de idiomas

A interface do usuário é definida em XAML, portanto, você não precisa fazer muito para configurá-la, exceto chamar `InitializeComponent()`. Uma coisa que você precisa fazer é adicionar os nomes amigáveis dos idiomas às listas suspensas **Traduzir de** e **Traduzir para**; isso é feito com o método `PopulateLanguageMenus()`.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao projeto, abaixo do método `GetLanguagesForTranslate()`:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Esse método itera no dicionário `languageCodesAndTitles` e adiciona cada chave em ambos os menus. Depois que os menus são preenchidos, o padrão de e para os idiomas são definidos como **Detectar** e **Inglês**, respectivamente.

> [!TIP]
> Sem uma seleção padrão para os menus, o usuário poderá clicar em **Traduzir** sem escolher um idioma “para” ou “de”. Os padrões eliminam a necessidade de lidar com esse problema.

Agora que `MainWindow` foi inicializado e a interface do usuário criada, o código somente será executado ao clicar no botão **Traduzir**.

## <a name="detect-language-of-source-text"></a>Detectar o idioma do texto de origem

Agora, vamos criar um método para detectar o idioma do texto de origem (texto inserido na área de texto) usando a API de Tradução de Texto. O valor retornado por essa solicitação será usado posteriormente na solicitação de tradução.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao projeto, abaixo do método `PopulateLanguageMenus()`:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Esse método cria uma solicitação `POST` HTTP ao recurso Detectar. Ele usa um único argumento, `text`, que é passado como o corpo da solicitação. Posteriormente, ao criarmos nossa solicitação de tradução, o texto inserido na interface do usuário será passado para esse método para detecção do idioma.

Além disso, esse método avalia a pontuação de confiança da resposta. Se a pontuação for maior que `0.5`, o idioma detectado será exibido na interface do usuário.

## <a name="spell-check-the-source-text"></a>Verificação ortográfica do texto de origem

Agora vamos criar um método para verificar a ortografia do nosso texto de origem, usando a API de Verificação Ortográfica do Bing. Isso garante que obteremos traduções precisas da API de Tradução de Texto. Todas as correções ao texto de origem são passadas junto com a solicitação de tradução quando o botão **Traduzir** é clicado.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao projeto, abaixo do método `DetectLanguage()`:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Traduzir texto ao clicar

A última coisa que precisamos fazer é criar um método que é invocado quando o botão **Traduzir** é clicado na interface do usuário.

1. No Visual Studio, abra a guia para `MainWindow.xaml.cs`.
2. Adicione este código ao projeto, abaixo do método `CorrectSpelling()` e salve:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

A primeira etapa é obter os idiomas "de" e "para" e o texto inserido pelo usuário no formulário. Se o idioma de origem estiver definido como **Detectar**, `DetectLanguage()` será chamado para determinar o idioma do texto de origem. O texto pode estar em um idioma sem suporte pela API do Tradutor. Nesse caso, será exibida uma mensagem para informar o usuário e retornar sem traduzir o texto.

Se o idioma de origem for o inglês (especificado ou detectado), verifique a ortografia do texto com `CorrectSpelling()` e aplique todas as correções. O texto corrigido é adicionado de volta à área de texto para que o usuário veja que uma correção foi feita.

O código para traduzir o texto deve parecer familiar: crie a URI, crie uma solicitação, envie-a e analise a resposta. A matriz JSON pode conter mais de um objeto para a tradução, no entanto, nosso aplicativo requer apenas um.

Depois de uma solicitação bem-sucedida, `TranslatedTextLabel.Content` é substituído pelo `translation`, o que atualiza a interface do usuário para exibir o texto traduzido.

## <a name="run-your-wpf-app"></a>Executar seu aplicativo do WPF

Isso é tudo, você tem um aplicativo de tradução funcionando, criado com uso do WPF. Para executar o aplicativo, clique no botão **Iniciar** no Visual Studio.

## <a name="source-code"></a>Código-fonte

O código-fonte desse projeto está disponível no GitHub.

* [Explorar o código-fonte](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de Tradução de Texto da Microsoft](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
