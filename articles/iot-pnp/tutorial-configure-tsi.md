---
title: Tutorial – Usar o Azure Time Series Insights para armazenar e analisar a telemetria de seu dispositivo IoT Plug and Play do Azure
description: Tutorial – Configurar um ambiente do Time Series Insights e conectar o hub IoT para exibir e analisar a telemetria por meio de seus dispositivos IoT Plug and Play.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28cda9fb6997500f6cd7c4c4349635e7b7a36398
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504270"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Tutorial: Criar e configurar um ambiente do Time Series Insights Gen2

Neste tutorial, você aprende a criar e configurar um ambiente do [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) para integração à sua solução de IoT Plug and Play. Use o Time Series Insights para coletar, processar, armazenar, consultar e visualizar dados de série temporal na escala da IoT (Internet das Coisas).

Neste tutorial, você

> [!div class="checklist"]
> * Provisiona um ambiente do Time Series Insights e conecta o hub IoT como uma origem do evento de streaming.
> * Trabalha com a sincronização do modelo para criar seu [Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md).
> * Usa os arquivos do modelo de exemplo de [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl) que você usou para os dispositivos de controlador de temperatura e termostato.

> [!NOTE]
> Essa integração entre o Time Series Insights e o IoT Plug and Play está em versão prévia. A maneira como os modelos do dispositivo DTDL são mapeados para o Modelo de Série Temporal do Time Series Insights pode mudar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Até agora, você tem:

* Um Hub IoT do Azure.
* Uma instância do DPS (Serviço de Provisionamento de Dispositivos) vinculada ao seu hub IoT. A instância do DPS deve ter um registro de dispositivo individual para seu dispositivo IoT Plug and Play.
* Uma conexão com o hub IoT por meio de um dispositivo de componente único ou de vários componentes que transmite os dados simulados.

Para evitar a necessidade de instalar a CLI do Azure localmente, use o Azure Cloud Shell para configurar os serviços de nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Preparar a origem do evento

O hub IoT criado anteriormente será a [origem do evento](../time-series-insights/concepts-streaming-ingestion-event-sources.md) de seu ambiente do Time Series Insights.

