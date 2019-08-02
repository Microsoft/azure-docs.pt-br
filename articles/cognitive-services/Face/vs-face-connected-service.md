---
title: 'Tutorial: API de Detecção Facial C#'
titleSuffix: Azure Cognitive Services
description: Crie um aplicativo do Windows que usa a API de Detecção Facial de Serviços Cognitivos para detectar características de rostos em uma imagem.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: ghogen
ms.openlocfilehash: 7907a79289149d9e165dd6df0c09bee596e624e2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606807"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Conectar a API de Detecção Facial dos Serviços Cognitivos usando Serviços Conectados no Visual Studio

Usando a API de Detecção Facial dos Serviços Cognitivos é possível detectar, analisar, organizar e marcar faces nas fotos.

Este artigo e os artigos complementares fornecem detalhes sobre o uso do recurso de Serviço Conectado do Visual Studio para API de Detecção Facial dos Serviços Cognitivos. O recurso está disponível no Visual Studio 2017 15.7 ou posterior, com a extensão dos Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Microsoft Visual Studio 2017 versão 15.7 ou superior com a carga de trabalho de **Desenvolvimento para a Web** instalada. [Baixe agora](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Criar um projeto e adicionar suporte à API de Detecção Facial dos Serviços Cognitivos

1. Criar um novo projeto Web do ASP.NET Core. Use o modelo de projeto Vazio. 

1. No **Gerenciador de Soluções**, selecione **Adicionar** > **Serviço Conectado**.
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.

   ![Item de menu Adicionar Serviço Conectado](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **API de Detecção Facial**.

   ![Escolher o serviço para conectar](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Se você tiver entrado no Visual Studio e tiver uma assinatura do Azure associada à sua conta, será exibida uma página com uma lista suspensa com suas assinaturas.

   ![Selecione sua assinatura](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Selecione a assinatura que você quer usar e, em seguida, escolha um nome para a API de Detecção Facial ou escolha o link Editar para modificar o nome gerado automaticamente, escolha o grupo de recursos e Tipo de Preço.

   ![Editar detalhes do serviço conectado](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Acompanhe o link para obter detalhes sobre os tipos de preços.

1. Escolha Adicionar para adicionar suporte para o Serviço Conectado.
   O Visual Studio modifica o projeto para adicionar os pacotes NuGet, entradas do arquivo de configuração e outras alterações para dar suporte a uma conexão com API de Detecção Facial.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Usar a API de Detecção Facial para detectar atributos de faces em uma imagem

1. Adicione o seguinte usando instruções no Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adicione um campo de configuração e adicione um construtor que inicializa o campo de configuração na classe de Inicialização para habilitar a Configuração no programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Na pasta wwwroot do projeto, adicione uma pasta de imagens e adicione um arquivo de imagem à pasta wwwroot. Por exemplo, é possível usar uma das imagens nesta [página de API de Detecção Facial](https://azure.microsoft.com/services/cognitive-services/face/). Clique com o botão direito do mouse em uma das imagens, salve no disco rígido local e, em Gerenciador de Soluções, clique com o botão direito do mouse na pasta de imagens e escolha **Adicionar** > **Item Existente** para adicionar ao projeto. O projeto deve ser semelhante a este no Gerenciador de Soluções:
 
   ![pasta de imagens com arquivo de imagem](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Clique com o botão direito do mouse no arquivo de imagem, escolha Propriedades e, em seguida, escolha **Copiar se mais recente**.

   ![Copiar se mais recente](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Substitua o método Configurar pelo seguinte código para acessar a API de Detecção Facial e testar uma imagem. Altere a cadeia de caracteres imagePath para o caminho e nome de arquivo corretos para a imagem de face.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    O código desta etapa constrói uma solicitação HTTP com uma chamada à API REST de Detecção Facial usando a chave adicionada quando você adicionou o serviço conectado.

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

1. Execute o aplicativo Web e veja o que a API de Detecção Facial localizou na imagem.
 
   ![Imagem e resultados formatados da API de Detecção Facial](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o serviço cognitivo e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
1. Selecione **Excluir grupo de recursos**.
1. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:**, digite o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a API de Detecção Facial, lendo as informações na [Documentação da API de Detecção Facial](Overview.md).
