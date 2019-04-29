---
title: Configurar a topologia Connected Factory - Azure | Microsoft Docs
description: Como configurar a topologia de um acelerador de solução Connected Factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 3fd160fbccfb5298cefed6a731797ca6962b997c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450016"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configurar o acelerador da solução Connected Factory

O acelerador de solução Connected Factory mostra um painel simulado para uma empresa fictícia chamada Contoso. Essa empresa tem fábricas localizadas em vários locais globalmente.

Este artigo usa a Contoso como um exemplo para descrever como configurar a topologia de uma solução Connected Factory.

## <a name="simulated-factories-configuration"></a>Configuração de fábricas simuladas

Cada fábrica da Contoso tem linhas de produção compostas por três estações. Cada estação é um servidor OPC UA real com uma função específica:

* Estação de montagem
* Estação de teste
* Estação de empacotamento

Esses servidores OPC UA têm nós OPC UA e o [Editor de OPC](https://github.com/Azure/iot-edge-opc-publisher) envia os valores desses nós a uma Connected Factory. Isso inclui:

* Status operacional atual, como o consumo de energia atual.
* Informações de produção, como o número de produtos produzidos.

Você pode usar o painel para detalhar a topologia da fábrica da Contoso de uma exibição global para uma exibição no nível da estação. O painel da Connected Factory possibilita:

* A visualização de valores de OEE e KPI para cada camada da topologia.
* A visualização de valores atuais dos nós OPC UA nas estações.
* A agregação dos valores de OEE e KPI do nível da estação para o nível global.
* A visualização de alertas e ações a serem executadas caso os valores atinjam limites específicos.

## <a name="connected-factory-topology"></a>Topologia Connected Factory

A topologia das fábricas, linhas de produção e estações é hierárquica:

* O nível global tem os nós de fábrica como filhos.
* As fábricas têm os nós de linha de produção como filhos.
* As linhas de produção têm os nós de estação como filhos.
* As estações (servidores OPC UA) têm os nós OPC UA como filhos.

Cada nó da topologia tem um conjunto comum de propriedades que definem:

* Um identificador exclusivo do nó da topologia.
* Um nome.
* Uma descrição.
* Uma imagem.
* Os filhos do nó da topologia.
* Os valores mínimo, de destino e máximo para os números de OEE e KPI e as ações de alerta a serem executadas.

## <a name="topology-configuration-file"></a>Arquivo de configuração de topologia

Para configurar as propriedades listadas na seção anterior, a solução Connected Factory usa um arquivo de configuração chamado [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Você pode encontrar esse arquivo no código-fonte da solução na pasta `WebApp/Contoso/Topology`.

O snippet de código a seguir mostra a estrutura do arquivo de configuração de `ContosoTopologyDescription.json`:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

As propriedades comuns de  `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>` e `<station_configuration>` são:

* **Name** (tipo cadeia de caracteres)

  Define um nome descritivo, que deve ser apenas uma palavra para o nó da topologia exibir no painel.

* **Description** (tipo cadeia de caracteres)

  Descreve o nó da topologia em mais detalhes.

* **Image** (tipo cadeia de caracteres)

  O caminho para uma imagem na solução WebApp para ser mostrado quando informações sobre o nó da topologia forem mostradas no painel.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (tipo `<performance_definition>`)

  Essas propriedades definem os valores mínimo, de destino e máximo do número operacional usado para gerar alertas. Essas propriedades também definem as ações a serem executadas caso um alerta seja detectado.

Os itens `<factory_configuration>` e `<production_line_configuration>` têm uma propriedade:

* **Guid** (tipo cadeia de caracteres)

  Identifica o nó da topologia exclusivamente.

`<factory_configuration>` tem uma propriedade:

* **Location** (tipo `<location_definition>`)

  Especifica onde a fábrica está localizada.

`<station_configuration>` tem as propriedades:

* **OpcUri** (tipo cadeia de caracteres)

  Essa propriedade deve ser definida como o URI do aplicativo de OPC UA do servidor OPC UA.
  Como deve ser globalmente exclusiva segundo a especificação do OPC UA, essa propriedade é usada para identificar o nó da topologia da estação.

* **OpcNodes**, que são uma matriz de nós de OPC UA (tipo `<opc_node_description>`)

`<location_definition>` tem as propriedades:

* **City** (tipo cadeia de caracteres)

  Nome da cidade mais próxima do local

* **Country** (tipo cadeia de caracteres)

  O país/região da localização

* **Latitude** (tipo double)

  A latitude da localização

* **Longitude** (tipo double)

  A longitude da localização

`<performance_definition>` tem as propriedades:

* **Minimum** (tipo double)

  O limite inferior que o valor pode atingir. Se o valor atual estiver abaixo desse limite, um alerta será gerado.

* **Target** (tipo double)

  Valor de destino ideal.

* **Maximum** (tipo double)

  O limite superior que o valor pode atingir. Se o valor atual estiver acima desse limite, um alerta será gerado.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações que podem ser executadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações que podem ser executadas como resposta a um alerta máximo.

`<alert_action`> tem as propriedades:

* **Type** (tipo cadeia de caracteres)

  Tipo da ação de alerta. Os seguintes tipos são conhecidos:

  * **AcknowledgeAlert**: o status do alerta deve mudar para confirmado.
  * **CloseAlert**: todos os alertas mais antigos do mesmo tipo não devem mais ser mostrados no painel.
  * **CallOpcMethod**: um método de OPC UA deve ser chamado.
  * **OpenWebPage**: uma janela do navegador deve ser aberta mostrando informações contextuais adicionais.

* **Description** (tipo cadeia de caracteres)

  Descrição da ação mostrada no painel.

* **Parameter** (tipo cadeia de caracteres)

  Parâmetros necessários para executar a ação. O valor depende do tipo de ação.

  * **AcknowledgeAlert**: nenhum parâmetro necessário.
  * **CloseAlert**: nenhum parâmetro necessário.
  * **CallOpcMethod**: as informações do nó e os parâmetros do método OPC UA a ser chamado no formato "NodeId do nó pai, NodeId do método a ser chamado, URI do servidor OPC UA".
  * **OpenWebPage**: a URL a ser mostrada na janela do navegador.

`<opc_node_description>` contém informações sobre nós do OPC UA em uma estação (servidor OPC UA). Nós que não representam nenhum nó do OPC UA existente, mas são usados como armazenamento na lógica de cálculo da Connected Factory, também são válidos. Ele tem as seguintes propriedades:

* **NodeId** (tipo cadeia de caracteres)

  Endereço do nó do OPC UA no espaço de endereço da estação (servidor OPC UA). A sintaxe deve ser conforme especificado na especificação do OPC UA para um NodeId.

* **SymbolicName** (tipo cadeia de caracteres)

  Nome a ser mostrado no painel quando o valor do nó do OPC UA for mostrado.

* **Relevance** (matriz do tipo de cadeia de caracteres)

  Indica para qual computação de OEE ou KPI o valor do nó do OPC UA é relevante. Cada elemento da matriz pode ser um dos seguintes valores:

  * **OeeAvailability_Running**: o valor é relevante para o cálculo de disponibilidade do OEE.
  * **OeeAvailability_Fault**: o valor é relevante para o cálculo de disponibilidade do OEE.
  * **OeePerformance_Ideal**: o valor é relevante para o cálculo de desempenho do OEE e normalmente é um valor constante.
  * **OeePerformance_Actual**: o valor é relevante para o cálculo de desempenho do OEE.
  * **OeeQuality_Good**: o valor é relevante para o cálculo da qualidade do OEE.
  * **OeeQuality_Bad**: o valor é relevante para o cálculo da qualidade do OEE.
  * **Kpi1**: o valor é relevante para o cálculo de KPI1.
  * **Kpi2**: o valor é relevante para o cálculo de KPI2.

* **OpCode** (tipo cadeia de caracteres)

  Indica como o valor do nó OPC UA é tratado em consultas do Time Series Insight e em cálculos de OEE/KPI. Cada consulta do Time Series Insight tem como alvo um período específico, que é um parâmetro da consulta e fornece um resultado. O OpCode controla como o resultado é calculado e pode ser um dos seguintes valores:

  * **Diff**: diferença entre o último e o primeiro valor do período.
  * **Avg**: a média de todos os valores do período.
  * **Sum**: a soma de todos os valores do período.
  * **Last**: atualmente não usado.
  * **Count**: o número de todos os valores do período.
  * **Max**: o valor máximo do período.
  * **Min**: o valor mínimo do período.
  * **Const**: o resultado é o valor especificado pela propriedade ConstValue.
  * **SubMaxMin**: a diferença entre o valor máximo e o mínimo.
  * **TimeSpan**: o período.

* **Units** (tipo cadeia de caracteres)

  Define uma unidade do valor de exibição no painel.

* **Visible** (tipo booliano)

  Controla se o valor deve ser mostrado no painel.

* **ConstValue** (tipo double)

  Se **OpCode** for **Const**, essa propriedade será o valor do nó.

* **Minimum** (tipo double)

  Se o valor atual estiver abaixo desse valor, será gerado um alerta mínimo.

* **Maximum** (tipo double)

  Se o valor atual estiver acima desse valor, será gerado um alerta máximo.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações que podem ser executadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações que podem ser executadas como resposta a um alerta máximo.

No nível da estação, você também vê objetos de **Simulação**. Esses objetos são usados somente para configurar a simulação Connected Factory e não devem ser usados para configurar uma topologia real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Como os dados de configuração são usados em tempo de execução

Todas as propriedades usadas no arquivo de configuração podem ser agrupadas em categorias diferentes dependendo de como são usadas. Essas categorias são:

### <a name="visual-appearance"></a>Aparência

As propriedades nesta categoria definem a aparência do painel Connected Factory. Os exemplos incluem:

* NOME
* DESCRIÇÃO
* Image
* Local padrão
* Unidades
* Visible

### <a name="internal-topology-tree-addressing"></a>Endereçamento da árvore de topologia interna

O aplicativo Web mantém um dicionário de dados interno que contém informações de todos os nós da topologia. As propriedades **Guid** e **OpcUri** são usadas como chaves para acessar este dicionário e devem ser exclusivas.

### <a name="oeekpi-computation"></a>Computação OEE/KPI

Os números de OEE/KPI para a simulação Connected Factory são parametrizados por:

* Os valores de nó do OPC UA a serem incluídos no cálculo.
* Como o número é computado por meio dos valores de telemetria.

A Connected Factory usa as fórmulas OEE, conforme publicadas pelo https://www.oeefoundation.org.

Os objetos de nó do OPC UA nas estações permitem a marcação para uso no cálculo de OEE/KPI. A propriedade **Relevance** indica para qual número de OEE/KPI o valor do nó do OPC UA deve ser usado. A propriedade **OpCode** define como o valor é incluído na computação.

### <a name="alert-handling"></a>Tratamento de alertas

A Connected Factory é compatível com um mecanismo simples de geração de alerta baseado em limites mínimo/máximo. Há uma série de ações predefinidas que você pode configurar em resposta a esses alertas. As seguintes propriedades controlam este mecanismo:

* Máximo
* Mínimo
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlação com os dados de telemetria

Para determinadas operações, como visualizar o último valor ou criar consultas do Time Series Insights, o aplicativo Web precisa de um esquema de endereçamento para os dados de telemetria ingeridos. A telemetria enviada à Connected Factory também precisa ser armazenada em estruturas de dados internas. As duas propriedades que permitem essas operações estão no nível da estação (servidor OPC UA) e do nó do OPC UA:

* **OpcUri**

  Identifica (globalmente exclusivo) o servidor OPC UA do qual a telemetria é proveniente. Nas mensagens ingeridas, essa propriedade é enviada como **ApplicationUri**.

* **NodeId**

  Identifica o valor do nó do servidor de OPC UA. O formato da propriedade deve ser conforme especificado na especificação do OPC UA. Nas mensagens ingeridas, essa propriedade é enviada como **NodeId**.

Verifique [esta](https://github.com/Azure/iot-edge-opc-publisher) página do GitHub para obter mais informações sobre como os dados de telemetria são ingeridos na Connected Factory usando o Editor de OPC.

## <a name="example-how-kpi1-is-calculated"></a>Exemplo: Como o KPI1 é calculado

A configuração no arquivo `ContosoTopologyDescription.json` controla como os valores de KPI/OEE são calculados. O exemplo a seguir mostra como propriedades deste arquivo controlam o cálculo do KPI1.

Na Connected Factory, o KPI1 é usado para medir o número de produtos fabricados com êxito na última hora. Cada estação (servidor OPC UA) na simulação Connected Factory fornece um nó de OPC UA (`NodeId: "ns=2;i=385"`), que fornece a telemetria para calcular esse KPI.

A configuração desse nó do OPC UA se parece com o snippet de código a seguir:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Essa configuração habilita a consulta dos valores de telemetria deste nó usando o Time Series Insights. A consulta do Time Series Insights recupera:

* O número de valores.
* O valor mínimo.
* O valor máximo.
* A média de todos os valores.
* A soma de todos os valores para todos pares de **OpcUri** (**ApplicationUri**), **NodeId** exclusivos em um determinado período.

Uma característica do valor do nó **NumberOfManufactureredProducts** é que ele só aumenta. Para calcular o número de produtos fabricados no período, a Connected Factory usa **OpCode** **SubMaxMin**. O cálculo recupera o valor mínimo no início do período e o valor máximo no final do período.

O **OpCode** na configuração configura a lógica de cálculo para calcular o resultado da diferença dos valores máximo e mínimo. Esses resultados são acumulados de baixo para cima até o nível da raiz (global) e mostrados no painel.

## <a name="next-steps"></a>Próximas etapas

Sugerimos como próxima etapa aprender a [Implantar um gateway no Windows ou no Linux para o acelerador de solução Connected Factory](iot-accelerators-connected-factory-gateway-deployment.md).