> [!IMPORTANT]
> Desabilite as rotas existentes do Hub IoT. Há um problema conhecido com o uso de um hub IoT com [roteamento](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configurado. Desabilite temporariamente os pontos de extremidade de roteamento. Quando o hub IoT estiver conectado ao Time Series Insights, você poderá habilitar os pontos de extremidade de roteamento novamente.

No hub IoT, crie um grupo de consumidores exclusivo para consumo do Time Series Insights. No exemplo a seguir, substitua `my-pnp-hub` pelo nome do hub IoT usado anteriormente.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Escolha uma ID do Time Series

Ao provisionar seu ambiente do Time Series Insights, você precisará selecionar uma *ID da Série Temporal*. É importante selecionar a ID da Série Temporal apropriada. Essa propriedade é imutável e não pode ser alterada depois de definida. Uma ID da Série Temporal é como uma chave de partição de banco de dados. A ID da Série Temporal funciona como a chave primária do Modelo de Série Temporal. Para obter mais informações, confira [Melhores práticas para a escolha de uma ID do Time Series](../time-series-insights/how-to-select-tsid.md).

Como usuário do IoT Plug and Play, para a ID da Série Temporal, especifique uma _chave composta_ que consista em `iothub-connection-device-id` e `dt-subject`. O hub IoT adiciona essas propriedades do sistema que contêm a identificação do dispositivo IoT Plug and Play e os nomes dos componentes do dispositivo, respectivamente.

Mesmo que seus modelos de dispositivos IoT Plug and Play não usem componentes no momento, inclua `dt-subject` como parte de uma chave composta de modo que possa usar componentes no futuro. Como a ID da Série Temporal é imutável, a Microsoft recomenda habilitar essa opção caso você precise dela no futuro.

> [!NOTE]
> Os exemplos neste artigo são referentes ao dispositivo `TemperatureController` com vários componentes. No entanto, os conceitos são os mesmos para o dispositivo `Thermostat` sem componentes.

## <a name="provision-your-time-series-insights-environment"></a>Provisionar o ambiente do Time Series Insights

Esta seção descreve como provisionar seu ambiente do Azure Time Series Insights Gen2.

Execute o seguinte comando para:

* Criar uma conta de armazenamento do Azure para o [armazenamento frio](../time-series-insights/concepts-storage.md#cold-store) de seu ambiente. Essa conta foi projetada para retenção e análise de longo prazo de dados históricos.
  * No código, substitua `mytsicoldstore` por um nome exclusivo para sua conta de armazenamento frio.
* Criar um ambiente do Azure Time Series Insights Gen2. O ambiente será criado com um armazenamento warm com um período de retenção de sete dias. A conta de armazenamento frio será anexada para retenção infinita.
  * No código, substitua `my-tsi-env` por um nome exclusivo para seu ambiente do Time Series Insights.
  * No código, substitua `my-pnp-resourcegroup` pelo nome do grupo de recursos usado durante a configuração.
  * A propriedade da ID da Série Temporal é `iothub-connection-device-id, dt-subject`.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

Conecte a origem do evento do Hub IoT. Substitua `my-pnp-resourcegroup`, `my-pnp-hub` e `my-tsi-env` pelos valores escolhidos. O seguinte comando referencia o grupo de consumidores do Time Series Insights criado anteriormente:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

No [portal do Azure](https://portal.azure.com), vá até seu grupo de recursos e selecione o novo ambiente do Time Series Insights. Vá até a **URL do Gerenciador do Time Series Insights** mostrada na visão geral da instância:

![Captura de tela da página de visão geral do portal.](./media/tutorial-configure-tsi/view-environment.png)

No Gerenciador, você verá três instâncias:

* &lt;a identificação do dispositivo PnP&gt;, thermostat1
* &lt;a identificação do dispositivo PnP&gt;, thermostat2
* &lt;a identificação do dispositivo PnP&gt;, `null`

> [!NOTE]
> A terceira marca representa a telemetria do próprio `TemperatureController`, como o conjunto de trabalho da memória do dispositivo. Como essa é uma propriedade de nível superior, o valor do nome do componente é nulo. Em uma etapa posterior, você deixará esse nome mais amigável.

![Captura de tela mostrando três instâncias no Gerenciador.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurar a conversão do modelo

Em seguida, você converterá o modelo de dispositivo DTDL no modelo de ativo no Azure Time Series Insights. No Time Series Insights, o Modelo de Série Temporal é uma ferramenta de modelagem semântica para contextualização de dados. O modelo tem três componentes principais:

* [Instâncias do Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-instances) são representações virtuais da série temporal. As instâncias são identificadas exclusivamente pela ID da Série Temporal.
* [Hierarquias do Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organizam instâncias especificando nomes de propriedade e suas relações.
* [Tipos do Modelo de Série Temporal](../time-series-insights/concepts-model-overview.md#time-series-model-types) ajudam a definir [variáveis](../time-series-insights/concepts-variables.md) ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica.

### <a name="define-your-types"></a>Definir os tipos

Você pode começar ingerindo dados no Azure Time Series Insights Gen2 sem ter um modelo predefinido. Quando a telemetria é recebida, o Time Series Insights tenta resolver automaticamente as instâncias de série temporal com base nos valores da propriedade da ID da Série Temporal. Todas as instâncias recebem o *tipo padrão*. Você precisa criar um tipo manualmente para categorizar corretamente as instâncias.

Os seguintes detalhes descrevem o método mais simples para sincronizar os modelos de dispositivo DTDL com os tipos do Modelo de Série Temporal:

* O identificador de modelo do gêmeo digital passa a ser a ID do tipo.
* O nome do tipo pode ser o nome do modelo ou o nome de exibição.
* A descrição do modelo passa a ser a descrição do tipo.
* Pelo menos uma variável de tipo é criada para cada telemetria com um esquema numérico.
  * Somente tipos de dados numéricos podem ser usados para as variáveis. Porém, se um valor é enviado como outro tipo que possa ser convertido, `"0"`, por exemplo, você pode usar uma função de [conversão](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions), como `toDouble`.
* O nome da variável pode ser o nome da telemetria ou o nome de exibição.
* Ao definir a variável de Expressão de Série Temporal, veja o nome da telemetria na conexão e o respectivo tipo de dados da telemetria.

| DTDL JSON | JSON do tipo de Modelo de Série Temporal | Valor de exemplo |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |
|`contents` (matriz)| `variables` (objeto)  | Veja o exemplo a seguir.

![Captura de tela mostrando a DTDL para o tipo de Modelo de Série Temporal.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Este exemplo mostra três variáveis, mas cada tipo pode ter até 100. Variáveis diferentes podem referenciar o mesmo valor de telemetria para fazer diferentes cálculos, conforme necessário. Para obter a lista completa de filtros, agregações e funções escalares, confira [Sintaxe da Expressão de Série Temporal do Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Abra um editor de texto e salve o JSON a seguir na unidade local.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

No Gerenciador do Time Series Insights, selecione o ícone do modelo à esquerda para abrir a guia **Modelo**. Selecione **Tipos** e escolha **Carregar JSON**:

![Captura de tela mostrando como carregar um JSON.](./media/tutorial-configure-tsi/upload-type.png)

Selecione **Escolher arquivo**, escolha o JSON que você salvou anteriormente e selecione **Carregar**.

Você verá o tipo de **Controlador de Temperatura** recém-definido.

### <a name="create-a-hierarchy"></a>Criar uma hierarquia

Crie uma hierarquia para organizar as marcas no pai `TemperatureController`. O exemplo simples a seguir tem um só nível, mas as soluções de IoT costumam ter muitos níveis de aninhamento para contextualizar as marcas na posição física e semântica dentro de uma organização.

Selecione **Hierarquias** e escolha **Adicionar hierarquia**. Para o nome, insira *Frota de Dispositivos*. Crie um nível chamado *Nome do Dispositivo*. Depois, selecione **Salvar**.

![Captura de tela mostrando como adicionar uma hierarquia.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Atribuir instâncias ao tipo correto

Em seguida, altere o tipo das instâncias e coloque-as na hierarquia.

Selecione a guia **Instâncias**. Localize a instância que representa o conjunto de trabalho do dispositivo e escolha o ícone **Editar** na extremidade direita.

![Captura de tela mostrando como editar uma instância.](./media/tutorial-configure-tsi/edit-instance.png)

Abra o menu suspenso **Tipo** e selecione **Controlador de temperatura**. Insira *defaultComponent, <your device name>* para atualizar o nome da instância que representa todas as marcas de nível superior associadas ao seu dispositivo.

![Captura de tela mostrando como alterar um tipo de instância.](./media/tutorial-configure-tsi/change-type.png)

Antes de selecionar **Salvar**, escolha a guia **Campos de Instância** e selecione **Frota de Dispositivos**. Para agrupar a telemetria, insira *\<your device name> – Controlador de Temperatura*. Depois, selecione **Salvar**.

![Captura de tela mostrando como atribuir uma instância a uma hierarquia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Repita as etapas anteriores para atribuir as marcas do termostato à hierarquia e ao tipo corretos.

## <a name="view-your-data"></a>Exibir seus dados

Volte ao painel de gráficos e expanda **Frota de Dispositivos** > seu dispositivo. Selecione **thermostat1**, escolha a variável **Temperatura** e selecione **Adicionar** para adicionar o valor ao gráfico. Faça o mesmo para **thermostat2** e o valor de **workingSet** do **defaultComponent**.

![Captura de tela mostrando como alterar o tipo de instância de thermostat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Para saber mais sobre as várias opções de gráficos, incluindo o dimensionamento de intervalos e os controles do eixo y, confira [Gerenciador do Azure Time Series Insights](../time-series-insights/concepts-ux-panels.md).
