---
title: 'Tutorial: Usar as APIs REST'
description: Este tutorial descreve como usar as APIs REST do Azure Purview para acessar o conteúdo do seu catálogo.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98951236"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Usar as APIs REST

Neste tutorial, você aprenderá a usar as APIs REST do Azure Purview. Qualquer pessoa que queira enviar dados para um Catálogo do Azure Purview, incluir o catálogo como parte de um processo automatizado ou criar uma experiência de usuário no catálogo pode usar as APIs REST para fazer isso.

Neste tutorial, você aprende a:

> [!div class="checklist"]
>
> * Criar uma entidade de serviço (aplicativo).
> * Configurar seu catálogo para confiar na entidade de serviço (aplicativo).
> * Exibir a documentação das APIs REST.
> * Coletar os valores necessários para usar as APIs REST.
> * Usar o cliente do Postman para chamar as APIs REST.
> * Gerar um cliente do .NET para chamar as APIs REST.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Para começar, você precisará já ter uma conta do Azure Purview. Caso não tenha um catálogo, confira o [guia de início rápido para criar uma conta do Azure Purview](create-catalog-portal.md).

* Se precisar adicionar dados ao catálogo, confira o [tutorial para executar o kit de início e examinar os dados](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Criar uma entidade de serviço (aplicativo)

Para que um cliente da API REST acesse o catálogo, ele precisará ter uma entidade de serviço (aplicativo) e uma identidade que o catálogo reconheça e esteja configurada para confiança. Quando você faz chamadas à API REST para o catálogo, elas usam a identidade da entidade de serviço.

Os clientes que usaram as entidades de serviço existentes (ID de aplicativo) tiveram uma alta taxa de falhas. Portanto, recomendamos criar uma entidade de serviço para chamar APIs.

Para criar uma entidade de serviço:

1. No [portal do Azure](https://portal.azure.com), procure e selecione **Azure Active Directory**.
1. Na página do **Azure Active Directory**, selecione **Registros de aplicativo** no painel esquerdo.
1. Selecione **Novo registro**.
1. Na página **Registrar um aplicativo**:
    1. Insira um **Nome** para o aplicativo (o nome da entidade de serviço).
    1. Selecione **Somente contas neste diretório organizacional (somente _&lt;o nome do seu locatário&gt;_ – Locatário único)** .
    1. Em **URI de Redirecionamento (opcional)** , escolha **Web** e insira um valor. Esse valor não precisa ser um URI válido.
    1. Selecione **Registrar**.
1. Na página da nova entidade de serviço, copie os valores de **Nome de exibição** e **ID do aplicativo (cliente)** para salvar para uso posterior.

   A ID do aplicativo é o valor `client_id` no código de exemplo.

Para usar a entidade de serviço (aplicativo), você precisará obter a senha dela. Aqui está como:

1. No portal do Azure, procure e selecione **Azure Active Directory** e selecione **Registros de aplicativo** no painel esquerdo.
1. Selecione a entidade de serviço (aplicativo) na lista.
1. Escolha **Certificados e segredos** no painel esquerdo.
1. Selecione **Novo segredo do cliente**.
1. Na página **Adicionar um segredo do cliente**, insira uma **Descrição**, selecione uma hora de expiração em **Expira em** e **Adicionar**.

   Na página **Segredos do cliente**, a cadeia de caracteres na coluna **Valor** do novo segredo é a sua senha. Salve esse valor.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Captura de tela mostrando um segredo do cliente.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Configurar seu catálogo para confiar na entidade de serviço (aplicativo)

Para configurar o Azure Purview para confiar na nova entidade de serviço:

1. Acesse sua conta do Purview

1. Na página **Conta do Purview**, selecione a guia **Controle de acesso (IAM)**

1. Clique em **+ Adicionar**

1. Selecione **Adicionar atribuição de função**

1. Em Função, digite **Curador de Dados do Purview**

    > [!Note]
    > Para obter mais informações sobre as permissões do Catálogo de Dados do Azure Purview, confira [Permissões do catálogo](catalog-permissions.md). Por exemplo, se você precisar obter uma permissão para disparar a verificação, o Tipo de função precisará ser **Administrador de Fonte de Dados do Purview**.

1. Em **Atribuir acesso a**, mantenha o padrão, **Usuário, grupo ou entidade de serviço**

1. Em **Selecionar**, insira o nome da entidade de serviço criada anteriormente que você deseja atribuir e clique no nome dela no painel de resultados.

1. Clique em **Salvar**

Você acabou de configurar a entidade de serviço como um administrador de aplicativos, o que permite que ela envie conteúdo para o catálogo.

## <a name="view-the-rest-apis-documentation"></a>Exibir a documentação das APIs REST

Para ver a documentação do Swagger da API, baixe [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), extraia os arquivos e abra index.html.

Caso deseje saber mais sobre a API de sugestão/pesquisa avançada fornecida pelo Azure Purview, confira a documentação do filtro de pesquisa da API REST. No momento, o cliente gerado AutoRest não dá suporte a parâmetros de pesquisa personalizados. Para solucionar o problema, siga o documento de filtro de pesquisa para definir as classes de filtro no código como parâmetros de chamada à API. O documento index.html traz exemplos dessas APIs.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Coletar os valores necessários para usar as APIs REST

Localize e salve os seguintes valores:

* ID do locatário:
  * No [portal do Azure](https://portal.azure.com), procure e selecione **Azure Active Directory**.
  * Na seção **Gerenciar** no painel esquerdo, selecione **Propriedades**, encontre a **ID do Locatário** e escolha o ícone **Copiar para a área de transferência** para salvar o valor dela.
* Ponto de extremidade do Atlas:
  * Na página [Contas do Azure Purview](https://aka.ms/purviewportal) no portal do Azure, localize e selecione sua conta do Azure Purview na lista.
  * Selecione **Propriedades**, encontre **Ponto de Extremidade do Atlas** e escolha o ícone **Copiar para a área de transferência** para salvar o valor dele. Remova a parte *https://* da cadeia de caracteres ao usá-la mais tarde.
* Nome da conta:
  * Extraia o nome do catálogo da cadeia de caracteres do ponto de extremidade do Atlas. Por exemplo, se o ponto de extremidade do Atlas for `https://ThisIsMyCatalog.catalog.purview.azure.com`, o nome da sua conta será `ThisIsMyCatalog`.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Usar o cliente do Postman para chamar as APIs REST

1. Instale o [cliente do Postman](https://www.getpostman.com/).
1. No cliente, selecione **Importar** e use [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Escolha **Coleções** e **Teste**.
1. Selecione **Obter Token**:
    1. Na URL ao lado de POST, substitua *&lt;sua-ID-de-locatário&gt;* pela ID de locatário copiada na seção anterior.
    1. Selecione a guia **Corpo** e substitua os espaços reservados no caminho e no corpo da etapa anterior.

       Depois que você selecionar **Enviar**, o corpo da resposta conterá uma estrutura JSON, incluindo o nome *access_token* e um valor de cadeia de caracteres entre aspas. 
    1. Copie o valor do token de portador (sem aspas) para usá-lo na próxima etapa.

1. Selecione **/v2/types/typedefs**:
    1. Substitua o espaço reservado no caminho pelo valor do ponto de extremidade do Atlas. Obtenha esse valor navegando até a instância do catálogo no portal do Ibiza e clicando em Visão geral. 

       O token de portador é definido na primeira etapa (ou você pode copiá-lo na guia “autorização” manualmente).

    1. Selecione **Enviar** para obter a resposta.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Gerar um cliente do .NET para chamar as APIs REST

### <a name="install-autorest"></a>Instalar o AutoRest



1. [Instale o Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Abra o PowerShell e execute o seguinte comando:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Baixar rest-api-specs.zip e criar o cliente

1. Baixe [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) e extraia os arquivos.
1. Execute o seguinte comando no PowerShell por meio da pasta rest-api-specs extraída:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   A saída desse processo cria uma pasta PurviewCatalogClient e uma subpasta CSharp na pasta rest-api-specs.

### <a name="create-a-sample-net-console-application"></a>Criar um aplicativo de console .NET de exemplo

1. Abra o Visual Studio 2019. Essas instruções foram testadas com a Community Edition gratuita.
1. Na página **Criar um projeto**, crie um projeto de **Aplicativo de Console (.NET Core)** em C#.
1. Copie e cole o [código de exemplo](#sample-code-for-the-console-application) fornecido.
1. Substitua *accountName*, *servicePrincipalId*, *servicePrincipalKey* e *tenantId* pelos valores coletados anteriormente.
1. Use o **Gerenciador de Soluções** para adicionar uma pasta chamada **Generated** no projeto do Visual Studio.
1. Copie a pasta rest-api-specs\PurviewCatalogClient\CSharp criada anteriormente para a pasta \Generated. Use o Explorador de Arquivos ou o prompt de linha de comando para a operação de cópia, o que vai disparar o Visual Studio a adicionar automaticamente os arquivos ao projeto.
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Gerenciar Pacotes NuGet**.
1. Selecione a guia **Procurar**. Localize e selecione **Microsoft.Rest.ClientRuntime**.
1. Verifique se a versão é, pelo menos, 2.3.21 e selecione **Instalar**.
1. Compile e execute o aplicativo.

O código de exemplo retorna uma contagem de quantos typedefs estão no catálogo e mostra como processar as atribuições de função. Para obter detalhes, confira `DoRoleAssignmentOperations()` no código de exemplo. Para obter mais informações sobre o projeto, confira [Configuração do projeto](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Código de exemplo do aplicativo de console

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar fontes de dados](manage-data-sources.md)
