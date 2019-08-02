---
title: 'Início Rápido: API de Tabela com .NET – Azure Cosmos DB'
description: Este guia de início rápido mostra como usar a API de Tabela do Azure Cosmos DB para criar um aplicativo com o Portal do Azure e o .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "65979044"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Início Rápido: compilar um aplicativo de API de Tabela com o SDK do .NET e o Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Este guia de início rápido mostra como usar o .NET e a [API de Tabela](table-introduction.md) do Azure Cosmos DB para criar um aplicativo por meio da clonagem de um exemplo do GitHub. Este guia de início rápido também mostra como criar uma conta do Azure Cosmos DB e como usar o Data Explorer para criar tabelas e entidades no Portal do Azure baseado na Web.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2019 instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

   ```bash
   md "C:\git-samples"
   ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

   ```bash
   cd "C:\git-samples"
   ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>Abrir o aplicativo de exemplo no Visual Studio

1. No Visual Studio, do menu **Arquivo**, escolha **Abrir** e, em seguida, escolha **Projeto/Solução**. 

   ![Abra a solução](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Navegue até a pasta onde você clonou o aplicativo de exemplo e abra o arquivo TableStorage.sln.

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado. 

1. No [Portal do Azure](https://portal.azure.com/), clique em **Cadeia de Conexão**. Use o botão de cópia no lado direito da tela para copiar a **CADEIA DE CONEXÃO PRIMÁRIA**.

   ![Exiba e copie a CADEIA DE CONEXÃO PRIMÁRIA no painel Cadeia de Conexão](./media/create-table-dotnet/connection-string.png)

2. No Visual Studio, abra o arquivo **Settings.json**. 

3. Cole a **CADEIA DE CONEXÃO PRIMÁRIA** do portal no valor de StorageConnectionString. Cole a cadeia de caracteres entre aspas.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Pressione CTRL+S para salvar o arquivo **Settings.json**.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Compilar e implantar o aplicativo

1. No Visual Studio, clique com o botão direito do mouse no projeto **CosmosTableSamples** em **Gerenciador de Soluções** e, depois, clique em **Gerenciar Pacotes NuGet**. 

   ![Gerenciar Pacotes NuGet](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. Na caixa **Procurar** do NuGet, digite Microsoft.Azure.Cosmos.Table. Isso localizará a biblioteca de clientes de API de tabela do Cosmos DB. Observe que essa biblioteca só está disponível atualmente para .NET Framework e .NET Standard. 
   
   ![Guia Procurar do NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Clique em **Instalar** para instalar a biblioteca **Microsoft.Azure.Cosmos.Table**. Isso instala o pacote da API de Tabela do Azure Cosmos DB e todas as dependências.

4. Quando você executa o aplicativo inteiro, os dados de exemplo são inseridos na entidade de tabela e excluídos no fina. Portanto, você não verá nenhum dado inserido se executar o exemplo inteiro. Porém, pode inserir alguns pontos de interrupção para exibir os dados. Abra o arquivo BasicSamples.cs file e clique com o botão direito do mouse na linha 52, selecione **Ponto de interrupção** e, em seguida, selecione **Inserir ponto de interrupção**. Insira outro ponto de interrupção na linha 55.

   ![Adicionar um ponto de interrupção](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Pressione F5 para executar o aplicativo. A janela de console exibe o nome do novo banco de dados de tabela (neste caso, demoa13b1) no Azure Cosmos DB. 
    
   ![Saída do console](media/create-table-dotnet/azure-cosmosdb-console.png)

   Quando atingir o primeiro ponto de interrupção, volte para o Data Explorer no Portal do Azure. Clique no botão **Atualizar**, expanda a tabela demonstração* e clique em **Entidades**. A guia **Entidades** à direita mostra a nova entidade que foi adicionada para Walter Harp. Observe que o número de telefone para a nova entidade é 425-555-0101.

   ![Nova entidade](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Se você receber um erro informando que o arquivo Settings.json não pode ser encontrado ao executar o projeto, será possível resolvê-lo adicionando a seguinte entrada XML às configurações do projeto. Clique com o botão direito do mouse em CosmosTableSamples, selecione Editar CosmosTableSamples.csproj e adicione o seguinte itemGroup: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Feche a guia **Entidades** no Data Explorer.
    
7. Pressione F5 para executar o aplicativo até o próximo ponto de interrupção. 

    Quando você atinge o ponto de interrupção, retorne ao Portal do Azure, clique em **Entidades** novamente para abrir a guia **Entidades** e observe que o número de telefone foi atualizado para 425-555-0105.

8. Pressione F5 para executar o aplicativo. 
 
   O aplicativo adiciona entidades para uso em um aplicativo de exemplo avançado que atualmente não é compatível com a API de Tabela. O aplicativo, em seguida, exclui a tabela criada pelo aplicativo de exemplo.

9. Na janela do console, pressione Enter para encerrar a execução do aplicativo. 
  

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar uma tabela usando o Data Explorer e como executar um aplicativo.  Agora, você pode consultar os dados usando a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de Tabela](table-import.md)

