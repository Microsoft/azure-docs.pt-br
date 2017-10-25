---
title: "Personalizar a fábrica conectada do Azure IoT Suite | Microsoft Docs"
description: "Uma descrição de como personalizar o comportamento da solução pré-configurada de fábrica conectada."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 90a6172dbd887ecda5a9f5d9082a4e136092bc10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar como a solução de fábrica conectada exibe dados dos servidores OPC UA

## <a name="introduction"></a>Introdução

A solução de fábrica conectada agrega e exibe dados dos servidores OPC UA conectados à solução. Você pode procurar e enviar comandos para os servidores OPC UA na solução. Para obter mais informações sobre o OPC UA, consulte as [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md).

Exemplos de dados agregados na solução incluem a OEE (Eficiência Geral de Equipamentos) e os KPIs (Indicadores Chave de Desempenho) que podem ser exibidos no painel nos níveis da fábrica, da linha e da estação. A captura de tela a seguir mostra os valores de OEE e KPI da estação **Assembly** na **Linha de produção 1**, na fábrica de **Munique**:

![Exemplo de valores de OEE e KPI na solução][img-oee-kpi]

A solução permite exibir informações detalhadas de itens de dados específicos dos servidores OPC UA, chamados *estações*. A seguinte captura de tela mostra gráficos do número de itens fabricados de uma estação específica:

![Gráficos de número de itens fabricados][img-manufactured-items]

Se você clicar em um dos gráficos, poderá explorar os dados ainda mais usando o TSI (Time Series Insights):

