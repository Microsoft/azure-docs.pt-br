---
title: "Visão geral de fábrica conectada do Azure IoT Suite | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de fábrica conectada do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.openlocfilehash: d502c8e2e2715899279f6ebcf7ed89c19a1bb9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>Introdução à solução pré-configurada de fábrica conectada

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam vários serviços de IoT do Azure para fornecer soluções de ponta a ponta que implementam cenários comuns de negócios de IoT. A solução pré-configurada de *fábrica conectada* conecta aos dispositivos industriais e os monitora. Você pode usar a solução para analisar o fluxo de dados de dispositivos e promover a lucratividade e a produtividade operacionais.

Este tutorial mostra como provisionar a solução pré-configurada de fábrica conectada. Ele também explica os recursos básicos da solução pré-configurada. Você pode acessar muitos desses recursos no *painel* de solução que é implantado como parte da solução pré-configurada:

![Painel de solução pré-configurada de fábrica conectada][img-cf-home]

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [!NOTE]
> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
> 
> 

## <a name="provision-the-solution"></a>Provisionar a solução

1. Faça logon em azureiotsuite.com usando as credenciais de sua conta do Azure e clique em "**+**" para criar uma nova solução.
2. Clique em **Selecionar** no bloco **Fábrica conectada**.
3. Digite um **Nome de solução** para a solução pré-configurada de fábrica conectada.
4. Selecione a **Assinatura** e a **Região** que você deseja usar para provisionar a solução.
5. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>Enquanto você aguarda até que o processo de provisionamento seja concluído

1. Clique no bloco da sua solução com o status **Provisionamento** .
2. Observe os **estados de Provisionamento** à medida que os serviços do Azure são implantados em sua assinatura do Azure.
3. Após o provisionamento ser concluído, o status será alterado para **Pronto**.
4. Clique no bloco para ver os detalhes da solução no painel à direita.

> [!NOTE]
> Se estiver tendo problemas para implantar a solução pré-configurada, dê uma olhada em [Permissões no site azureiotsuite.com][lnk-permissions] e nas [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md). Se os problemas persistirem, crie um tíquete de serviço no [portal][lnk-portal].

