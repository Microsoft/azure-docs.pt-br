---
title: Como clonar um hub IoT do Azure
description: Como clonar um hub IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 7f5553cc51927d878487b0875e72873451a3de3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059574"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Como clonar um hub IoT do Azure para outra região

Este artigo explora maneiras de clonar um Hub IoT e fornece algumas perguntas que você precisa responder antes de começar. Estes são vários motivos pelos quais você pode desejar clonar um hub IoT:
 
* Você está mudando sua empresa de uma região para outra, como da Europa para a América do Norte (ou vice-versa), e deseja que os seus recursos e os dados estejam geograficamente próximos à nova localização. Portanto, você precisa migrar o hub.

* Você está configurando um hub para um ambiente de desenvolvimento em comparação com um de produção.

* Você deseja fazer uma implementação personalizada da alta disponibilidade de vários hubs. Para obter mais informações, confira a [seção Como obter alta disponibilidade entre regiões do artigo Alta disponibilidade e recuperação de desastre do Hub IoT](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Você deseja aumentar o número de [partições](iot-hub-scaling.md#partitions) configuradas para o seu hub. Isso é definido quando você cria o hub pela primeira vez e não pode ser alterado. Use as informações deste artigo para clonar o hub e, quando o clone for criado, aumente o número de partições.

Para clonar um hub, você precisa ter uma assinatura com acesso administrativo ao hub original. Você pode colocar o novo hub em um novo grupo de recursos e um anova região, na mesma assinatura do hub original ou, até mesmo, em uma nova assinatura. Não é possível simplesmente usar o mesmo nome, porque o nome do hub deve ser globalmente exclusivo.

> [!NOTE]
> No momento, não há nenhum recurso disponível para clonar um hub IoT automaticamente. É basicamente um processo manual e, portanto, razoavelmente propenso a erros. A complexidade da clonagem de um hub é diretamente proporcional à complexidade do hub. Por exemplo, clonar um hub IoT sem roteamento de mensagens é bem simples. Se você adicionar o roteamento de mensagens como apenas uma complexidade, a clonagem do hub se tornará, pelo menos, uma ordem de magnitude mais complicada. Se você também mover os recursos usados para os pontos de extremidade de roteamento, essa será outra ordem de magnitude mais complicada. 

## <a name="things-to-consider"></a>Itens a serem considerados

Há várias coisas a serem consideradas antes de clonar um hub IoT.

* Verifique se todos os recursos disponíveis na localização original também estão disponíveis na nova localização. Alguns serviços estão em versão prévia, e nem todos os recursos estão disponíveis em todos os lugares.

* Não remova os recursos originais antes de criar e verificar a versão clonada. Depois que você remove um hub, ele desaparece para sempre, e não há como recuperá-lo para verificar as configurações ou os dados a fim de garantir que o hub seja replicado corretamente.

* Muitos recursos exigem nomes globalmente exclusivos. Portanto, você precisa usar nomes diferentes para as versões clonadas. Você também deve usar um nome diferente para o grupo de recursos ao qual o hub clonado pertence. 

* Os dados do hub IoT original não são migrados. Isso inclui mensagens de telemetria, comandos C2D (nuvem para dispositivo) e informações relacionadas ao trabalho, como agendamentos e histórico. As métricas e os resultados de log também não são migrados. 

* Quanto às mensagens ou aos dados roteados para o Armazenamento do Azure, você pode manter os dados na conta de armazenamento original, transferi-los para uma nova conta de armazenamento na nova região ou deixar os dados antigos em vigor e criar uma conta de armazenamento na nova localização para os novos dados. Para obter mais informações sobre como mover dados no Armazenamento de Blobs, confira [Introdução ao AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Os dados dos Hubs de Eventos e das filas e dos tópicos do Barramento de Serviço não podem ser migrados. Esses são dados pontuais e não são armazenados depois que as mensagens são processadas.

* É preciso agendar o tempo de inatividade para a migração. A clonagem dos dispositivos para o novo hub leva tempo. Se você está usando o método de Importação/Exportação, os testes de parâmetro de comparação revelaram que pode levar cerca de duas horas para mover 500 mil dispositivos e quatro horas para mover um milhão de dispositivos. 

* Você pode copiar os dispositivos para o novo hub sem desligar nem alterar os dispositivos. 

    * Se os dispositivos foram provisionados originalmente por meio do DPS, o reprovisionamento deles atualiza as informações de conexão armazenadas em cada dispositivo. 
    
    * Caso contrário, você precisa usar o método de Importação/Exportação para mover os dispositivos, e os dispositivos precisam ser modificados para usar o novo hub. Por exemplo, você pode configurar seu dispositivo para consumir o nome do host do Hub IoT nas propriedades desejadas do gêmeo. O dispositivo usará o nome do host do Hub IoT, desconectará o dispositivo do hub antigo e o reconectará ao novo.
    
* Você precisará atualizar todos os certificados que estiver usando para usá-los com os novos recursos. Além disso, provavelmente, você tem o hub definido em uma tabela DNS em algum lugar. Será necessário atualizar essas informações de DNS.

## <a name="methodology"></a>Metodologia

Esse é o método geral que recomendamos para mover um hub IoT de uma região para outra. Para o roteamento de mensagens, isso pressupõe que os recursos não estejam sendo movidos para a nova região. Para obter mais informações, confira a [seção sobre roteamento de mensagens](#how-to-handle-message-routing).

   1. Exporte o hub e as respectivas configurações para um modelo do Resource Manager. 
   
   1. Faça as alterações necessárias no modelo, como atualizar todas as ocorrências do nome e a localização do hub clonado. Para todos os recursos no modelo usado para pontos de extremidade de roteamento de mensagens, atualize a chave no modelo para esse recurso.
   
   1. Importe o modelo para um novo grupo de recursos na nova localização. Isso criará o clone.

   1. Depure-o, conforme necessário. 
   
   1. Adicione qualquer coisa que não tenha sido exportada para o modelo. 
   
       Por exemplo, os grupos de consumidores não são exportados para o modelo. Você precisará adicionar os grupos de consumidores ao modelo manualmente ou usar o [portal do Azure](https://portal.azure.com) depois que o hub for criado. Há um exemplo de como adicionar um grupo de consumidores a um modelo no artigo [Usar um modelo do Azure Resource Manager para configurar o roteamento de mensagens do Hub IoT](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie os dispositivos do hub original para o clone. Isso é abordado na seção [Como gerenciar os dispositivos registrados no hub IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Como tratar o roteamento de mensagens

Se o hub usar o [roteamento personalizado](iot-hub-devguide-messages-read-custom.md), a exportação do modelo para o hub incluirá a configuração de roteamento, mas não incluirá os próprios recursos. Você precisa escolher se deseja mover os recursos de roteamento para a nova localização ou deixá-los em vigor e continuar usando-os "no estado em que se encontram". 

Por exemplo, digamos que você tenha um hub no Oeste dos EUA que esteja roteando mensagens para uma conta de armazenamento (também no Oeste dos EUA) e deseje mover o hub para o Leste dos EUA. Você pode mover o hub e fazer com que ele ainda roteie mensagens para a conta de armazenamento no Oeste dos EUA ou mover o hub e a conta de armazenamento. Pode haver um pequeno impacto no desempenho de mensagens de roteamento para recursos de ponto de extremidade em outra região.

Você poderá mover um hub que usa o roteamento de mensagens com muita facilidade se também não mover os recursos usados para os pontos de extremidade de roteamento. 

Se o hub usar o roteamento de mensagens, você terá duas opções. 

1. Mover os recursos usados para os pontos de extremidade de roteamento para a nova localização.

    * Você precisa criar os recursos manualmente no [portal do Azure](https://portal.azure.com) ou por meio do uso de modelos do Resource Manager. 

    * Você precisa renomear todos os recursos ao criá-los na localização, pois eles têm nomes globalmente exclusivos. 
     
    * Você precisa atualizar os nomes e as chaves de recursos no modelo do novo hub antes de criar o hub. Os recursos deverão estar presentes quando o hub for criado.

1. Não mover os recursos usados para os pontos de extremidade de roteamento. Usá-los "in-loco".

   * Na etapa em que o modelo é editado, você precisará recuperar as chaves para cada recurso de roteamento e colocá-las no modelo antes de criar o hub. 

   * O hub ainda referencia os recursos de roteamento originais e roteia mensagens para eles, conforme configurado.

   * Você terá um pequeno impacto no desempenho porque o hub e os recursos do ponto de extremidade de roteamento não estão na mesma localização.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Preparar a migração do hub para outra região

Esta seção fornece instruções específicas para migrar o hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Localize o hub original e exporte-o para um modelo de recurso.

1. Faça logon no [Portal do Azure](https://portal.azure.com). 

1. Acesse **Grupos de Recursos** e selecione o grupo de recursos que contém o hub que você deseja mover. Você também pode acessar **Recursos** e encontrar o hub dessa maneira. Selecione o hub.

1. Escolha **Exportar modelo** na lista de propriedades e configurações do hub. 

   ![Captura de tela que mostra o comando usado para exportar o modelo para o Hub IoT.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecione **Baixar** para baixar o modelo. Salve o arquivo em um lugar em que possa encontrá-lo novamente. 

   ![Captura de tela que mostra o comando usado para baixar o modelo para o Hub IoT.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Exibição do modelo 

1. Acesse a pasta Downloads (ou para qualquer pasta usada quando você exportou o modelo) e localize o arquivo zip. Abra o arquivo zip e localize o arquivo chamado `template.json`. Selecione-o e escolha CTRL + C para copiar o modelo. Acesse uma pasta diferente que não esteja no arquivo zip e cole o arquivo (CTRL + V). Agora, você pode editá-lo.
 
    O exemplo a seguir refere-se a um hub genérico sem configuração de roteamento. É um hub de camada S1 (com uma unidade) chamado **ContosoTestHub29358** na região **westus**. Este é o modelo exportado.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Editar o modelo 

Você precisa fazer algumas alterações para usar o modelo e criar o hub na nova região. Use o [VS Code](https://code.visualstudio.com) ou um editor de texto para editar o modelo.

#### <a name="edit-the-hub-name-and-location"></a>Editar o nome e a localização do hub

1. Remova a seção de parâmetros do início. É muito mais simples usar apenas o nome do hub, pois não vamos ter vários parâmetros. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Altere o nome para usar o nome (novo) real em vez de recuperá-lo de um parâmetro (que você removeu na etapa anterior). 

    Para o novo hub, use o nome do hub original mais o *clone* da cadeia de caracteres para compor o novo nome. Comece limpando o nome e a localização do hub.
    
    Versão antiga:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nova versão: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Em seguida, você descobrirá que os valores de **path** contêm o nome do hub antigo. Altere-os para usar o novo. Estes são os valores de caminho em **eventHubEndpoints** chamados **events** e **OperationsMonitoringEvents**.

    Quando você terminar, a seção de pontos de extremidade do hub de eventos terá a seguinte aparência:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Atualizar as chaves para os recursos de roteamento que não estão sendo movidos

Ao exportar o modelo do Resource Manager para um hub que tenha o roteamento configurado, você verá que as chaves desses recursos não são fornecidas no modelo exportado: seu posicionamento é indicado por asteriscos. Você precisa preenchê-los acessando esses recursos no portal e recuperando as chaves **antes** de importar o modelo do novo hub e criar o hub. 

1. Recupere as chaves necessárias para um dos recursos de roteamento e coloque-as no modelo. Recupere as chaves do recurso no [portal do Azure](https://portal.azure.com). 

   Por exemplo, se estiver roteando mensagens para um contêiner de armazenamento, localize a conta de armazenamento no portal. Na seção Configurações, selecione **Chaves de acesso** e copie uma das chaves. Esta é a aparência da chave quando você exporta o modelo pela primeira vez:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depois de recuperar a chave de conta da conta de armazenamento, coloque-a no modelo na cláusula `AccountKey=****` no lugar dos asteriscos. 

1. Para as filas do Barramento de Serviço, obtenha a chave de acesso compartilhado que corresponda ao SharedAccessKeyName. Esta é a chave e o `SharedAccessKeyName` no JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Isso também se aplica aos tópicos do Barramento de Serviço e às conexões do Hub de Eventos.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Criar os recursos de roteamento na nova localização

Esta seção só se aplica se você está movendo os recursos usados pelo hub para os pontos de extremidade de roteamento.

Caso deseje mover os recursos de roteamento, configure manualmente os recursos na nova localização. Você pode criar os recursos de roteamento usando o [portal do Azure](https://portal.azure.com) ou exportando o modelo do Resource Manager para cada um dos recursos usados pelo roteamento de mensagens, editando-os e importando-os. Depois que os recursos forem configurados, você poderá importar o modelo do hub (que inclui a configuração de roteamento).

1. Crie cada recurso usado pelo roteamento. Faça isso manualmente usando o [portal do Azure](https://portal.azure.com) ou crie os recursos usando modelos do Resource Manager. Caso deseje usar modelos, estas são as etapas a serem seguidas:

    1. Para cada recurso usado pelo roteamento, exporte-o para um modelo do Resource Manager.
    
    1. Atualize o nome e a localização do recurso. 

    1. Atualize todas as referências cruzadas entre os recursos. Por exemplo, se você criar um modelo para uma nova conta de armazenamento, precisará atualizar o nome da conta de armazenamento nesse modelo e qualquer outro modelo que o referencie. Na maioria dos casos, a seção de roteamento no modelo para o hub é o único outro modelo que referencia o recurso. 

    1. Importe cada um dos modelos, o que implantará cada recurso.

    Depois que os recursos usados pelo roteamento estiverem configurados e em execução, você poderá continuar.

1. No modelo do hub IoT, altere o nome de cada um dos recursos de roteamento para o novo nome e atualize a localização, se necessário. 

Agora você tem um modelo que criará um hub quase parecido com o antigo, dependendo de como decidiu tratar o roteamento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Movimentação: criar o hub na nova região carregando o modelo

Crie o hub na nova localização usando o modelo. Se você tiver recursos de roteamento que serão movidos, os recursos deverão ser configurados na nova localização e as referências no modelo serão atualizadas para correspondência. Se você não estiver movendo os recursos de roteamento, eles deverão estar no modelo com as chaves atualizadas.

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. 

1. Na caixa de pesquisa, insira "implantação de modelo" e selecione ENTER.

1. Selecione **Implantação de modelo (implantar usando modelos personalizados)** . Isso levará você para uma tela da Implantação de modelo. Selecione **Criar**. Você verá esta tela:

   ![Captura de tela que mostra o comando usado para criar um modelo próprio](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Escolha **Criar um modelo próprio no editor**, o que permite carregar o modelo de um arquivo. 

1. Selecione **Carregar arquivo**. 

   ![Captura de tela que mostra o comando usado para carregar um arquivo de modelo](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Procure o novo modelo que você editou, selecione-o e escolha **Abrir**. Ele carregará seu modelo na janela de edição. Clique em **Salvar**. 

   ![Captura de tela que mostra o carregamento do modelo](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Preencha os campos a seguir.

   **Assinatura**: selecione a assinatura a ser usada.

   **Grupo de recursos**: crie um grupo de recursos em uma nova localização. Se você já tem uma nova configuração, selecione-a em vez de criar uma.

   **Localização**: se você tiver selecionado um grupo de recursos existente, esse campo será preenchido de modo a corresponder à localização do grupo de recursos. Se você criou um grupo de recursos, essa é a localização dele.

   **Caixa de seleção Eu concordo**: basicamente, indica que você concorda em pagar pelos recursos que está criando.

1. Selecione o botão **Comprar**.

O portal agora validará o modelo e implantará o hub clonado. Se você tiver dados de configuração de roteamento, eles serão incluídos no novo hub, mas apontarão para os recursos na localização anterior.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Como gerenciar os dispositivos registrados no hub IoT

Agora que o seu clone está em execução, você precisa copiar todos os dispositivos do hub original para o clone. 

Há várias maneiras de realizar isso. Você usou originalmente o [DPS (Serviço de Provisionamento de Dispositivos)](../iot-dps/about-iot-dps.md)para provisionar os dispositivos ou não. Em caso afirmativo, isso não será difícil. Caso contrário, isso poderá ser muito complicado. 

Se você não usou o DPS para provisionar seus dispositivos, ignore a próxima seção e comece com [Como usar a Importação/Exportação para mover os dispositivos para o novo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Como usar o DPS para reprovisionar os dispositivos no novo hub

Para usar o DPS a fim de mover os dispositivos para a nova localização, confira [Como reprovisionar dispositivos](../iot-dps/how-to-reprovision.md). Quando terminar, veja os dispositivos no [portal do Azure](https://portal.azure.com) e verifique se eles estão na nova localização.

Acesse o novo hub usando o [portal do Azure](https://portal.azure.com). Selecione o hub e **Dispositivos IoT**. Você verá os dispositivos que foram reprovisionados no hub clonado. Também poderá ver as propriedades do hub clonado. 

Se você tiver implementado o roteamento, teste e verifique se as mensagens são roteadas para os recursos corretamente.

### <a name="committing-the-changes-after-using-dps"></a>Como confirmar as alterações após o uso do DPS

Essa alteração foi confirmada pelo serviço DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reversão das alterações após o uso do DPS. 

Caso deseje reverter as alterações, reprovisione os dispositivos do novo hub para o antigo.

Você acabou de concluir a migração do hub e dos dispositivos. Prossiga para a seção [Limpeza](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Como usar a Importação/Exportação para mover os dispositivos para o novo hub

O aplicativo se destina ao .NET Core. Portanto, você pode executá-lo no Windows ou no Linux. Baixe o exemplo, recupere as cadeias de conexão, defina os sinalizadores para os bits que deseja executar e execute-o. Você pode fazer isso sem nunca abrir o código.

### <a name="downloading-the-sample"></a>Baixar o exemplo

1. Use os exemplos da IoT para C# desta página: [Exemplos da IoT do Azure para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Baixe o arquivo zip e descompacte-o no computador. 

1. O código pertinente está em ./iot-hub/Samples/service/ImportExportDevicesSample. Não é necessário ver nem editar o código para executar o aplicativo.

1. Para executar o aplicativo, especifique três cadeias de conexão e cinco opções. Transmita esses dados como argumentos de linha de comando ou use variáveis de ambiente ou, então, uma combinação dos dois. Vamos transmitir as opções como argumentos de linha de comando e as cadeias de conexão como variáveis de ambiente. 

   O motivo é que as cadeias de conexão são longas, volumosas é improváveis de serem alteradas, mas talvez seja útil alterar as opções e executar o aplicativo mais de uma vez. Para alterar o valor de uma variável de ambiente, você precisa fechar a janela Comando e o Visual Studio ou o VS Code, o que estiver usando. 

### <a name="options"></a>Opções

Estas são as cinco opções que você especifica ao executar o aplicativo. Vamos colocá-las na linha de comando em um minuto.

*   **addDevices** (argumento 1): defina isso como true caso deseje adicionar dispositivos virtuais que são gerados para você. Eles são adicionados ao hub de origem. Além disso, defina **numToAdd** (argumento 2) para especificar quantos dispositivos você deseja adicionar. O número máximo de dispositivos que você pode registrar em um hub é um milhão. Essa opção destina-se a testes. Você pode gerar um número específico de dispositivos e copiá-los para outro hub.

*   **copyDevices** (argumento 3): defina isso como true para copiar os dispositivos de um hub para outro. 

*   **deleteSourceDevices** (argumento 4): defina isso como true para excluir todos os dispositivos registrados no hub de origem. Recomendamos aguardar até que você tenha certeza de que todos os dispositivos foram transferidos antes de executar isso. Depois de excluir os dispositivos, você não poderá recuperá-los.

*   **deleteDestDevices** (argumento 5): defina isso como true para excluir todos os dispositivos registrados no hub de destino (o clone). O ideal é fazer isso se você quer copiar os dispositivos mais de uma vez. 

O comando básico será *dotnet run*: isso instrui o .NET a compilar o arquivo csproj local e executá-lo. Adicione os argumentos de linha de comando ao final antes de executá-lo. 

A linha de comando será semelhante a estes exemplos:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Como usar variáveis de ambiente para as cadeias de conexão

1. Para executar o exemplo, você precisa das cadeias de conexão dos hubs IoT antigos e novos e de uma conta de armazenamento que possa ser usada para arquivos de trabalho temporários. Armazenaremos os valores delas em variáveis de ambiente.

1. Para obter os valores das cadeias de conexão, entre no [portal do Azure](https://portal.azure.com). 

1. Coloque as cadeias de conexão em um lugar em que possa recuperá-las, como o Bloco de notas. Se você copiar o conteúdo a seguir, poderá colar as cadeias de conexão diretamente no local em que devem ser inseridas. Não adicione espaços em torno do sinal de igual nem altere o nome da variável. Além disso, não são necessárias aspas duplas em torno das cadeias de conexão. Se você colocar aspas em torno da cadeia de conexão da conta de armazenamento, isso não funcionará.

   Para o Windows, defina as variáveis de ambiente desta forma:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Para o Linux, defina as variáveis de ambiente assim:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Para as cadeias de conexão do hub IoT, acesse cada hub no portal. Pesquise em **Recursos** para encontrar o hub. Se você conhece o grupo de recursos, acesse **Grupos de recursos**, selecione o grupo de recursos e escolha o hub na lista de ativos desse grupo de recursos. 

1. Selecione **Políticas de acesso compartilhado** nas configurações do hub e escolha **iothubowner** e copie uma das cadeias de conexão. Faça o mesmo para o hub de destino. Adicione-as aos comandos SET apropriados.

1. Para a cadeia de conexão da conta de armazenamento, localize a conta de armazenamento em **Recursos** ou no respectivo **Grupo de recursos** e abra-a. 
   
1. Na seção Configurações, selecione **Chaves de acesso** e copie uma das cadeias de conexão. Coloque a cadeia de conexão no arquivo de texto para o comando SET apropriado. 

Agora você tem as variáveis de ambiente em um arquivo com os comandos SET e sabe quais são os argumentos de linha de comando. Vamos executar o exemplo.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Como executar o aplicativo de exemplo e usar argumentos de linha de comando

1. Abra una janela de prompt de comando. Selecione Windows e digite `command prompt` para obter a janela do prompt de comando.

1. Copie os comandos que definem as variáveis de ambiente, um de cada vez, cole-os na janela do prompt de comando e selecione ENTER. Quando terminar, digite `SET` na janela do prompt de comando para ver as variáveis de ambiente e os respectivos valores. Depois de copiá-los para a janela do prompt de comando, você não precisará copiá-los novamente, a menos que abra uma nova janela do prompt de comando.

1. Na janela do prompt de comando, altere os diretórios até que você esteja em ./ImportExportDevicesSample (local do arquivo ImportExportDevicesSample.csproj). Em seguida, digite o conteúdo a seguir e inclua os argumentos de linha de comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    O comando dotnet cria e executa o aplicativo. Como você está transmitindo as opções ao executar o aplicativo, os valores delas podem ser alterados sempre que você executa o aplicativo. Por exemplo, o ideal é executá-lo uma vez e criar dispositivos, executá-lo novamente e copiar esses dispositivos para um novo hub etc. Você também pode executar todas as etapas na mesma execução, embora não recomendemos excluir nenhum dispositivo até que você tenha certeza de que concluiu a clonagem. Este é um exemplo que cria mil dispositivos e os copia para o outro hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Depois de verificar se os dispositivos foram copiados com êxito, remova os dispositivos do hub de origem desta forma:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Como executar o aplicativo de exemplo usando o Visual Studio

1. Caso deseje executar o aplicativo no Visual Studio, altere o diretório atual para a pasta em que reside o arquivo IoTHubServiceSamples.sln. Em seguida, execute este comando na janela do prompt de comando para abrir a solução no Visual Studio. Você precisa fazer isso na mesma janela Comando em que define as variáveis de ambiente, de modo que elas sejam conhecidas.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Clique com o botão direito do mouse no projeto *ImportExportDevicesSample* e selecione **Definir como projeto de inicialização**.    
    
1. Defina as variáveis no início de Program.cs na pasta ImportExportDevicesSample para as cinco opções.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selecione F5 para executar o aplicativo. Depois que ele concluir a execução, você poderá ver os resultados.

### <a name="view-the-results"></a>Exibir os resultados 

Veja os dispositivos no [portal do Azure](https://portal.azure.com) e verifique se eles estão na nova localização.

1. Acesse o novo hub usando o [portal do Azure](https://portal.azure.com). Selecione o hub e **Dispositivos IoT**. Você verá os dispositivos que acabou de copiar do hub antigo para o hub clonado. Também poderá ver as propriedades do hub clonado. 

1. Verifique se há erros de importação/exportação acessando a conta de armazenamento do Azure no [portal do Azure](https://portal.azure.com) e procurando o `ImportErrors.log` no contêiner `devicefiles`. Se esse arquivo estiver vazio (o tamanho é 0), não haverá erros. Se você tentar importar o mesmo dispositivo mais de uma vez, ele rejeitará o dispositivo na segunda vez e adicionará uma mensagem de erro ao arquivo de log.

### <a name="committing-the-changes"></a>Como confirmar as alterações 

Neste ponto, você copiou o hub para a nova localização e migrou os dispositivos para o novo clone. Agora você precisa fazer alterações para que os dispositivos funcionem com o hub clonado.

Para fazer commit das alterações, confira estas etapas que você precisará executar: 

* Atualize cada dispositivo para alterar o nome do host do Hub IoT de modo a apontar o nome do host do Hub IoT para o novo hub. Faça isso usando o mesmo método que usou ao provisionar o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tem referentes ao hub antigo para que apontem para o novo hub.

* Depois que você terminar, o novo hub deverá estar em execução. O hub antigo não deve ter nenhum dispositivo ativo deve estar em um estado desconectado. 

### <a name="rolling-back-the-changes"></a>Como reverter as alterações

Se você decidir reverter as alterações, estas são as etapas a serem executadas:

* Atualize cada dispositivo para alterar o nome do host do Hub IoT de modo a apontar o nome do host do Hub IoT para o hub antigo. Faça isso usando o mesmo método que usou ao provisionar o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tem referentes ao novo hub para que apontem para o hub antigo. Por exemplo, caso você esteja usando o Azure Analytics, talvez seja necessário reconfigurar a [entrada do Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Exclua o novo hub. 

* Se você tiver recursos de roteamento, a configuração no hub antigo ainda deverá apontar para a configuração de roteamento correta e deverá funcionar com esses recursos depois que o hub for reiniciado.

### <a name="checking-the-results"></a>Como verificar os resultados 

Para verificar os resultados, altere sua solução de IoT a fim de apontá-la para o hub na nova localização e execute-o. Em outras palavras, com o novo hub, execute as mesmas ações realizadas com o hub anterior e verifique se elas funcionam corretamente. 

Se você tiver implementado o roteamento, teste e verifique se as mensagens são roteadas para os recursos corretamente.

## <a name="clean-up"></a>Limpar

Não limpe nada até que você realmente tenha certeza de que o novo hub está em execução e os dispositivos estão funcionando corretamente. Além disso, lembre-se de testar o roteamento se você estiver usando esse recurso. Quando estiver pronto, limpe os recursos antigos executando estas etapas:

* Se você ainda não fez isso, exclua o hub antigo. Isso removerá todos os dispositivos ativos do hub.

* Se você tiver recursos de roteamento transferidos para a nova localização, exclua os recursos de roteamento antigos.

## <a name="next-steps"></a>Próximas etapas

Em uma nova região, você clonou um hub IoT em um novo hub completo com os dispositivos. Para obter mais informações sobre como executar operações em massa no registro de identidade em um hub IoT, confira [Importar e exportar identidades de dispositivo Hub IoT em massa](iot-hub-bulk-identity-mgmt.md).

Para obter mais informações sobre o Hub IoT e o desenvolvimento para o hub, confira os artigos a seguir.

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)

* [Tutorial de roteamento do Hub IoT](tutorial-routing.md)

* [Visão geral do gerenciamento de dispositivos Hub IoT](iot-hub-device-management-overview.md)

* Caso deseje implantar o aplicativo de exemplo, confira [Implantação de aplicativo .NET Core](/dotnet/core/deploying/index).