---
title: Configurar o pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB
description: Tutorial sobre como configurar o fluxo de trabalho de build e versão no Azure DevOps usando a tarefa de build do emulador do Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: dad99a7e3d0463263e912aa05b5312edbcb89c0b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597678"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurar um pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB no Azure DevOps

O emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. O emulador permite desenvolver e testar seu aplicativo localmente sem criar uma assinatura do Azure ne, incorrer em custos. 

A tarefa de build do emulador do Azure Cosmos DB para o Azure DevOps permite que você faça o mesmo em um ambiente de CI. Com a tarefa de build, você pode executar testes no emulador como parte dos seus fluxos de trabalho de build e versão. A tarefa gira um contêiner do Docker com o emulador já em execução e fornece um ponto de extremidade que pode ser usado pelo restante da definição de build. Você pode criar e iniciar quantas instâncias do emulador precisar, cada uma executando em um contêiner separado. 

Este artigo demonstra como configurar um pipeline de CI no Azure DevOps para um aplicativo ASP.NET que usa a tarefa de build do emulador do Cosmos DB para executar testes. Você pode usar uma abordagem semelhante para configurar um pipeline de CI para um aplicativo do Node.js ou Python. 

## <a name="install-the-emulator-build-task"></a>Instalar a tarefa de build do emulador

Para usar a tarefa de build, primeiro precisamos instalá-la em nossa organização do Azure DevOps. Localize a extensão **Emulador do Azure Cosmos DB** no [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e clique em **Obter gratuitamente.**

![Localizar e instalar a tarefa de build do Emulador do Azure Cosmos DB no Marketplace do Azure DevOps](./media/tutorial-setup-ci-cd/addExtension_1.png)

Em seguida, escolha a organização na qual instalar a extensão. 

> [!NOTE]
> Para instalar uma extensão para uma organização do Azure DevOps, você deve ser um proprietário da conta ou administrador de coleção de projeto. Se você não tiver permissões, mas for um membro da conta, poderá solicitar as extensões em vez disso. [Saiba mais.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Escolha uma organização do DevOps do Azure na qual instalar uma extensão](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Criar a definição de build

Agora que a extensão está instalada, entre em sua conta do Azure DevOps e encontre o projeto no painel de projetos. Você pode adicionar um [pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) ao projeto ou modificar um pipeline de build existente. Se você já tiver um pipeline de build, pule para [Adicionar a tarefa de build do Emulador a uma definição de build](#addEmulatorBuildTaskToBuildDefinition).

1. Para criar uma nova definição de build, navegue até a guia **Builds** no Azure DevOps. Selecione **+Novo.** \> **Novo pipeline de build**

   ![Criar um novo pipeline de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Selecione a **fonte**, o **projeto de equipe**, o **repositório**e o **branch Padrão desejados para builds manuais e agendados**. Depois de escolher as opções necessárias, selecione **Continuar**

   ![Selecionar o projeto de equipe, o repositório e o branch para o pipeline de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Por fim, selecione o modelo desejado para o pipeline de build. Vamos selecionar o modelo do **ASP.NET** neste tutorial. 

> [!NOTE]
> O pool de agente a ser selecionado para este CI deve ter o Docker for Windows instalado, a menos que a instalação seja feita manualmente em uma tarefa anterior como parte do CI. Consulte o artigo [Agentes hospedados da Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) para ver uma seleção de pools de agente; é recomendável começar com `Hosted VS2017` ou `Hosted VS2019`. 

Agora temos um pipeline de build que podemos configurar para usar a tarefa de build do emulador do Azure Cosmos DB. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Adicionar a tarefa a um pipeline de build

1. Antes de adicionar uma tarefa ao pipeline de build, você deve adicionar um trabalho de agente. Navegue até o pipeline de build, selecione o **...** e escolha **Adicionar um trabalho de agente**.

1. Em seguida, selecione o símbolo **+** ao lado do trabalho de agente para adicionar a tarefa de build do emulador. Pesquise **cosmos** na caixa de pesquisa, selecione **Emulador do Azure Cosmos DB** e adicione-o ao trabalho de agente. A tarefa de build iniciará um contêiner com uma instância do emulador do Cosmos DB já em execução. A tarefa do emulador do Azure Cosmos DB deve ser colocada antes de outras tarefas que estejam esperando o emulador ficar em execução.

   ![Adicionar a tarefa de build do Emulador à definição de build](./media/tutorial-setup-ci-cd/addExtension_3.png)

Neste tutorial, você adicionará a tarefa ao início para fazer com que o emulador esteja disponível antes da execução de nossos testes.

## <a name="configure-tests-to-use-the-emulator"></a>Configurar testes para usar o emulador

Agora, vamos configurar nossos testes para usar o emulador. A tarefa de build do emulador exporta uma variável de ambiente – 'CosmosDbEmulator.Endpoint' – para a qual qualquer tarefa no pipeline de build pode emitir solicitações. 

Neste tutorial, vamos usar a [tarefa de Teste do Visual Studio](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) para executar testes de unidade configuradas por meio de um arquivo **.RunSettings**. Para saber mais sobre a configuração do teste de unidade, leia a [documentação](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). O exemplo de código do aplicativo Todo completo usado neste documento está disponível no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Abaixo está um exemplo de um arquivo **.RunSettings** que define os parâmetros a serem passados para os testes de unidade do aplicativo. Observe que a variável `authKey` usada é a [chave bem conhecida](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) para o emulador. Esta `authKey` é a chave esperada pela tarefa de build do emulador e deve ser definida em seu arquivo **.RunSettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Se você estiver configurando um pipeline de CI/CD para um aplicativo que usa a API do Azure Cosmos DB para o MongoDB, a cadeia de conexão incluirá por padrão o número da porta 10255. No entanto, essa porta não está aberta no momento, como uma alternativa, você deve usar a porta 10250 para estabelecer a conexão. A cadeia de conexão da API do Azure Cosmos DB para MongoDB permanece a mesma, com a exceção de que o número da porta compatível é 10250, em vez de 10255.

Estes parâmetros `TestRunParameters` são referenciados por meio de uma propriedade `TestContext` no projeto de teste do aplicativo. Aqui está um exemplo de um teste executado no Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Navegue até as Opções de Execução na tarefa de Teste do Visual Studio. Na opção **Arquivo de configurações**, especifique que os testes são configurados usando o arquivo **.RunSettings**. Na opção **Substituir parâmetros de execução de teste**, adicione `-endpoint $(CosmosDbEmulator.Endpoint)`. Fazer isso configurará a tarefa de Teste para referir-se ao ponto de extremidade da tarefa de build do emulador, em vez daquele definido no arquivo **.RunSettings**.  

![Substituir a variável de ponto de extremidade com o ponto de extremidade de tarefa de build do Emulador](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Executar a compilação

Agora, **salve e coloque o build na fila**. 

![Salvar e executar o build](./media/tutorial-setup-ci-cd/runBuild_1.png)

Depois que o build tiver sido iniciado, observe que a tarefa do emulador do Cosmos DB começou a obter a imagem do Docker com o emulador instalado. 

![Salvar e executar o build](./media/tutorial-setup-ci-cd/runBuild_4.png)

Depois que o build for concluído, observe que seus testes são aprovados, todos em execução no emulador do Cosmos DB da tarefa de build!

![Salve e execute o build](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o emulador para teste e desenvolvimento locais, veja [Usar o emulador do Azure Cosmos DB para desenvolvimento e teste locais](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Para exportar certificados SSL do emulador, veja [Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
