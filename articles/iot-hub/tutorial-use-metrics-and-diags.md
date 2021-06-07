---
title: Tutorial – Configurar e usar métricas e logs com um Hub IoT do Azure
description: Tutorial – Saiba como configurar e usar métricas e logs com um Hub IoT do Azure. Isso fornecerá dados a serem analisados para ajudar a diagnosticar problemas que seu hub pode ter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 099b7f4e812e92503c7ed8e3eb733f2e49ccd8b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768063"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Tutorial: Configurar e usar métricas e logs com um hub IoT

Você pode usar o Azure Monitor para coletar métricas e logs para o Hub IoT, o que poderá ajudar você a monitorar a operação de sua solução e resolver problemas quando eles ocorrerem. Neste artigo, você verá como criar gráficos com base em métricas, como criar alertas que sejam disparados de acordo com métricas, como enviar operações e erros do Hub IoT para Logs do Azure Monitor e como verificar se há erros nos logs.

Este tutorial usa o exemplo do Azure do [início rápido de envio de telemetria do .NET](quickstart-send-telemetry-dotnet.md) para enviar mensagens ao hub IoT. Você sempre pode usar um dispositivo ou outro exemplo para enviar mensagens, mas terá que modificar algumas etapas para se adequar.

Alguma familiaridade com os conceitos do Azure Monitor poderá ser útil para iniciar este tutorial. Para saber mais, confira [Monitorar o Hub IoT](monitor-iot-hub.md). Para saber mais sobre as métricas e os logs de recursos emitidos pelo Hub IoT, consulte a [referência de dados de monitoramento](monitor-iot-hub-reference.md).

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
>
> * Usar a CLI do Azure para criar um hub IoT, registrar um dispositivo simulado e criar um workspace do Log Analytics.  
> * Enviar conexões do Hub IoT e logs de recursos de telemetria do dispositivo para Logs do Azure Monitor no workspace do Log Analytics.
> * Usar o gerenciador de métricas para criar um gráfico com base em métricas selecionadas e fixá-lo em seu painel.
> * Criar alertas de métricas para ser notificado por email quando condições importantes ocorrerem.
> * Baixar e executar um aplicativo que simule um dispositivo IoT que envia mensagens para o hub IoT.
> * Exibir os alertas quando as condições ocorrerem.
> * Exibir o gráfico de métricas em seu painel.
> * Exibir erros e operações do Hub IoT em Logs do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- É necessário ter o SDK do .NET Core 2.1 ou versão mais recente em seu computador de desenvolvimento. Você pode fazer o download do SDK do .NET Core para várias plataformas a partir do [.NET](https://www.microsoft.com/net/download/all).

  Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

  ```cmd/sh
  dotnet --version
  ```

- Uma conta de email que possa receber emails.

- Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste tutorial usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisará de um hub IoT, um workspace do Log Analytics e um dispositivo IoT simulado. Esses recursos podem ser criados usando a CLI do Azure ou o Azure PowerShell. Use o mesmo grupo de recursos e o local para todos os recursos. Em seguida, ao concluir o tutorial, você poderá remover tudo em uma única etapa, excluindo o grupo de recursos.

Veja as etapas necessárias.

1. Crie um [grupo de recursos](../azure-resource-manager/management/overview.md).

2. Crie um Hub IoT.

3. Criar um espaço de trabalho do Log Analytics.

4. Registre uma identidade do dispositivo para o dispositivo simulado que envia mensagens para o hub IoT. Salve a cadeia de conexão do dispositivo para usar para configurar o dispositivo simulado.

### <a name="set-up-resources-using-azure-cli"></a>Configurar recursos usando a CLI do Azure

Copie e cole o script no Cloud Shell. Supondo que você já esteja conectado, ele executa o script uma linha de cada vez. Alguns dos comandos podem demorar um pouco para serem executados. Os novos recursos são criados no grupo de recursos *ContosoResources*.

O nome de alguns recursos deve ser exclusivo no Azure. O script gera um valor aleatório com a função `$RANDOM` e o armazena em uma variável. Para esses recursos, o script acrescenta esse valor aleatório a um nome de base para o recurso, tornando o nome do recurso exclusivo.

Apenas um hub IoT gratuito é permitido por assinatura. Se você já tiver um hub IoT gratuito em sua assinatura, exclua-o antes de executar o script ou modifique o script para usar o hub IoT gratuito ou um Hub IoT que use a camada padrão ou básica.

O script imprime o nome do hub IoT, o nome do workspace do Log Analytics e a cadeia de conexão do dispositivo que ele registra. Anote essas informações, pois você precisará delas mais tarde neste artigo.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Ao criar a identidade do dispositivo, você poderá receber o seguinte erro: *Nenhuma chave encontrada para a política iothubowner do Hub IoT ContosoTestHub*. Para corrigir esse erro, atualize a Extensão de IoT da CLI do Azure e, em seguida, execute os dois últimos comandos no script novamente. 
>
>Veja a seguir o comando para atualizar a extensão. Execute este comando na sua instância do Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Coletar logs para conexões e telemetria de dispositivo

O Hub IoT emite logs de recursos para várias categorias de operações; no entanto, para exibir esses logs, você deve criar uma configuração de diagnóstico para enviá-los a um destino. Um desses destinos são os Logs do Azure Monitor, que são coletados em um workspace do Log Analytics. Os logs de recursos do Hub IoT são agrupados em categorias diferentes. Você pode selecionar quais categorias deseja enviar para os Logs do Azure Monitor na configuração de diagnóstico. Neste artigo, coletaremos logs para operações e erros que tenham relação com conexões e telemetria de dispositivo. Para obter uma lista completa das categorias com suporte para o Hub IoT, confira [logs de recursos do Hub IoT](monitor-iot-hub-reference.md#resource-logs).

Para criar uma configuração de diagnóstico para enviar logs de recursos do Hub IoT para logs do Azure Monitor, siga estas etapas:

1. Primeiro, caso ainda não esteja em seu hub no portal, selecione **Grupos de recursos** e selecione o grupo de recursos ContosoResources. Selecione o hub IoT na lista de recursos exibidos.

1. Procure a seção **Monitoramento** na folha Hub IoT. Selecione **Configurações de diagnóstico**. Em seguida, selecione **Adicionar configuração de diagnóstico**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Captura de tela que realça as Configurações do diagnóstico na seção Monitoramento.":::

1. No painel **Configuração de diagnóstico**, dê um nome descritivo à configuração, como "Enviar conexões e telemetria para logs".

1. Em **Detalhes da categoria**, selecione **Conexões** e **Telemetria do Dispositivo**.

1. Em **Detalhes de destino**, selecione **Enviar para o Log Analytics**; em seguida, use o seletor do workspace do Log Analytics para selecionar o workspace que você anotou anteriormente. Quando tiver terminado, a configuração de diagnóstico deverá ficar semelhante à seguinte captura de tela:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Captura de tela mostrando as configurações finais de log de diagnóstico.":::

1. Clique em **Salvar** para salvar as configurações. Feche o painel **Configuração de diagnóstico**. Veja sua nova configuração na lista de configurações de diagnóstico.

## <a name="set-up-metrics"></a>Configurar métricas

Agora, usaremos o gerenciador de métricas para criar um gráfico que exiba as métricas que você deseja controlar. Você fixará esse gráfico ao painel padrão na portal do Azure.

1. No painel esquerdo do hub IoT, selecione **Métricas** na seção **Monitoramento**.

1. Na parte superior da tela, clique em **Últimas 24 horas (Automático)** . Na lista suspensa exibida, selecione **Últimas 4 horas** para **Intervalo de Tempo** e defina **Granularidade de Tempo** como **1 minuto**. Depois, selecione **Local** em **Mostrar hora como**. Selecione **Aplicar** para salvar essas configurações. A configuração agora deve dizer **Hora local: Últimas 4 horas (1 minuto)** .

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Captura de tela mostrando as configurações de hora das métricas.":::

1. No gráfico, há uma configuração de métrica parcial exibida no escopo do seu hub IoT. Deixe os valores de **Escopo** e **Namespace de Métrica** em seus padrões. Selecione a configuração **Métrica** e digite "Telemetria" e, em seguida, selecione **Mensagens de telemetria enviadas** na lista suspensa. A **agregação** será definida automaticamente como **Soma**. Observe que o título do gráfico também é alterado.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Captura de tela que mostra a adição da métrica Mensagens de telemetria enviadas ao gráfico.":::

1. Agora selecione **Adicionar métrica** para adicionar outra métrica ao gráfico. Em **Métrica**, selecione **Número total de mensagens usadas**. A **agregação** será definida automaticamente como **Méd**. Observe novamente que o título do gráfico alterou-se para incluir essa métrica.

   Agora a tela mostra a métrica minimizada para *Mensagens de telemetria enviadas*, além da nova métrica para *Número total de mensagens usadas*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Captura de tela que mostra a adição da métrica Número total de mensagens usadas ao gráfico.":::

1. No canto superior direito do gráfico, selecione **Fixar no painel**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Captura de tela que realça o botão Fixar no painel.":::

1. No painel **Fixar no painel**, selecione a guia **Existente**. Selecione **Privado** e, em seguida, selecione **Painel** na lista suspensa Painel. Por fim, selecione **Fixar** para fixar o gráfico ao seu painel padrão no portal do Azure. Se você não fixar o gráfico em um painel, as configurações não serão mantidas quando você sair do gerenciador de métricas.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Captura de tela que mostra as configurações de Fixar no painel.":::

## <a name="set-up-metric-alerts"></a>Configurar alertas de métricas

Agora, configuraremos alertas para disparar com base em duas métricas: *Mensagens de telemetria enviadas* e *Número total de mensagens usadas*.

A métrica *Mensagens de telemetria enviadas* é uma boa métrica a ser monitorada para rastrear a taxa de transferência de mensagens e evitar a limitação. Para um Hub IoT na camada gratuita, o limite é 100 mensagens por segundo. Com um único dispositivo, não será possível alcançar essa taxa de transferência. portanto, vamos configurar para disparar o alerta se o número de mensagens exceder mil em um período de cinco minutos. Em produção, você pode definir o sinal como um valor mais significativo com base na camada, na edição e no número de unidades do hub IoT.

O *Número total de mensagens usadas* controla o número diário de mensagens usadas. Essa métrica é redefinida todos os dias às 00:00 UTC. Se você exceder a cota diária após um determinado limite, o Hub IoT não aceitará mais mensagens. Para um Hub IoT na camada gratuita, a cota diária é de 8 mil mensagens. Vamos configurar o alerta para disparar se o número total de mensagens exceder 4 mil, 50% da cota. Na prática, você provavelmente definiria esse percentual com um valor mais alto. O valor da cota diária depende da camada, da edição e do número de unidades do seu hub IoT.

Para obter mais informações sobre limites de cota e limitações com o Hub IoT, confira [Cotas e limitação](iot-hub-devguide-quotas-throttling.md).

Para configurar alertas de métrica:

1. Acesse o hub IoT no portal do Azure.

1. Em **Monitoramento**, selecione **Alertas**. Em seguida, selecione **Nova regra de alerta**.  O painel **Criar regra de alerta** se abrirá.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Captura de tela mostrando o painel Criar regra de alerta.":::

    No painel **Criar regra de alerta**, há quatro seções:

    * O **Escopo** já está definido para o seu hub IoT, portanto, deixaremos esta seção como está.
    * A **Condição** define o sinal e as condições que vão disparar o alerta.
    * A seção **Ações** configura o que acontece quando o alerta é disparado.
    * **Detalhes da regra de alerta** permite que você defina um nome e uma descrição para o alerta.

1. Primeiro, configure a condição em que o alerta será disparado.

    1. Em **Condição**, selecione **Adicionar condição**. No painel **Configurar lógica de sinal**, digite "telemetria" na caixa de pesquisa e selecione **Mensagens de telemetria enviadas**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Captura de tela mostrando a seleção da métrica.":::

    1. No painel **Configurar lógica de sinal**, defina ou confirme os seguintes campos em **Lógica de alerta** (você pode ignorar o gráfico):

       **Limite**:  *Estático*.

       **Operador**: *Maior que*.

       **Tipo de agregação**: *Total*.

       **Valor do limite**: 1.000.

       **Granularidade de agregação (Período)** : *5 minutos*.

       **Frequência de avaliação**: *A cada minuto*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Captura de tela mostrando as configurações das condições de alerta.":::

       Essas configurações definem o sinal para totalizar o número de mensagens em um período de cinco minutos. Esse total será avaliado a cada minuto e, se o total dos cinco minutos anteriores exceder mil mensagens, o alerta será disparado.

       Selecione **Concluído** para salvar a lógica do sinal.

1. Agora, configure a ação para o alerta.

    1. De volta ao painel **Criar regra de alerta**, em **Ações**, selecione **Adicionar grupos de ações**. No painel **Selecionar um grupo de ações para anexar a esta regra de alerta**, clique em **Criar grupo de ações**.

    1. Na guia **Básico** no painel **Criar grupo de ações**, atribua um nome e um nome de exibição ao grupo de ações.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Captura de tela mostrando a guia Básico do painel Criar grupo de ações.":::

    1. Selecione a guia **Notificações**. Em **Tipo de notificação**, selecione **Email/mensagem SMS/Push/Voz** na lista suspensa. O painel **Email/mensagem SMS/Push/Voz** de voz é aberto.

    1. No painel **Email/mensagem SMS/Push/Voz**, selecione email e insira seu endereço de email e, em seguida, selecione **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Captura de tela mostrando a configuração de endereço de email.":::

    1. De volta ao painel **Notificações**, insira um nome para a notificação.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Captura de tela mostrando o painel Notificações concluído.":::

    1. (Opcional) Se você selecionar a guia **Ações** e selecionar o menu suspenso **Tipo de ação**, você verá os tipos de ações que podem ser disparados com um alerta. Neste artigo usaremos apenas notificações, portanto ignore as configurações nessa guia.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Captura de tela mostrando os tipos de ação disponíveis no painel Ações.":::

    1. Selecione a guia **Examinar e Criar**, verifique as configurações e selecione **Criar**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Captura de tela mostrando o painel Examinar e Criar.":::

    1. De volta ao painel **Criar regra de alerta**, observe que o novo grupo de ações foi adicionado às ações do alerta.  

1. Por fim, configure os detalhes da regra de alerta e salve a regra de alerta.

    1. No painel **Criar regra de alerta**, em Detalhes da regra de alerta, insira um nome e uma descrição para o alerta; por exemplo, "Alertar se houver mais de mil mensagens em cinco minutos". Não se esqueça de marcar a opção **Habilitar regra de alerta ao criar**. A regra de alerta concluída ficará semelhante a esta captura de tela.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Captura de tela mostrando o painel Criar regra de alerta concluído.":::

    1. Selecione **Criar regra de alerta** para salvar a nova regra.

1. Agora configure outro alerta para o *Número total de mensagens usadas*. Essa métrica é útil se você deseja enviar um alerta quando o número de mensagens usadas se aproximar da cota diária do hub IoT, momento em que o hub IoT começará a rejeitar mensagens. Siga as etapas que você fez antes, com as diferenças a seguir.

    * Para o sinal no painel **Configurar lógica de sinal**, selecione **Número total de mensagens usadas**.

    * No painel **Configurar lógica de sinal**, defina ou confirme os seguintes campos (você pode ignorar o gráfico):

       **Limite**:  *Estático*.

       **Operador**: *Maior que*.

       **Tipo de agregação**: *Máximo*.

       **Valor do limite**: 4000.

       **Granularidade de agregação (Período)** : *1 minuto*.

       **Frequência de avaliação**: *A cada minuto*

       Essas configurações definem o sinal para ser disparado quando o número de mensagens alcançar 4 mil. A métrica é avaliada a cada minuto.

    * Ao especificar a ação para a regra de alerta, basta selecionar o grupo de ações que você criou anteriormente.

    * Para os detalhes do alerta, escolha um nome e uma descrição diferentes do que foi feito anteriormente.

1. Selecione **Alertas**, em **Monitoramento** no painel esquerdo do hub IoT. Agora, selecione **Gerenciar regras de alerta** no menu na parte superior do painel **Alertas**. O painel **Regras** é aberto. Agora você deverá ver seus dois alertas:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Captura de tela mostrando o painel Regras com as novas regras de alerta.":::

1. Feche o painel **Regras**.

Com essas configurações, um alerta será disparado e você receberá uma notificação por email quando mais de mil mensagens forem enviadas em um período de cinco minutos e também quando o número total de mensagens usadas exceder 4 mil (50% da cota diária de um hub IoT na camada gratuita).

## <a name="run-the-simulated-device-app"></a>Executar um aplicativo de dispositivo simulado

Na seção [Configurar recursos](#set-up-resources), você registrou uma identidade do dispositivo para usar para simular o uso de um dispositivo IoT. Nesta seção, você baixa um aplicativo de console do .NET que simula um dispositivo que envia mensagens do dispositivo para nuvem para um Hub IoT, configura-o para enviar essas mensagens ao seu hub IoT e, em seguida, executa-o.

> [!IMPORTANT]
>
> Os alertas podem levar até 10 minutos para serem totalmente configurados e habilitados pelo Hub IoT. Aguarde pelo menos 10 minutos entre o momento em que você configura o último alerta e a execução do aplicativo de dispositivo simulado.

Baixe a solução para a [Simulação de dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Esse link baixa um repositório contendo vários aplicativos. O que você está procurando está em iot-hub/Quickstarts/simulated-device/.

1. Em uma janela de terminal local, navegue até a pasta raiz da solução. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.cs** em seu editor de texto preferido.

    1. Substitua o valor da variável `s_connectionString` pela cadeia de conexão do dispositivo que você anotou quando executou o script para configurar os recursos.

    1. No método `SendDeviceToCloudMessagesAsync`, altere o `Task.Delay` de 1000 para 1, o que reduzirá o tempo entre o envio de mensagens de 1 para 0,001 segundo. A redução desse atraso aumenta o número de mensagens enviadas. (Provavelmente você não obterá uma taxa de 100 mensagens por segundo).

        ```csharp
        await Task.Delay(1);
        ```

    1. Salve as alterações no **SimulatedDevice.cs**.

1. Na janela de terminal local, execute o seguinte comando para instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

1. Na janela de terminal local, execute os seguintes comandos para compilar e executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet run
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Captura de tela mostrando a saída do dispositivo simulado.":::

Deixe o aplicativo ser executado por pelo menos 10 a 15 minutos. O ideal é que ele seja executado até que pare de enviar mensagens (cerca de 20 a 30 minutos). Isso ocorrerá ao exceder a cota de mensagens diária para o hub IoT e parar de aceitar mais mensagens.

> [!NOTE]
> Se você deixar o aplicativo de dispositivo em execução por um período estendido depois que ele parar de enviar mensagens, você poderá se deparar com uma exceção. Você pode ignorar essa exceção com segurança e fechar a janela do aplicativo.

## <a name="view-metrics-chart-on-your-dashboard"></a>Exibir o gráfico de métricas no seu painel

1. No canto superior esquerdo do portal do Azure, abra o menu do portal e, em seguida, selecione **Painel**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Captura de tela de como selecionar seu painel.":::

1. Localize o gráfico que você fixou anteriormente e clique em qualquer lugar no bloco fora dos dados do gráfico para expandi-lo. Ele mostra as mensagens de telemetria enviadas e o número total de mensagens usadas no gráfico. Os números mais recentes aparecem na parte inferior do gráfico. Mova o cursor no gráfico para ver os valores de métrica de horários específicos. Você também pode alterar o valor temporal e a granularidade na parte superior do gráfico para restringir ou expandir os dados para um período de interesse.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Captura de tela mostrando o gráfico de métricas.":::

   Nesse cenário, a taxa de transferência de mensagens do dispositivo simulado não é grande o suficiente para fazer com que o Hub IoT limite as mensagens. Em um cenário que realmente envolva a limitação, você poderá ver que as mensagens de telemetria enviadas excedem o limite do seu hub IoT por um período limitado. Isso serve para acomodar o tráfego intermitente. Para obter detalhes, consulte [traffic shaping](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Exibir os alertas

Quando o número de mensagens enviadas excede os limites definidos nas regras de alerta, você começa a receber alertas por email.

Para ver se há alertas ativos, selecione **Alertas** em **Monitoramento** no painel esquerdo do hub IoT. O painel **Alertas** mostra o número de alertas que foram disparados, classificados por severidade, para o intervalo de tempo especificado.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Captura de tela mostrando o resumo de alertas.":::

Selecione a linha da severidade Sev 3. O painel **Todos os Alertas** será aberto e listará os alertas Sev 3 que foram disparados.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Captura de tela mostrando o painel Todos os Alertas.":::

Selecione um dos alertas para ver os respectivos detalhes.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Captura de tela mostrando detalhes do alerta.":::

Verifique sua caixa de entrada em busca de emails do Microsoft Azure. A linha do assunto descreverá o alerta que foi disparado. Por exemplo, *Azure: Severidade Ativada: 3 Alertar se houver mais de mil mensagens em cinco minutos*. O corpo será semelhante à seguinte imagem:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Captura de tela do email mostrando que os alertas foram disparados.":::

## <a name="view-azure-monitor-logs"></a>Exibir os Logs do Azure Monitor

Na seção [Coletar logs de conexões e telemetria de dispositivo](#collect-logs-for-connections-and-device-telemetry), você criou uma configuração de diagnóstico para enviar logs de recursos emitidos por seu hub IoT para operações de conexão e telemetria de dispositivo para os Logs do Azure Monitor. Nesta seção, você executará uma consulta Kusto em Logs do Azure Monitor para observar os erros que ocorreram.

1. Em **Monitoramento** no painel esquerdo do hub IoT no portal do Azure, selecione **Logs**. Feche a janela inicial de **Consultas** se ela for aberta.

1. No painel Nova Consulta, selecione a guia **Consultas** e, em seguida, expanda **Hub IoT** para ver a lista de consultas padrão.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Captura de tela das consultas padrão do Hub IoT.":::

1. Selecione a consulta *Resumo de erros*. A consulta aparece no painel do Editor de consultas. Selecione **Executar** no painel do editor e observe os resultados da consulta. Expanda uma das linhas para ver os detalhes.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Captura de tela dos logs retornados pela consulta Resumo de erros.":::

   > [!NOTE]
   > Se você não vir nenhum erro, tente executar a consulta *Dispositivos conectados recentemente*. Isso deverá retornar uma linha para o dispositivo simulado.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que você criou neste tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ela remove o hub IoT, o workspace do Log Analytics e o próprio grupo de recursos. Se você tiver fixado gráficos de métricas no painel, precisará removê-las manualmente clicando nos três pontos no canto superior direito de cada gráfico e selecionar **Remover**. Salve as alterações depois de excluir os gráficos.

Para remover o grupo de recursos, use o comando [excluir grupo az](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar métricas e logs do Hub IoT executando as seguintes tarefas:

> [!div class="checklist"]
>
> * Usar a CLI do Azure para criar um hub IoT, registrar um dispositivo simulado e criar um workspace do Log Analytics.  
> * Enviar conexões do Hub IoT e logs de recursos de telemetria do dispositivo para Logs do Azure Monitor no workspace do Log Analytics.
> * Usar o gerenciador de métricas para criar um gráfico com base em métricas selecionadas e fixá-lo em seu painel.
> * Criar alertas de métricas para ser notificado por email quando condições importantes ocorrerem.
> * Baixar e executar um aplicativo que simule um dispositivo IoT que envia mensagens para o hub IoT.
> * Exibir os alertas quando as condições ocorrerem.
> * Exibir o gráfico de métricas em seu painel.
> * Exibir erros e operações do Hub IoT em Logs do Azure Monitor.

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configurar seus dispositivos de um serviço de back-end](tutorial-device-twins.md)