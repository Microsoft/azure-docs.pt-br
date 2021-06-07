---
title: 'Tutorial: Criar um grafo nos Gêmeos Digitais do Azure (CLI)'
titleSuffix: Azure Digital Twins
description: Tutorial para criar um cenário dos Gêmeos Digitais do Azure usando a CLI do Azure
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 578befe3e26ebb42fa2172976e07d0a5836e3743
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107121"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Tutorial: Criar um grafo dos Gêmeos Digitais do Azure usando a CLI do Azure

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Neste tutorial, você criará um grafo nos Gêmeos Digitais do Azure usando modelos, gêmeos e relações. A ferramenta usada neste tutorial é o [conjunto de comandos dos Gêmeos Digitais do Azure para a **CLI do Azure**](how-to-use-cli.md). 

Você pode usar os comandos da CLI para executar ações essenciais dos Gêmeos Digitais do Azure, como carregar modelos, criar e modificar gêmeos e criar relações. Você também pode examinar a [documentação de referência do conjunto de comandos de *az dt*](/cli/azure/dt) para ver o conjunto completo de comandos da CLI.

Neste tutorial, você vai...
> [!div class="checklist"]
> * Modelar um ambiente
> * Criar gêmeos digitais
> * Adicionar relações para formar um grafo
> * Consultar o grafo para responder perguntas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste tutorial, primeiro conclua os pré-requisitos a seguir.

Se você não tiver uma assinatura do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

### <a name="download-the-sample-models"></a>Baixar os modelos de exemplo

