---
title: Migrar uma instância para uma região do Azure diferente
titleSuffix: Azure Digital Twins
description: Confira como migrar uma instância dos Gêmeos Digitais do Azure de uma região do Azure para outra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102049841"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Migrar uma instância dos Gêmeos Digitais do Azure para uma região do Azure diferente

Caso precise migrar sua instância dos Gêmeos Digitais do Azure de uma região para outra, o processo atual é recriar os recursos em uma nova região, depois excluir os recursos originais. No final desse processo, você trabalhará com uma nova instância dos Gêmeos Digitais do Azure idêntica à primeira, exceto pela localização atualizada.

Este artigo fornecerá diretrizes sobre de que modo executar a migração completa e copiar todas as informações necessárias para que a nova instância seja correspondente à original.

Este processo inclui as seguintes etapas:

1. Preparo: baixe os modelos, os gêmeos e o grafo originais.
1. Criação: crie uma instância dos Gêmeos Digitais do Azure em uma nova região.
1. Migração: insira dados da instância original em uma nova instância dos Gêmeos Digitais do Azure.
    - Carregue os modelos, os gêmeos e o grafo originais.
    - Recrie pontos de extremidade e rotas.
    - Vincule novamente os recursos conectados.
1. Limpeza dos recursos de origem: exclua a instância original.

## <a name="prerequisites"></a>Pré-requisitos

Antes de tentar recriar sua instância dos Gêmeos Digitais do Azure, confira os componentes da instância original para ter uma ideia concreta sobre todas as partes que precisarão ser recriadas.

Aqui estão algumas perguntas a serem feitas:

* Quais *modelos* foram carregados em minha instância? Quantos modelos existem?
* Quais *gêmeos* tenho em minha instância? Quantos gêmeos existem?
* Qual é a forma geral do *grafo* em minha instância? Quantas relações existem?
* Quais *pontos de extremidade* tenho em minha instância?
* Quais *rotas* tenho em minha instância? Elas têm filtros?
* Em que local minha instância *se conecta a outros serviços do Azure*? Alguns pontos de integração comuns incluem:

    - A Grade de Eventos do Azure, os Hubs de Eventos do Azure ou o Barramento de Serviço do Azure
    - Funções do Azure
    - Aplicativos Lógicos do Azure
    - Azure Time Series Insights
    - Mapas do Azure
    - Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
* Quais *aplicativos pessoais ou empresariais* também tenho que se conectam à minha instância?

