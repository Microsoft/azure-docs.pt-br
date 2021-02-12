---
title: Mover instância para uma região do Azure diferente
titleSuffix: Azure Digital Twins
description: Consulte como mover uma instância de gêmeos digital do Azure de uma região do Azure para outra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 6393b0b8d794345fded95718a2581ae9b929ad49
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381143"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Mover uma instância do gêmeos digital do Azure para uma região diferente do Azure

Se você precisar mover sua instância do gêmeos digital do Azure de uma região para outra, o processo atual será recriar os recursos na nova região e, em seguida, excluir os recursos originais. No final desse processo, você trabalhará com uma nova instância de gêmeos digital do Azure que é idêntica à primeira, exceto para o local atualizado.

Este artigo fornece orientação sobre como fazer uma movimentação completa e copiar sobre tudo o que será necessário para que a nova instância corresponda ao original.

Este processo inclui as seguintes etapas:

1. Preparar: Baixe seus modelos, gêmeos e grafo originais.
1. Mover: Crie uma nova instância de gêmeos digital do Azure em uma nova região.
1. Mover: repopular a nova instância de gêmeos digital do Azure.
    - Carregue os modelos, o gêmeos e o grafo originais.
    - Recrie pontos de extremidade e rotas.
    - Vincule novamente os recursos conectados.
1. Limpar recursos de origem: exclua a instância original.

## <a name="prerequisites"></a>Pré-requisitos

Antes de tentar recriar sua instância do gêmeos digital do Azure, consulte os componentes da sua instância original para ter uma ideia clara de todas as partes que precisarão ser recriadas.

Aqui estão algumas perguntas a serem feitas:

* Quais são os *modelos* carregados na minha instância? Quantas existem?
* Quais são os *gêmeos* na minha instância? Quantas existem?
* Qual é a forma geral do *grafo* na minha instância? Quantas relações existem?
* Quais *pontos de extremidade* eu tenho em minha instância?
* Quais *rotas* tenho em minha instância? Eles têm filtros?
* Onde a minha instância *se conecta a outros serviços do Azure* ? Alguns pontos de integração comuns incluem:

    - Grade de eventos do Azure, hubs de eventos do Azure ou barramento de serviço do Azure
    - Funções do Azure
    - Aplicativos Lógicos do Azure
    - Azure Time Series Insights
    - Mapas do Azure
    - Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
* Quais outros *aplicativos pessoais ou da empresa* tenho que se conectem à minha instância?

