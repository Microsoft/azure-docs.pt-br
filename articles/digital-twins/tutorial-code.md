---
title: 'Tutorial: Codificar um aplicativo cliente'
titleSuffix: Azure Digital Twins
description: Tutorial para escrever o código mínimo de um aplicativo cliente usando o SDK do .NET (C#).
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102424454"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Tutorial: Codificação com as APIs dos Gêmeos Digitais do Azure

É comum que os desenvolvedores que trabalham com os Gêmeos Digitais do Azure escrevam um aplicativo cliente para interagir com a instância do serviço Gêmeos Digitais do Azure. Este tutorial voltado para o desenvolvedor fornece uma introdução à programação no serviço Gêmeos Digitais do Azure usando o [SDK dos Gêmeos Digitais do Azure para .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). Ele mostra como escrever um aplicativo cliente de console C# passo a passo do zero.

> [!div class="checklist"]
> * Configurar o projeto
> * Introdução ao código do projeto   
> * Exemplo de código completo
> * Limpar os recursos
> * Próximas etapas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial usa a linha de comando para a instalação e o trabalho do projeto. Portanto, você pode usar qualquer editor de códigos para acompanhar os exercícios.

O que você precisa para começar:
* Qualquer editor de códigos
* **.NET Core 3.1** no computador de desenvolvimento. Baixe esta versão do SDK do .NET Core para várias plataformas em [Baixar o .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparar uma instância dos Gêmeos Digitais do Azure

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Configurar o projeto

Quando estiver pronto para começar a usar sua instância dos Gêmeos Digitais do Azure, inicie a configuração do projeto de aplicativo cliente. 

Abra um prompt de comando ou outra janela do console no computador e crie um diretório de projeto vazio no qual deseja armazenar seu trabalho durante este tutorial. Nomeie o diretório como desejar (por exemplo, *DigitalTwinsCodeTutorial*).

Acesse o novo diretório.

Quando estiver no diretório do projeto, **crie um projeto de aplicativo de console .NET vazio**. Na janela de comando, é possível executar o seguinte comando a fim de criar um projeto C# mínimo para o console:

```cmd/sh
dotnet new console
```

Isso criará vários arquivos dentro do diretório, incluindo um chamado *Program.cs*, no qual você escreverá a maior parte do código.

Mantenha a janela Comando aberta, pois você continuará usando-a em todo o tutorial.

Depois, **adicione duas dependências ao seu projeto**, que serão necessárias para trabalhar com os Gêmeos Digitais do Azure. A primeira é o pacote do [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client), e a segunda fornece ferramentas para ajudar com a autenticação no Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Introdução ao código do projeto

Nesta seção, você começará a escrever o código do novo projeto de aplicativo para trabalhar com os Gêmeos Digitais do Azure. As ações abordadas incluem:
* Autenticação no serviço
* Upload de um modelo
* Captura de erros
* Criação de gêmeos digitais
* Criação de relações
* Consulta de gêmeos digitais

Também há uma seção que mostra o código completo no final do tutorial. Use isso como uma referência para verificar seu programa durante as etapas.

Para começar, abra o arquivo *Program.cs* em qualquer editor de códigos. Você verá um modelo com o mínimo de código que terá a seguinte aparência:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Um snippet de código de exemplo. Há uma instrução 'using System;', um namespace chamado DigitalTwinsCodeTutorial; uma classe no namespace chamada Program; e um método Main na classe com uma assinatura padrão de 'static void MainMain(string[] args)'. O método main contém uma instrução print Olá, Mundo." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Primeiro, adicione algumas linhas `using` à parte superior do código para extrair as dependências necessárias.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Em seguida, você adicionará o código a esse arquivo para preencher algumas funcionalidades. 

### <a name="authenticate-against-the-service"></a>Autenticar-se no serviço

A primeira coisa que o seu aplicativo precisará fazer é se autenticar no serviço Gêmeos Digitais do Azure. Em seguida, você poderá criar uma classe de cliente de serviço para acessar as funções do SDK.

Para se autenticar, você precisa de *hostName* da instância dos Gêmeos Digitais do Azure.

Em *Program.cs*, cole o código a seguir abaixo da linha de cópia impressa "Olá, mundo!" no método `Main`. Defina o valor de `adtInstanceUrl` como o *hostName* da instância dos Gêmeos Digitais do Azure.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Salve o arquivo. 

Na janela Comando, execute o código com este comando: 

```cmd/sh
dotnet run
```

Isso vai restaurar as dependências na primeira execução e executar o programa. 
* Se nenhum erro ocorrer, o programa imprimirá *Cliente de serviço criado – pronto para uso*.
* Como ainda não há nenhum tratamento de erro neste projeto, se houver problemas, você verá uma exceção gerada pelo código.

### <a name="upload-a-model"></a>Carregar um modelo

Os Gêmeos Digitais do Azure não têm um vocabulário de domínio intrínseco. Os tipos de elementos no seu ambiente que podem ser representados nos Gêmeos Digitais do Azure são definidos por você por meio de **modelos**. Os [modelos](concepts-models.md) são semelhantes às classes nas linguagens de programação orientadas a objeto; elas fornecem modelos definidos pelo usuário para os [gêmeos digitais](concepts-twins-graph.md) seguirem e criarem uma instância deles posteriormente. Eles são escritos em uma linguagem semelhante a JSON chamada **DTDL (Digital Twins Definition Language)** .

A primeira etapa na criação de uma solução dos Gêmeos Digitais do Azure é definir, pelo menos, um modelo em um arquivo DTDL.

No diretório em que você criou o projeto, crie um arquivo *.json* chamado *SampleModel.json*. Cole-o no corpo do seguinte arquivo: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Se você estiver usando o Visual Studio para este tutorial, o ideal será selecionar o arquivo JSON recém-criado e definir a propriedade *Copiar para o Diretório de Saída* na inspeção de propriedade como *Copiar se for o Mais Recente* ou *Sempre Copiar*. Isso permitirá que o Visual Studio localize o arquivo JSON com o caminho padrão quando você executar o programa com **F5** durante o restante do tutorial.

> [!TIP] 
> Há uma [amostra de Validador DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) independente de linguagem que pode ser usada para verificar os documentos do modelo e ver se a DTDL é válida. Ela se baseia na biblioteca do analisador de DTDL, que é explicada mais detalhadamente em [*Como analisar e validar modelos*](how-to-parse-models.md).

Em seguida, adicione mais um código a *Program.cs* para carregar o modelo recém-criado na sua instância dos Gêmeos Digitais do Azure.

Primeiro, adicione algumas instruções `using` à parte superior do arquivo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Em seguida, prepare-se para usar os métodos assíncronos no SDK do serviço C#, alterando a assinatura do método `Main` para permitir a execução assíncrona. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> O uso de `async` não é estritamente necessário, pois o SDK também fornece versões síncronas de todas as chamadas. Este tutorial ensina o uso de `async`.

Em seguida, vem o primeiro trecho de código que interage com o serviço Gêmeos Digitais do Azure. Esse código carrega o arquivo DTDL que você criou no disco e carrega-o na instância do serviço Gêmeos Digitais do Azure. 

Cole o código a seguir no código de autorização adicionado anteriormente.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Na janela Comando, execute o programa com este comando: 

```cmd/sh
dotnet run
```
"Carregar um modelo" será impresso na saída, indicando que esse código foi atingido, mas ainda não há nenhuma saída para indicar se o upload foi bem-sucedido.

Para adicionar uma instrução de impressão mostrando todos os modelos que foram carregados com êxito na instância, adicione o seguinte código logo após a seção anterior:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Antes de executar o programa novamente para testar esse novo código**, lembre-se de que na última vez que você executou o programa, você já tinha carregado o modelo. Os Gêmeos Digitais do Azure não permitem que você carregue o mesmo modelo duas vezes. Sendo assim, se você tentar carregar o mesmo modelo novamente, o programa deverá lançar uma exceção.

Com isso em mente, execute o programa novamente com este comando na janela Comando:

```cmd/sh
dotnet run
```

O programa deverá gerar uma exceção. Quando você tenta carregar um modelo que já foi carregado, o serviço retorna um erro de "solicitação inválida" por meio da API REST. Como resultado, o SDK do cliente dos Gêmeos Digitais do Azure, por sua vez, vai gerar uma exceção para cada código de retorno do serviço diferente de êxito. 

A próxima seção aborda exceções como essa e como tratá-las no código.

### <a name="catch-errors"></a>Capturar erros

Para impedir a falha do programa, adicione um código de exceção ao código de upload do modelo. Encapsule a chamada de cliente existente `await client.CreateModelsAsync(typeList)` em um manipulador try/catch desta forma:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Se você executar o programa com `dotnet run` na janela Comando agora, receberá um código de erro. A saída do código de criação do modelo mostra este erro:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Saída do programa mostrando uma mensagem que indica '409: falha na solicitação de serviço. Status: 409 (Conflito).', seguido de uma impressão do erro indicando que dtmi:example:SampleModel;1 já existe":::

Daqui em diante, o tutorial encapsulará todas as chamadas a métodos de serviço em manipuladores try/catch.

### <a name="create-digital-twins"></a>Criar gêmeos digitais

Agora que você carregou um modelo nos Gêmeos Digitais do Azure, use essa definição de modelo para criar **gêmeos digitais**. Os [gêmeos digitais](concepts-twins-graph.md) são instâncias de um modelo e representam as entidades no seu ambiente de negócios – itens como sensores em um farm, salas em um prédio ou luzes em um carro. Esta seção cria alguns gêmeos digitais com base no modelo carregado anteriormente.

Adicione o código a seguir ao final do método `Main` para criar e inicializar três gêmeos digitais com base nesse modelo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Na janela Comando, execute o programa com `dotnet run`. Na saída, procure as mensagens de impressão que indicam que *sampleTwin-0*, *sampleTwin-1* e *sampleTwin-2* foram criados. 

Depois, execute o programa novamente. 

Observe que nenhum erro é gerado quando os gêmeos são criados na segunda vez, mesmo que eles já existam após a primeira execução. Ao contrário da criação do modelo, a criação de gêmeos é, no nível da REST, uma chamada *PUT* com a semântica de *upsert*. Isso significa que, se já houver um gêmeo, uma tentativa de criar o mesmo gêmeo novamente apenas substituirá o gêmeo original. Nenhum erro será gerado.

### <a name="create-relationships"></a>Criar relações

Em seguida, você pode criar **relações** entre os gêmeos criados para conectá-los a um **grafo de gêmeos**. Os [grafos de gêmeos](concepts-twins-graph.md) são usados para representar todo o ambiente.

Adicione um **novo método estático** à classe `Program`, abaixo do método `Main` (o código agora tem dois métodos):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Em seguida, adicione o seguinte código ao final do método `Main` para chamar o método `CreateRelationship` e usar o código que você acabou de escrever:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Na janela Comando, execute o programa com `dotnet run`. Na saída, procure instruções de impressão indicando que as duas relações foram criadas com êxito.

Observe que os Gêmeos Digitais do Azure não permitirão que você crie uma relação se já existir outra com a mesma ID. Portanto, se você executar o programa várias vezes, receberá exceções na criação de relação. Este código captura as exceções e as ignora. 

### <a name="list-relationships"></a>Listar relações

O próximo código que você adicionará permite ver a lista de relações criadas.

Adicione o seguinte **novo método** à classe `Program`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Em seguida, adicione o seguinte código ao final do método `Main` para chamar o código `ListRelationships`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Na janela Comando, execute o programa com `dotnet run`. Você verá uma lista de todas as relações criadas em uma instrução de saída parecida com esta:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Saída do programa mostrando a mensagem 'O gêmeo sampleTwin-0 está conectado a: contains->sampleTwin-1, -contains->sampleTwin-2'" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Consultar os gêmeos digitais

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente.

A última seção de código a ser adicionada neste tutorial executa uma consulta na instância dos Gêmeos Digitais do Azure. A consulta usada neste exemplo retorna todos os gêmeos digitais na instância.

Adicione esta instrução `using` para permitir o uso da classe `JsonSerializer` a fim de ajudar a apresentar as informações do gêmeo digital:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Depois, adicione o seguinte código ao final do método `Main`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Na janela Comando, execute o programa com `dotnet run`. Você deverá ver todas os gêmeos digitais nessa instância na saída.

## <a name="complete-code-example"></a>Exemplo de código completo

Neste ponto do tutorial, você tem um aplicativo cliente completo, que pode executar ações básicas nos Gêmeos Digitais do Azure. Para referência, o código completo do programa em *Program.cs* é listado abaixo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir este tutorial, você poderá escolher quais recursos gostaria de remover, dependendo do que você gostaria de fazer em seguida.

* **Se você planeja prosseguir para o próximo tutorial**, a instância usada neste tutorial pode ser reutilizada no próximo. Você pode manter os recursos dos Gêmeos Digitais do Azure configurados aqui e ignorar o restante desta seção.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Talvez seja interessante excluir a pasta do projeto do computador local.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo cliente de console .NET do zero. Você escreveu o código para esse aplicativo cliente a fim de executar as ações básicas em uma instância dos Gêmeos Digitais do Azure.

Prossiga para o próximo tutorial e explore as coisas que você pode fazer com um aplicativo cliente de exemplo: 

> [!div class="nextstepaction"]
> [*Tutorial: Explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md)
