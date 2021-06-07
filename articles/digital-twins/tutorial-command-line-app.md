---
title: 'Tutorial: criar um grafo nos Gêmeos Digitais do Azure (aplicativo cliente)'
titleSuffix: Azure Digital Twins
description: Tutorial para criar um cenário dos Gêmeos Digitais do Azure usando um aplicativo de linha de comando de exemplo
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493690"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Tutorial: criar um grafo dos Gêmeos Digitais do Azure usando um aplicativo cliente de exemplo

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Neste tutorial, você criará um grafo nos Gêmeos Digitais do Azure usando modelos, gêmeos e relações. A ferramenta para este tutorial é o **aplicativo cliente de linha de comando de exemplo** para interagir com uma instância dos Gêmeos Digitais do Azure. O aplicativo cliente é semelhante ao escrito no [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md).

Você pode usar este exemplo para executar ações essenciais dos Gêmeos Digitais do Azure, como carregar modelos, criar e modificar gêmeos e criar relações. Você também pode examinar o [código do exemplo](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) para saber mais sobre as APIs dos Gêmeos Digitais do Azure, bem como praticar a implementação de comando próprios modificando o projeto de exemplo como preferir.

Neste tutorial, você vai...
> [!div class="checklist"]
> * Modelar um ambiente
> * Criar gêmeos digitais
> * Adicionar relações para formar um grafo
> * Consultar o grafo para responder perguntas

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Executar o projeto de exemplo

Agora que o aplicativo e a autenticação estão configurados, execute o projeto com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Captura de tela do botão de início do Visual Studio (projeto SampleClientApp)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Uma janela de console será aberta, executará a autenticação e aguardará um comando. 
* A autenticação é feita no navegador: seu navegador da Web padrão será aberto com um prompt de autenticação. Use esse prompt para entrar com suas credenciais do Azure. Em seguida, você pode fechar a guia ou a janela do navegador.

Veja uma captura de tela mostrando a aparência do console do projeto:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Captura de tela da mensagem de boas-vindas do aplicativo de linha de comando." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Para obter uma lista de todos os comandos possíveis que você pode usar com este projeto, digite `help` no console do projeto e pressione retornar.

Mantenha o console do projeto em execução no restante das etapas deste tutorial.

## <a name="model-a-physical-environment-with-dtdl"></a>Modelar um ambiente físico com DTDL

Agora que a instância dos Gêmeos Digitais do Azure e o aplicativo de exemplo estão configurados, você pode começar a criar um grafo de um cenário. 

A primeira etapa na criação de uma solução dos Gêmeos Digitais do Azure é definir [**modelos**](concepts-models.md) gêmeos para o ambiente. 

Os modelos são semelhantes às classes nas linguagens de programação orientadas a objeto; elas fornecem modelos definidos pelo usuário para os [gêmeos digitais](concepts-twins-graph.md) seguirem e criarem uma instância deles posteriormente. Eles são escritos em uma linguagem semelhante a JSON chamada **DTDL (Digital Twins Definition Language)** e podem definir as *propriedades*, a *telemetria*, as *relações* e os *componentes* de um gêmeo.

> [!NOTE]
> A DTDL também permite a definição de *comandos* em gêmeos digitais. No entanto, atualmente os comandos não têm suporte nos Gêmeos Digitais do Azure.

Na janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto, use o painel do *Gerenciador de Soluções* para navegar até a pasta *AdtSampleApp\SampleClientApp\Models*. Esta pasta contém modelos de exemplo.

Selecione *Room.json* para abri-lo na janela de edição e altere-o das seguintes maneiras:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Carregar modelos nos Gêmeos Digitais do Azure

Depois de criar modelos, você precisa carregá-los na instância dos Gêmeos Digitais do Azure. Isso configura a instância de serviço dos Gêmeos Digitais do Azure com seu próprio vocabulário de domínio personalizado. Depois de carregar os modelos, você pode criar instâncias de gêmeos que os utilizam.

1. Na janela do console do projeto, execute o comando a seguir para carregar o modelo *Room* atualizado, bem como o modelo *Floor* que você também usará na próxima seção para criar tipos diferentes de gêmeos.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    A saída deve indicar que os modelos foram criados com êxito.

1. Verifique se os modelos foram criados executando o comando `GetModels true`. Isso consultará a instância dos Gêmeos Digitais do Azure para todos os modelos que foram carregados e imprimirá suas informações completas. Procure pelo modelo *Room* editado nos resultados:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Captura de tela do resultado do GetModels mostrando o modelo Room atualizado." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Errors

O aplicativo de exemplo também trata erros do serviço. 

