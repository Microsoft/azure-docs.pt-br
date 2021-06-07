---
title: Importar dados em massa para a conta da API do SQL do Azure Cosmos DB usando o SDK do .NET
description: Saiba como importar ou ingerir dados para o Azure Cosmos DB criando um aplicativo de console do .NET que otimiza a taxa de transferência provisionada (RU/s) necessária para importar dados
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563599"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Importar dados em massa para a conta da API do SQL do Azure Cosmos DB usando o SDK do .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial mostra como criar um aplicativo de console .NET que otimiza a taxa de transferência provisionada (RU/s) necessária para importar dados para o Azure Cosmos DB. Neste artigo, você lerá dados de uma fonte de dados de exemplo e os importará em um contêiner do Azure Cosmos.
Este tutorial usa a [Versão 3.0 e posterior](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do SDK do .NET do Azure Cosmos DB, que pode ser direcionada para o .NET Framework ou o .NET Core.

Este tutorial abrange:

> [!div class="checklist"]
> * Como criar uma conta do Azure Cosmos
> * Configurar seu projeto
> * Conectar-se a uma conta do Azure Cosmos com suporte em massa habilitado
> * Executar uma importação de dados por meio de operações de criação simultânea

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [SDK do .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Você pode verificar qual versão está disponível em seu ambiente executando `dotnet --version`.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta do Azure Cosmos DB

[Crie uma conta da API do SQL do Azure Cosmos DB](create-cosmosdb-resources-portal.md) no portal do Azure portal. Você também pode criar a conta usando o [Emulador do Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Etapa 2: Configurar o projeto .NET

Abra o prompt de comando do Windows ou uma janela do Terminal no computador local. Você executará todos os comandos nas próximas seções no prompt de comando ou no terminal. Execute o novo comando dotnet a seguir para criar um novo aplicativo com o nome *bulk-import-demo*. O parâmetro `--langVersion` define a propriedade *LangVersion* no arquivo de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A saída esperada da compilação deve ser algo como:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Etapa 3: Adicionar o pacote do Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Azure Cosmos DB para .NET Core usando o comando para adicionar pacote do dotnet.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Etapa 4: Obter suas credenciais da conta do Azure Cosmos

O aplicativo de exemplo precisa autenticar sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais da conta do Azure Cosmos seguindo estas etapas:

1.  Entre no [portal do Azure](https://portal.azure.com/).
1.  Navegue até a conta do Azure Cosmos.
1.  Abra o painel **Chaves** e copie o **URI** e a **CHAVE PRIMÁRIA** da sua conta.

Se você estiver usando o Emulador do Azure Cosmos DB, obtenha as [credenciais do emulador deste artigo](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Etapa 5: Inicializar o objeto CosmosClient com suporte para execução em massa

Abra o arquivo `Program.cs` gerado em um editor de código. Você criará uma instância do CosmosClient com a execução em massa habilitada e a usará para realizar operações em relação ao Azure Cosmos DB. 

Vamos começar substituindo o método `Main` padrão e definindo as variáveis globais. Essas variáveis globais incluirão as chaves de ponto de extremidade e de autorização, o nome do banco de dados, o contêiner que você criará e o número de itens que você inserirá na execução em massa. Substitua os valores de chave de autorização e URL de ponto de extremidade de acordo com seu ambiente. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

No método `Main`, adicione o seguinte código para inicializar o objeto CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Depois que a execução em massa estiver habilitada, o CosmosClient agrupará internamente as operações simultâneas em chamadas de serviço individuais. Dessa forma, ele otimiza a utilização da taxa de transferência distribuindo chamadas de serviço entre partições e finalmente atribuindo os resultados individuais aos chamadores originais.

Você pode criar um contêiner para armazenar todos os nossos itens.  Defina `/pk` como a chave de partição, 50 mil RU/s como taxa de transferência provisionada e uma política de indexação personalizada que excluirá todos os campos para otimizar a taxa de transferência de gravação. Adicione o código a seguir após a instrução de inicialização CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Etapa 6: Popular uma lista de tarefas simultâneas

Para usar o suporte à execução em massa, crie uma lista de tarefas assíncronas com base na fonte de dados e nas operações que você deseja executar e use `Task.WhenAll` para executá-las simultaneamente.
Vamos começar usando dados falsos para gerar uma lista de itens com base em nosso modelo de dados. Em um aplicativo do mundo real, os itens seriam provenientes de sua fonte de dados desejada.

Primeiro, adicione o pacote do Bogus à solução usando o comando add package do dotnet.

   ```bash
   dotnet add package Bogus
   ```

Configure a definição dos itens que você deseja salvar. Você precisa definir a classe `Item` dentro do arquivo `Program.cs`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Em seguida, crie uma função auxiliar dentro da classe `Program`. Esta função auxiliar obterá o número de itens que você definiu para serem inseridos e gerará dados aleatórios:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Use a função auxiliar para inicializar uma lista de documentos com os quais trabalhar:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Em seguida, use a lista de documentos para criar tarefas simultâneas e preencha a lista de tarefas para inserir os itens no contêiner. Para executar essa operação, adicione o código a seguir à classe `Program`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Todas essas operações de ponto simultâneas serão executadas em conjunto (ou seja, em massa) conforme descrito na seção de introdução.

## <a name="step-7-run-the-sample"></a>Etapa 7: Executar o exemplo

Você pode executar o exemplo simplesmente pelo comando `dotnet`:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Obter o exemplo completo

Se você não teve tempo para concluir as etapas neste tutorial ou se deseja apenas baixar os exemplos de código, poderá obtê-los no [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Após clonar o projeto, atualize as credenciais desejadas dentro de [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25).

O exemplo pode ser executado alterando o diretório do repositório e usando `dotnet`:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez as seguintes tarefas:

> [!div class="checklist"]
> * Como criar uma conta do Azure Cosmos
> * Configurar seu projeto
> * Conectar-se a uma conta do Azure Cosmos com suporte em massa habilitado
> * Executar uma importação de dados por meio de operações de criação simultânea

Agora você pode passar para o próximo tutorial:

> [!div class="nextstepaction"]
>[Consultar o Azure Cosmos DB usando a API do SQL](tutorial-query-sql-api.md)