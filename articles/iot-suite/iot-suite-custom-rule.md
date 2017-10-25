---
title: Criar uma regra personalizada no Azure IoT Suite | Microsoft Docs
description: "Como criar uma regra personalizada em uma solução pré-configurada do IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d58c27234ea05a82aaa3e8d72f70c1449980df09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Criar uma regra personalizada na solução pré-configurada de monitoramento remota

## <a name="introduction"></a>Introdução

Nas soluções pré-configuradas, você pode configurar [regras que serão disparadas quando um valor de telemetria de um dispositivo atingir um limite específico][lnk-builtin-rule]. [Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto][lnk-dynamic-telemetry] descreve como você pode adicionar valores personalizados de telemetria, como *ExternalTemperature*, à sua solução. Este artigo mostra como criar uma regra personalizada para tipos de telemetria dinâmicos na sua solução.

Este tutorial usa um dispositivo simulado Node.js simples para gerar telemetria dinâmica a ser enviada ao back-end da solução pré-configurada. Você então adiciona regras personalizadas à solução **RemoteMonitoring** do Visual Studio e implanta esse back-end personalizado na sua assinatura do Azure.

Para concluir este tutorial, você precisará:

* Uma assinatura ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
* [Node.js][lnk-node] versão 0.12.x ou posterior para criar um dispositivo simulado.
* Visual Studio 2015 ou Visual Studio 2017 para modificar o back-end da solução pré-configurada com suas novas regras.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

Faça uma anotação com o nome da solução que você escolheu para sua implantação. Você precisará do nome dessa solução mais tarde neste tutorial.

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

Você pode interromper o aplicativo de console do Node.js quando tiver confirmado que ele está enviando a telemetria de **ExternalTemperature** para a solução pré-configurada. Mantenha a janela do console aberta, porque você executará o aplicativo de console do Node.js novamente depois de adicionar a regra personalizada à solução.

## <a name="rule-storage-locations"></a>Locais de armazenamento de regras

As informações sobre as regras são mantidas em dois locais:

* Tabela **DeviceRulesNormalizedTable** – essa tabela armazena uma referência normalizada para as regras definidas pelo portal de solução. Quando o portal de solução exibe as regras do dispositivo, ele consulta esta tabela para obter as definições de regras.
* Blob de **DeviceRules** – esse blob armazena todas as regras definidas para todos os dispositivos registrados e é definido como uma entrada de referência para os trabalhos do Stream Analytics do Azure.
 
Quando você atualiza uma regra existente ou define uma nova regra no portal de solução, a tabela e o blob são atualizados para refletirem as alterações. A definição de regra exibida no portal vem do armazenamento da tabela e a definição da regra referenciada pelos trabalhos do Stream Analytics vem do blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Atualizar a solução RemoteMonitoring do Visual Studio

As etapas a seguir mostram como modificar a solução RemoteMonitoring do Visual Studio para incluir uma nova regra que usa a telemetria de **ExternalTemperature** enviada do dispositivo simulado:

1. Caso ainda não o tenha feito, faça um clone do repositório **azure-iot-remote-monitoring** em um local adequado na sua máquina local usando o seguinte comando Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. No Visual Studio, abra o arquivo RemoteMonitoring.sln da sua cópia local do repositório **azure-iot-remote-monitoring**.

3. Abra o arquivo Infrastructure\Models\DeviceRuleBlobEntity.cs e adicione uma propriedade **ExternalTemperature** da seguinte maneira:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. No mesmo arquivo, adicione uma propriedade **ExternalTemperatureRuleOutput** da seguinte maneira:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Abra o arquivo Infrastructure\Models\DeviceRuleDataFields.cs e adicione a seguinte propriedade **ExternalTemperature** após a propriedade **Humidity** existente:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. No mesmo arquivo, atualize o método **_availableDataFields** para incluir **ExternalTemperature** da seguinte maneira:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Abra o arquivo Infrastructure\Repository\DeviceRulesRepository.cs e modifique o método **BuildBlobEntityListFromTableRows** da seguinte maneira:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Recompile e reimplante a solução.

Agora, você pode implantar a solução atualizada para sua assinatura do Azure.

1. Abra um prompt de comando elevado e navegue até a raiz da sua cópia local do repositório azure-iot-remote-monitoring.

2. Para implantar a solução atualizada, execute o seguinte comando, substituindo **{nome da implantação}** pelo nome da implantação de solução pré-configurada que você anotou anteriormente:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Atualizar o trabalho do Stream Analytics

Quando a implantação for concluída, você poderá atualizar o trabalho do Stream Analytics para usar novas definições de regras.

1. No Portal do Azure, navegue até o grupo de recursos que contém os recursos de solução pré-configurada. Esse grupo de recursos tem o mesmo nome que você especificou para a solução durante a implantação.

2. Navegue até o trabalho do Stream Analytics {nome da implantação}-Rules. 

3. Clique em **Parar** para interromper a execução do trabalho do Stream Analytics. (Você deve aguardar a interrupção do trabalho para editar a consulta).

4. Clique em **Consulta**. Edite a consulta para incluir a instrução **SELECT** para **ExternalTemperature**. O exemplo a seguir mostra a consulta completa com a nova instrução **SELECT**:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Clique em **Salvar** para alterar a consulta de regra atualizada.

6. Clique em **Iniciar** para iniciar a execução do trabalho do Stream Analytics novamente.

## <a name="add-your-new-rule-in-the-dashboard"></a>Adicionar sua nova regra no painel de controle

Agora, você pode adicionar a regra **ExternalTemperature** a um dispositivo no painel de solução.

1. Navegue até o portal de solução.

2. Navegue até o painel **Dispositivos**.

3. Localize o dispositivo personalizado que você criou que envia a telemetria de **ExternalTemperature** e, no painel **Detalhes do Dispositivo**, clique em **Adicionar Regra**.

4. Selecione **ExternalTemperature** no **Campo de Dados**.

5. Defina o **Limite** como 56. Em seguida, clique em **Salvar e exibir regras**.

6. Retorne ao painel para visualizar o histórico de alarme.

7. Na janela do console que você deixou aberta, inicie o aplicativo de console do Node.js para começar a enviar os dados de telemetria de **ExternalTemperature**.

8. Observe que a tabela **Histórico do Alarme** mostra novos alarmes quando a nova regra é acionada.
 
## <a name="additional-information"></a>Informações adicionais

Alterar o operador **>** é mais complexo e vai além das etapas descritas neste tutorial. Embora você possa alterar o trabalho do Stream Analytics para usar qualquer operador que você queira, refletir esse operador no portal de solução é uma tarefa mais complexa. 

## <a name="next-steps"></a>Próximas etapas
Agora que você já viu como criar regras personalizadas, você pode saber mais sobre as soluções pré-configuradas:

- [Conectar um aplicativo lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite][lnk-logic-app]
- [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md