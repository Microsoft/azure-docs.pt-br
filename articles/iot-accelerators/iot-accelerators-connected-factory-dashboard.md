---
title: Usar o dashboard Connected Factory - Azure | Microsoft Docs
description: Entenda como usar os recursos do painel da fábrica conectada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 05befc72f512f502456f798f25b6c72571451363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450855"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Use funcionalidades no painel do acelerador de solução da Fábrica Conectada

O guia de início rápido [implantar uma solução baseada em nuvem para gerenciar meus dispositivos de IoT industriais](quickstart-connected-factory-deploy.md) mostrou como o painel de navegar e responder aos alarmes. Este guia de instruções mostra alguns recursos adicionais do painel que você pode usar para monitorar e gerenciar seus dispositivos de IoT industriais.

## <a name="apply-filters"></a>Aplicar filtros

Você pode filtrar as informações exibidas no painel ou no painel **locais de fábrica** ou no painel **alarmes**:

1. Clique no **funil** para exibir uma lista de filtros disponíveis no painel nos locais de fábrica ou no painel de alarmes.

1. O painel de filtros é exibido:

    [![Filtros do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Escolha o filtro necessário e clique em **Aplicar**. Também é possível digitar texto livre nos campos de filtro.

1. O filtro é aplicado. O ícone de funil extra indica que um filtro é aplicado:

    [![Filtros do acelerador de solução da Fábrica Conectada aplicada](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Um filtro ativo não afeta os valores OEE e KPI exibidos. Ele apenas filtra o conteúdo da lista.

1. Para limpar um filtro, clique em **Limpar** e clique no filtro no painel de filtro.

## <a name="browse-an-opc-ua-server"></a>Procurar um servidor de OPC UA

Ao implantar o acelerador de solução, você provisiona automaticamente servidores OPC UA simulados que pode procurar por meio do navegador de solução. Com os servidores simulados, você pode experimentar mais facilmente o acelerador de solução sem a necessidade de implantar servidores reais. Se deseja conectar um servidor de OPC UA real à solução, confira o tutorial [Conectar seu dispositivo OPC UA ao acelerador de solução da Fábrica Conectada](iot-accelerators-connected-factory-gateway-deployment.md).

1. Clique no **ícone do navegador** na barra de navegação do painel:

    [![Navegador do servidor do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Escolha um dos servidores da lista que mostra os servidores implantados no Acelerador de solução:

    [![Lista do servidor do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Clique em **Conectar**. Uma caixa de diálogo de segurança será exibida. Para a simulação, é seguro clicar em **Continuar**.

1. Clique em um dos nós na árvore de servidor para expandi-lo. Nós que estão publicando telemetria têm uma marcação ao lado deles:

    [![Árvore do servidor do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Clique com o botão direito do mouse em um item para ler, gravar, publicar ou chamar esse nó. As ações disponíveis para você dependem de suas permissões e dos atributos do nó. A opção de leitura exibe um painel de contexto mostrando o valor do nó específico. A opção de gravação exibe um painel de contexto em que você pode inserir um novo valor. A opção de chamada exibe um nó em que você pode inserir os parâmetros para a chamada.

## <a name="publish-a-node"></a>Publicar um nó

Ao procurar um *servidor OPC UA simulado*, você também pode optar por publicar novos nós. Você pode analisar a telemetria desses nós na solução. Esses *servidores OPC UA simulados* facilitam o teste do acelerador de solução sem implantar dispositivos reais:

1. Navegue até um nó na árvore do navegador de servidor OPC UA que você deseja publicar.

1. Clique com o botão direito do mouse no nó. Clicar em **Publicar**:

    [![Nó de publicação do acelerador de solução Connected Factory](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. É exibido um painel de contexto que informa que a publicação foi bem-sucedida. O nó aparece na exibição do nível de estação com uma marca de seleção ao lado dele:

    [![Sucesso de publicação do acelerador de solução Connected Factory](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Comando e controle

A Fábrica Conectada permite que você comande e controle os dispositivos do setor diretamente da nuvem. Você pode usar esse recurso para responder aos alarmes gerados pelo dispositivo. Por exemplo, você pode enviar um comando para o dispositivo para abrir uma válvula de pressão. Você pode encontrar os comandos disponíveis no nó **StationCommands** na árvore do navegador de servidores OPC UA. Nesse cenário, você abre uma válvula de liberação de pressão na estação de montagem de uma linha de produção em Munique. Para usar a funcionalidade de comando e controle, você deve estar na função de **Administrador** para a implantação do acelerador de solução:

1. Navegue até o nó **StationCommands** na árvore do navegador de servidor OPC UA para Munique, a linha de produção 0, a estação de assembly.

1. Escolha o comando que você deseja usar. Clique com botão direito do nó **OpenPressureReleaseValve**. Clique em **Chamar**:

    [![Comando de chamada do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. É exibido um painel de contexto que informa o método que você está prestes a chamar e quaisquer detalhes de parâmetros. Clique em **Chamar**:

    [![Parâmetros de chamada do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. O painel de contexto é atualizado para informar que a chamada de método foi bem-sucedida. Você pode verificar se a chamada foi bem-sucedida lendo o valor do nó de pressão atualizado como resultado da chamada.

    [![Sucesso de chamada do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Nos bastidores

Ao implantar um acelerador de solução, o processo de implantação criará vários recursos na assinatura do Azure que você selecionou. Você pode exibir esses recursos no [portal](https://portal.azure.com) do Azure. O processo de implantação cria um **grupo de recursos** com um nome baseado no nome escolhido para seu acelerador de solução:

[![Grupo de recurso do acelerador de solução da Fábrica Conectada](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Você pode exibir as configurações de cada recurso selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte do acelerador de solução está no repositório do GitHub [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory).

Quando você terminar, poderá excluir o acelerador de solução de sua assinatura do Azure no site [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard). Esse site permite que você exclua facilmente todos os recursos que foram provisionados quando criou o acelerador de solução.

> [!NOTE]
> Para garantir que você exclua tudo relacionado ao acelerador de solução, exclua-a no site [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard). Não exclua o grupo de recursos no portal.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um acelerador de solução de trabalho, poderá continuar a introdução aos aceleradores de solução IoT lendo os seguintes artigos:

* [Passo a passo do acelerador de solução da Connected Factory](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Conecte o dispositivo ao acelerador de solução da Fábrica Conectada](iot-accelerators-connected-factory-gateway-deployment.md)
* [Permissões no site azureiotsolutions.com](iot-accelerators-permissions.md)
