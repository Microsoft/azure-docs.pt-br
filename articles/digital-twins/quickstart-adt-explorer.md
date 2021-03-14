---
title: Início Rápido – Explorar um cenário de exemplo
titleSuffix: Azure Digital Twins
description: Início Rápido – Usar o exemplo do ADT Explorer para visualizar e explorar um cenário predefinido.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 9d09bca246938f972a212f7ee71f03a618e16ac4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575663"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Início Rápido – Explorar um cenário de exemplo dos Gêmeos Digitais do Azure usando o ADT Explorer

Com os Gêmeos Digitais do Azure, você pode criar modelos dinâmicos de seus ambientes do mundo real e interagir com eles. Primeiro, você modela os elementos individuais como *gêmeos digitais*. Em seguida, você os conecta a um *grafo* de conhecimento que pode responder a eventos ao vivo e ser consultado para obter informações.

Neste início rápido, você vai explorar um grafo predefinido dos Gêmeos Digitais do Azure com ajuda de um aplicativo de exemplo chamado [ADT (Gêmeos Digitais do Azure) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Use o ADT Explorer para:

- Carregar uma representação digital de um ambiente.
- Exibir imagens visuais dos gêmeos e do grafo que são criadas para representar o ambiente nos Gêmeos Digitais do Azure.
- Realizar outras atividades de gerenciamento por meio de uma experiência visual baseada no navegador.

O guia de início rápido contém as seguintes etapas principais:

1. Configurar uma instância dos Gêmeos Digitais do Azure e o ADT Explorer.
1. Carregar modelos predefinidos e dados de grafo para construir o cenário de exemplo.
1. Explorar o grafo do cenário criado.
1. Fazer alterações no grafo.

O grafo de exemplo com que você trabalhará representa um edifício com dois andares e duas salas. O grafo se parecerá com esta imagem:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Exibição de um grafo formado por quatro nós circulares conectados por setas. Um círculo com o rótulo 'Floor1' está conectado por uma seta com o rótulo 'Contains' a um círculo com o rótulo 'Room1'. Um círculo com o rótulo 'Floor0' está conectado por uma seta com o rótulo 'Contains' a um círculo com o rótulo 'Room0'. 'Floor1' e 'Floor0' não estão conectados.":::

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma assinatura do Azure para concluir este guia de início rápido. Se não tiver uma, [crie-a gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora mesmo.

Você também precisará do **Node.js** em seu computador. Para obter a versão mais recente, confira [Node.js](https://nodejs.org/).

Por fim, você também precisará baixar o exemplo que será usado no início rápido. O aplicativo de exemplo é o **ADT Explorer**. Este exemplo contém o aplicativo que você usa no início rápido para carregar e explorar um cenário dos Gêmeos Digitais do Azure. Ele também contém os arquivos do cenário de exemplo. Para obter o exemplo, vá até [ADT (Gêmeos Digitais do Azure) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selecione o botão **Baixar ZIP** para baixar um arquivo zip deste código de exemplo em seu computador. Descompacte a pasta **Azure_Digital_Twins__ADT__explorer.zip** e extraia os arquivos.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Configurar os Gêmeos Digitais do Azure e o ADT Explorer

A primeira etapa para trabalhar com os Gêmeos Digitais do Azure é configurar uma instância dos Gêmeos Digitais do Azure. Depois de criar uma instância do serviço e configurar suas credenciais para se autenticar no ADT Explorer, você poderá se conectar à instância no ADT Explorer e preenchê-la com os dados de exemplo mais adiante no início rápido.

O restante desta seção orientará você nessas etapas.

### <a name="set-up-an-azure-digital-twins-instance"></a>Configurar uma instância dos Gêmeos Digitais do Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

O aplicativo do ADT Explorer usa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte da biblioteca `Azure.Identity`) para autenticar os usuários em uma instância dos Gêmeos Digitais do Azure quando você o executa no computador local. Para obter mais informações sobre as diferentes maneiras como um aplicativo cliente pode se autenticar com os Gêmeos Digitais do Azure, confira [Escrever o código de autenticação do aplicativo](how-to-authenticate-client.md).

Com esse tipo de autenticação, o ADT Explorer pesquisa pelas credenciais no ambiente local, como as credenciais do Azure em uma [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) local, no Visual Studio ou no Visual Studio Code. Por isso, será necessário *entrar no Azure localmente* por meio de um desses mecanismos para configurar as credenciais do aplicativo do ADT Explorer.

Se já estiver conectado ao Azure por uma dessas formas, vá para a [próxima seção](#run-and-configure-adt-explorer).

Caso contrário, instale a CLI do Azure local com estas etapas:

1. Siga o processo descrito [neste link de instalação](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para concluir a instalação que corresponde ao seu sistema operacional.
1. Abra uma janela do console no computador.
1. Execute `az login` e siga os prompts de autenticação para entrar em sua conta do Azure.
1. Última etapa possível: se você usa várias assinaturas do Azure nessa conta, defina o contexto de autenticação como a assinatura do Azure que contém a instância dos Gêmeos Digitais do Azure executando `az account set --subscription "<your-subscription-name-or-ID>"` (o nome ou o valor da ID da assinatura funcionará).

Depois que você entrar, o ADT Explorer utilizará suas credenciais do Azure automaticamente quando você o executar na próxima seção.

Feche a janela do console de autenticação se desejar. Ou deixe-a aberta para ser usada na próxima etapa.

### <a name="run-and-configure-adt-explorer"></a>Executar e configurar o ADT Explorer

Em seguida, execute o aplicativo ADT Explorer e configure-o para sua instância dos Gêmeos Digitais do Azure.

1. Vá até a pasta **Azure_Digital_Twins__ADT__explorer** baixada e descompactada.
Abra uma janela do console na localização da pasta **Azure_Digital_Twins__ADT__explorer/client/src**.

1. Execute `npm install` para baixar todas as dependências necessárias.

1. Inicie o aplicativo executando `npm run start`.

   Após alguns segundos, uma janela do navegador é aberta e o aplicativo é exibido no navegador.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de ADT Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Selecione o botão **Entrar** no canto superior direito da janela, conforme mostrado na imagem a seguir, para configurar o ADT Explorer para trabalhar com a instância que você configurou.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer realçando o ícone Entrar próximo à parte superior da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Insira a URL da instância dos Gêmeos Digitais do Azure coletada anteriormente na seção [Configurar uma instância dos Gêmeos Digitais do Azure](#set-up-an-azure-digital-twins-instance) no formato *https://{nome do host da instância}* .

>[!NOTE]
> Você pode revisitar ou editar essas informações a qualquer momento selecionando o mesmo ícone para abrir a caixa **Entrar** novamente. Ela manterá os valores que você passou.

> [!TIP]
> Se uma mensagem de erro `SignalRService.subscribe` aparecer quando você se conectar, verifique se a URL dos Gêmeos Digitais do Azure começa com *https://* .

Se você vir uma janela pop-up **Permissões solicitadas** da Microsoft, dê o consentimento para esse aplicativo e aceite para continuar.

## <a name="add-the-sample-data"></a>Adicionar os dados de exemplo

Em seguida, você importará o cenário e o grafo de exemplo para o ADT Explorer. O cenário de exemplo também está localizado na pasta **Azure_Digital_Twins__ADT__explorer** que você baixou anteriormente.

### <a name="models"></a>Modelos

A primeira etapa em uma solução dos Gêmeos Digitais do Azure é definir o vocabulário do ambiente. Você criará [modelos](concepts-models.md) personalizados que descrevem os tipos de entidade que existem no ambiente.

Cada modelo é escrito em uma linguagem semelhante ao JSON-LD chamada DTDL (Digital Twin Definition Language). Cada modelo descreve um tipo de entidade em termos de suas *propriedades*, *telemetria*, *relações* e *componentes*. Posteriormente, você usará esses modelos como a base para os gêmeos digitais que representam instâncias específicas desses tipos.

Normalmente, ao criar um modelo, você executará três etapas:

1. Escrever a definição do modelo. No início rápido, esta etapa já está concluída como parte da solução de exemplo.
1. Validá-lo para garantir que a sintaxe esteja precisa. No início rápido, esta etapa já está concluída como parte da solução de exemplo.
1. Carregá-lo na instância dos Gêmeos Digitais do Azure.
 
Neste início rápido, os arquivos do modelo já foram escritos e validados para você. Eles estão incluídos na solução baixada. Nesta seção, você carregará dois modelos pré-escritos em sua instância a fim de definir estes componentes do ambiente do edifício:

* Piso
* Sala

#### <a name="upload-models"></a>Carregar modelos

Siga estas etapas para carregar os modelos.

1. Na caixa **EXIBIÇÃO DE MODELO**, selecione o ícone **Carregar um Modelo**.

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Na caixa Exibição de Modelo, o ícone do meio é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Na caixa do seletor de arquivos exibida, vá até a pasta **Azure_Digital_Twins__ADT__explorer/client/examples** no repositório baixado.
1. Selecione **Room.json** e **Floor.json** e selecione **OK**. Você poderá carregar modelos adicionais se desejar, mas eles não serão usados neste início rápido.
1. Siga a caixa de diálogo pop-up que solicita que você entre em sua conta do Azure.

>[!NOTE]
>Se você vir a seguinte mensagem de erro: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Uma caixa pop-com o texto &quot;Erro: erro ao buscar os modelos: ClientAuthError: erro ao abrir a janela pop-up. Isso poderá acontecer se você estiver usando o IE ou se os pop-ups estiverem bloqueados no navegador.&quot; com um botão Fechar na parte inferior." border="false"::: 
> Tente desabilitar o bloqueador de pop-ups ou usar outro navegador.

O ADT Explorer carrega esses arquivos de modelo na instância dos Gêmeos Digitais do Azure. Eles devem aparecer na caixa **EXIBIÇÃO DE MODELO** e exibir os nomes amigáveis e as IDs de modelo completo. Você pode selecionar os ícones de informações do **Modelo de Exibição** para ver o código DTDL por trás deles.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Uma exibição da caixa Exibição de Modelo com duas definições de modelo listadas, Andar (dtmi:example:Floor;1) e Sala (dtmi:example:Room;1). O ícone Exibir Informações do Modelo mostrando uma letra &quot;i&quot; em um círculo está realçado para cada modelo." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Gêmeos e o grafo de gêmeos

Agora que alguns modelos foram carregados para sua instância dos Gêmeos Digitais do Azure, você pode adicionar [gêmeos digitais](concepts-twins-graph.md) que seguem as definições do modelo.

O Digital Gêmeos representam as entidades reais em seu ambiente de negócios. Eles podem ser itens como sensores em uma fazenda, luzes em um carro ou – neste início rápido – as salas no andar de um edifício. Você pode criar muitos gêmeos de qualquer tipo de modelo, como várias salas que usam o modelo *Room*. Você os conecta com relações a um *grafo de gêmeo* que representa o ambiente completo.

Nesta seção, você carregará gêmeos criados previamente que estão conectados a um grafo criado previamente. O grafo contém dois andares e dois cômodos, conectados no seguinte layout:

* Floor0
    - Contém Room0
* Floor1
    - Contém Room1

#### <a name="import-the-graph"></a>Importar o grafo

Siga estas etapas para importar o grafo.

1. Na caixa **EXIBIÇÃO DE GRAFO**, selecione o ícone **Importar Grafo**.

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Na caixa Exibição de Grafo, um ícone é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. Na caixa do seletor de arquivos, vá até a pasta **Azure_Digital_Twins__ADT__explorer/client/examples** e selecione o arquivo de planilha **buildingScenario.xlsx**. Esse arquivo contém uma descrição do grafo de exemplo. Selecione **OK**.

   Após alguns segundos, o ADT Explorer abre uma exibição **Importar** que mostra uma visualização do grafo que será carregado.

3. Para confirmar o upload do grafo, selecione o ícone **Salvar** no canto superior direito da **EXIBIÇÃO DE GRAFO**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Realce do ícone Salvar no painel Visualização do Grafo." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Agora, o ADT Explorer usa o arquivo carregado para criar os gêmeos e as relações solicitadas entre eles. Uma caixa de diálogo é exibida após a conclusão. Selecione **Fechar**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Uma caixa de diálogo indicando êxito na importação do grafo. Ela contém a mensagem &quot;Importação bem-sucedida. Quatro gêmeos importados. Duas relações importadas.'" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. O grafo agora foi carregado para o ADT Explorer. Para ver o grafo, selecione o botão **Executar Consulta** na caixa **EXPLORADOR DO GRAPH**, próximo à parte superior da janela do ADT Explorer.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="O botão Executar Consulta no canto superior direito da janela está realçado." lightbox="media/quickstart-adt-explorer/run-query.png":::

Essa ação executa a consulta padrão para selecionar e exibir todos os gêmeos digitais. O ADT Explorer recupera todos os gêmeos e relações do serviço. Ele desenha o grafo definido por eles na caixa **EXIBIÇÃO DE GRAFO**.

## <a name="explore-the-graph"></a>Explorar o grafo

Agora, você pode ver o grafo carregado do cenário de exemplo.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Exibição da caixa Exibição de Grafo com um grafo. Um círculo com o rótulo 'floor1' está conectado por uma seta com o rótulo 'contains' a um círculo com o rótulo 'room1'. Um círculo com o rótulo 'floor0' está conectado por uma seta com o rótulo 'contains' a um círculo com o rótulo 'room0'.":::

Os círculos ("nós" do grafo) representam os gêmeos digitais. As linhas representam as relações. O gêmeo **Floor0** contém **Room0** e o gêmeo **Floor1** contém **Room1**.

Se estiver usando um mouse, arraste partes do grafo para movê-las.

### <a name="view-twin-properties"></a>Exibir propriedades do gêmeo

Você pode selecionar um gêmeo para ver uma lista das propriedades e dos valores dele na caixa **GERENCIADOR DE PROPRIEDADES**.

Aqui estão as propriedades de Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Realce em torno da caixa Gerenciador de Propriedades mostrando as propriedades de Room0, que incluem, entre outros, um campo $dtId de Room0, um campo Temperatura de 70 e um campo Umidade de 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 tem uma temperatura de 70.

Aqui estão as propriedades de Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Realce em torno da caixa Gerenciador de Propriedades mostrando as propriedades de Room1, que incluem, entre outros, um campo $dtId de Room1, um campo Temperatura de 80 e um campo Umidade de 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 tem uma temperatura de 80.

### <a name="query-the-graph"></a>Consultar o grafo

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente.

Uma forma de consultar os gêmeos em seu grafo é pelas *propriedades* deles. Fazer consultas com base em propriedades pode ajudar a responder a várias perguntas. Por exemplo, você pode encontrar exceções no ambiente que podem precisar de atenção.

Nesta seção, você executará uma consulta para descobrir quantos gêmeos no ambiente têm uma temperatura superior a 75.

Para ver a resposta, execute a consulta a seguir na caixa **GERENCIADOR DE CONSULTAS**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Lembre-se que, na exibição das propriedades do gêmeo anteriormente, Room0 tinha uma temperatura de 70 e Room1 tinha uma temperatura de 80. Por esse motivo, somente Room1 aparece nos resultados aqui.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultados da consulta de propriedade mostrando apenas Room1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Também há suporte para outros operadores de comparação (<,>, = ou !=) dentro da consulta anterior. Você pode tentar conectar esses operadores, valores diferentes ou propriedades de mesclagem diferentes à consulta para tentar responder às suas perguntas.

## <a name="edit-data-in-the-graph"></a>Editar dados no grafo

Você pode usar o ADT Explorer para editar as propriedades dos gêmeos representados em seu grafo. Nesta seção, aumentaremos a temperatura de Room0 para 76.

Para começar, selecione **Room0** para ver sua lista de propriedades na caixa **GERENCIADOR DE PROPRIEDADES**.

As propriedades nessa lista são editáveis. Selecione o valor de temperatura de **70** para habilitar a inserção de um novo valor. Digite **76** e selecione o ícone **Salvar** para atualizar a temperatura para **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="A caixa &quot;Gerenciador de Propriedades&quot; mostrando as propriedades de Room0. O valor da temperatura é uma caixa editável exibindo 76 e há um realce ao redor do ícone Salvar." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Agora, você verá uma janela **Informações de Patch** em que aparece o código do patch usado nos bastidores com as [APIs](how-to-use-apis-sdks.md) dos Gêmeos Digitais do Azure para fazer a atualização. Selecione **Fechar**.

### <a name="query-to-see-the-result"></a>Consultar para ver o resultado

Para verificar se o grafo registrou com êxito sua atualização para a temperatura de Room0, execute novamente a consulta anterior para obter todos os gêmeos no ambiente com uma temperatura acima de 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Agora que a temperatura da Room0 foi alterada de 70 para 76, os dois gêmeos devem aparecer no resultado.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultados da consulta de propriedade mostrando Room0 e Room1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Examinar e contextualizar aprendizados

Neste guia de início rápido, você criou uma instância dos Gêmeos Digitais do Azure, conectou-a ao ADT Explorer e a preencheu com um cenário de exemplo.

Depois, você explorou o grafo:

* Usando uma consulta para responder a uma pergunta sobre o cenário.
* Editando uma propriedade em um gêmeo digital.
* Executando a consulta novamente para ver como a resposta foi alterada em decorrência da sua atualização.

A intenção deste exercício é demonstrar como você pode usar o grafo dos Gêmeos Digitais do Azure para responder a perguntas sobre seu ambiente, mesmo conforme o ambiente continue mudando.

Neste início rápido, você fez a atualização da temperatura manualmente. É comum, nos Gêmeos Digitais do Azure, conectar gêmeos digitais a dispositivos IoT reais para que eles recebam atualizações automaticamente, com base nos dados de telemetria. Dessa forma, você pode criar um grafo dinâmico que sempre reflete o estado real do ambiente. Você pode usar consultas para obter informações sobre o que está acontecendo em seu ambiente em tempo real.

## <a name="clean-up-resources"></a>Limpar os recursos

Para concluir o trabalho deste guia de início rápido, primeiro finalize o aplicativo de console em execução. Essa ação desliga a conexão com o aplicativo ADT Explorer no navegador. Você não poderá mais exibir dados dinâmicos no navegador. Feche a guia do navegador.

Em seguida, você poderá escolher quais recursos gostaria de remover, dependendo do que você gostaria de fazer em seguida.

* **Se planejar continuar com os tutoriais dos Gêmeos Digitais do Azure**, você poderá reutilizar a instância neste guia de início rápido para estes artigos e não precisará removê-la.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Talvez seja interessante excluir a pasta do projeto do computador local.

## <a name="next-steps"></a>Próximas etapas

Em seguida, prossiga para os tutoriais dos Gêmeos Digitais do Azure para desenvolver o próprio cenário e as ferramentas de interação dos Gêmeos Digitais do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Codificar um aplicativo cliente](tutorial-code.md)