![Explorar dados usando o Time Series Insights][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias exibições da solução.
- Como você pode personalizar a maneira como a solução exibe os dados.

## <a name="data-sources"></a>Fontes de dados

A solução de fábrica conectada exibe dados dos servidores OPC UA conectados à solução. A instalação padrão inclui vários servidores OPC UA que executam uma simulação de fábrica. Você pode adicionar seus próprios servidores OPC UA que [se conectam por meio de um gateway][lnk-connect-cf] à solução.

Você pode procurar itens de dados que um servidor OPC UA conectado pode enviar para a solução no painel:

1. Navegue para a exibição **Selecionar um servidor OPC UA**:

    ![Navegar para a exibição Selecionar um servidor OPC UA][img-select-server]

1. Selecione um servidor e clique em **Conectar**. Clique em **Continuar** quando o aviso de segurança for exibido.

    > [!NOTE]
    > Esse aviso só será exibido uma vez para cada servidor e estabelece uma relação de confiança entre o painel da solução e o servidor.

1. Agora você pode procurar itens de dados que o servidor pode enviar para a solução. Os itens que estão sendo enviados para a solução têm uma marca de seleção verde:

    ![Itens publicados][img-published]

1. Se você for um *Administrador* da solução, poderá optar por publicar um item de dados para disponibilizá-lo na solução de fábrica conectada. Como Administrador, você também pode alterar o valor de itens de dados e chamar métodos no servidor OPC UA.

## <a name="map-the-data"></a>Mapear os dados

A solução de fábrica conectada mapeia e agrega os itens de dados publicados do servidor OPC UA nas várias exibições da solução. A solução de fábrica conectada é implantada em sua conta do Azure quando você provisiona a solução. Um arquivo JSON da solução de fábrica conectada do Visual Studio armazena essas informações de mapeamento. Exiba e modifique esse arquivo de configuração JSON na solução de fábrica conectada do Visual Studio. Você pode reimplantar a solução depois de fazer uma alteração.

Você pode usar o arquivo de configuração para:

- Editar as fábricas simuladas, linhas de produção e estações existentes.
- Mapear dados de servidores OPC UA reais que estão conectados à solução.

Para clonar uma cópia da solução de fábrica conectada do Visual Studio, use o seguinte comando do Git:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

O arquivo **ContosoTopologyDescription.json** define o mapeamento dos itens de dados do servidor OPC UA para as exibições no painel da solução de fábrica conectada. Encontre esse arquivo de configuração na pasta **Contoso\Topology** do projeto **WebApp** na solução do Visual Studio.

O conteúdo do arquivo JSON é organizado como uma hierarquia de nós de fábrica, linha de produção e estação. Essa hierarquia define a hierarquia de navegação no painel da fábrica conectada. Os valores em cada nó da hierarquia determinam as informações exibidas no painel. Por exemplo, o arquivo JSON contém os seguintes valores para a fábrica de Munique:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

O nome, a descrição e a localização aparecem nesta exibição no painel:

![Dados de Munique no painel][img-munich]

Cada fábrica, linha de produção e estação tem uma propriedade de imagem. Encontre esses arquivos JPEG na pasta **Content\img** do projeto **WebApp**. Esses arquivos de imagem são exibidos no painel da fábrica conectada.

Cada estação inclui várias propriedades detalhadas que definem o mapeamento dos itens de dados do OPC UA. Essas propriedades são descritas nas seguintes seções:

### <a name="opcuri"></a>OpcUri

O valor de **OpcUri** é o URI do Aplicativo do OPC UA que identifica exclusivamente o servidor OPC UA. Por exemplo, o valor de **OpcUri** da estação de assembly na linha de produção 1 em Munique tem esta aparência: **urn:scada2194:ua:munich:productionline0:assemblystation**.

Exiba os URIs dos servidores OPC UA conectados no painel da solução:

![Exibir URIs do servidor OPC UA][img-server-uris]

### <a name="simulation"></a>Simulação

As informações do nó **Simulation** são específicas à simulação do OPC UA executada nos servidores do OPC UA provisionados por padrão. Ele não é usado para um servidor OPC UA real.

### <a name="kpi1-and-kpi2"></a>Kpi1 e Kpi2

Esses nós descrevem como os dados da estação contribuem para os dois valores de KPI no painel. Em uma implantação padrão, esses valores de KPI são unidades por hora e kWh por hora. A solução calcula os valores de KPI no nível de uma estação e agrega-os nos níveis da fábrica e da linha de produção.

Cada KPI tem um valor mínimo, máximo e de destino. Cada valor de KPI também pode definir ações de alerta a serem executados pela solução de fábrica conectada. O seguinte trecho mostra as definições de KPI para a estação de assembly na linha de produção 1 em Munique:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

A captura de tela a seguir mostra os dados de KPI no painel.

![Informações de KPI no painel][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

Os nós **OpcNodes** identificam os itens de dados publicados do servidor OPC UA e especificam como processar esses dados.

O valor de **NodeId** identifica a NodeID do OPC UA específica do servidor OPC UA. O primeiro nó da estação de assembly na linha de produção 1 em Munique tem um valor igual a **ns=2;i=385**. Um valor de **NodeId** especifica o item de dados a ser lido do servidor OPC UA e **SymbolicName** fornece um nome amigável ao usuário ser usado no painel para os dados.

Outros valores associados a cada nó são resumidos na seguinte tabela:

| Valor | Descrição |
| ----- | ----------- |
| Relevância  | Os valores de KPI e OEE com os quais esses dados contribuem. |
| OpCode     | Como os dados são agregados. |
| Unidades      | As unidades a serem usadas no painel.  |
| Visible    | Indica se esse valor será exibido no painel. Alguns valores são usados em cálculos, mas não são exibidos.  |
| Máximo    | O valor máximo que dispara um alerta no painel. |
| MaximumAlertActions | Uma ação a ser tomada em resposta a um alerta. Por exemplo, enviar um comando para uma estação. |
| ConstValue | Um valor constante usado em um cálculo. |

## <a name="deploy-the-changes"></a>Implantar as alterações

Quando você terminar de fazer alterações no arquivo **ContosoTopologyDescription.json**, deverá reimplantar a solução de fábrica conectada à sua conta do Azure.

O repositório **azure-iot-connected-factory** inclui um script **build.ps1** do PowerShell que pode ser usado para recompilar e implantar a solução.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a solução pré-configurada de fábrica conectada lendo os seguintes artigos:

* [Passo a passo de solução pré-configurada de fábrica conectada][lnk-rm-walkthrough]
* [Implantar um gateway para a fábrica conectada][lnk-connect-cf]
* [Permissões no site azureiotsuite.com][lnk-permissions]
* [Perguntas frequentes do factory conectado](iot-suite-faq-cf.md)
* [Perguntas frequentes][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md