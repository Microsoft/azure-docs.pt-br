---
title: "Introdução às soluções pré-configuradas | Microsoft Docs"
description: "Siga este tutorial para aprender a implantar uma solução pré-configurada do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 466825ab78a5ac9773d8beff69cca90ff9db6c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Introdução a soluções pré-configuradas

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam vários serviços de IoT do Azure para fornecer soluções de ponta a ponta que implementam cenários comuns de negócios de IoT. A solução pré-configurada de *monitoramento remoto* conecta-se seus dispositivos e os monitora. Você pode usar a solução para analisar o fluxo de dados de dispositivos e melhorar os resultados de negócios fazendo com que os processos respondam automaticamente a esse fluxo de dados.

Este tutorial mostra como provisionar a solução pré-configurada de monitoramento remoto. Ele também explica os recursos básicos da solução pré-configurada. Você pode acessar muitos desses recursos no *painel* de solução que é implantado como parte da solução pré-configurada:

![Painel de solução pré-configurada de monitoramento remoto][img-dashboard]

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [!NOTE]
> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

Quando você implanta a solução pré-configurada de monitoramento remoto, ela é preenchida com recursos que permitem que você percorra um cenário comum de monitoramento remoto. Nesse cenário, vários dispositivos conectados à solução estão relatando os valores de temperatura inesperados. As seções a seguir mostram como:

* Identificar os dispositivos enviando valores de temperatura inesperados.
* Configurar esses dispositivos para enviar telemetria mais detalhada.
* Corrigir o problema atualizando o firmware nesses dispositivos.
* Verificar se o problema foi resolvido por sua ação.

Um recurso-chave desse cenário é que você pode executar todas essas ações remotamente no painel da solução. Não é necessário acesso físico aos dispositivos.

## <a name="view-the-solution-dashboard"></a>Exibir o painel de solução

O painel de solução permite que você gerencie a solução implantada. Por exemplo, você pode exibir telemetria, adicionar dispositivos e configurar regras.

1. Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, escolha **Iniciar** para abrir o portal de solução de monitoramento remoto em uma nova guia.

    ![Iniciar a solução pré-configurada][img-launch-solution]

1. Por padrão, o portal de solução mostra o *painel*. Você pode navegar para outras áreas do portal de solução usando o menu ao lado esquerdo da página.

    ![Painel de solução pré-configurada de monitoramento remoto][img-menu]

O painel exibe as seguintes informações:

* Um mapa que exibe o local de cada dispositivo conectado à solução. Quando você executa a solução, há 25 dispositivos simulados. Os dispositivos simulados são implementados como trabalhos Web do Azure e a solução usa a API do Bing Mapas para plotar informações no mapa. Confira as [Perguntas frequentes][lnk-faq] para aprender a fazer o mapa dinâmico.
* Um painel **Histórico de Telemetria** que plota telemetria de umidade e temperatura de um dispositivo selecionado em tempo quase real e exibe dados de agregação, como umidade média, mínima e máxima.
* Um painel **Histórico de Alarme** que mostra eventos recentes de alarme quando um valor de telemetria excedeu um limite. Você pode definir seus próprios alarmes além dos exemplos criados pela solução pré-configurada.
* Um painel **Trabalhos** que exibe informações sobre os trabalhos agendados. Você pode agendar seus próprios trabalhos na página **Trabalhos de gerenciamento**.

## <a name="view-alarms"></a>Exibir alarmes

O painel do histórico de alarme mostra que cinco dispositivos estão relatando valores de telemetria maiores que o esperado.

![Histórico de Alarme TODO no painel de solução][img-alarms]

