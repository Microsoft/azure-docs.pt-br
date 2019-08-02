---
title: Serviço Conectado do Visual Studio – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conectar-se à API da Pesquisa Visual Computacional de um aplicativo Web ASP.NET Core usando o recurso de Serviço Conectado do Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: ff3ae9ec4a775e2450a552e414ec52597593dd39
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604273"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Usar serviços conectados no Visual Studio para se conectar à API da Pesquisa Visual Computacional

Este artigo e os artigos complementares fornecem detalhes sobre como usar o recurso de Serviço Conectado do Visual Studio para API da Pesquisa Visual Computacional dos Serviços Cognitivos. O recurso está disponível no Visual Studio 2017 15.7 ou posterior, com a extensão dos Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Microsoft Visual Studio 2017 versão 15.7 ou superior com a carga de trabalho de **Desenvolvimento para a Web** instalada. [Baixe agora](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Adicionar suporte ao projeto para API da Pesquisa Visual Computacional dos Serviços Cognitivos

1. Criar um novo projeto Web do ASP.NET Core. Use o modelo de projeto Vazio. 

1. No **Gerenciador de Soluções**, selecione **Adicionar** > **Serviço Conectado**.
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.

   ![menu aberto clicando com o botão direito do mouse em um projeto do Visual Studio: Adicionar > Serviço Conectado](../media/vs-common/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **API da Pesquisa Visual Computacional dos Serviços Cognitivos**.

   ![Menu de Serviços Conectados: O item Analisar Imagens... está delineado](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Se você tiver entrado no Visual Studio e tiver uma assinatura do Azure associada à sua conta, será exibida uma página com uma lista suspensa com suas assinaturas.

   ![Janela de API da Pesquisa Visual Computacional com o menu suspenso Assinatura realçado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Selecione a assinatura que você quer usar e, em seguida, escolha um nome para a API da Pesquisa Visual Computacional ou escolha o link Editar para modificar o nome gerado automaticamente, escolha o grupo de recursos e Tipo de Preço.

   ![Editar detalhes do serviço conectado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Acompanhe o link para obter detalhes sobre os tipos de preços.

1. Escolha Adicionar para adicionar suporte para o Serviço Conectado.
   O Visual Studio modifica o projeto para adicionar os pacotes NuGet, entradas do arquivo de configuração e outras alterações para dar suporte a uma conexão com API da Pesquisa Visual Computacional. A Janela de Saída mostra o log do que está acontecendo com o projeto. Você verá algo semelhante ao que se segue:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Use a API da Pesquisa Visual Computacional para detectar atributos de uma imagem

1. Adicione o seguinte usando instruções no Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adicione um campo de configuração e adicione um construtor que inicializa o campo de configuração na classe `Startup` para habilitar a configuração no programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Na pasta wwwroot do projeto, adicione uma pasta de imagens e adicione um arquivo de imagem à pasta wwwroot. Por exemplo, é possível usar uma das imagens nesta [página de API da Pesquisa Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Clique com o botão direito do mouse em uma das imagens, salve no disco rígido local e, em Gerenciador de Soluções, clique com o botão direito do mouse na pasta de imagens e escolha **Adicionar** > **Item Existente** para adicionar ao projeto. O projeto deve ser semelhante a este no Gerenciador de Soluções: 
  
   ![Captura de tela da exibição do gerenciador de soluções com um arquivo de imagem selecionado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Clique com o botão direito do mouse no arquivo de imagem, escolha Propriedades e, em seguida, escolha **Copiar se mais recente**. 

   ![Janela propriedades da imagem; opção Copiar para Diretório de Saída está definida como Copiar se mais recente](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Substitua o método Configurar pelo seguinte código para acessar a API da Pesquisa Visual Computacional testar uma imagem.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    O código aqui constrói uma solicitação HTTP com o URI e a imagem como conteúdo binário para uma chamada à API REST da Pesquisa Visual Computacional.

1. Adicione as funções auxiliares GetImageAsByteArray e JsonPrettyPrint.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Execute o aplicativo Web e veja o que a API da Pesquisa Visual Computacional localizou na imagem.

   ![Imagem e resultados formatados da API da Pesquisa Visual Computacional](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o serviço cognitivo e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a API da Pesquisa Visual Computacional lendo a [documentação da API da Pesquisa Visual Computacional](Home.md).