É possível coletar essas informações usando o [portal do Azure](https://portal.azure.com), [APIs e SDKs dos Gêmeos Digitais do Azure](how-to-use-apis-sdks.md), [comandos da CLI dos Gêmeos Digitais do Azure](how-to-use-cli.md) ou o [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

## <a name="prepare"></a>Preparar

Nesta seção, você vai se preparar para recriar a instância baixando os modelos, os gêmeos e o grafo originais da primeira instância. Este artigo usa o [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) de exemplo para executar esta tarefa.

>[!NOTE]
>Talvez você já tenha na instância arquivos que contêm os modelos ou o grafo. Nesse caso, não será preciso baixar tudo novamente. Baixe somente as informações ausentes ou que podem ter mudado desde o momento em que você carregou esses arquivos. Por exemplo, talvez você tenha alguns gêmeos que foram atualizados com os novos dados.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Limitações do Azure Digital Twins Explorer

O [Azure Digital Twins Explorer de exemplo](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) é um aplicativo cliente de exemplo compatível com uma representação visual do grafo. Além disso, ele fornece uma interação visual com sua instância. Este artigo mostrará de que modo usar o Azure Digital Twins Explorer para baixar, depois recarregar modelos, gêmeos e grafos.

Este exemplo não é uma ferramenta completa. Não foi executado um teste de estresse nele, tampouco ele foi criado para lidar com grafos de grande porte. Portanto, lembre-se das seguintes limitações do exemplo pronto para uso:

* No momento, o exemplo foi testado somente em tamanhos de grafo de até 1.000 nós e 2.000 relações.
* O exemplo não é compatível com repetições no caso de falhas intermitentes.
* Caso os dados carregados estejam incompletos, o exemplo não será obrigado a notificar o usuário.
* O exemplo não lida com erros decorrentes de grafos muito grandes que excedem o tamanho de recursos disponíveis, como a memória.

Caso o exemplo não possa lidar com o tamanho do grafo, será possível exportar e importar o grafo usando outras ferramentas para desenvolvedores dos Gêmeos Digitais do Azure:

* [Comandos da CLI dos Gêmeos Digitais do Azure](how-to-use-cli.md)
* [APIs e SDKs dos Gêmeos Digitais do Azure](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Configurar o aplicativo do Azure Digital Twins Explorer

Para usar o Azure Digital Twins Explorer, primeiro baixe o código do aplicativo de exemplo, depois configure-o para ser executado em seu computador.

Para obter o exemplo, confira o [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Clique no botão **Baixar arquivo zip** para baixar um arquivo zip desse código de exemplo em seu computador como **Azure_Digital_Twins__ADT__explorer.zip**. Descompacte o arquivo.

Depois, configure e defina as permissões do Azure Digital Twins Explorer. Siga as instruções da seção [Configurar os Gêmeos Digitais do Azure e o Azure Digital Twins Explorer](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) no guia de início rápido dos Gêmeos Digitais do Azure. A seção mostrará detalhes sobre as seguintes etapas:

1. Configurar uma instância dos Gêmeos Digitais do Azure. É possível ignorar esta parte porque você já tem uma instância.
1. Configurar as credenciais locais do Azure para fornecer acesso à sua instância.
1. Executar e configurar o Azure Digital Twins Explorer para estabelecer uma conexão com a sua instância. Será necessário usar o *nome do host* da instância original dos Gêmeos Digitais do Azure que está sendo migrada.

Agora o aplicativo de exemplo do Azure Digital Twins Explorer está em execução no navegador de seu computador. O exemplo deverá ser conectado à instância original dos Gêmeos Digitais do Azure.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Janela do navegador mostrando um aplicativo em execução no localhost:3000. O aplicativo é chamado de Azure Digital Twins Explorer e contém caixas para um Gerenciador de Consultas, uma Exibição de Modelo, uma Exibição de Grafo e um Gerenciador de Propriedades. Ainda não há dados na tela." lightbox="media/how-to-move-regions/explorer-blank.png":::

Para verificar a conexão, clique no botão **Executar Consulta** para executar uma consulta padrão que exibe todos os gêmeos e todas as relações no grafo da caixa **EXPLORADOR DO GRAPH**.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Um botão executando a leitura da opção Executar Consulta está realçado no canto superior direito da janela." lightbox="media/how-to-move-regions/run-query.png":::

É possível manter o Azure Digital Twins Explorer em execução porque ele será usado posteriormente neste artigo para recarregar esses itens em sua nova instância na região de destino.

### <a name="download-models-twins-and-graph"></a>Baixar os modelos, os gêmeos e o grafo

Em seguida, baixe os modelos, os gêmeos e o grafo em sua solução no computador.

Para baixar todos esses itens de uma vez, primeiro verifique se o grafo completo aparece na caixa de **VISUALIZAÇÃO DO GRAFO**. Caso o grafo completo ainda não seja exibido, execute novamente a consulta padrão de `SELECT * FROM digitaltwins` na caixa **GERENCIADOR DE CONSULTAS**.
 
Depois, clique no ícone **Exportar Grafo** na caixa de **VISUALIZAÇÃO DO GRAFO**.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Um ícone está realçado na caixa de Visualização do Grafo. Ele mostra uma seta saindo de uma nuvem e apontando para baixo." lightbox="media/how-to-move-regions/export-graph.png":::

Esse tipo de ação habilitará um link para **Download** na caixa de **EXIBIÇÃO DO GRAFO**. Clique nele para baixar uma representação do resultado da consulta baseada em JSON, que inclui: os modelos, os gêmeos e as relações. Isso deverá baixar um arquivo .json em seu computador.

>[!NOTE]
>Caso o arquivo baixado aparente ter uma extensão de arquivo diferente, tente editar a extensão diretamente e alterá-la para .json.

## <a name="move"></a>Mover

Em seguida, será necessário concluir a "migração" de sua instância criando uma instância na região de destino. Depois, será necessário preencher essa instância com os dados e os componentes da instância original.

### <a name="create-a-new-instance"></a>Criar uma instância

Primeiro, crie uma instância dos Gêmeos Digitais do Azure em sua região de destino. Siga as instruções descritas em [Instruções: configurar uma instância e uma autenticação](how-to-set-up-instance-portal.md). Lembre-se dos seguintes pontos:

* É possível manter o mesmo nome *se* a nova instância estiver em um grupo de recursos diferente. Caso precise usar o mesmo grupo de recursos da instância original, a nova instância precisará de um nome diferente.
* Insira a nova região de destino quando receber uma solicitação para indicar uma localização.

Após a conclusão dessa etapa, será preciso obter o nome do host da nova instância para prosseguir com a configuração usando seus dados. Caso não tenha anotado o nome do host durante a instalação, siga [estas instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) para obtê-lo agora no portal do Azure.

### <a name="repopulate-the-old-instance"></a>Preencher a instância anterior novamente

Em seguida, será preciso configurar a nova instância para que ela seja uma cópia da original.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Carregar os modelos, os gêmeos e o grafo originais usando o Azure Digital Twins Explorer

Nesta seção, será possível recarregar os modelos, os gêmeos e o grafo em sua nova instância. Caso não tenha os modelos, os gêmeos nem o grafo da instância original ou tampouco queira migrá-los para a nova instância, será possível prosseguir para a [próxima seção](#re-create-endpoints-and-routes).

Caso contrário, retorne à janela do navegador executando o Azure Digital Twins Explorer e siga estas etapas.

##### <a name="connect-to-the-new-instance"></a>Conectar-se à nova instância

Atualmente, o Azure Digital Twins Explorer está conectado à instância original dos Gêmeos Digitais do Azure. Mude a conexão para indicar a nova instância clicando no botão **Entrar** localizado no canto superior direito da janela.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="O Azure Digital Twins Explorer realçando o ícone Entrar localizado na parte superior da janela. O ícone mostra o contorno de uma chave sobreposta ao contorno simples de uma pessoa." lightbox="media/how-to-move-regions/sign-in.png":::

Substitua a **URL do ADT** para representar a nova instância. Altere esse valor para que a leitura dele seja *https://{novo nome do host da instância}* .

Selecione **Conectar**. Talvez você receba uma solicitação para entrar novamente com as credenciais do Azure ou consentir que este aplicativo acesse a instância.

##### <a name="upload-models-twins-and-graph"></a>Carregar os modelos, os gêmeos e o grafo

Em seguida, carregue os componentes da solução baixada anteriormente em sua nova instância.

Para carregar os modelos, os gêmeos e o grafo, clique no ícone **Importar Grafo** na caixa de **EXIBIÇÃO DO GRAFO**. Essa opção carregará os três componentes ao mesmo tempo. Ela até mesmo carregará modelos que não estão sendo usados atualmente no grafo.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Na caixa Exibição de Grafo, um ícone é realçado. Ele mostra uma seta apontando para uma nuvem." lightbox="media/how-to-move-regions/import-graph.png":::

Acesse o grafo baixado na caixa de seletor de arquivos. Selecione o arquivo **.json** do grafo, depois clique em **Abrir**.

Após alguns segundos, o Azure Digital Twins Explorer abre uma exibição **Importar** que mostra uma visualização do grafo que será carregado.

Para confirmar o upload do grafo, selecione o ícone **Salvar** no canto superior direito da **EXIBIÇÃO DE GRAFO**.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Realce do ícone Salvar no painel Visualização do Grafo." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

O Azure Digital Twins Explorer agora carrega os modelos e o grafo (incluindo os gêmeos e as relações) em sua nova instância dos Gêmeos Digitais do Azure. Será exibida uma mensagem de êxito indicando quantos modelos, gêmeos e relações foram carregados.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Caixa de diálogo indicando que a importação do grafo foi realizada com êxito. A mensagem mostra que &quot;A importação foi realizada com êxito. Dois modelos importados. Quatro gêmeos importados. Duas relações importadas.&quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Para verificar se o carregamento foi realizado com êxito, clique no botão **Executar Consulta** na caixa **EXPLORADOR DO GRAPH** para executar a consulta padrão que exibe todos os gêmeos e todas as relações do grafo. Isso também atualizará a lista de modelos na caixa de **EXIBIÇÃO DE MODELO**.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Realce em volta do botão Executar Consulta localizado no canto superior direito da janela." lightbox="media/how-to-move-regions/run-query.png":::

O grafo será exibido com todos os gêmeos e todas as relações na caixa **EXPLORADOR DO GRAPH**. Também serão exibidos os modelos listados na caixa de **EXIBIÇÃO DE MODELO**.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Uma exibição do Azure Digital Twins Explorer mostrando dois modelos realçados na caixa de Visualização de Modelo, bem como um grafo realçado na caixa de Explorador do Graph." lightbox="media/how-to-move-regions/post-upload.png":::

Essas exibições confirmam que os modelos, os gêmeos e o grafo foram carregados novamente em uma nova instância da região de destino.

#### <a name="re-create-endpoints-and-routes"></a>Recrie pontos de extremidade e rotas

Caso tenha pontos de extremidade ou rotas na instância original, será preciso recriá-los em outra instância. Caso não tenha pontos de extremidade nem rotas na instância original ou tampouco queira migrá-los para a nova instância, será possível prosseguir para a [próxima seção](#relink-connected-resources).

Caso contrário, siga as etapas em [Instruções: gerenciar pontos de extremidade e rotas](how-to-manage-routes-portal.md) usando a nova instância. Lembre-se dos seguintes pontos:

* *Não* é preciso recriar a Grade de Eventos, os Hubs de Eventos nem o recurso do Barramento de Serviço que estão sendo usados no ponto de extremidade. Para obter mais informações, confira a seção de "Pré-requisitos" nas instruções do ponto de extremidade. É preciso recriar somente o ponto de extremidade na instância dos Gêmeos Digitais do Azure.
* É possível reutilizar os nomes do ponto de extremidade e da rota porque eles estão no escopo de uma instância diferente.
* Lembre-se de adicionar todos os filtros necessários às rotas criadas.

#### <a name="relink-connected-resources"></a>Vincular novamente os recursos conectados

Caso tenha outros aplicativos ou recursos do Azure conectados à instância original dos Gêmeos Digitais do Azure, será preciso editar a conexão para que eles acessem a nova instância. Esses recursos poderão incluir outros serviços do Azure ou aplicativos pessoais/empresarias configurados para trabalhar em conjunto com os Gêmeos Digitais do Azure.

Caso não tenha outros recursos conectados à instância original ou tampouco queira migrá-los para a nova instância, será possível prosseguir para a [próxima seção](#verify).

Caso contrário, considere usar os recursos conectados em seu cenário. Não é preciso excluir nem recriar os recursos conectados. Em vez disso, é preciso editar somente os pontos em que eles se conectam a uma instância dos Gêmeos Digitais do Azure por meio do nome do host. Depois, atualize esses pontos para usar o nome do host da nova instância em vez da original.

Os recursos exatos que precisam de edição dependem do cenário. No entanto, veja abaixo alguns pontos de integração comuns:

* O Azure Functions. Caso tenha uma função do Azure com um código que inclui o nome do host da instância original, será preciso atualizar esse valor para o nome do host da nova instância e republicar a função.
* A Grade de Eventos, os Hubs de Eventos ou o Barramento de Serviço.
* Aplicativos Lógicos.
* O Time Series Insights.
* O Azure Mapas.
* O Serviço de Provisionamento de Dispositivos no Hub IoT.
* Os aplicativos pessoais ou empresariais fora do Azure, como o aplicativo cliente criado no [Tutorial: codificar um aplicativo cliente](tutorial-code.md), que se conectam à instância, bem como executam uma chamada às APIs dos Gêmeos Digitais do Azure.
* Os Registros de aplicativo do Azure AD *não* precisam ser recriados. Caso esteja usando um [registro de aplicativo](how-to-create-app-registration.md) para se conectar às APIs dos Gêmeos Digitais do Azure, será possível reutilizá-lo em sua nova instância.

Depois de concluir esta etapa, a nova instância da região de destino deverá ser uma cópia da instância original.

## <a name="verify"></a>Verificar

Use as seguintes ferramentas para verificar se a nova instância foi configurada de modo correto:

* [Portal do Azure](https://portal.azure.com). O portal é um local adequado para verificar se a nova instância existe, bem como está na região de destino apropriada. Ele também é adequado para verificar pontos de extremidade, rotas e conexões de outros serviços do Azure.
* [Comandos da CLI dos Gêmeos Digitais do Azure](how-to-use-cli.md). Esses comandos são adequados para verificar se a nova instância existe, bem como está na região de destino apropriada. Eles também podem ser usados para verificar dados da instância.
* O [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). O Azure Digital Twins Explorer é adequado para verificar dados da instância, como modelos, gêmeos e grafos.
* [APIs e SDKs dos Gêmeos Digitais do Azure](how-to-use-apis-sdks.md). Esses recursos são adequados para verificar dados da instância, como modelos, gêmeos e grafos. Eles também são apropriados para verificar pontos de extremidade e rotas.

Também é possível tentar executar aplicativos personalizados ou fluxos de ponta a ponta que estavam em execução na instância original para ajudar você a verificar se eles estão funcionando de modo correto em sua nova instância.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Agora é possível excluir a instância original já que a nova instância está configurada na região de destino com uma cópia dos dados e das conexões da instância original.

É possível usar o [portal do Azure](https://portal.azure.com), a [CLI do Azure](how-to-use-cli.md) ou [APIs do painel de controle](how-to-use-apis-sdks.md#overview-control-plane-apis).

Para excluir a instância usando o portal do Azure, [abra o portal](https://portal.azure.com) em uma janela do navegador, depois acesse a instância original dos Gêmeos Digitais do Azure pesquisando o nome dela na barra de pesquisa do portal.

Clique no botão **Excluir** e siga os prompts para concluir a exclusão.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Exibição dos detalhes da instância dos Gêmeos Digitais do Azure na guia de Visão Geral do portal do Azure. O botão Excluir está realçado.":::