O tutorial usa dois modelos pré-gravados que fazem parte do [projeto de exemplo de ponta a ponta](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) de C# para os Gêmeos Digitais do Azure. Os arquivos de modelo estão localizados aqui: 
* [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Para ter os arquivos em seu computador, use os links de navegação acima e copie os corpos dos arquivos em arquivos locais no computador, usando os mesmos nomes (*Room.json* e *Floor.json*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparar uma instância dos Gêmeos Digitais do Azure

Para trabalhar com os Gêmeos Digitais do Azure neste artigo, primeiro você precisa **configurar uma instância dos Gêmeos Digitais do Azure** e as permissões necessárias para usá-la. Se você já tiver uma instância dos Gêmeos Digitais do Azure configurada de algum trabalho anterior, poderá usar essa instância.

Caso contrário, siga as instruções presentes em [*Como configurar uma instância e uma autenticação*](how-to-set-up-instance-cli.md). As instruções também contêm etapas para confirmar se você concluiu cada etapa com êxito e está pronto para passar a usar sua nova instância.

Depois de configurar sua instância do Gêmeos Digitais do Azure, anote os seguintes valores que você precisará para se conectar à instância mais tarde:
* o **_nome do host_** da instância
* a **assinatura do Azure** que você usou para criar a instância. 

Você pode obter esses dois valores para sua instância na saída do seguinte comando da CLI do Azure: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Captura de tela da janela do navegador do Cloud Shell mostrando a saída do comando az dt show. O campo hostName e a ID da assinatura (parte do campo de ID) estão em realçados.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modelar um ambiente físico com DTDL

Agora que a CLI e a instância dos Gêmeos Digitais do Azure estão configurados, você pode começar a criar um grafo de um cenário. 

A primeira etapa na criação de uma solução dos Gêmeos Digitais do Azure é definir [**modelos**](concepts-models.md) gêmeos para o ambiente. 

Os modelos são semelhantes às classes nas linguagens de programação orientadas a objeto; elas fornecem modelos definidos pelo usuário para os [gêmeos digitais](concepts-twins-graph.md) seguirem e criarem uma instância deles posteriormente. Eles são escritos em uma linguagem semelhante a JSON chamada **DTDL (Digital Twins Definition Language)** e podem definir as *propriedades*, a *telemetria*, as *relações* e os *componentes* de um gêmeo.

> [!NOTE]
> A DTDL também permite a definição de *comandos* em gêmeos digitais. No entanto, atualmente os comandos não têm suporte nos Gêmeos Digitais do Azure.

Navegue no computador até o arquivo *Room.json* que você criou na seção [Pré-requisitos](#prerequisites). Abra-o em um editor de código e altere-o das seguintes maneiras:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Carregar modelos nos Gêmeos Digitais do Azure

Depois de criar modelos, você precisa carregá-los na instância dos Gêmeos Digitais do Azure. Isso configura a instância de serviço dos Gêmeos Digitais do Azure com seu próprio vocabulário de domínio personalizado. Depois de carregar os modelos, você pode criar instâncias de gêmeos que os utilizam.

1. Para adicionar modelos usando o Cloud Shell, você precisará carregar seus arquivos de modelo no armazenamento do Cloud Shell para que os arquivos fiquem disponíveis quando você executar o comando do Cloud Shell que os utiliza. Para fazer isso, selecione o ícone "Carregar/baixar arquivos" e escolha "Carregar".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Captura de tela da janela do navegador do Cloud Shell mostrando a seleção do ícone Carregar.":::
    
    Navegue até o arquivo *Room.json* no computador e selecione "Abrir". Em seguida, repita essa etapa para *Floor.json*.

1. Em seguida, use o comando [**az dt model create**](/cli/azure/dt/model#az_dt_model_create) conforme mostrado abaixo para carregar o modelo *Room* atualizado para sua instância dos Gêmeos Digitais do Azure. O segundo comando carrega outro modelo, *Floor*, que você também usará na próxima seção para criar tipos diferentes de gêmeos.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    A saída de cada comando exibirá informações sobre o modelo carregado com êxito.

    >[!TIP]
    >Você também pode carregar todos os modelos em um diretório ao mesmo tempo usando a opção `--from-directory` para o comando model create. Para obter mais informações, confira [Parâmetros opcionais para *az dt model create*](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Verifique se os modelos foram criados com o comando [**az dt model list**](/cli/azure/dt/model#az_dt_model_list), conforme mostrado abaixo. Isso imprimirá uma lista com todos os modelos que foram carregados a instância dos Gêmeos Digitais do Azure, com todas as suas informações. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Procure pelo modelo *Room* editado nos resultados:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado do comando model list, que inclui o modelo Room atualizado." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Errors

A CLI também trata erros do serviço. 

Execute novamente o comando `az dt model create` para tentar carregar novamente um dos mesmos modelos que você acabou de carregar, uma segunda vez:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Como os modelos não podem ser substituídos, será retornado o código de erro `ModelIdAlreadyExists`.

## <a name="create-digital-twins"></a>Criar gêmeos digitais

Agora que alguns modelos foram carregados em sua instância dos Gêmeos Digitais do Azure, você pode criar [**gêmeos digitais**](concepts-twins-graph.md) com base nas definições do modelo. Gêmeos digitais representam as entidades no seu ambiente de negócios – itens como sensores em uma fazenda, salas em um prédio ou luzes em um carro. 

Para criar um gêmeo digital, use o comando [**az dt twin create**](/cli/azure/dt/twin#az_dt_twin_create). Você precisa referenciar o modelo em que o gêmeo se baseia e pode optar por definir valores iniciais para qualquer propriedade no modelo. Você não precisa passar nenhuma informação de relação neste estágio.

1. Execute este código no Cloud Shell para criar vários gêmeos, com base no modelo *Room* que você atualizou anteriormente e em outro modelo, *Floor*. Lembre-se de que *Room* tem três propriedades, de modo que você pode fornecer argumentos com os valores iniciais delas. (Em geral, a inicialização de valores de propriedade é opcional, mas eles são necessários para este tutorial.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Se estiver usando o Cloud Shell no ambiente do PowerShell, talvez você precise escapar os caracteres de aspas para que o valor JSON `--properties` seja analisado corretamente. Com essa edição, os comandos para criar os gêmeos do cômodo têm a seguinte aparência:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Isso é refletido na captura de tela abaixo.
    
    A saída de cada comando mostrará informações sobre o gêmeo criado com êxito (incluindo as propriedades dos gêmeos do cômodo que foram inicializadas com eles).

1. Verifique se os gêmeos foram criados usando o comando [**as dt twin query**](/cli/azure/dt/twin#az_dt_twin_query), conforme mostrado abaixo. A consulta mostrada localiza todos os gêmeos digitais em sua instância dos Gêmeos Digitais do Azure.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Procure pelos gêmeos *room0*, *room1*, *floor0* e *floor1* nos resultados. Veja um trecho que mostra parte do resultado da consulta.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado parcial da consulta de gêmeos, incluindo room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Modificar um gêmeo digital

Você também pode modificar as propriedades de um gêmeo que criou. 

1. Execute este comando [**az dt twin update**](/cli/azure/dt/twin#az_dt_twin_update) para alterar o RoomName de *room0* de *Room0* para *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Se estiver usando o Cloud Shell no ambiente do PowerShell, talvez você precise escapar os caracteres de aspas para que o valor JSON `--json-patch` seja analisado corretamente. Com essa edição, o comando para atualizar o gêmeo é semelhante ao seguinte:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Isso é refletido na captura de tela abaixo.
    
    A saída desse comando mostrará as informações atuais do gêmeo e você verá o novo valor de `RoomName` no resultado.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado do comando update, que inclui um RoomName igual a PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Você pode verificar se a atualização foi bem-sucedida executando o comando [**az dt twin show**](/cli/azure/dt/twin#az_dt_twin_show) para ver as informações de *room0*:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    A saída deve refletir o nome atualizado.

## <a name="create-a-graph-by-adding-relationships"></a>Criar um grafo adicionando relações

Em seguida, você pode criar algumas **relações** entre esses gêmeos para conectá-los em um [**grafo de gêmeos**](concepts-twins-graph.md). Grafos de gêmeos são usados para representar todo o ambiente. 

Os tipos de relações que você pode criar de um tipo de gêmeo para outro são definidos nos [modelos](#model-a-physical-environment-with-dtdl) que você carregou anteriormente. A [definição de modelo para *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) especifica que os andares podem ter um tipo de relação chamado *contains*. Isso possibilita criar uma relação do tipo *contains* de cada gêmeo *Floor* para a sala correspondente que ele contém.

Para adicionar uma relação, use o comando [**az dt twin relationship create**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create). Especifique o gêmeo no qual a relação tem origem, o tipo de relação e o gêmeo ao qual a relação está se conectando. Por fim, dê à relação uma ID exclusiva. Se uma relação tiver sido definida para ter propriedades, você também poderá inicializar as propriedades dela nesse comando.

1. Execute o código a seguir para adicionar uma relação do tipo *contains* de cada um dos gêmeos de *Floor* que você criou anteriormente a cada gêmeo de *Room* correspondente. As relações são nomeadas *relationship0* e *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >A relação *contains* no [modelo *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) também foi definida com duas propriedades, `ownershipUser` e `ownershipDepartment`. Portanto, você também pode fornecer argumentos com os valores iniciais para elas ao criar as relações.
    > Para criar uma relação com essas propriedades inicializadas, adicione a opção `--properties` a qualquer um dos comandos acima, desta forma:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Se estiver usando o Cloud Shell no ambiente do PowerShell, talvez você precise escapar os caracteres de aspas para que o valor JSON `--properties` seja analisado corretamente.
    
    A saída de cada comando exibirá informações sobre a relação criada com êxito.

1. Você pode verificar as relações com qualquer um dos comandos a seguir, que consultam as relações em sua instância dos Gêmeos Digitais do Azure.
    * Para ver todas as relações provenientes de cada andar (exibindo as relações de um lado):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Para ver todas as relações chegando em cada sala (exibindo a relação do "outro" lado):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Para procurar essas relações individualmente, por ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

O gêmeos e as relações que você configurou neste tutorial formam o seguinte grafo conceitual:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Um diagrama mostrando um grafo conceitual. floor0 está conectado por meio de relationship0 a room0 e floor1 está conectado por meio de relationship1 a room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Consultar o grafo de gêmeos para responder às perguntas sobre o ambiente

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente. Na CLI do Azure, isso é feito com o comando [**az dt twin query**](/cli/azure/dt/twin#az_dt_twin_query).

Execute as consultas a seguir no Cloud Shell para responder algumas perguntas sobre o ambiente de exemplo.

1. **Quais são todas as entidades do meu ambiente representadas nos Gêmeos Digitais do Azure?** (consultar tudo)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Isso permite que você faça um resumo rápido de seu ambiente e verifique se tudo está representado como você gostaria dentro dos Gêmeos Digitais do Azure. O resultado disso é uma saída que contém cada gêmeo digital com os detalhes. Veja um trecho:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado parcial da consulta de gêmeos, incluindo room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Você pode reconhecer que esse é o mesmo comando usado na seção [*Criar gêmeos digitais*](#create-digital-twins) para localizar todos os Gêmeos Digitais do Azure na instância.

1. **Quais são as salas em meu ambiente?** (consultar por modelo)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Você pode restringir a consulta aos gêmeos de um determinado tipo a fim de obter informações mais específicas sobre o que é representado. O resultado mostra *room0* e *room1*, mas **não** mostra *floor0* nem *floor1* (pois eles são andares, não salas).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado da consulta de modelo, que inclui somente room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Quais são as salas em *floor0*?** (consultar por relação)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Você pode consultar com base nas relações em seu grafo para obter informações sobre como os gêmeos estão conectados ou para restringir a consulta a uma determinada área. Somente *room0* está em *floor0* e, portanto, é a única sala no resultado.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado da consulta de relação, que inclui room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Observe que uma ID de gêmeo (como *floor0* na consulta acima) é consultada usando o campo de metadados `$dtId`. 
    >
    >Ao usar o Cloud Shell para executar uma consulta com campos de metadados como este, iniciados por `$`, você deve escapar o `$` com um acento grave para informar ao Cloud Shell que ele não é uma variável e deve ser consumido como um literal no texto da consulta. Isso é refletido na captura de tela acima.

1. **Quais são todos os gêmeos em meu ambiente com temperatura acima de 75?** (consultar por propriedade)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Você pode consultar o grafo com base nas propriedades para responder a uma variedade de perguntas, incluindo para localizar exceções no ambiente que podem precisar de atenção. Também há suporte para outros operadores de comparação ( *<* , *>* , *=* ou *!=* ). *room1* aparece nos resultados aqui porque tem uma temperatura de 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado da consulta de propriedade, que inclui somente room1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Quais são as salas em *floor0* com temperatura acima de 75?** (consulta composta)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Você também pode combinar as consultas anteriores como faria no SQL, usando operadores de combinação como `AND`, `OR` e `NOT`. Essa consulta usa `AND` para deixar a consulta anterior sobre as temperaturas dos gêmeos mais específica. O resultado agora inclui apenas salas com temperaturas acima de 75 que estão em *floor0*– que, nesse caso, é nenhuma delas. O conjunto de resultados é vazio.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Captura de tela do Cloud Shell mostrando o resultado da consulta composta, que não inclui nenhum item." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir este tutorial, você poderá escolher quais recursos gostaria de remover, dependendo do que você gostaria de fazer em seguida.

* **Se planeja prosseguir para o próximo tutorial**, você pode manter os recursos configurados aqui e reutilizar a instância do Gêmeos Digitais do Azure sem limpar nada por enquanto.

* **Caso deseje continuar usando a instância dos Gêmeos Digitais do Azure, mas sem nenhum dos seus modelos, gêmeos e relações**, use os comandos [**az dt twin relationship delete**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete), [**az dt twin delete**](/cli/azure/dt/twin#az_dt_twin_delete) e [**az dt model delete**](/cli/azure/dt/model#az_dt_model_delete) para limpar as relações, os gêmeos e os modelos em sua instância, respectivamente.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Talvez você também queira excluir os arquivos de modelo criados no computador local.

## <a name="next-steps"></a>Próximas etapas 

Neste tutorial, você começou a usar os Gêmeos Digitais do Azure criando um grafo em sua instância usando a CLI do Azure. Você criou modelos, gêmeos digitais e relações para formar um grafo. Você também executou algumas consultas no grafo para ter uma ideia de quais tipos de perguntas os Gêmeos Digitais do Azure podem responder sobre um ambiente.

Continue para o próximo tutorial a fim de combinar os Gêmeos Digitais do Azure com outros serviços do Azure para concluir um cenário de ponta a ponta orientado por dados:
> [!div class="nextstepaction"]
> [*Tutorial: Conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)