Há detalhes que você esperaria ver e que não estão listados para sua solução? Envie sugestões de recursos no [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="scenario-overview"></a>Visão geral do cenário

Quando você implantar a solução pré-configurada de fábrica conectada, ela será pré-populada com recursos que permitem que você percorra um cenário industrial comum. Nesse cenário, várias fábricas conectadas à solução relatam os valores de dados necessários para calcular a OEE (eficiência geral de equipamentos) e KPIs (indicadores chave de desempenho). As seções a seguir mostram como:

* Monitorar fábrica, linhas de produção, OEE de estação e valores de KPI
* Analisar os dados de telemetria gerados por esses dispositivos usando Análises de Séries Temporais do Azure
* Agir sobre alertas para corrigir problemas

Um recurso-chave desse cenário é que você pode executar todas essas ações remotamente no painel da solução. Não é necessário acesso físico aos dispositivos.

## <a name="view-the-solution-dashboard"></a>Exibir o painel de solução

O painel de solução permite que você gerencie a solução implantada. É uma representação hierárquica de uma configuração global de fábrica. Por exemplo, você pode exibir KPIs e OEE, publicar novos nós para alertas de telemetria e de ação.

1. Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, escolha **Iniciar** para abrir o portal de solução de fábrica conectada em uma nova guia.

    ![Iniciar a solução pré-configurada][img-launch-solution]

1. Por padrão, o portal de solução mostra o *painel*. Para navegar para outras áreas do portal, use o menu no lado esquerdo da página.

    ![Painel de solução pré-configurada de fábrica conectada][cf-img-menu]

O painel exibe as seguintes informações:

* Um painel **Lista de fábricas** que mostra o status, o local e a configuração atual de produção na solução. Quando você executa a solução pela primeira vez, há vários dispositivos simulados. A simulação de linha de produção é composta de três servidores OPC UA reais por linha de produção que executam tarefas simuladas e compartilham dados. Para obter mais informações sobre o OPC UA, consulte as [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md).
* Um **mapa** que exibe o local de cada dispositivo conectado à solução. A solução pode usar a API do Bing Maps para plotar informações no mapa. Se sua assinatura estiver habilitada para a API do Bing Maps Enterprise, esse recurso será usado automaticamente. Caso contrário, confira as [Perguntas frequentes][lnk-faq] para aprender a fazer o mapa dinâmico.
* Um painel **Alertas** que exibe alertas gerados quando um valor KPI/OEE ou de telemetria excede um limite específico.
* Um painel **Eficiência Geral de Equipamento** que mostra os valores OEE para toda a empresa ou a fábrica/produção linha/estação que você está exibindo. Esse valor é agregado da exibição de estação para o nível corporativo. A figura de OEE e seus elementos constituintes podem ser mais analisados.
* Painel **Indicadores Chave de Desempenho** que exibe o número de unidades produzidas e a energia usada por toda a empresa ou a linha de produção/fábrica/estação que você está exibindo. Esses valores são agregados de uma exibição de estação para o nível corporativo.

## <a name="view-factories"></a>Exibir fábricas

O painel *Fábricas* mostra a localização geográfica de todas as fábricas da solução, seus status e a configuração de produção atual. Na lista de locais, você pode navegar para os outros níveis na hierarquia de solução. As linhas na lista são hiperlinks que vinculam os detalhes das linhas de produção nesse local. Em seguida, é possível analisar os detalhes da linha de produção e a exibição de nível de estação. Você também pode aplicar um filtro à lista.

![Fábricas de solução pré-configurada de fábrica conectada][cf-img-factories] 

1. O **painel Fábrica** mostra a lista de fábricas para essa solução.

2. A lista de fábrica inicialmente mostra seis fábricas criadas pelo processo de provisionamento. Você pode adicionar dispositivos simulados e físicos extras à solução.

3. Para exibir os detalhes de uma fábrica, clique na linha da lista de fábricas.

4. Para exibir os detalhes de uma linha de produção, clique na linha na lista.

5. Para exibir os nós OPC UA publicados de uma estação na linha de produção, clique na linha na lista.

6. Para exibir detalhes sobre um nó específico na estação, clique na linha na lista. Essa ação abre o painel de contexto com visualizações de Informações da Série Temporal. Clique nesses gráficos para continuar a análise no ambiente do explorador de Análises de Séries Temporais.

## <a name="view-map"></a>Exibir mapa

Se sua assinatura tiver acesso à API do Bing Maps, o mapa de *Fábricas* mostrará a localização geográfica e o status de todas as fábricas na solução. Para analisar os detalhes de local, clique nos locais exibidos no mapa.

![Mapa de solução pré-configurada de fábrica conectada][cf-img-map]

## <a name="view-alerts"></a>Exibir alertas

O painel **Alerta** mostra os alertas gerados devido a um valor relatado ou a um valor calculado de OEE/KPI que excedeu seu limite configurado. Este painel exibe alertas em cada nível da hierarquia do modo de exibição de nível de estação para o modo de exibição global. Os alertas contêm uma descrição do alerta, data, hora, local e número de ocorrências. Você pode se aprofundar nos dados que causaram o alerta usando os dados de informações da série temporal. Os dados de Informações de Série Temporal são visualizados em alertas, quando aplicável. Se for Administrador, você poderá executar ações de padrão em relação aos alertas, como:

* Feche o alerta.
* Reconhecer o alerta.

Opcionalmente, você pode executar ações mais complexas. Por exemplo, para o nó Pressure OPC UA do Assembly, você pode:

* Exiba informações de suporte em uma página da Web em uma nova janela do navegador.
* Mitigar a causa do alerta chamando um método de OPC UA no dispositivo.
* Suprima a disponibilidade das ações padrão.

    ![Alertas de solução pré-configurada de fábrica conectada][cf-img-alerts]

> [!NOTE]
> Esses alertas são gerados por regras que são especificadas em um arquivo de configuração da solução pré-configurada. Essas regras podem gerar alertas quando os valores de nó de UA OPC ou valores de OEE ou KPI excedem o limite configurado.

1. O **Painel de alertas** mostra os alertas gerados nesta solução.

2. Para exibir os detalhes de um alerta, clique no alerta no painel de alertas.

3. Para analisar melhor os dados de alerta, clique no gráfico no painel de alerta para abrir o ambiente de soluções de informações da série temporal.

4. Para resolver o alerta, várias ações estão disponíveis no painel de alerta. Escolha a opção apropriada para você e clique no botão de comando de execução de ação.

## <a name="view-overall-equipment-efficiency"></a>Exibir a eficiência geral do equipamento

O OEE classifica a eficiência do processo de fabricação, usando uma chave parâmetros operacionais relacionados à produção. OEE é um padrão de medida do setor calculado multiplicando-se a taxa de disponibilidade, a taxa de desempenho e a taxa de qualidade: OEE = disponibilidade x qualidade x desempenho.

![OEE de solução pré-configurada de fábrica conectada][cf-img-oee]

1. Para exibir o OEE para qualquer nível na hierarquia, navegue até o modo de exibição específico de que você precisa. O OEE para essa exibição é mostrado no painel junto com cada um dos elementos que compõem a porcentagem de OEE.

2. Para analisar melhor o OEE para qualquer nível dos dados da hierarquia, clique na porcentagem de OEE, de disponibilidade, de desempenho ou de qualidade. É exibido um painel de contexto com Informações da Série de Tempo com visualizações que mostra dados da última hora, das últimas 24 horas e dos últimos sete dias.

    ![Visualização de TSI de solução pré-configurada de fábrica conectada][cf-img-tsi-visualization]

3. Para analisar melhor os dados de alerta, clique no gráfico no painel de alerta. Essa ação abre o ambiente de explorador Insights de Informações da Série Temporal.

    ![Explorador de TSI de solução pré-configurada de fábrica conectada][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Exibir Indicadores Chave de Desempenho

A solução fornece dois indicadores-chave de desempenho, *unidades por hora* e *energia usada em kWh*.

![KPI de solução pré-configurada de fábrica conectada][cf-img-kpi]

1. Para exibir as unidades por hora ou energia usadas para qualquer nível na hierarquia, navegue até o modo de exibição específico de que você precisa. As unidades por hora e energia usadas são exibidas no painel.

2. Para analisar melhor as unidades por hora ou a energia usada em qualquer nível na hierarquia, clique no medidor do painel **Indicadores chave de desempenho**. É exibido um painel de contexto com Informações da Série de Tempo com visualizações, habilitando-o a exibir dados da última hora, das últimas 24 horas e dos últimos sete dias.

## <a name="scenario-review"></a>Análise do cenário

Nesse cenário, você monitorou os valores de OEE e KPIs de fábricas no painel. Você usou então Informações da Série Temporal para fornecer mais informações e analisar ainda mais os dados de telemetria de OEE e KPIs para ajudar na detecção de anomalias. Você também usou o painel de alerta para exibir os problemas com as fábricas e usou as ações disponíveis para resolver o alerta.

## <a name="other-features"></a>Outros recursos

As seções a seguir descrevem alguns recursos adicionais de solução de fábrica conectada que não são descritos no cenário anterior.

## <a name="apply-filters"></a>Aplicar filtros

1. Clique na **divisa** para exibir uma lista de filtros disponíveis no painel nos locais de fábrica ou no painel de alertas.

2. O painel de filtros é exibido para você. 

    ![Filtros de solução pré-configurados de fábrica conectada][cf-img-alert-filter]

3. Escolha o filtro necessário. Também é possível digitar texto livre nos campos de filtro.

4. O filtro é aplicado para você. O estado do filtro também é mostrado no painel por meio de um funil que é exibido nas tabelas de fábricas e alertas.

    ![Filtros de solução pré-configurados de fábrica conectada][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Um filtro ativo não afeta os valores OEE e KPI exibidos. Ele apenas filtra o conteúdo da lista.

5. Para limpar um filtro, clique no funil e clique no filtro no painel de contexto de filtro. O texto **Tudo** é exibido nas tabelas de alertas e fábricas.

## <a name="browse-an-opc-ua-server"></a>Procurar um servidor de OPC UA

Ao implantar a solução pré-configurada, você provisiona automaticamente servidores OPC UA simulados que pode procurar por meio do navegador de solução. Esses servidores são *servidores OPC UA simulados*. Com os servidores simulados, você pode experimentar mais facilmente a solução pré-configurada sem a necessidade de implantar servidores físicos reais. Se deseja conectar um servidor de OPC UA real à solução, confira o tutorial [Conectar seu dispositivo OPC UA à solução pré-configurada de fábrica conectada][lnk-connect-cf].

1. Clique no **ícone de fábrica** na barra de navegação do painel.

    ![Navegador de servidor de chamada de solução pré-configurada de fábrica conectada][cf-img-server-browser]

2. Escolha um dos servidores na lista pré-configurada. Esta lista mostra os servidores que são implantados na solução pré-configurada.

    ![Seleção de chamada de solução pré-configurada de fábrica conectada][cf-img-server-choice]

3. Clique em **Conectar**. Uma caixa de diálogo de segurança será exibida. Para a simulação, é seguro clicar em **Continuar**

4. Clique em um dos nós na árvore de servidor para expandi-lo. Nós que estão publicando telemetria têm uma marcação ao lado deles.

    ![Árvore de chamada de solução pré-configurada de fábrica conectada][cf-img-server-tree]

5. Clique com o botão direito do mouse em um item para ler, gravar, publicar ou chamar esse nó. As ações disponíveis para você dependem de suas permissões e dos atributos do nó. A opção de leitura de exibe um painel de contexto mostrando o valor de nó específico. A opção de gravação exibe um painel de contexto em que você pode inserir um novo valor. A opção de chamada exibe um nó em que você pode inserir os parâmetros para a chamada.

## <a name="publish-a-node"></a>Publicar um nó

Ao procurar um *servidor OPC UA simulado*, você também pode optar por publicar novos nós. Você pode analisar a telemetria desses nós na solução. Esses *servidores OPC UA simulados* facilitam o teste da solução pré-configurada sem implantar dispositivos físicos reais.

1. Navegue até um nó na árvore do navegador de servidor OPC UA que você deseja publicar.

2. Clique com o botão direito do mouse no nó.

3. Escolha **Publicar**.

    ![A fábrica conectada publica o nó][cf-img-publish-node]

4. É exibido um painel de contexto que informa que a publicação foi bem-sucedida. O nó aparece na exibição do nível de estação com uma marca de seleção ao lado dele.

    ![Publicação com êxito de chamada de solução pré-configurada de fábrica conectada][cf-img-publish-success]

## <a name="command-and-control"></a>Comando e controle

A fábrica conectada permite que você comande e controle os dispositivos do setor diretamente da nuvem. Você pode usar esse recurso para responder aos alertas gerados pelo dispositivo. Por exemplo, você pode enviar um comando para o dispositivo da nuvem. Você pode encontrar os comandos disponíveis no nó **StationCommands** na árvore do navegador de servidores OPC UA. Nesse cenário, você abre uma válvula de liberação de pressão na estação de montagem de uma linha de produção em Munique. Para usar a funcionalidade de comando e controle, você deve estar na função de **Administrador** para a implantação da solução pré-configurada.

1. Navegue até o **StationCommands** nó na árvore do navegador do servidor UA OPC.

2. Escolha o comando que você deseja usar.

3. Clique com o botão direito do mouse no nó.

4. Escolha **Chamar**.

    ![Comando de chamada de solução pré-configurada de fábrica conectada][cf-img-call-command]

5. É exibido um painel de contexto que informa o método que você está prestes a chamar e quaisquer detalhes de parâmetros aplicáveis.

6. Escolha **Chamar**.

    ![Contexto de chamada de solução pré-configurada de fábrica conectada][cf-img-call-context]

7. O painel de contexto é atualizado para informar que a chamada de método foi bem-sucedida. Você pode verificar se a chamada foi bem-sucedida lendo o valor do nó de pressão atualizado como resultado da chamada.

    ![Sucesso de chamada de solução pré-configurada de fábrica conectada][cf-img-call-success]


## <a name="behind-the-scenes"></a>Nos bastidores

Ao implantar uma solução pré-configurada, o processo de implantação criará vários recursos na assinatura do Azure que você selecionou. Você pode exibir esses recursos no [portal][lnk-portal] do Azure. O processo de implantação cria um **grupo de recursos** com um nome baseado no nome escolhido para sua solução pré-configurada:

![Solução pré-configurada no portal do Azure][img-cf-portal]

Você pode exibir as configurações de cada recurso selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte para a solução pré-configurada. O código-fonte da solução pré-configurada de fábrica conectada está no repositório do GitHub [azure-iot-connected-factory][lnk-cfgithub]:

Quando você terminar, poderá excluir a solução pré-configurada de sua assinatura do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Esse site permite que você exclua facilmente todos os recursos que foram provisionados quando criou a solução pré-configurada.

> [!NOTE]
> Para garantir que você exclua tudo relacionado à solução pré-configurada, exclua-a no site [azureiotsuite.com][lnk-azureiotsuite]. Não exclua o grupo de recursos no portal.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou uma solução de trabalho pré-configurada, poderá continuar a introdução ao Suite IoT lendo os seguintes artigos:

* [Passo a passo de solução pré-configurada de fábrica conectada][lnk-rm-walkthrough]
* [Conecte o dispositivo à solução pré-configurada de fábrica conectada][lnk-connect-cf]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md