Execute novamente o comando `CreateModels` para tentar carregar novamente um dos mesmos modelos que você acabou de carregar, uma segunda vez:

```cmd/sh
CreateModels Room
```

Como os modelos não podem ser substituídos, um erro de serviço será retornado.
Para obter detalhes sobre como excluir os modelos existentes, confira [*Como Gerenciar modelos de DTDL*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Criar gêmeos digitais

Agora que alguns modelos foram carregados em sua instância dos Gêmeos Digitais do Azure, você pode criar [**gêmeos digitais**](concepts-twins-graph.md) com base nas definições do modelo. Gêmeos digitais representam as entidades no seu ambiente de negócios – itens como sensores em uma fazenda, salas em um prédio ou luzes em um carro. 

Para criar um gêmeo digital, use o comando `CreateDigitalTwin`. Você precisa referenciar o modelo em que o gêmeo se baseia e pode optar por definir valores iniciais para qualquer propriedade no modelo. Você não precisa passar nenhuma informação de relação neste estágio.

1. Execute este código no console do projeto em execução para criar vários gêmeos, com base no modelo *Room* que você atualizou anteriormente e em outro modelo, *Floor*. Lembre-se de que *Room* tem três propriedades, de modo que você pode fornecer argumentos com os valores iniciais delas. (Em geral, a inicialização de valores de propriedade é opcional, mas eles são necessários para este tutorial.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    A saída desses comandos deve indicar que os gêmeos foram criados com êxito. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Captura de tela mostrando um trecho do resultado dos comandos CreateDigitalTwin, que inclui floor0, floor1, room0 e room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Você pode verificar se os gêmeos foram criados executando o comando `Query`. Esse comando consulta sua instância dos Gêmeos Digitais do Azure para todos os gêmeos digitais que ela contém. Procure pelos gêmeos *room0*, *room1*, *floor0* e *floor1* nos resultados.

### <a name="modify-a-digital-twin"></a>Modificar um gêmeo digital

Você também pode modificar as propriedades de um gêmeo que criou. 

> [!NOTE]
> A API REST subjacente usa o formato [patch JSON](http://jsonpatch.com/) para definir atualizações em um gêmeo. O aplicativo de linha de comando também usa esse formato, para proporcionar uma experiência mais verdadeira com o que as APIs subjacentes esperam.

1. Execute este comando para alterar o RoomName de *room0* de *Room0* para *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    A saída deve indicar que o gêmeo foi atualizado com êxito.

1. Você pode verificar a atualização executando esse comando para ver as informações de *room0*:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    A saída deve refletir o nome atualizado.


## <a name="create-a-graph-by-adding-relationships"></a>Criar um grafo adicionando relações

Em seguida, você pode criar algumas **relações** entre esses gêmeos para conectá-los em um [**grafo de gêmeos**](concepts-twins-graph.md). Grafos de gêmeos são usados para representar todo o ambiente. 

Os tipos de relações que você pode criar de um tipo de gêmeo para outro são definidos nos [modelos](#model-a-physical-environment-with-dtdl) que você carregou anteriormente. A [definição de modelo para *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) especifica que os andares podem ter um tipo de relação chamado *contains*. Isso possibilita criar uma relação do tipo *contains* de cada gêmeo *Floor* para a sala correspondente que ele contém.

Para adicionar uma relação, use o comando `CreateRelationship`. Especifique o gêmeo no qual a relação tem origem, o tipo de relação e o gêmeo ao qual a relação está se conectando. Por fim, dê à relação uma ID exclusiva.

1. Execute o código a seguir para adicionar uma relação "contains" de cada um dos gêmeos de *Floor* que você criou anteriormente a cada *Room* correspondente. As relações são nomeadas *relationship0* e *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >A relação *contains* no [modelo *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) também foi definida com duas propriedades de cadeia de caracteres, `ownershipUser` e `ownershipDepartment`. Portanto, você também pode fornecer argumentos com os valores iniciais para eles ao criar as relações.
    > Esta é uma versão alternativa do comando acima para criar *relationship0* que também especifica valores iniciais para essas propriedades:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    A saída desses comandos confirma que as relações foram criadas com êxito:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Captura de tela de um trecho do resultado dos comandos CreateRelationship, que inclui relationship0 e relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Você pode verificar as relações com qualquer um dos comandos a seguir, que consultam as relações em sua instância dos Gêmeos Digitais do Azure.
    * Para ver todas as relações provenientes de cada andar (exibindo as relações de um lado):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Para ver todas as relações chegando em cada sala (exibindo a relação do "outro" lado):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Para procurar essas relações individualmente, por ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

O gêmeos e as relações que você configurou neste tutorial formam o seguinte grafo conceitual:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Um diagrama mostrando um grafo conceitual. floor0 está conectado por meio de relationship0 a room0 e floor1 está conectado por meio de relationship1 a room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Consultar o grafo de gêmeos para responder às perguntas sobre o ambiente

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente. 

Execute os comandos a seguir no console do projeto em execução para responder a algumas perguntas sobre o ambiente de exemplo.

1. **Quais são todas as entidades do meu ambiente representadas nos Gêmeos Digitais do Azure?** (consultar tudo)

    ```cmd/sh
    Query
    ```

    Isso permite que você faça um resumo rápido de seu ambiente e verifique se tudo está representado como você gostaria dentro dos Gêmeos Digitais do Azure. O resultado disso é uma saída que contém cada gêmeo digital com os detalhes. Veja um trecho:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Captura de tela mostrando um resultado parcial da consulta de gêmeos, incluindo room0 e floor1.":::

    >[!NOTE]
    >No projeto de exemplo, o comando `Query` sem argumentos adicionais é o equivalente de `Query SELECT * FROM DIGITALTWINS`. Para consultar todos os gêmeos em sua instância usando as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos da CLI](how-to-use-cli.md), use a consulta mais longa (completa).

1. **Quais são as salas em meu ambiente?** (consultar por modelo)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Você pode restringir a consulta aos gêmeos de um determinado tipo a fim de obter informações mais específicas sobre o que é representado. O resultado mostra *room0* e *room1*, mas **não** mostra *floor0* nem *floor1* (pois eles são andares, não salas).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Captura de tela do resultado da consulta de modelo mostrando apenas room0 e room1.":::

1. **Quais são as salas em *floor0*?** (consultar por relação)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Você pode consultar com base nas relações em seu grafo para obter informações sobre como os gêmeos estão conectados ou para restringir a consulta a uma determinada área. Somente *room0* está em *floor0* e, portanto, é a única sala no resultado.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Captura de tela do resultado da consulta de relação mostrando room0.":::

1. **Quais são todos os gêmeos em meu ambiente com temperatura acima de 75?** (consultar por propriedade)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Você pode consultar o grafo com base nas propriedades para responder a uma variedade de perguntas, incluindo para localizar exceções no ambiente que podem precisar de atenção. Também há suporte para outros operadores de comparação ( *<* , *>* , *=* ou *!=* ). *room1* aparece nos resultados aqui porque tem uma temperatura de 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Captura de tela do resultado da consulta de propriedade mostrando apenas room1.":::

1. **Quais são as salas em *floor0* com temperatura acima de 75?** (consulta composta)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Você também pode combinar as consultas anteriores como faria no SQL, usando operadores de combinação como `AND`, `OR` e `NOT`. Essa consulta usa `AND` para deixar a consulta anterior sobre as temperaturas dos gêmeos mais específica. O resultado agora inclui apenas salas com temperaturas acima de 75 que estão em *floor0*– que, nesse caso, é nenhuma delas. O conjunto de resultados é vazio.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Captura de tela do resultado da consulta composta não mostrando nenhum resultado." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir este tutorial, você poderá escolher quais recursos gostaria de remover, dependendo do que você gostaria de fazer em seguida.

* **Caso planeje prosseguir para o próximo tutorial**, você pode manter os recursos configurados aqui para continuar usando essa instância dos Gêmeos Digitais do Azure e o aplicativo de exemplo configurado para o próximo tutorial

* **Caso deseje continuar usando a instância dos Gêmeos Digitais do Azure, mas limpar todos os seus modelos, gêmeos e relações**, poderá usar os comandos `DeleteAllTwins` e `DeleteAllModels` do aplicativo de exemplo para limpar o gêmeos e os modelos em sua instância, respectivamente.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Talvez seja interessante excluir a pasta do projeto do computador local.

## <a name="next-steps"></a>Próximas etapas 

Neste tutorial, você começou a usar os Gêmeos Digitais do Azure criando um grafo em sua instância usando um aplicativo cliente de exemplo. Você criou modelos, gêmeos digitais e relações para formar um grafo. Você também executou algumas consultas no grafo para ter uma ideia de quais tipos de perguntas os Gêmeos Digitais do Azure podem responder sobre um ambiente.

Continue para o próximo tutorial a fim de combinar os Gêmeos Digitais do Azure com outros serviços do Azure para concluir um cenário de ponta a ponta orientado por dados:
> [!div class="nextstepaction"]
> [*Tutorial: Conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)