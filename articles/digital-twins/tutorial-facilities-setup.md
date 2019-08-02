---
title: 'Tutorial: Implantar os Gêmeos Digitais do Azure | Microsoft Azure'
description: Saiba como implantar a instância dos Gêmeos Digitais do Azure e configurar seus recursos espaciais usando as etapas deste tutorial.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: alinast
ms.openlocfilehash: 15a152d6941a8c77cae2ef7771be93db4ddceae4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "67484685"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutorial: Implantar a Versão prévia dos Gêmeos Digitais do Azure e configurar um grafo espacial

Você pode usar o serviço de versão prévia dos Gêmeos Digitais do Azure para reunir pessoas, lugares e dispositivos em um sistema espacial coerente. Esta série de tutoriais demonstra como usar os Gêmeos Digitais do Azure para detectar a ocupação de salas com condições ideais de temperatura e qualidade do ar. 

Esses tutoriais percorrerão com você um aplicativo de console do .NET para criar o cenário de um prédio de escritórios. O prédio tem vários andares e salas em cada andar. As salas contêm dispositivos com sensores conectados que detectam movimento, temperatura ambiente e qualidade do ar. 

Você aprenderá a replicar as áreas físicas e entidades no prédio como objetos digitais usando o serviço Gêmeos Digitais do Azure. Você simulará eventos de dispositivo usando outro aplicativo de console. Em seguida, você aprenderá a monitorar os eventos provenientes dessas áreas físicas e entidades quase em tempo real. 

Um administrador do escritório pode usar essas informações para ajudar um funcionário que trabalhe no prédio a reservar salas de reunião com condições ideais. Um gerente de instalações comerciais pode usar a configuração para obter tendências de uso de salas e monitorar as condições de trabalho para fins de manutenção.

No primeiro tutorial desta série, você aprenderá a:

> [!div class="checklist"]
> * Implantar os Gêmeos Digitais.
> * Conceder permissões ao aplicativo.
> * Modificar um aplicativo de exemplo dos Gêmeos Digitais.
> * Provisionar o prédio.

Estes tutoriais usam e modificam os mesmos exemplos que o [início rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md) usa, para uma cobertura mais detalhada e aprofundada dos conceitos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você ainda não tem uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- O SDK do .NET Core. Os exemplos dos Gêmeos Digitais do Azure usados nestes tutoriais estão escritos em C#. Instale o [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar a amostra. Verifique se a versão correta está instalada em seu computador executando `dotnet --version` em uma janela de comando.

