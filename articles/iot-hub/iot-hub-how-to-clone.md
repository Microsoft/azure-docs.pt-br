---
title: Como clonar um hub IoT do Azure
description: Como clonar um hub IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 370ea2f16632ae18142f0770742e5a52d3cabae0
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151658"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Como clonar um hub IoT do Azure para outra região

Este artigo explora maneiras de clonar um hub IoT e fornece algumas perguntas que você precisa responder antes de começar. Aqui estão vários motivos pelos quais você pode desejar clonar um hub IoT:
 
* Você está movendo sua empresa de uma região para outra, como da Europa para América do Norte (ou vice-versa) e deseja que seus recursos e dados sejam geograficamente próximos ao seu novo local, portanto, você precisa mover o Hub.

* Você está configurando um hub para um ambiente de desenvolvimento versus produção.

* Você deseja fazer uma implementação personalizada da alta disponibilidade de vários hubs. Para obter mais informações, consulte a [seção como obter alta disponibilidade entre regiões do Hub IOT e recuperação de desastres](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Você deseja aumentar o número de [partições](iot-hub-scaling.md#partitions) configuradas para o seu hub. Isso é definido quando você cria o Hub pela primeira vez e não pode ser alterado. Você pode usar as informações neste artigo para clonar o Hub e, quando o clone for criado, aumentar o número de partições.

Para clonar um Hub, você precisa de uma assinatura com acesso administrativo ao Hub original. Você pode colocar o novo hub em um novo grupo de recursos e região, na mesma assinatura que o Hub original ou até mesmo em uma nova assinatura. Você simplesmente não pode usar o mesmo nome porque o nome do hub deve ser globalmente exclusivo.

> [!NOTE]
> Neste momento, não há nenhum recurso disponível para clonar um hub IoT automaticamente. É basicamente um processo manual e, portanto, é razoavelmente propenso a erros. A complexidade da clonagem de um hub é diretamente proporcional à complexidade do Hub. Por exemplo, clonar um hub IoT sem roteamento de mensagens é bem simples. Se você adicionar o roteamento de mensagens como apenas uma complexidade, a clonagem do hub se tornará pelo menos uma ordem de magnitude mais complicada. Se você também mover os recursos usados para os pontos de extremidade de roteamento, essa será outra ordem de magniture mais complicada. 

## <a name="things-to-consider"></a>Itens a serem considerados

Há várias coisas a considerar antes de clonar um hub IoT.

* Verifique se todos os recursos disponíveis no local original também estão disponíveis no novo local. Alguns serviços estão em versão prévia, e nem todos os recursos estão disponíveis em todos os lugares.

* Não remova os recursos originais antes de criar e verificar a versão clonada. Depois de remover um Hub, ele não é feito para sempre, e não há como recuperá-lo para verificar as configurações ou os dados para garantir que o Hub seja replicado corretamente.

* Muitos recursos exigem nomes globalmente exclusivos, portanto, você deve usar nomes diferentes para as versões clonadas. Você também deve usar um nome diferente para o grupo de recursos ao qual o Hub clonado pertence. 

* Os dados do Hub IoT original não são migrados. Isso inclui mensagens de telemetria, comandos da nuvem para o dispositivo (C2D) e informações relacionadas ao trabalho, como agendas e histórico. As métricas e os resultados de log também não são migrados. 

* Para dados ou mensagens roteadas para o armazenamento do Azure, você pode deixar os dados na conta de armazenamento original, transferi-los para uma nova conta de armazenamento na nova região ou deixar os dados antigos em vigor e criar uma nova conta de armazenamento no novo local para os novos dados. Para obter mais informações sobre como mover dados no armazenamento de BLOBs, consulte Introdução [ao AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Os dados para os hubs de eventos e para as filas e os tópicos do barramento de serviço não podem ser migrados. Esses são dados de ponto no tempo e não são armazenados depois que as mensagens são processadas.

* Você precisa agendar o tempo de inatividade para a migração. Clonar os dispositivos para o novo hub leva tempo. Se você estiver usando o método de importação/exportação, os testes de parâmetro de comparação revelaram que pode levar cerca de duas horas para mover 500.000 dispositivos e quatro horas para mover um milhão de dispositivos. 

* Você pode copiar os dispositivos para o novo Hub sem desligar ou alterar os dispositivos. 

    * Se os dispositivos foram provisionados originalmente usando o DPS, o reprovisionamento atualiza as informações de conexão armazenadas em cada dispositivo. 
    
    * Caso contrário, você precisa usar o método de importação/exportação para mover os dispositivos e, em seguida, os dispositivos precisam ser modificados para usar o novo hub. Por exemplo, você pode configurar seu dispositivo para consumir o nome de host do Hub IoT nas propriedades desejadas. O dispositivo usará o nome de host do Hub IoT, desconectará o dispositivo do hub antigo e o reconectará ao novo.
    
* Você precisa atualizar todos os certificados que estiver usando para poder usá-los com os novos recursos. Além disso, você provavelmente tem o Hub definido em uma tabela DNS em algum lugar — será necessário atualizar essas informações de DNS.

## <a name="methodology"></a>Metodologia

Esse é o método geral que recomendamos para mover um hub IoT de uma região para outra. Para o roteamento de mensagens, isso pressupõe que os recursos não estão sendo movidos para a nova região. Para obter mais informações, consulte a [seção sobre roteamento de mensagens](#how-to-handle-message-routing).

   1. Exporte o Hub e suas configurações para um modelo do Resource Manager. 
   
   1. Faça as alterações necessárias no modelo, como atualizar todas as ocorrências do nome e o local do Hub clonado. Para todos os recursos no modelo usado para pontos de extremidade de roteamento de mensagens, atualize a chave no modelo para esse recurso.
   
   1. Importe o modelo para um novo grupo de recursos no novo local. Isso cria o clone.

   1. Depurar conforme necessário. 
   
   1. Adicione qualquer coisa que não tenha sido exportada para o modelo. 
   
       Por exemplo, os grupos de consumidores não são exportados para o modelo. Você precisa adicionar os grupos de consumidores ao modelo manualmente ou usar o [portal do Azure](https://portal.azure.com) depois que o Hub for criado. Há um exemplo de como adicionar um grupo de consumidores a um modelo no artigo [usar um modelo de Azure Resource Manager para configurar o roteamento de mensagens do Hub IOT](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Copie os dispositivos do Hub original para o clone. Isso é abordado na seção [Gerenciando os dispositivos registrados para o Hub IOT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Como tratar o roteamento de mensagens

Se o Hub usar o [Roteamento personalizado](iot-hub-devguide-messages-read-custom.md), a exportação do modelo para o Hub incluirá a configuração de roteamento, mas não inclui os próprios recursos. Você deve escolher se deseja mover os recursos de roteamento para o novo local ou deixá-los em vigor e continuar a usá-los "como estão". 

Por exemplo, digamos que você tenha um Hub no oeste dos EUA que está Roteando mensagens para uma conta de armazenamento (também no oeste dos EUA) e deseja mover o Hub para o leste dos EUA. Você pode mover o Hub e fazer com que ele ainda encaminhe mensagens para a conta de armazenamento no oeste dos EUA, ou você pode mover o Hub e também mover a conta de armazenamento. Pode haver um pequeno impacto no desempenho de mensagens de roteamento para recursos de ponto de extremidade em uma região diferente.

Você pode mover um Hub que usa o roteamento de mensagens com muita facilidade se também não mover os recursos usados para os pontos de extremidade de roteamento. 

Se o Hub usar o roteamento de mensagens, você terá duas opções. 

1. Mova os recursos usados para os pontos de extremidade de roteamento para o novo local.

    * Você deve criar os novos recursos manualmente no [portal do Azure](https://portal.azure.com) ou por meio do uso de modelos do Resource Manager. 

    * Você deve renomear todos os recursos ao criá-los no novo local, pois eles têm nomes globalmente exclusivos. 
     
    * Você deve atualizar os nomes de recursos e as chaves de recurso no modelo do novo hub, antes de criar o novo hub. Os recursos devem estar presentes quando o novo hub for criado.

1. Não mova os recursos usados para os pontos de extremidade de roteamento. Use-os "no local".

   * Na etapa em que você edita o modelo, você precisará recuperar as chaves para cada recurso de roteamento e colocá-las no modelo antes de criar o novo hub. 

   * O Hub ainda faz referência aos recursos de roteamento originais e roteia mensagens para eles conforme configurado.

   * Você terá um pequeno impacto no desempenho porque o Hub e os recursos do ponto de extremidade de roteamento não estão no mesmo local.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Preparar para migrar o Hub para outra região

Esta seção fornece instruções específicas para migrar o Hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Localize o Hub original e exporte-o para um modelo de recurso.

1. Entre no [portal do Azure](https://portal.azure.com). 

1. Vá para **grupos de recursos** e selecione o grupo de recursos que contém o Hub que você deseja mover. Você também pode acessar **recursos** e encontrar o Hub dessa maneira. Selecione o Hub.

1. Selecione **Exportar modelo** na lista de propriedades e configurações para o Hub. 

   ![Captura de tela mostrando o comando para exportar o modelo para o Hub IoT.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecione **baixar** para baixar o modelo. Salve o arquivo em algum lugar em que você possa encontrá-lo novamente. 

   ![Captura de tela mostrando o comando para baixar o modelo para o Hub IoT.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Exibição do modelo 

1. Vá para a pasta de downloads (ou para qualquer pasta usada quando você exportou o modelo) e localize o arquivo zip. Abra o arquivo zip e localize o arquivo chamado `template.json` . Selecione-o e, em seguida, selecione CTRL + C para copiar o modelo. Vá para uma pasta diferente que não esteja no arquivo zip e cole o arquivo (Ctrl + V). Agora você pode editá-lo.
 
    O exemplo a seguir é para um hub genérico sem configuração de roteamento. É um hub de camada S1 (com 1 unidade) chamado **ContosoTestHub29358** na região **westus**. Este é o modelo exportado.

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

Você precisa fazer algumas alterações antes de poder usar o modelo para criar o novo hub na nova região. Use [vs Code](https://code.visualstudio.com) ou um editor de texto para editar o modelo.

#### <a name="edit-the-hub-name-and-location"></a>Editar o nome e o local do Hub

1. Remova a seção de parâmetros na parte superior – é muito mais simples usar apenas o nome do Hub, pois não vamos ter vários parâmetros. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Altere o nome para usar o nome (novo) real em vez de recuperá-lo de um parâmetro (que você removeu na etapa anterior). 

    Para o novo hub, use o nome do Hub original mais o *clone* da cadeia de caracteres para criar o novo nome. Comece limpando o nome e o local do Hub.
    
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

    Em seguida, você descobrirá que os valores de **path** contêm o nome do hub antigo. Altere-os para usar o novo. Esses são os valores de caminho em **eventHubEndpoints** chamados **Events** e **OperationsMonitoringEvents**.

    Quando terminar, a seção pontos de extremidade do hub de eventos deverá ter a seguinte aparência:

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

Ao exportar o modelo do Resource Manager para um Hub que tem o roteamento configurado, você verá que as chaves desses recursos não são fornecidas no modelo exportado – seu posicionamento é indicado por asteriscos. Você deve preenchê-los indo até esses recursos no portal e recuperando as chaves **antes** de importar o modelo do novo hub e criar o Hub. 

1. Recupere as chaves necessárias para qualquer um dos recursos de roteamento e coloque-as no modelo. Você pode recuperar as chaves do recurso no [portal do Azure](https://portal.azure.com). 

   Por exemplo, se você estiver Roteando mensagens para um contêiner de armazenamento, localize a conta de armazenamento no Portal. Na seção Configurações, selecione **chaves de acesso**e, em seguida, copie uma das chaves. Esta é a aparência da chave quando você exporta o modelo pela primeira vez:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depois de recuperar a chave de conta da conta de armazenamento, coloque-a no modelo na cláusula `AccountKey=****` no lugar dos asteriscos. 

1. Para as filas do barramento de serviço, obtenha a chave de acesso compartilhado que corresponde ao SharedAccessKeyName. Aqui está a chave e o `SharedAccessKeyName` no JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. O mesmo se aplica aos tópicos do barramento de serviço e conexões do hub de eventos.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Criar os novos recursos de roteamento no novo local

Esta seção se aplica somente se você estiver movendo os recursos usados pelo hub para os pontos de extremidade de roteamento.

Se desejar mover os recursos de roteamento, você deverá configurar manualmente os recursos no novo local. Você pode criar os recursos de roteamento usando o [portal do Azure](https://portal.azure.com)ou exportando o modelo do Resource Manager para cada um dos recursos usados pelo roteamento de mensagens, editando-os e importando-os. Depois que os recursos forem configurados, você poderá importar o modelo do Hub (que inclui a configuração de roteamento).

1. Crie cada recurso usado pelo roteamento. Você pode fazer isso manualmente usando o [portal do Azure](https://portal.azure.com)ou criar os recursos usando modelos do Resource Manager. Se você quiser usar modelos, estas são as etapas a serem seguidas:

    1. Para cada recurso usado pelo roteamento, exporte-o para um modelo do Resource Manager.
    
    1. Atualize o nome e o local do recurso. 

    1. Atualize todas as referências cruzadas entre os recursos. Por exemplo, se você criar um modelo para uma nova conta de armazenamento, precisará atualizar o nome da conta de armazenamento nesse modelo e qualquer outro modelo que faça referência a ele. Na maioria dos casos, a seção de roteamento no modelo para o Hub é o único outro modelo que faz referência ao recurso. 

    1. Importe cada um dos modelos, que implanta cada recurso.

    Depois que os recursos usados pelo roteamento estiverem configurados e em execução, você poderá continuar.

1. No modelo do Hub IoT, altere o nome de cada um dos recursos de roteamento para seu novo nome e atualize o local, se necessário. 

Agora você tem um modelo que criará um novo hub que parece quase exatamente como o Hub antigo, dependendo de como você decidiu manipular o roteamento.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Mover--criar o novo hub na nova região carregando o modelo

Crie o novo hub no novo local usando o modelo. Se você tiver recursos de roteamento que serão movidos, os recursos deverão ser configurados no novo local e as referências no modelo serão atualizadas para corresponder. Se você não estiver movendo os recursos de roteamento, eles deverão estar no modelo com as chaves atualizadas.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. 

1. Na caixa de pesquisa, coloque "implantação de modelo" e selecione Enter.

1. Selecione **implantação de modelo (implantar usando modelos personalizados)**. Isso levará você para uma tela para o Implantação de modelo. Selecione **Criar**. Você verá esta tela:

   ![Captura de tela mostrando o comando para criar seu próprio modelo](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selecione **criar seu próprio modelo no editor**, o que permite que você carregue o modelo de um arquivo. 

1. Selecione **carregar arquivo**. 

   ![Captura de tela mostrando o comando para carregar um arquivo de modelo](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Procure o novo modelo que você editou e selecione-o e, em seguida, selecione **abrir**. Ele carrega seu modelo na janela Editar. Clique em **Salvar**. 

   ![Captura de tela mostrando o carregamento do modelo](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Preencha os campos a seguir.

   **Assinatura**: selecione a assinatura a ser usada.

   **Grupo de recursos**: Crie um novo grupo de recursos em um novo local. Se você já tiver uma nova configuração, poderá selecioná-la em vez de criar uma nova.

   **Local**: se você selecionou um grupo de recursos existente, ele será preenchido para que você corresponda ao local do grupo de recursos. Se você criou um novo grupo de recursos, esse será seu local.

   **Eu concordo a caixa de seleção**: isso basicamente diz que você concorda em pagar pelos recursos que está criando.

1. Selecione o botão **Comprar**.

O Portal agora valida o modelo e implanta o Hub clonado. Se você tiver dados de configuração de roteamento, eles serão incluídos no novo hub, mas apontarão para os recursos no local anterior.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Gerenciando os dispositivos registrados para o Hub IoT

Agora que seu clone está em execução, você precisa copiar todos os dispositivos do Hub original para o clone. 

Há várias maneiras de realizar isso. Você usou originalmente o [DPS (serviço de provisionamento de dispositivos)](../iot-dps/about-iot-dps.md)para provisionar os dispositivos ou não fez isso. Se você fez isso, isso não é difícil. Se você não fez isso, isso pode ser muito complicado. 

Se você não usou o DPS para provisionar seus dispositivos, poderá ignorar a próxima seção e começar a [usar a importação/exportação para mover os dispositivos para o novo hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Usando o DPS para reprovisionar os dispositivos no novo hub

Para usar o DPS para mover os dispositivos para o novo local, consulte [como reprovisionar dispositivos](../iot-dps/how-to-reprovision.md). Quando tiver terminado, você poderá exibir os dispositivos no [portal do Azure](https://portal.azure.com) e verificar se eles estão no novo local.

Vá para o novo hub usando o [portal do Azure](https://portal.azure.com). Selecione o Hub e, em seguida, selecione **dispositivos IOT**. Você verá os dispositivos que foram reprovisionados para o Hub clonado. Você também pode exibir as propriedades do Hub clonado. 

Se você implementou o roteamento, teste e certifique-se de que suas mensagens são roteadas para os recursos corretamente.

### <a name="committing-the-changes-after-using-dps"></a>Confirmando as alterações depois de usar o DPS

Essa alteração foi confirmada pelo serviço DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Reverter as alterações depois de usar o DPS. 

Se você quiser reverter as alterações, provisione novamente os dispositivos do novo hub para o antigo.

Agora você terminou de migrar seu hub e seus dispositivos. Você pode ignorar a [limpeza](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Usando Import-Export para mover os dispositivos para o novo hub

O aplicativo se destina ao .NET Core, para que você possa executá-lo no Windows ou no Linux. Você pode baixar o exemplo, recuperar suas cadeias de conexão, definir os sinalizadores para os quais você deseja executar e executá-lo. Você pode fazer isso sem nunca abrir o código.

### <a name="downloading-the-sample"></a>Baixar o exemplo

1. Use os exemplos do IoT C# desta página: [exemplos de IOT do Azure para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Baixe o arquivo zip e descompacte-o no computador. 

1. O código pertinente está em./iot-hub/Samples/service/ImportExportDevicesSample. Você não precisa exibir ou editar o código para executar o aplicativo.

1. Para executar o aplicativo, especifique três cadeias de conexão e cinco opções. Você passa esses dados como argumentos de linha de comando ou usa variáveis de ambiente ou usa uma combinação dos dois. Vamos passar as opções no como argumentos de linha de comando e as cadeias de conexão como variáveis de ambiente. 

   O motivo para isso é porque as cadeias de conexão são longas e destinadas, o que é improvável de ser alterado, mas talvez você queira alterar as opções e executar o aplicativo mais de uma vez. Para alterar o valor de uma variável de ambiente, você precisa fechar a janela de comando e o Visual Studio ou VS Code, o que estiver usando. 

### <a name="options"></a>Opções

Aqui estão as cinco opções que você especifica ao executar o aplicativo. Vamos colocá-los na linha de comando em um minuto.

*   **Devices** (argumento 1) – Defina como true se você quiser adicionar dispositivos virtuais que são gerados para você. Eles são adicionados ao Hub de origem. Além disso, defina **numToAdd** (argumento 2) para especificar quantos dispositivos você deseja adicionar. O número máximo de dispositivos que você pode registrar em um hub é 1 milhão. A finalidade dessa opção é para teste – você pode gerar um número específico de dispositivos e, em seguida, copiá-los para outro hub.

*   **copyDevices** (argumento 3)--Defina como true para copiar os dispositivos de um hub para outro. 

*   **deleteSourceDevices** (argumento 4)--Defina como true para excluir todos os dispositivos registrados no Hub de origem. Recomendamos aguardar até que você tenha certeza de que todos os dispositivos foram transferidos antes de executar isso. Depois de excluir os dispositivos, você não pode obtê-los de volta.

*   **deleteDestDevices** (argumento 5)--Defina como true para excluir todos os dispositivos registrados para o Hub de destino (o clone). Talvez você queira fazer isso se quiser copiar os dispositivos mais de uma vez. 

O comando básico será a *execução de dotnet* – isso diz ao .net para criar o arquivo csproj local e, em seguida, executá-lo. Você adiciona seus argumentos de linha de comando ao final antes de executá-lo. 

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

### <a name="using-environment-variables-for-the-connection-strings"></a>Usando variáveis de ambiente para as cadeias de conexão

1. Para executar o exemplo, você precisa das cadeias de conexão para os hubs IoT antigos e novos e para uma conta de armazenamento que pode ser usada para arquivos de trabalho temporários. Armazenaremos os valores para eles em variáveis de ambiente.

1. Para obter os valores da cadeia de conexão, entre no [portal do Azure](https://portal.azure.com). 

1. Coloque as cadeias de conexão em algum lugar em que você possa recuperá-las, como o bloco de notas. Se você copiar o seguinte, poderá colar as cadeias de conexão diretamente onde elas vão. Não adicione espaços em volta do sinal de igual ou altere o nome da variável. Além disso, você não precisa de aspas duplas em volta das cadeias de conexão. Se você colocar aspas em volta da cadeia de conexão da conta de armazenamento, ela não funcionará.

   Para o Windows, é assim que você define as variáveis de ambiente:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Para o Linux, é assim que você define as variáveis de ambiente:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Para as cadeias de conexão do Hub IoT, vá para cada Hub no Portal. Você pode pesquisar em **recursos** para o Hub. Se você souber o grupo de recursos, poderá ir para **grupos de recursos**, selecionar o grupo de recursos e, em seguida, selecionar o Hub na lista de ativos desse grupo de recursos. 

1. Selecione **políticas de acesso compartilhado** nas configurações do Hub e, em seguida, selecione **iothubowner** e copie uma das cadeias de conexão. Faça o mesmo para o Hub de destino. Adicione-os aos comandos SET apropriados.

1. Para a cadeia de conexão da conta de armazenamento, localize a conta de armazenamento em **recursos** ou em seu **grupo de recursos** e abra-a. 
   
1. Na seção Configurações, selecione **chaves de acesso** e copie uma das cadeias de conexão. Coloque a cadeia de conexão em seu arquivo de texto para o comando SET apropriado. 

Agora você tem as variáveis de ambiente em um arquivo com os comandos SET e sabe quais são seus argumentos de linha de comando. Vamos executar o exemplo.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Executando o aplicativo de exemplo e usando argumentos de linha de comando

1. Abra una janela de prompt de comando. Selecione Windows e digite `command prompt` para obter a janela do prompt de comando.

1. Copie os comandos que definem as variáveis de ambiente, um de cada vez, e cole-os na janela de prompt de comando e selecione Enter. Quando tiver terminado, digite `SET` na janela de prompt de comando para ver suas variáveis de ambiente e seus valores. Depois de copiá-los para a janela de prompt de comando, você não precisa copiá-los novamente, a menos que abra uma nova janela de prompt de comando.

1. Na janela do prompt de comando, altere os diretórios até que você esteja em./ImportExportDevicesSample (onde o arquivo ImportExportDevicesSample. csproj existe). Em seguida, digite o seguinte e inclua seus argumentos de linha de comando.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    O comando dotnet cria e executa o aplicativo. Como você está passando as opções ao executar o aplicativo, você pode alterar os valores deles sempre que executar o aplicativo. Por exemplo, talvez você queira executá-lo uma vez e criar novos dispositivos, executá-lo novamente e copiar esses dispositivos para um novo hub e assim por diante. Você também pode executar todas as etapas na mesma execução, embora seja recomendável não excluir nenhum dispositivo até que você tenha certeza de que concluiu a clonagem. Aqui está um exemplo que cria 1000 dispositivos e os copia para o outro hub.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Depois de verificar se os dispositivos foram copiados com êxito, você pode remover os dispositivos do hub de origem como este:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Executando o aplicativo de exemplo usando o Visual Studio

1. Se você quiser executar o aplicativo no Visual Studio, altere o diretório atual para a pasta onde o arquivo IoTHubServiceSamples. sln reside. Em seguida, execute este comando na janela de prompt de comando para abrir a solução no Visual Studio. Você deve fazer isso na mesma janela de comando em que você define as variáveis de ambiente, portanto, essas variáveis são conhecidas.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Clique com o botão direito do mouse no projeto *ImportExportDevicesSample* e selecione **definir como projeto de inicialização**.    
    
1. Defina as variáveis na parte superior de Program.cs na pasta ImportExportDevicesSample para as cinco opções.

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

1. Selecione F5 para executar o aplicativo. Depois de concluir a execução, você poderá exibir os resultados.

### <a name="view-the-results"></a>Exibir os resultados 

Você pode exibir os dispositivos no [portal do Azure](https://portal.azure.com) e verificar se eles estão no novo local.

1. Vá para o novo hub usando o [portal do Azure](https://portal.azure.com). Selecione o Hub e, em seguida, selecione **dispositivos IOT**. Você vê os dispositivos que acabou de copiar do hub antigo para o Hub clonado. Você também pode exibir as propriedades do Hub clonado. 

1. Verifique se há erros de importação/exportação acessando a conta de armazenamento do Azure no [portal do Azure](https://portal.azure.com) e procurando no `devicefiles` contêiner para o `ImportErrors.log` . Se esse arquivo estiver vazio (o tamanho é 0), não haverá erros. Se você tentar importar o mesmo dispositivo mais de uma vez, ele rejeitará o dispositivo na segunda vez e adicionará uma mensagem de erro ao arquivo de log.

### <a name="committing-the-changes"></a>Confirmando as alterações 

Neste ponto, você copiou o Hub para o novo local e migrou os dispositivos para o novo clone. Agora você precisa fazer alterações para que os dispositivos funcionem com o Hub clonado.

Para confirmar as alterações, aqui estão as etapas que você precisa executar: 

* Atualize cada dispositivo para alterar o nome de host do Hub IoT para apontar o nome de host do Hub IoT para o novo hub. Você deve fazer isso usando o mesmo método usado ao provisionar o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tem que se referem ao Hub antigo para apontar para o novo hub.

* Depois que você terminar, o novo Hub deverá estar em execução. O Hub antigo não deve ter nenhum dispositivo ativo e estar em um estado desconectado. 

### <a name="rolling-back-the-changes"></a>Reverter as alterações

Se você decidir reverter as alterações, estas são as etapas a serem executadas:

* Atualize cada dispositivo para alterar o nome de host do Hub IoT para apontar o nome de host do Hub IoT para o Hub antigo. Você deve fazer isso usando o mesmo método usado ao provisionar o dispositivo pela primeira vez.

* Altere todos os aplicativos que você tem que se referem ao novo hub para apontar para o Hub antigo. Por exemplo, se você estiver usando a análise do Azure, talvez seja necessário reconfigurar sua [entrada de Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Exclua o novo hub. 

* Se você tiver recursos de roteamento, a configuração no Hub antigo ainda deve apontar para a configuração de roteamento correta e deve funcionar com esses recursos depois que o Hub for reiniciado.

### <a name="checking-the-results"></a>Verificando os resultados 

Para verificar os resultados, altere sua solução de IoT para apontar para o Hub no novo local e executá-lo. Em outras palavras, execute as mesmas ações com o novo hub que você realizou com o Hub anterior e certifique-se de que elas funcionam corretamente. 

Se você implementou o roteamento, teste e certifique-se de que suas mensagens são roteadas para os recursos corretamente.

## <a name="clean-up"></a>Limpar

Não limpe até que você realmente esteja certo de que o novo Hub esteja em execução e os dispositivos estejam funcionando corretamente. Além disso, certifique-se de testar o roteamento se você estiver usando esse recurso. Quando estiver pronto, limpe os recursos antigos executando estas etapas:

* Se você ainda não fez isso, exclua o Hub antigo. Isso remove todos os dispositivos ativos do Hub.

* Se você tiver recursos de roteamento movidos para o novo local, poderá excluir os recursos de roteamento antigos.

## <a name="next-steps"></a>Próximas etapas

Você clonou um hub IoT em um novo hub em uma nova região, completo com os dispositivos. Para obter mais informações sobre como executar operações em massa em relação ao registro de identidade em um hub IoT, consulte [importar e exportar identidades de dispositivo do Hub IOT em massa](iot-hub-bulk-identity-mgmt.md).

Para obter mais informações sobre o Hub IoT e o desenvolvimento para o Hub, consulte os artigos a seguir.

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)

* [Tutorial de roteamento do Hub IoT](tutorial-routing.md)

* [Visão geral do gerenciamento de dispositivos do Hub IoT](iot-hub-device-management-overview.md)

* Se você quiser implantar o aplicativo de exemplo, consulte [implantação de aplicativo do .NET Core](/dotnet/core/deploying/index).