Você pode coletar essas informações usando o [portal do Azure](https://portal.azure.com), [APIs e SDKs do gêmeos digital do Azure](how-to-use-apis-sdks.md), [comandos da CLI do gêmeos digital do Azure](how-to-use-cli.md)ou o exemplo do [Explorer do Azure digital gêmeos (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Preparar

Nesta seção, você irá se preparar para recriar sua instância baixando seus modelos, gêmeos e grafo originais da sua instância original. Este artigo usa o exemplo do [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) para essa tarefa.

>[!NOTE]
>Talvez você já tenha arquivos que contenham os modelos ou o grafo em sua instância. Nesse caso, você não precisa baixar tudo novamente — apenas as partes que você está perdendo ou as coisas que podem ter mudado desde que você carregou esses arquivos originalmente. Por exemplo, você pode ter gêmeos que foram atualizados com novos dados.

### <a name="limitations-of-adt-explorer"></a>Limitações do ADT Explorer

O [exemplo do ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) é um aplicativo cliente de exemplo que dá suporte a uma representação visual do grafo e fornece interação visual com sua instância. Este artigo mostra como usá-lo para baixar e, posteriormente, recarregar, seus modelos, gêmeos e grafos.

Este exemplo não é uma ferramenta completa. Ele não foi testado com estresse e não foi criado para lidar com grafos de um grande tamanho. Consequentemente, tenha em mente as seguintes limitações de exemplo prontas para uso:

* No momento, o exemplo só foi testado em tamanhos de grafo de até 1.000 nós e 2.000 relações.
* O exemplo não dá suporte à repetição no caso de falhas intermitentes.
* O exemplo não notificará necessariamente o usuário se os dados carregados estiverem incompletos.
* O exemplo não trata erros resultantes de grafos muito grandes que excedem os recursos disponíveis, como a memória.

Se o exemplo não puder lidar com o tamanho do grafo, você poderá exportar e importar o grafo usando outras ferramentas de desenvolvedor do Azure digital gêmeos:

* [Comandos da CLI do gêmeos digital do Azure](how-to-use-cli.md)
* [SDKs e APIs do Azure digital gêmeos](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Configurar o aplicativo ADT Explorer

Para prosseguir com o ADT Explorer, primeiro Baixe o código do aplicativo de exemplo e configure-o para ser executado em seu computador.

Para obter o exemplo, consulte [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selecione o botão **baixar zip** para baixar um arquivo. zip desse código de exemplo em seu computador como **Azure_Digital_Twins__ADT__explorer.zip**. Descompacte o arquivo.

Em seguida, configure e configure permissões para o ADT Explorer. Siga as instruções na seção [Configurar o Gerenciador de gêmeos e ADT do Azure digital](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) do guia de início rápido do gêmeos do Azure digital. Esta seção orienta você pelas seguintes etapas:

1. Configure uma instância de gêmeos digital do Azure. Você pode ignorar esta parte porque já tem uma instância.
1. Configure as credenciais locais do Azure para fornecer acesso à sua instância.
1. Execute o ADT Explorer e configure-o para se conectar à sua instância. Você usará o *nome do host* da sua instância do Azure digital gêmeos original que está sendo movida.

Agora você deve ter o aplicativo de exemplo do ADT Explorer em execução em um navegador em seu computador. O exemplo deve ser conectado à instância original do Azure digital gêmeos.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost: 3000. O aplicativo é chamado de ADT Explorer e contém caixas para Gerenciador de consultas, exibição de modelo, exibição de gráfico e Gerenciador de propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/explorer-blank.png":::

Para verificar a conexão, selecione o botão **Executar consulta** para executar a consulta padrão que exibe todas as gêmeos e relações no grafo na caixa **Explorador do Graph** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Uma consulta de execução de botão de leitura no canto superior direito da janela é realçada." lightbox="media/how-to-move-regions/run-query.png":::

Você pode deixar o ADT Explorer em execução porque você o usará novamente mais tarde neste artigo para recarregar esses itens para a nova instância na região de destino.

### <a name="download-models-twins-and-graph"></a>Baixar modelos, gêmeos e grafo

Em seguida, baixe os modelos, o gêmeos e o grafo em sua solução para seu computador.

Para baixar todos esses itens de uma vez, primeiro verifique se o grafo completo aparece na caixa de **exibição de gráfico** . Se o grafo completo ainda não aparecer, execute novamente a consulta padrão do `SELECT * FROM digitaltwins` na caixa **Gerenciador de consultas** .
 
Em seguida, selecione o ícone **Exportar grafo** na caixa **exibição de gráfico** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Na caixa exibição de gráfico, um ícone é realçado. Ele mostra uma seta apontando para fora de uma nuvem." lightbox="media/how-to-move-regions/export-graph.png":::

Essa ação habilita um link de **Download** na caixa de **exibição de gráfico** . Selecione-o para baixar uma representação baseada em JSON do resultado da consulta, que inclui seus modelos, gêmeos e relações. Essa ação deve baixar um arquivo. JSON em seu computador.

>[!NOTE]
>Se o arquivo baixado parecer ter uma extensão de arquivo diferente, tente editar a extensão diretamente e alterá-la para. JSON.

## <a name="move"></a>Mover

Em seguida, você concluirá a "movimentação" de sua instância criando uma nova instância na região de destino. Em seguida, você o preencherá com os dados e componentes da sua instância original.

### <a name="create-a-new-instance"></a>Criar uma nova instância

Primeiro, crie uma nova instância do Azure digital gêmeos em sua região de destino. Siga as etapas em [como: configurar uma instância e uma autenticação](how-to-set-up-instance-portal.md). Mantenha esses ponteiros em mente:

* Você pode manter o mesmo nome para a nova instância *se* ela estiver em um grupo de recursos diferente. Se você precisar usar o mesmo grupo de recursos que contém sua instância original, sua nova instância precisará de seu próprio nome distinto.
* Insira a nova região de destino quando for solicitada uma localização.

Após a conclusão dessa etapa, você precisará do nome do host da sua nova instância para continuar a configurá-lo com seus dados. Se você não fez uma observação do nome do host durante a instalação, siga [estas instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) para obtê-lo agora na portal do Azure.

### <a name="repopulate-the-old-instance"></a>Repopular a instância antiga

Em seguida, você configurará a nova instância para que ela seja uma cópia do original.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Carregar os modelos, gêmeos e grafo originais usando o ADT Explorer

Nesta seção, você pode recarregar seus modelos, gêmeos e grafo para a nova instância. Se você não tiver modelos, gêmeos ou grafo em sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#re-create-endpoints-and-routes).

Caso contrário, retorne à janela do navegador que executa o ADT Explorer e siga estas etapas.

##### <a name="connect-to-the-new-instance"></a>Conectar-se à nova instância

Atualmente, o ADT Explorer está conectado à instância original do Azure digital gêmeos. Alterne a conexão para apontar para a nova instância selecionando o botão **entrar** no canto superior direito da janela.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="ADT Explorer realçando o ícone de entrada no canto superior direito da janela. O ícone mostra uma silhueta simples de uma pessoa sobreposta com uma silhueta de uma chave." lightbox="media/how-to-move-regions/sign-in.png":::

Substitua a **URL ADT** para refletir sua nova instância. Altere esse valor para que ele leia *https://{novo nome de host de instância}*.

Selecione **Conectar**. Você pode ser solicitado a entrar novamente com suas credenciais do Azure ou conceder a este consentimento do aplicativo para sua instância.

##### <a name="upload-models-twins-and-graph"></a>Carregar modelos, gêmeos e grafo

Em seguida, carregue os componentes da solução que você baixou anteriormente para sua nova instância.

Para carregar seus modelos, gêmeos e grafo, selecione o ícone **importar grafo** na caixa **exibição de gráfico** . Essa opção carrega todos os três desses componentes ao mesmo tempo. Ele até carrega modelos que não estão sendo usados atualmente no grafo.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Na caixa Exibição de Grafo, um ícone é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/how-to-move-regions/import-graph.png":::

Na caixa seletor de arquivo, vá para o grafo baixado. Selecione o arquivo Graph **. JSON** e selecione **abrir**.

Depois de alguns segundos, o ADT Explorer abre um modo de exibição de **importação** que mostra uma visualização do grafo a ser carregado.

Para confirmar o carregamento do grafo, selecione o ícone **salvar** no canto superior direito da caixa de **exibição de gráfico** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Realçando o ícone salvar no painel visualização do gráfico." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O ADT Explorer agora carrega seus modelos e o grafo (incluindo o gêmeos e as relações) para sua nova instância do Azure digital gêmeos. Você deverá ver uma mensagem de êxito indicando quantos modelos, gêmeos e relações foram carregados.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Caixa de diálogo que indica êxito na importação de grafo. Ele lê ' importação bem-sucedida. 2 modelos importados. 4 gêmeos importados. 2 relações importadas. '" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Para verificar se tudo foi carregado com êxito, selecione o botão **Executar consulta** na caixa **Gerenciador de gráficos** para executar a consulta padrão que exibe todas as gêmeos e relações no grafo. Essa ação também atualiza a lista de modelos na caixa exibição de **modelo** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Realce ao lado do botão Executar consulta no canto superior direito da janela." lightbox="media/how-to-move-regions/run-query.png":::

Você deve ver seu grafo com todas as suas gêmeos e relações exibidas na caixa **Explorador do Graph** . Você também deve ver seus modelos listados na caixa **exibição de modelo** .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Uma exibição do ADT Explorer mostrando dois modelos realçados na caixa exibição de modelo e um gráfico realçado na caixa Gerenciador de gráficos." lightbox="media/how-to-move-regions/post-upload.png":::

Essas exibições confirmam que seus modelos, gêmeos e grafo foram carregados novamente para a nova instância na região de destino.

#### <a name="re-create-endpoints-and-routes"></a>Recriar pontos de extremidade e rotas

Se você tiver pontos de extremidade ou rotas em sua instância original, precisará recriá-los em sua nova instância. Se você não tiver pontos de extremidade ou rotas em sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#relink-connected-resources).

Caso contrário, siga as etapas em [instruções: gerenciar pontos de extremidade e rotas](how-to-manage-routes-portal.md) usando a nova instância. Mantenha esses ponteiros em mente:

* Você *não* precisa recriar a grade de eventos, os hubs de eventos ou o recurso do barramento de serviço que você está usando para o ponto de extremidade. Para obter mais informações, consulte a seção "pré-requisitos" nas instruções do ponto de extremidade. Você só precisa recriar o ponto de extremidade na instância do gêmeos digital do Azure.
* Você pode reutilizar os nomes de ponto de extremidade e de rota porque eles estão no escopo de uma instância diferente.
* Lembre-se de adicionar todos os filtros necessários às rotas que você criar.

#### <a name="relink-connected-resources"></a>Vincular novamente os recursos conectados

Se tiver outros aplicativos ou recursos do Azure conectados à instância original do Azure digital gêmeos, você precisará editar a conexão para que ela alcance sua nova instância. Esses recursos podem incluir outros serviços do Azure ou aplicativos pessoais ou da empresa que você configurou para trabalhar com o gêmeos digital do Azure.

Se você não tiver outros recursos conectados à sua instância original ou não quiser movê-los para a nova instância, poderá pular para a [próxima seção](#verify).

Caso contrário, considere os recursos conectados em seu cenário. Você não precisa excluir e recriar os recursos conectados. Em vez disso, você só precisa editar os pontos onde eles se conectam a uma instância do gêmeos digital do Azure por meio de seu nome de host. Em seguida, você atualiza esses pontos para usar o nome de host da nova instância, em vez do original.

Os recursos exatos que você precisa editar dependem do seu cenário, mas aqui estão alguns pontos de integração comuns:

* Azure Functions. Se você tiver uma função do Azure cujo código inclui o nome do host da instância original, deverá atualizar esse valor para o nome de host da nova instância e republicar a função.
* Grade de eventos, hubs de eventos ou barramento de serviço.
* Aplicativos Lógicos.
* Time Series Insights.
* Mapas do Azure.
* Serviço de provisionamento de dispositivos no Hub IoT.
* Aplicativos pessoais ou da empresa fora do Azure, como o aplicativo cliente criado no [tutorial: codificar um aplicativo cliente](tutorial-code.md), que se conecta à instância e chama as APIs do Azure digital gêmeos.
* Os registros de aplicativo do Azure AD *não* precisam ser recriados. Se você estiver usando um [registro de aplicativo](how-to-create-app-registration.md) para se conectar às APIs do gêmeos digital do Azure, poderá reutilizar o mesmo registro de aplicativo com sua nova instância.

Depois de concluir esta etapa, sua nova instância na região de destino deverá ser uma cópia da instância original.

## <a name="verify"></a>Verificar

Para verificar se a nova instância foi configurada corretamente, use as seguintes ferramentas:

* [Portal do Azure](https://portal.azure.com). O portal é bom para verificar se a nova instância existe e está na região de destino correta. Também é bom para verificar pontos de extremidade e rotas e conexões com outros serviços do Azure.
* [Comandos da CLI do gêmeos digital do Azure](how-to-use-cli.md). Esses comandos são bons para verificar se a nova instância existe e está na região de destino correta. Eles também podem ser usados para verificar os dados da instância.
* [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). O ADT Explorer é bom para verificar dados de instância, como modelos, gêmeos e grafos.
* [SDKs e APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Esses recursos são bons para verificar dados de instância, como modelos, gêmeos e grafos. Eles também são bons para verificar pontos de extremidade e rotas.

Você também pode tentar executar qualquer aplicativo personalizado ou fluxos de ponta a ponta que você tenha executado com sua instância original para ajudá-lo a verificar se eles estão funcionando corretamente com a nova instância.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Agora que a nova instância está configurada na região de destino com uma cópia dos dados e das conexões da instância original, você pode excluir a instância original.

Você pode usar as APIs [portal do Azure](https://portal.azure.com), [CLI do Azure](how-to-use-cli.md)ou plano de [controle](how-to-use-apis-sdks.md#overview-control-plane-apis).

Para excluir a instância usando o portal do Azure, [abra o portal](https://portal.azure.com) em uma janela do navegador e vá para a instância original do Azure digital gêmeos pesquisando o nome na barra de pesquisa do Portal.

Selecione o botão **excluir** e siga os prompts para concluir a exclusão.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Exibição dos detalhes da instância do gêmeos digital do Azure na portal do Azure, na guia Visão geral. O botão excluir é realçado.":::