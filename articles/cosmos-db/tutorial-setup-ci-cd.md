---
title: Configurar o pipeline de CI/CD com a tarefa de Build do emulador Azure Cosmos DB
description: Tutorial sobre como configurar o fluxo de trabalho de build e versão no Azure DevOps usando a tarefa de build do emulador do Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: c7246511a88e2d2756a8ef56c5adf51ddbfd3e58
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560524"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurar um pipeline de CI/CD com a tarefa de Build do emulador Azure Cosmos DB no Azure DevOps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. O emulador permite desenvolver e testar seu aplicativo localmente sem criar uma assinatura do Azure ne, incorrer em custos. 

A tarefa de Build do emulador Azure Cosmos DB para o Azure DevOps permite que você faça o mesmo em um ambiente de CI. Com a tarefa de build, você pode executar testes no emulador como parte dos seus fluxos de trabalho de build e versão. A tarefa gira um contêiner do Docker com o emulador já em execução e fornece um ponto de extremidade que pode ser usado pelo restante da definição de build. Você pode criar e iniciar quantas instâncias do emulador precisar, cada uma executando em um contêiner separado. 

Este artigo demonstra como configurar um pipeline de CI no Azure DevOps para um aplicativo ASP.NET que usa a tarefa de build do emulador do Cosmos DB para executar testes. Você pode usar uma abordagem semelhante para configurar um pipeline de CI para um aplicativo do Node.js ou Python. 

## <a name="install-the-emulator-build-task"></a>Instalar a tarefa de build do emulador

Para usar a tarefa de build, primeiro precisamos instalá-la em nossa organização do Azure DevOps. Localize a extensão **Emulador do Azure Cosmos DB** no [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e clique em **Obter gratuitamente.**

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Localizar e instalar a tarefa de build do Emulador do Azure Cosmos DB no Marketplace do Azure DevOps":::

Em seguida, escolha a organização na qual instalar a extensão. 

> [!NOTE]
> Para instalar uma extensão para uma organização do Azure DevOps, você deve ser um proprietário da conta ou administrador de coleção de projeto. Se você não tiver permissões, mas for um membro da conta, poderá solicitar as extensões em vez disso. [Saiba mais.](/azure/devops/marketplace/faq-extensions)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Escolha uma organização do Azure DevOps na qual instalar uma extensão":::

## <a name="create-a-build-definition"></a>Criar a definição de build

Agora que a extensão está instalada, entre em sua organização do Azure DevOps e encontre o projeto no painel de projetos. Você pode adicionar um [pipeline de build](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav) ao projeto ou modificar um pipeline de build existente. Se você já tiver um pipeline de build, pule para [Adicionar a tarefa de build do Emulador a uma definição de build](#addEmulatorBuildTaskToBuildDefinition).

1. Para criar uma nova definição de build, navegue até a guia **Builds** no Azure DevOps. Selecione **+Novo.** \> **Novo pipeline de build**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Criar um pipeline de build":::

2. Selecione a **fonte**, o **projeto de equipe**, o **repositório** e o **branch Padrão desejados para builds manuais e agendados**. Depois de escolher as opções necessárias, selecione **Continuar**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Selecionar o projeto de equipe, o repositório e o branch para o pipeline de build":::

3. Por fim, selecione o modelo desejado para o pipeline de build. Vamos selecionar o modelo do **ASP.NET** neste tutorial. Agora você tem um pipeline de compilação que pode ser configurado para usar a tarefa de Build de Azure Cosmos DB Emulator. 

> [!NOTE]
> O pool de agente a ser selecionado para este CI deve ter o Docker for Windows instalado, a menos que a instalação seja feita manualmente em uma tarefa anterior como parte do CI. Confira o artigo [Agentes hospedados da Microsoft](/azure/devops/pipelines/agents/hosted?tabs=yaml) para ver uma seleção de pools de agentes. Recomendamos começar com `Hosted VS2017`.

Atualmente, o emulador de Azure Cosmos DB não dá suporte ao pool de agentes VS2019 hospedado. No entanto, o emulador já vem com o VS2019 instalado e você o usa iniciando o emulador com os cmdlets do PowerShell a seguir. Se tiver problemas com o uso do VS2019, entre em contato com a equipe do [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) para obter ajuda:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Adicionar a tarefa a um pipeline de build

1. Antes de adicionar uma tarefa ao pipeline de build, você deve adicionar um trabalho de agente. Navegue até o pipeline de build, selecione o **...** e escolha **Adicionar um trabalho de agente**.

1. Em seguida, selecione o símbolo **+** ao lado do trabalho de agente para adicionar a tarefa de build do emulador. Pesquise **cosmos** na caixa de pesquisa, selecione **Emulador do Azure Cosmos DB** e adicione-o ao trabalho de agente. A tarefa de build iniciará um contêiner com uma instância do emulador do Cosmos DB já em execução. A tarefa do emulador do Azure Cosmos DB deve ser colocada antes de outras tarefas que estejam esperando o emulador ficar em execução.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Adicionar a tarefa de build do Emulador à definição de build":::

Neste tutorial, você adicionará a tarefa ao início para fazer com que o emulador esteja disponível antes da execução de nossos testes.

### <a name="add-the-task-using-yaml"></a>Adicionar a tarefa usando YAML

Essa etapa é opcional e só será necessária se você estiver configurando o pipeline de CI/CD usando uma tarefa de YAML. Nesses casos, defina a tarefa de YAML conforme mostrado no código a seguir:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Configurar testes para usar o emulador

Agora, vamos configurar nossos testes para usar o emulador. A tarefa de build do emulador exporta uma variável de ambiente – 'CosmosDbEmulator.Endpoint' – para a qual qualquer tarefa no pipeline de build pode emitir solicitações. 

Neste tutorial, vamos usar a [tarefa de Teste do Visual Studio](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) para executar testes de unidade configuradas por meio de um arquivo **.RunSettings**. Para saber mais sobre a configuração do teste de unidade, leia a [documentação](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017). O exemplo de código do aplicativo Todo completo usado neste documento está disponível no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Abaixo está um exemplo de um arquivo **.RunSettings** que define os parâmetros a serem passados para os testes de unidade do aplicativo. Observe que a variável `authKey` usada é a [chave bem conhecida](./local-emulator.md#authenticate-requests) para o emulador. Esta `authKey` é a chave esperada pela tarefa de build do emulador e deve ser definida em seu arquivo **.RunSettings**.

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

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Substituir a variável de ponto de extremidade pelo ponto de extremidade da tarefa de build do Emulador":::

## <a name="run-the-build"></a>Executar a compilação

Agora, **salve e coloque o build na fila**. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Captura de tela mostra um Build com a fila salvar & selecionada.":::

Depois que o build tiver sido iniciado, observe que a tarefa do emulador do Cosmos DB começou a obter a imagem do Docker com o emulador instalado. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Captura de tela mostra a tarefa de emulador Cosmos D B que está sendo puxada.":::

Depois que o build for concluído, observe que seus testes são aprovados, todos em execução no emulador do Cosmos DB da tarefa de build!

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Captura de tela mostra o valor de progressão na guia Resumo.":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o emulador para teste e desenvolvimento locais, veja [Usar o emulador do Azure Cosmos DB para desenvolvimento e teste locais](./local-emulator.md).

Para exportar certificados TLS/SSL do emulador, confira [Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js](./local-emulator-export-ssl-certificates.md)