> [!NOTE]
> Esses alarmes são gerados por uma regra que está incluída na solução pré-configurada. Essa regra gera um alerta quando o valor de temperatura enviado por um dispositivo excede 60. Você pode definir suas próprias regras e ações escolhendo [Regras](#add-a-rule) e [Ações](#add-an-action) no menu esquerdo.

## <a name="view-devices"></a>Exibir dispositivos

A lista de *dispositivos* mostra todos os dispositivos registrados na solução. Na lista de dispositivos, você pode exibir e editar os metadados do dispositivo, adicionar ou remover os dispositivos e chamar métodos nos dispositivos. Você pode filtrar e classificar a lista de dispositivos na lista de dispositivos. Você também pode personalizar as colunas mostradas na lista de dispositivos.

1. Escolha **Dispositivos** para mostrar a lista de dispositivos para esta solução.

   ![Exiba a lista de dispositivos no portal de solução][img-devicelist]

1. A lista de dispositivos mostra inicialmente 25 dispositivos simulados criados pelo processo de provisionamento. Você pode adicionar dispositivos simulados e físicos extras à solução.

1. Clique em um dispositivo na lista de dispositivos para exibir os respectivos detalhes.

   ![Exiba os detalhes do dispositivo no portal de solução][img-devicedetails]

O painel **Detalhes do Dispositivo** contém seis seções:

* Uma coleção de links que permite personalizar o ícone do dispositivo, desabilitar o dispositivo, adicionar uma regra, chamar um método ou enviar um comando. Para ter uma comparação de comandos (mensagens do dispositivo para nuvem) e métodos (métodos diretos), consulte [Orientação para comunicações entre o dispositivo e a nuvem][lnk-c2d-guidance].
* A seção **Dispositivo Gêmeo - Marcações** permite editar os valores da marcação para o dispositivo. Você pode exibir os valores da marcação na lista de dispositivos e usar tais valores para filtrar a lista de dispositivos.
* A seção **Dispositivo Gêmeo - Propriedades Desejadas** permite definir os valores da propriedade a ser enviados para o dispositivo.
* A seção **Dispositivo Gêmeo - Propriedades Relatadas** mostra os valores da propriedade enviados do dispositivo.
* A seção **Propriedades do Dispositivo** mostra as informações do registro de identidade, como a ID do dispositivo e as chaves de autenticação.
* A seção **Trabalhos Recentes** mostra as informações sobre qualquer trabalho destinado recentemente a esse dispositivo.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Você pode usar um filtro para exibir apenas os dispositivos que estão enviando valores de temperatura inesperados. A solução pré-configurada de monitoramento remoto inclui o filtro **Dispositivos sem integridade** para mostrar os dispositivos com um valor de temperatura médio maior que 60. Você também pode [criar seus próprios filtros](#add-a-filter).

1. Escolha **Abrir filtro salvo** para exibir uma lista de filtros disponíveis. Escolha **Dispositivos sem integridade** para aplicar o filtro:

    ![Exibir a lista de filtros][img-unhealthy-filter]

1. A lista de dispositivos agora mostra somente os dispositivos com um valor de temperatura média maior que 60.

    ![Exibir a lista filtrada de dispositivo mostrando dispositivos sem integridade][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Atualizar as propriedades desejadas

Você identificou agora um conjunto de dispositivos que precisam de correção. No entanto, você decide se a frequência de dados de 15 segundos não é suficiente para um diagnóstico claro do problema. Alterando a frequência de telemetria para 5 segundos para fornecer a você mais pontos de dados para diagnosticar melhor o problema. Você pode enviar essa alteração de configuração para seus dispositivos remotos no portal de solução. Você pode fazer a alteração uma vez, avaliar o impacto e, em seguida, agir nos resultados.

Siga estas etapas para executar um trabalho que altera a propriedade desejada **TelemetryInterval** para os dispositivos afetados. Quando os dispositivos recebem o novo valor de propriedade **TelemetryInterval**, eles alteram suas configurações para enviar telemetria a cada 5 segundos, em vez de a cada 15 segundos:

1. Enquanto você está mostrando a lista de dispositivos não íntegros na lista de dispositivos, escolha **Agendador de trabalhos** e, em seguida, **Editar dispositivo Gêmeo**.

1. Chame o trabalho **Alterar intervalo de telemetria**.

1. Altere o valor do nome da **Propriedade desejada** **desired.Config.TelemetryInterval** para cinco segundos.

1. Escolha **Agenda**.

    ![Altere a propriedade TelemetryInterval para cinco segundos][img-change-interval]

1. Monitore o progresso do trabalho na página **Trabalhos de Gerenciamento** no portal.

> [!NOTE]
> Se você quiser alterar um valor de propriedade desejada para um dispositivo individual, use a seção **Propriedades Desejadas** no painel **Detalhes do Dispositivo** em vez de executar um trabalho.

Esse trabalho define o valor da propriedade desejada **TelemetryInterval** no dispositivo gêmeo para todos os dispositivos selecionados pelo filtro. Os dispositivos recuperam esse valor do dispositivo gêmeo e atualizam seu comportamento. Quando um dispositivo recupera e processa uma propriedade desejada do dispositivo gêmeo, ele define a propriedade de valor relatado correspondente.

## <a name="invoke-methods"></a>Invocar métodos

Enquanto o trabalho é executado, você observará na lista de dispositivos não íntegros que todos esses dispositivos têm versões de firmware antigas (abaixo da versão 1.6).

![Exiba a versão do firmware relatada para os dispositivos não íntegros][img-old-firmware]

Essa versão de firmware pode ser a causa raiz dos valores de temperatura inesperados porque você sabe que outros dispositivos íntegros foram atualizados recentemente para a versão 2.0. Use o filtro interno **Dispositivos antigos do firmware** para identificar todos os dispositivos com versões antigas do firmware. No portal, você poderá atualizar remotamente todos os dispositivos ainda executando versões antigas de firmware:

1. Escolha **Abrir filtro salvo** para exibir uma lista de filtros disponíveis. Escolha **Dispositivos com firmware antigo** para aplicar o filtro:

    ![Exibir a lista de filtros][img-old-filter]

1. A lista de dispositivos agora mostra somente os dispositivos com versões antigas de firmware. Essa lista inclui 5 dispositivos identificados pelo filtro **Dispositivos não íntegros** e 3 dispositivos adicionais:

    ![Exibir a lista filtrada de dispositivo mostrando dispositivos antigos][img-filtered-old-list]

1. Escolha **Agendador de Trabalho** e, em seguida, **Chamar o Método**.

1. Defina **Nome do Trabalho** para **Atualização do firmware para a versão 2.0**.

1. Escolha **InitiateFirmwareUpdate** como o **Método**.

1. Defina o parâmetro **FwPackageUri** para **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Escolha **Agenda**. O padrão é para que o trabalho seja executado agora.

    ![Crie um trabalho para atualizar o firmware dos dispositivos selecionados][img-method-update]

> [!NOTE]
> Se você deseja invocar um método em um dispositivo individual, escolha **Métodos** no painel **Detalhes do Dispositivo** em vez de executar um trabalho.

Esse trabalho invoca o método direto **InitiateFirmwareUpdate** em todos os dispositivos selecionados pelo filtro. Os dispositivos respondem imediatamente ao Hub IoT e, em seguida, iniciam o processo de atualização de firmware assincronamente. Os dispositivos fornecem informações de status sobre o processo de atualização de firmware através de valores de propriedade relatados, conforme mostrado nas capturas de tela a seguir. Escolha o ícone **Atualizar** para atualizar as informações nas listas de dispositivo e de trabalho:

![Lista de trabalho, mostrando a lista de atualização de firmware em execução][img-update-1]
![Lista de dispositivos, mostrando o status de atualização de firmware][img-update-2]
![Lista de trabalho, mostrando a lista de atualização de firmware concluída][img-update-3]

> [!NOTE]
> Em um ambiente de produção, você pode agendar trabalhos para execução durante uma janela de manutenção designada.

## <a name="scenario-review"></a>Análise do cenário

Nesse cenário, você identificou um problema potencial com alguns de seus dispositivos remotos usando o histórico de alarme no painel e um filtro. Você então usou o filtro e um trabalho para configurar remotamente os dispositivos para fornecer mais informações para ajudar a diagnosticar o problema. Finalmente, você usou um filtro e um trabalho para agendar a manutenção nos dispositivos afetados. Se você retornar para o painel, poderá verificar que não há alarmes provenientes de dispositivos em sua solução. Você pode usar um filtro para verificar se o firmware está atualizado em todos os dispositivos da sua solução e que não há mais dispositivos não íntegros:

![Filtro mostrando que todos os dispositivos têm um firmware atualizado][img-updated]

![Filtro mostrando que todos os dispositivos estão íntegros][img-healthy]

## <a name="other-features"></a>Outros recursos

As seções a seguir descrevem alguns recursos adicionais de solução pré-configurada de monitoramento remoto que não são descritos como parte do cenário anterior.

### <a name="customize-columns"></a>Personalizar colunas

É possível personalizar as informações exibidas na lista de dispositivos clicando no **Editor de colunas**. Você pode adicionar e remover as colunas que exibem os valores de propriedade e marcação relatados. Também pode reorganizar e renomear as colunas:

   ![Editor de colunas na lista de dispositivos][img-columneditor]

### <a name="customize-the-device-icon"></a>Personalizar o ícone do dispositivo

Você pode personalizar o ícone do dispositivo exibido na lista de dispositivos no painel **Detalhes do Dispositivo** como a seguir:

1. Escolha o ícone de lápis para abrir o painel **Editar imagem** de um dispositivo:

   ![Abrir o editor de imagens do dispositivo][img-startimageedit]

1. Carregue uma nova imagem ou use uma das imagens existentes e, em seguida, escolha **Salvar**:

   ![Editar o editor de imagens do dispositivo][img-imageedit]

1. A imagem selecionada agora é exibida na coluna **Ícone** do dispositivo.

> [!NOTE]
> A imagem é colocada no armazenamento de blobs. Uma marcação no dispositivo gêmeo contém um link para a imagem no armazenamento de blobs.

### <a name="add-a-device"></a>Adicionar um dispositivo

Ao implantar a solução pré-configurada, você provisiona automaticamente os 25 dispositivos de exemplo exibidos na lista de dispositivos. Esses dispositivos são *dispositivos simulados* em execução em um Trabalho Web do Azure. Com os dispositivos simulados, você pode experimentar mais facilmente a solução pré-configurada sem a necessidade de implantar dispositivos físicos reais. Se você quiser conectar um dispositivo real à solução, confira o tutorial [Conectar o dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm].

As etapas a seguir mostram como adicionar um dispositivo simulado à solução:

1. Navegue de volta para a lista de dispositivos.

1. Para adicionar um dispositivo, escolha **+ Adicionar um Dispositivo** no canto inferior esquerdo.

   ![Adicionar um dispositivo à solução pré-configurada][img-adddevice]

1. Escolha **Adicionar Novo** no bloco **Dispositivo Simulado**.

   ![Definir novos detalhes do dispositivo no painel][img-addnew]

   Além de criar um novo dispositivo simulado, você também poderá adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para saber mais sobre como conectar dispositivos físicos à solução, confira [Conectar o dispositivo à solução pré-configurada de monitoramento remoto do IoT Suite][lnk-connect-rm].

1. Selecione **Deixe-me definir minha própria ID** de dispositivo e adicione um nome de ID de dispositivo exclusivo, como **mydevice_01**.

1. Escolha **Criar**.

   ![Salvar um novo dispositivo][img-definedevice]

1. Na etapa 3 de **Adicionar um dispositivo simulado**, escolha **Concluído** para retornar à lista de dispositivos.

1. Você pode exibir o dispositivo **Executando** na lista de dispositivos.

    ![Exibir novo dispositivo na lista de dispositivos][img-runningnew]

1. Você também pode exibir a telemetria simulada do novo dispositivo no painel:

    ![Exibir telemetria do novo dispositivo][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Desabilitar e excluir um dispositivo

Você pode desabilitar um dispositivo e depois que ele for desabilitado, poderá removê-lo:

![Desabilitar e remover um dispositivo][img-disable]

### <a name="add-a-rule"></a>Adicionar uma regra

Não existem regras para o novo dispositivo recém-adicionado. Nesta seção, você adiciona uma regra que dispara um alarme quando a temperatura relatada pelo novo dispositivo excede 47 graus. Antes de começar, observe que o histórico de telemetria para o novo dispositivo no painel mostra que a temperatura do dispositivo nunca excede 45 graus.

1. Navegue de volta para a lista de dispositivos.

1. Para adicionar uma regra do dispositivo, selecione o novo dispositivo na **Lista de Dispositivos** e escolha **Adicionar regra**.

1. Crie uma regra que usa **Temperature** como o campo de dados e **AlarmTemp** como a saída quando a temperatura excede 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule]

1. Para salvar suas alterações, escolha **Salvar e Exibir Regras**.

1. Clique em **Comandos** no painel de detalhes do novo dispositivo.

    ![Adicionar uma regra de dispositivo][img-adddevicerule2]

1. Selecione **ChangeSetPointTemp** na lista de comandos e defina **SetPointTemp** como 45. Escolha **Enviar Comando**:

    ![Adicionar uma regra de dispositivo][img-adddevicerule3]

1. Navegue até o painel. Após um curto período, você verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura relatada pelo seu novo dispositivo exceder o limite de 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule4]

1. Você pode revisar e editar todas as regras na página **Regras** do painel:

    ![Listar regras de dispositivo][img-rules]

1. Você pode revisar e editar todas as ações que podem ser realizadas em resposta a uma regra na página **Ações** do painel:

    ![Listar ações de dispositivo][img-actions]

> [!NOTE]
> É possível definir ações que podem enviar uma mensagem de email ou SMS em resposta a uma regra ou se integrar com um sistema de linha de negócios por meio de um [Aplicativo Lógico][lnk-logic-apps]. Para obter mais informações, confira [Conectar o Aplicativo Lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Gerenciar filtros

Na lista de dispositivos, você pode criar, salvar e recarregar os filtros para exibir uma lista personalizada de dispositivos conectados ao hub. Para criar um filtro:

1. Escolha o ícone para editar o filtro acima da lista de dispositivos:

    ![Abrir o editor de filtro][img-editfiltericon]

1. No **Editor de filtro**, adicione os campos, operadores e valores para filtrar a lista de dispositivos. Você pode adicionar várias cláusulas para refinar seu filtro. Escolha **Filtrar** para aplicar o filtro:

    ![Crie um filtro][img-filtereditor]

1. Neste exemplo, a lista é filtrada pelo fabricante e número do modelo:

    ![Lista filtrada][img-filterelist]

1. Para salvar seu filtro com um nome personalizado, escolha o ícone **Salvar como**:

    ![Salvar um filtro][img-savefilter]

1. Para reaplicar um filtro salvo anteriormente, escolha o ícone **Abrir filtro salvo**:

    ![Abrir um filtro][img-openfilter]

Você pode criar filtros com base na id do dispositivo, estado do dispositivo, propriedades desejadas, propriedades relatadas e marcações. Adicione rótulos personalizados para um dispositivo na seção **Rótulos** do painel **Detalhes do Dispositivo** ou execute um trabalho para atualizar os rótulos em vários dispositivos.

> [!NOTE]
> No **Editor de filtro**, você pode usar a **Exibição avançada** para editar o texto da consulta diretamente.

### <a name="commands"></a>Comandos

No painel **Detalhes do Dispositivo**, você pode enviar comandos ao dispositivo. Quando um dispositivo é iniciado, ele envia informações sobre os comandos a que ele dá suporte para a solução. Para obter uma discussão sobre as diferenças entre os métodos e comandos, confira [Opções de nuvem para dispositivo do Hub IoT do Azure][lnk-c2d-guidance].

1. Escolha **Comandos** no painel **Detalhes do Dispositivo**  do dispositivo selecionado:

   ![Comandos de dispositivo no painel][img-devicecommands]

1. Selecione **PingDevice** na lista de comandos.

1. Escolha **Enviar Comando**.

1. Você pode ver o status do comando no histórico de comandos.

   ![Status do comando no painel][img-pingcommand]

A solução rastreia o status de cada comando enviado. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido como **Sucesso**.

## <a name="behind-the-scenes"></a>Nos bastidores

Ao implantar uma solução pré-configurada, o processo de implantação criará vários recursos na assinatura do Azure que você selecionou. Você pode exibir esses recursos no [portal][lnk-portal] do Azure. O processo de implantação cria um **grupo de recursos** com um nome baseado no nome escolhido para sua solução pré-configurada:

![Solução pré-configurada no portal do Azure][img-portal]

Você pode exibir as configurações de cada recurso selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte para a solução pré-configurada. O código-fonte da solução pré-configurada de monitoramento remoto está no repositório do GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* A pasta **DeviceAdministration** contém o código-fonte para o painel.
* A pasta **Simulator** contém o código-fonte do dispositivo simulado.
* A pasta **EventProcessor** contém o código-fonte para o processo de back-end que manipula a telemetria de entrada.

Quando você terminar, poderá excluir a solução pré-configurada de sua assinatura do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Esse site permite que você exclua facilmente todos os recursos que foram provisionados quando criou a solução pré-configurada.

> [!NOTE]
> Para garantir que você excluirá tudo relacionado à solução pré-configurada, exclua-a no site [azureiotsuite.com][lnk-azureiotsuite] e não simplesmente exclua o grupo de recursos no portal.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou uma solução de trabalho pré-configurada, poderá continuar a introdução ao Suite IoT lendo os seguintes artigos:

* [Passo a passo da solução pré-configurada de monitoramento remoto][lnk-rm-walkthrough]
* [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md