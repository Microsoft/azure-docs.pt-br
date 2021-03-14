---
title: Gerenciar o grafo gêmeo com relacionamentos
titleSuffix: Azure Digital Twins
description: Veja como gerenciar um grafo de gêmeos digital conectando-os com relações.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433311"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerenciar um grafo de gêmeos digital usando relações

O coração do Azure digital gêmeos é o [grafo de entrelaçamento](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico de entrelaçamento é feito de gêmeos digitais individuais conectados por meio de **relações**. 

Depois que você tiver uma [instância do gêmeos do Azure digital](how-to-set-up-instance-portal.md) em funcionamento e tiver configurado o código de [autenticação](how-to-authenticate-client.md) em seu aplicativo cliente, poderá usar as [**APIs do DigitalTwins**](/rest/api/digital-twins/dataplane/twins) para criar, modificar e excluir gêmeos digitais e suas relações em uma instância do gêmeos digital do Azure. Você também pode usar o [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de relações e no grafo como um todo; para trabalhar com gêmeos digitais individuais, consulte [*como gerenciar o digital gêmeos*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Maneiras de gerenciar o Graph

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Você também pode fazer alterações no grafo usando o exemplo do Gerenciador de gêmeos digital do Azure, que permite visualizar seu gêmeos e grafo e usa o SDK nos bastidores. A próxima seção descreve esse exemplo em detalhes.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relações

As relações descrevem como diferentes gêmeos digitais são conectadas entre si, que formam a base do grafo de entrelaçamento.

As relações são criadas usando a `CreateOrReplaceRelationshipAsync()` chamada. 

Para criar uma relação, você precisa especificar:
* A ID de fonte de origem ( `srcId` no exemplo de código abaixo): a ID do "My" para onde a relação se origina.
* O ID de entrelaçamento de destino ( `targetId` no exemplo de código abaixo): a ID do ' filetreme "onde a relação chega.
* Um nome de relação ( `relName` no exemplo de código abaixo): o tipo genérico de relationship, algo como _Contains_.
* Uma ID de relação ( `relId` no exemplo de código abaixo): o nome específico para essa relação, algo como _Relationship1_.

A ID da relação deve ser exclusiva dentro da fonte fornecida. Ele não precisa ser globalmente exclusivo.
Por exemplo, para o *filefoo*, cada ID de relação específica deve ser exclusiva. No entanto, outra *barra* de entrelaçamento pode ter uma relação de saída que corresponde à mesma ID de uma relação *foo* .

O exemplo de código a seguir ilustra como criar uma relação em sua instância de gêmeos digital do Azure. Ele usa a chamada do SDK (realçado) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Essa função personalizada agora pode ser chamada para criar uma relação _Contains_ como esta: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Se você quiser criar várias relações, poderá repetir as chamadas para o mesmo método, passando tipos de relação diferentes para o argumento. 

Para obter mais informações sobre a classe auxiliar `BasicRelationship` , consulte [*How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Criar várias relações entre gêmeos

As relações podem ser classificadas como: 

* Relações de saída: relações que pertencem a esta conexão que aponta para fora para conectá-lo a outros gêmeos. O `GetRelationshipsAsync()` método é usado para obter relações de saída de um entrelaçamento.
* Relações de entrada: relações que pertencem a outros gêmeos que apontam para esse entrelaçamento para criar um link de "entrada". O `GetIncomingRelationshipsAsync()` método é usado para obter relações de entrada de um entrelaçamento.

Não há nenhuma restrição quanto ao número de relações que você pode ter entre dois gêmeos — você pode ter quantas relações entre gêmeos desejar. 

Isso significa que você pode expressar vários tipos diferentes de relações entre duas gêmeos ao mesmo tempo. Por exemplo, *a* bificar a pode ter uma relação *armazenada* e uma relação *fabricada* com a *n º B*.

Você pode até mesmo criar várias instâncias do mesmo tipo de relação entre os mesmos dois gêmeos, se desejar. Neste exemplo, a "n" pode ter duas relações *armazenadas* diferentes com a *n* º de cima, desde que as relações tenham *IDs de relação* diferentes.

## <a name="list-relationships"></a>Listar relações

Para acessar a lista de relações de **saída** para um determinado tenda no grafo, você pode usar o `GetRelationships()` método como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Isso retorna um `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>` , dependendo se você usa a versão síncrona ou assíncrona da chamada.

Aqui está um exemplo que recupera uma lista de relações. Ele usa a chamada do SDK (realçado) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Agora você pode chamar esse método personalizado para ver as relações de saída do gêmeos como esta:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Você pode usar as relações recuperadas para navegar para outros gêmeos em seu grafo. Para fazer isso, leia o `target` campo da relação retornada e use-o como a ID para a próxima chamada para `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Localizar relações de entrada para um email digital

O gêmeos digital do Azure também tem uma API para localizar todas as relações de **entrada** para um determinado. Isso geralmente é útil para navegação reversa ou ao excluir um "r".

>[!NOTE]
> `IncomingRelationship` as chamadas não retornam o corpo completo da relação. Para obter mais informações sobre a `IncomingRelationship` classe, consulte a [documentação de referência](/dotnet/api/azure.digitaltwins.core.incomingrelationship).

O exemplo de código na seção anterior concentrou-se na localização de relações de saída de um tenda. O exemplo a seguir é estruturado da mesma forma, mas localiza as relações de *entrada* para a. Este exemplo também usa a chamada do SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Agora você pode chamar esse método personalizado para ver as relações de entrada do gêmeos assim:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Listar todas as propriedades e relações de entrelaçamento

Usando os métodos acima para listar as relações de saída e de entrada para um tipo de dados, você pode criar um método que imprima as informações de cópias completas, incluindo as propriedades de ' s ' e os dois tipos de suas relações. Este é um exemplo de método personalizado que mostra como combinar os métodos personalizados acima para essa finalidade.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Agora você pode chamar essa função personalizada como esta: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Atualizar relações

As relações são atualizadas usando o `UpdateRelationship` método. 

>[!NOTE]
>Esse método é para atualizar as **Propriedades** de uma relação. Se você precisar alterar o conjunto de fontes de gêmeos ou de destino da relação, será necessário [excluir a relação](#delete-relationships) e [recriá-](#create-relationships) la usando o novo.

Os parâmetros necessários para a chamada do cliente são a ID da fonte de armazenamento (o d "d", onde a relação se origina), a ID da relação a ser atualizada e um documento de [patch JSON](http://jsonpatch.com/) que contém as propriedades e os novos valores que você gostaria de atualizar.

Este é um código de exemplo que mostra como usar esse método. Este exemplo usa a chamada do SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Aqui está um exemplo de uma chamada para esse método personalizado, passando um documento de patch JSON com as informações para atualizar uma propriedade.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Excluir relações

O primeiro parâmetro especifica a fonte de los (o nome do entrelaçado no qual a relação se origina). O outro parâmetro é a ID da relação. Você precisa da ID de FileUp e da ID de relação, pois as IDs de relação são exclusivas apenas no escopo de um entrelaçamento.

Este é um código de exemplo que mostra como usar esse método. Este exemplo usa a chamada do SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Agora você pode chamar esse método personalizado para excluir uma relação como esta:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Exemplo de monograph executável

O seguinte trecho de código executável usa as operações de relação deste artigo para criar um grafo de bispersão de gêmeos e relações digitais.

### <a name="set-up-the-runnable-sample"></a>Configurar o exemplo executável

O trecho de código usa o [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [*Floor.jsem*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definições de modelo do [*tutorial: explorar o gêmeos digital do Azure com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Você pode usar esses links para ir diretamente para os arquivos ou baixá-los como parte do projeto de exemplo completo de ponta a ponta [aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Antes de executar o exemplo, faça o seguinte:
1. Baixe os arquivos de modelo, coloque-os em seu projeto e substitua os `<path-to>` espaços reservados no código abaixo para informar o programa onde encontrá-los.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de host da instância do gêmeos digital do Azure.
3. Adicione duas dependências ao seu projeto que serão necessárias para trabalhar com o gêmeos digital do Azure. A primeira é o pacote do [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client), e a segunda fornece ferramentas para ajudar com a autenticação no Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Você também precisará configurar as credenciais locais se desejar executar o exemplo diretamente. A próxima seção descreve isso.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Execute o exemplo

Depois de concluir as etapas acima, você poderá executar diretamente o código de exemplo a seguir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Aqui está a saída do console do programa acima: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Saída do console mostrando os detalhes de entrelaçamento, as relações de entrada e saída do gêmeos." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> O gráfico de entrelaçamento é um conceito de criação de relações entre gêmeos. Se você quiser exibir a representação visual do grafo de entrelaçamento, consulte a seção [*Visualização*](how-to-manage-graph.md#visualization) deste artigo. 

## <a name="create-graph-from-a-csv-file"></a>Criar grafo a partir de um arquivo CSV

Em casos de uso prático, muitas vezes, as hierarquias de entrelaçamento serão criadas a partir dos dados armazenados em um banco de dado diferente, ou talvez em uma planilha ou em um arquivo CSV. Esta seção ilustra como ler dados de um arquivo CSV e criar um gráfico de cima para fora dele.

Considere a tabela de dados a seguir, descrevendo um conjunto de gêmeos e relações digitais.

|  ID do Modelo    | ID de entrelaça (deve ser exclusivo) | Nome da Relação  | ID do entrelaçado de destino  | Dados de inicialização de entrelaçamento |
| --- | --- | --- | --- | --- |
| dtmi: exemplo: Floor; 1    | Floor1 | contains | Room1 | |
| dtmi: exemplo: Floor; 1    | Floor0 | contains | Room0 | |
| dtmi: exemplo: sala; 1    | Room1 | | | {"Temperatura": 80} |
| dtmi: exemplo: sala; 1    | Room0 | | | {"Temperatura": 70} |

Uma maneira de obter esses dados no Azure digital gêmeos é converter a tabela em um arquivo CSV e gravar o código para interpretar o arquivo em comandos para criar gêmeos e relações. O exemplo de código a seguir ilustra a leitura dos dados do arquivo CSV e a criação de um grafo de entrelaçamento no Azure digital gêmeos.

No código abaixo, o arquivo CSV é chamado de *data.csv*, e há um espaço reservado que representa o **nome do host** da instância de gêmeos digital do Azure. O exemplo também usa vários pacotes que você pode adicionar ao seu projeto para ajudar com esse processo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como consultar um grafo gêmeos do Azure digital:
* [*Conceitos: linguagem de consulta*](concepts-query-language.md)
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)