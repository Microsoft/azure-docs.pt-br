---
title: Gerenciar o grafo gêmeo com relacionamentos
titleSuffix: Azure Digital Twins
description: Veja como gerenciar um grafo de gêmeos digital por meio de uma conexão com relações.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102433311"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerenciar um grafo de gêmeos digital usando relações

O núcleo dos Gêmeos Digitais do Azure é o [grafo de gêmeos](concepts-twins-graph.md), que representa todo o ambiente. O grafo é formado por gêmeos digitais individuais conectados por meio de **relações**. 

Depois criar uma [instância dos Gêmeos Digitais do Azure](how-to-set-up-instance-portal.md) e configurar o código de [autenticação](how-to-authenticate-client.md) no aplicativo cliente, você pode usar as [**APIs de Gêmeos Digitais**](/rest/api/digital-twins/dataplane/twins) para criar, modificar e excluir gêmeos digitais e as relações entre eles na instância dos Gêmeos Digitais do Azure. Você também pode usar o [SDK do .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client) ou a [CLI dos Gêmeos Digitais do Azure](how-to-use-cli.md).

O foco deste artigo é o gerenciamento de relações e do grafo como um todo. Para trabalhar com gêmeos digitais individuais, confira [*Instruções: gerenciar gêmeos digitais*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Maneiras de gerenciar o grafo

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Você também pode fazer alterações no grafo usando o exemplo do Azure Digital Twins Explorer, que permite visualizar os gêmeos e o grafo e usa o SDK nos bastidores. A próxima seção descreve essa amostra em detalhes.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relações

As relações descrevem a conexão entre gêmeos digitais, que forma a base do grafo de gêmeos.

As relações são criadas usando a chamada `CreateOrReplaceRelationshipAsync()`. 

Para criar uma relação, você precisa especificar:
* A ID do gêmeo de origem (`srcId` no exemplo de código abaixo): a ID do gêmeo que é a origem da relação.
* A ID do gêmeo de destino (`targetId` no exemplo de código abaixo): a ID do gêmeo que é o destino da relação.
* O nome da relação (`relName` no exemplo de código abaixo): o tipo genérico de relação, algo como _contém_.
* A ID da relação (`relId` no exemplo de código abaixo): o nome específico da relação, algo como _Relação1_.

A ID da relação deve ser exclusiva dentro do gêmeo de origem. Ela não precisa ser globalmente exclusiva.
Por exemplo, para o gêmeo *foo*, cada ID da relação específica deve ser exclusiva. No entanto, outro gêmeo, *bar*, pode ter uma relação de saída com a mesma ID de uma relação de *foo*.

O exemplo de código a seguir mostra como criar relações em uma instância dos Gêmeos Digitais do Azure. O exemplo usa a chamada ao SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Essa função personalizada agora pode ser chamada para criar uma relação _Contém_ como esta: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Para criar mais de uma relação, repita as chamadas ao mesmo método passando tipos de relação diferentes como argumento. 

Veja mais informações sobre a classe auxiliar `BasicRelationship` em [*Instruções: usar as APIs e os SDKs dos Gêmeos Digitais do Azure*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Criar mais de uma relação entre gêmeos

As relações podem ser classificadas como: 

* Relações de saída: relações que pertencem a este gêmeo e apontam para fora, formando conexão com outros gêmeos. O método `GetRelationshipsAsync()` é usado para obter as relações de saída de um gêmeo.
* Relações de entrada: relações que pertencem a outros gêmeos e apontam para este gêmeo, criando um link de "entrada". O método `GetIncomingRelationshipsAsync()` é usado para obter as relações de entrada de um gêmeo.

Não há restrição quanto ao número de relações entre dois gêmeos. Você pode ter quantas relações entre gêmeos desejar. 

Isso significa que você pode expressar vários tipos diferentes de relações entre dois gêmeos ao mesmo tempo. Por exemplo, o *Gêmeo A* pode ter tanto a relação *armazenado* quanto a relação *fabricado* com o *Gêmeo B*.

Você pode até mesmo criar mais de uma instância do mesmo tipo de relação entre os mesmos gêmeos, caso queira. Neste exemplo, o *Gêmeo A* pode ter duas relações *armazenado* diferentes com o *Gêmeo B*, contanto que as relações tenham IDs diferentes.

## <a name="list-relationships"></a>Listar relações

Para acessar a lista de relações de **saída** de um determinado gêmeo no grafo, você pode usar o método `GetRelationships()` desta forma:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Isso retorna `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>`, dependendo se você usa a versão síncrona ou assíncrona da chamada.

Veja um exemplo que recupera uma lista de relações. O exemplo usa a chamada ao SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Agora você pode chamar esse método personalizado para ver as relações de saída dos gêmeos desta forma:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Você pode usar as relações recuperadas para navegar para outros gêmeos no grafo. Para fazer isso, leia o campo `target` da relação retornada e use-o como a ID para a próxima chamada a `GetDigitalTwin()`.

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Localizar as relações de entrada de um gêmeo digital

Os Gêmeos Digitais do Azure também têm uma API para localizar todas as relações de **entrada** de um determinado gêmeo. Isso geralmente é útil para navegação reversa ou ao excluir gêmeos.

>[!NOTE]
> As chamadas a `IncomingRelationship` não retornam o corpo completo da relação. Veja mais informações sobre a classe `IncomingRelationship` na [documentação de referência](/dotnet/api/azure.digitaltwins.core.incomingrelationship).

O exemplo de código da seção anterior identifica as relações de saída de um gêmeo. O exemplo a seguir tem uma estrutura semelhante, mas identifica as relações de *entrada* do gêmeo. O exemplo também usa a chamada ao SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Agora você pode chamar esse método personalizado para ver as relações de entrada dos gêmeos desta forma:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Exibir todas as propriedades e relações dos gêmeos

Usando os métodos acima para exibir as relações de saída e de entrada do gêmeo, você pode criar um método que exibe todas as informações dele, incluindo as propriedades e os dois tipos de relações. Este é um exemplo de método personalizado que mostra como combinar os métodos personalizados acima para essa finalidade.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Agora você pode chamar essa função personalizada desta forma: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Atualizar relações

Use o método `UpdateRelationship` para editar relações. 

>[!NOTE]
>Ele serve para editar as **propriedades** da relação. Se você precisar alterar o gêmeo de origem ou de destino da relação, [exclua a relação](#delete-relationships) e [crie-a novamente](#create-relationships) com os novos gêmeos.

Os parâmetros necessários da chamada do cliente são a ID do gêmeo de origem da relação, a ID da relação a ser atualizada e um documento de [patch JSON](http://jsonpatch.com/) com as propriedades e os novos valores para a edição.

Veja um exemplo de código que mostra como usar o método. O exemplo usa a chamada ao SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Veja um exemplo de chamada ao método personalizado, passando um documento de patch de JSON com as informações para atualizar uma propriedade.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Excluir relações

O primeiro parâmetro especifica o gêmeo de origem da relação. O outro parâmetro é a ID da relação. É necessário usar tanto a ID do gêmeo quanto a ID da relação porque as IDs de relação são exclusivas apenas no escopo de um gêmeo.

Veja um exemplo de código que mostra como usar o método. O exemplo usa a chamada ao SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Agora você pode chamar esse método personalizado para excluir uma relação desta forma:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Exemplo de grafo de gêmeo executável

O snippet de código executável a seguir usa as operações de relação deste artigo para criar um grafo de gêmeos digitais e relações.

### <a name="set-up-the-runnable-sample"></a>Configurar a amostra executável

O snippet usa as definições de modelo [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) de [*Tutorial: explorar os Gêmeos Digitais do Azure com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Você pode usar esses links para acessar diretamente os arquivos ou baixá-los como parte do projeto de exemplo completo [aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Siga estas etapas antes de executar a amostra:
1. Baixe os arquivos do modelo, coloque-os no seu projeto e substitua os espaços reservados `<path-to>` no código abaixo com a localização deles.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome do host da instância dos Gêmeos Digitais do Azure.
3. Adicione duas dependências ao seu projeto, que serão necessárias para trabalhar com os Gêmeos Digitais do Azure. A primeira é o pacote do [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client), e a segunda fornece ferramentas para ajudar com a autenticação no Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Você também precisará configurar as credenciais locais se desejar executar a amostra diretamente. A próxima seção descreve isso.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Execute o exemplo

Depois de concluir as etapas acima, você pode executar diretamente o exemplo de código a seguir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Aqui está a saída do console do programa acima: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Saída do console mostrando os detalhes e as relações de entrada e saída dos gêmeos." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> O grafo de gêmeos é um conceito de criação de relações entre gêmeos. Para ver a representação visual do grafo de gêmeos, confira a seção [*Visualização*](how-to-manage-graph.md#visualization) deste artigo. 

## <a name="create-graph-from-a-csv-file"></a>Criar grafos usando arquivos CSV

Em casos de uso prático, muitas vezes, as hierarquias de gêmeos são criadas usando dados armazenados em um banco de dados diferente, uma planilha ou um arquivo CSV. Esta seção mostra como ler os dados de arquivos CSV para criar grafos de gêmeos.

Considere a tabela de dados a seguir, que descreve um conjunto de gêmeos digitais e relações.

|  ID do Modelo    | ID de gêmeo (deve ser exclusiva) | Nome da Relação  | ID do gêmeo de destino  | Dados de inicialização de gêmeos |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | contém | Room1 | |
| dtmi:example:Floor;1    | Floor0 | contém | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature": 80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature": 70} |

Uma forma de inserir os dados nos Gêmeos Digitais do Azure é converter a tabela em um arquivo CSV e criar código para interpretar o arquivo em comandos que criam os gêmeos e as relações. O exemplo de código a seguir mostra como ler os dados do arquivo CSV e criar um grafo de gêmeos nos Gêmeos Digitais do Azure.

No código abaixo, o arquivo CSV é chamado de *data.csv*, e há um espaço reservado que representa o **nome do host** da instância dos Gêmeos Digitais do Azure. O exemplo também usa vários pacotes que você pode adicionar ao seu projeto para ajudar no processo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre consultas a grafos de gêmeos dos Gêmeos Digitais do Azure:
* [*Conceitos: linguagem de consulta*](concepts-query-language.md)
* [*Instruções: consultar o grafo de gêmeos*](how-to-query-graph.md)