- Use o [Visual Studio Code](https://code.visualstudio.com/) para explorar o código de exemplo. 

<a id="deploy"></a>

## <a name="deploy-digital-twins"></a>Implantar Gêmeos Digitais

Use as etapas nesta seção para criar uma nova instância do serviço Gêmeos Digitais do Azure. Apenas uma instância pode ser criada por assinatura. Vá para a próxima seção se você já tiver uma em execução. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

<a id="permissions"></a>

## <a name="grant-permissions-to-your-app"></a>Conceder permissões ao aplicativo

Os Gêmeos Digitais usam o [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) (Azure Active Directory) para controlar o [acesso de leitura/gravação](../active-directory/develop/v1-permissions-and-consent.md) ao serviço. Todo aplicativo que precise se conectar à instância dos Gêmeos Digitais deve estar registrado no Azure AD. As etapas nesta seção mostram como registrar seu aplicativo de exemplo.

Se você já tiver um registro de aplicativo, poderá reutilizá-lo para o exemplo. No entanto, percorra esta seção para ter certeza de que o registro do aplicativo está configurado corretamente.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configurar o exemplo dos Gêmeos Digitais

Esta seção explora um aplicativo dos Gêmeos Digitais do Azure que se comunica com as [APIs REST dos Gêmeos Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Baixar o exemplo

Se você já tem os exemplos baixados do [início rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md), pode ignorar estas etapas.

1. Baixe os [exemplos do .NET dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extraia o conteúdo da pasta ZIP em seu computador.

### <a name="explore-the-sample"></a>Explorar o exemplo

Na pasta de exemplo extraída, abra o arquivo **digital-twins-samples-csharp\digital-twins-samples.code-workspace** no Visual Studio Code. Ele contém dois projetos:

* Você pode usar o exemplo de provisionamento **occupancy-quickstart** para configurar e provisionar um [grafo de inteligência espacial](concepts-objectmodel-spatialgraph.md#graph). Esse grafo é a imagem digitalizada de seus espaços físicos e os recursos presentes neles. Ele usa um [modelo de objeto](concepts-objectmodel-spatialgraph.md#model) que define objetos de um prédio inteligente. Para obter uma lista completa das APIs REST e dos objetos dos Gêmeos Digitais, confira [esta documentação da API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou a URL da API de Gerenciamento que foi criada para [sua instância](#deploy).

   Para explorar o exemplo e ver como ele se comunica com sua instância dos Gêmeos Digitais, você pode começar pela pasta **src\actions**. Os arquivos na pasta implementam os comandos que você usará nestes tutoriais:
    - O arquivo **provisionSample.cs** mostra como provisionar o grafo espacial.
    - O arquivo **getSpaces.cs** obtém informações sobre os espaços provisionados.
    - O arquivo **getAvailableAndFreshSpaces.cs** obtém os resultados de uma função personalizada chamada de função definida pelo usuário.
    - O arquivo **createEndpoints.cs** cria pontos de extremidade para interagir com outros serviços.

* O exemplo de simulação **device-connectivity** simula dados de sensor e o envia para o hub IoT que está provisionado para a instância dos Gêmeos Digitais. Você usará essa amostra no [próximo tutorial depois de provisionar o grafo espacial](tutorial-facilities-udf.md#simulate). Os identificadores de sensores e dispositivos que você usou para configurar este exemplo devem ser os mesmos que serão usados para provisionar o grafo.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de provisionamento

1. Abra uma janela de comando e vá para o exemplo baixado. Execute o comando a seguir:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Restaure as dependências para o projeto de exemplo executando este comando:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra o arquivo [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) no projeto **occupancy-quickstart**. Atualize os seguintes valores:
   * **ClientId**: insira a ID de aplicativo do registro de seu aplicativo do Azure AD. Você anotou essa ID na seção onde [definiu permissões de aplicativo](#permissions).
   * **Tenant**: insira a ID de diretório de seu [locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Você também anotou essa ID na seção onde [definiu permissões de aplicativo](#permissions).
   * **BaseUrl**: insira a URL da instância dos Gêmeos Digitais. Para obter essa URL, substitua os espaços reservados nessa URL pelos valores da instância: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Você também pode obter essa URL modificando a URL da API de Gerenciamento na [seção de implantação](#deploy). Substitua **swagger/** por **api/v1.0/** .

1. Confira uma lista de recursos dos Gêmeos Digitais que você pode explorar usando o exemplo. Execute o comando a seguir:

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces"></a>

## <a name="understand-the-provisioning-process"></a>Entender o processo de provisionamento

Esta seção mostra como o exemplo provisiona um grafo espacial de um prédio.

No Visual Studio Code, navegue até a pasta **occupancy-quickstart\src\actions** e abra o arquivo **provisionSample.cs**. Observe a seguinte função:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Essa função usa [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) na mesma pasta. Abra esse arquivo e observe a hierarquia de um prédio comercial: *Local*, *Andar*, *Área* e *Salas*. Qualquer um desses espaços físicos pode conter *dispositivos* e *sensores*. Cada entrada tem um modelo predefinido `type`&mdash;por exemplo, Andar, Sala.

O arquivo de exemplo **yaml** mostra um grafo espacial que usa o modelo de objeto dos Gêmeos Digitais `Default`. Esse modelo fornece nomes genéricos para a maioria dos tipos. Os nomes genéricos são suficientes para um prédio. Os exemplos incluem Temperature para SensorDataType e Map para SpaceBlobType. Um tipo de espaço de exemplo é Room, com subtipos FocusRoom, ConferenceRoom e assim por diante. 

Se você precisou criar um grafo espacial para um local de tipo diferente, como uma fábrica, talvez seja necessário usar um modelo de objeto diferente. Execute o comando `dotnet run GetOntologies` na linha de comando do exemplo de provisionamento para saber quais modelos estão disponíveis para uso. 

Para obter mais informações sobre grafos espaciais e modelos de objeto, leia [Noções básicas sobre modelos de objeto e grafo de inteligência espacial dos Gêmeos Digitais](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modificar grafo espacial de exemplo

O arquivo **provisionSample.yaml** contém os seguintes nós:

- **resources**: o nó `resources` cria um recurso do Hub IoT do Azure para se comunicar com os dispositivos em sua configuração. Um hub IoT no nó raiz do grafo pode se comunicar com todos os dispositivos e sensores no grafo.  

- **spaces**: no modelo de objeto dos Gêmeos Digitais, `spaces` representa os locais físicos. Cada espaço tem um `Type`&mdash;por exemplo, Region, Venue ou Customer&mdash;, e um `Name` amigável. Os espaços podem pertencer a outros espaços, criando uma estrutura hierárquica. O arquivo provisionSample.yaml tem um grafo espacial de um prédio imaginário. Observe o aninhamento lógico dos espaços do tipo `Floor` em `Venue`, `Area` em um andar e nós `Room` em uma área. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerenciam uma quantidade de sensores. Por exemplo, um dispositivo pode ser o telefone do usuário, um pod de sensor do Raspberry Pi ou um gateway. No prédio imaginário do exemplo, observe como a sala chamada **Sala de Foco** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um único `hardwareId`, que está codificado no exemplo. Para configurar esse exemplo para uma produção real, substitua-os pelos valores de sua configuração.  

- **sensors**: um dispositivo pode conter vários `sensors`. Eles podem detectar e registrar alterações físicas, como temperatura, movimento e nível de bateria. Cada nó de sensor é identificado exclusivamente por um `hardwareId`, que, no nosso caso, está no código. Em um aplicativo real, substitua-os usando os identificadores exclusivos dos sensores da instalação. O arquivo provisionSample.yaml tem dois sensores para gravar *Motion* e *CarbonDioxide*. Adicione outro sensor para registrar a *Temperatura*, adicionando as linhas a seguir abaixo das linhas do sensor CarbonDioxide. Observe que eles são fornecidos em provisionSample.yaml como linhas comentadas. Você pode remover os comentários removendo o caractere `#` na frente de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Verifique se as chaves `dataType` e `hardwareId` também estão alinhadas com as instruções acima desse trecho de código. Verifique também se o editor não substitui espaços por tabulações. 

Salve e feche o arquivo provisionSample.yaml. No próximo tutorial, você adicionará outras informações a esse arquivo e provisionará o prédio de exemplo dos Gêmeos Digitais do Azure.

> [!TIP]
> É possível exibir e modificar seu grafo espacial usando o [Visualizador de Grafos dos Gêmeos Digitais do Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser parar de explorar os Gêmeos Digitais do Azure neste momento, fique à vontade para excluir recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](https://portal.azure.com), escolha **Todos os recursos**, marque o grupo de recursos dos Gêmeos Digitais e a opção **Excluir**.

    > [!TIP]
    > Se você teve problemas para excluir sua instância de Gêmeos Digitais, lançamos uma atualização de serviço com a correção. Tente novamente excluir a instância.

1. Se necessário, exclua o aplicativo de exemplo em seu computador de trabalho.

## <a name="next-steps"></a>Próximas etapas

Para saber como implementar uma lógica personalizada para monitorar as condições do prédio de exemplo, vá para o próximo tutorial da série: 
> [!div class="nextstepaction"]
> [Tutorial: Provisionar o prédio e monitorar as condições de trabalho](tutorial-facilities-udf.md)
