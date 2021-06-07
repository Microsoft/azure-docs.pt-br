---
title: Personalizar a solução Connected Factory - Azure | Microsoft Docs
description: Uma descrição de como personalizar o comportamento do acelerador de solução Connected Factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645954"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar como a solução Connected Factory exibe dados dos servidores OPC UA

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

A solução Connected Factory agrega e exibe dados dos servidores OPC UA conectados à solução. Você pode procurar e enviar comandos para os servidores OPC UA na solução. Para obter mais informações sobre o OPC UA, consulte as [perguntas frequentes sobre a fábrica conectada](iot-accelerators-faq-cf.md).

Exemplos de dados agregados na solução incluem a OEE (Eficiência Geral de Equipamentos) e os KPIs (Indicadores Chave de Desempenho) que podem ser exibidos no painel nos níveis da fábrica, da linha e da estação. A captura de tela a seguir mostra os valores de OEE e KPI da estação **Assembly** na **Linha de produção 1**, na fábrica de **Munique**:

![Exemplo de valores de OEE e KPI na solução][img-oee-kpi]

A solução permite exibir informações detalhadas de itens de dados específicos dos servidores OPC UA, chamados *estações*. A seguinte captura de tela mostra gráficos do número de itens fabricados de uma estação específica:

![Gráficos de número de itens fabricados][img-manufactured-items]

Se você clicar em um dos grafos, poderá explorar os dados ainda mais usando o TSI (Time Series Insights):

![Explorar dados usando o Time Series Insights][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias exibições da solução.
- Como você pode personalizar a maneira como a solução exibe os dados.

## <a name="data-sources"></a>Fontes de dados

A solução Connected Factory exibe dados dos servidores OPC UA conectados à solução. A instalação padrão inclui vários servidores OPC UA que executam uma simulação de fábrica. Você pode adicionar seus próprios servidores OPC UA que [conectem-se por meio de um gateway] [lnk-Connect-CF] à sua solução.

Você pode procurar itens de dados que um servidor OPC UA conectado pode enviar para a solução no painel:

1. Escolha **Procurar** para navegar para a exibição **Selecionar um servidor OPC UA**:

    ![Navegar para a exibição Selecionar um servidor OPC UA][img-select-server]

1. Selecione um servidor e clique em **Conectar**. Clique em **Continuar** quando o aviso de segurança for exibido.

    > [!NOTE]
    > Esse aviso só será exibido uma vez para cada servidor e estabelece uma relação de confiança entre o painel da solução e o servidor.

1. Agora você pode procurar itens de dados que o servidor pode enviar para a solução. Os itens que estão sendo enviados para a solução têm uma marca de seleção:

    ![Itens publicados][img-published]

1. Se você for um *administrador* na solução, poderá optar por publicar um item de dados para disponibilizá-lo na solução de fábrica conectada. Como Administrador, você também pode alterar o valor de itens de dados e chamar métodos no servidor OPC UA.

## <a name="map-the-data"></a>Mapear os dados

A solução Connected Factory mapeia e agrega os itens de dados publicados do servidor OPC UA nas várias exibições da solução. A solução Connected Factory é implantada em sua conta do Azure quando você provisiona a solução. Um arquivo JSON da solução Connected Factory do Visual Studio armazena essas informações de mapeamento. Exiba e modifique esse arquivo de configuração JSON na solução Connected Factory do Visual Studio. Você pode reimplantar a solução depois de fazer uma alteração.

Você pode usar o arquivo de configuração para:

- Editar as fábricas simuladas, linhas de produção e estações existentes.
- Mapear dados de servidores OPC UA reais que estão conectados à solução.

Para saber mais sobre o mapeamento e a agregação dos dados a fim de atender às suas necessidades específicas, consulte [Como configurar o acelerador de solução Connected Factory](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Implantar as alterações

Quando terminar de fazer alterações na **ContosoTopologyDescription.jsno** arquivo, você deverá reimplantar a solução de fábrica conectada em sua conta do Azure.

O repositório **azure-iot-connected-factory** inclui um script **build.ps1** do PowerShell que pode ser usado para recompilar e implantar a solução.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o acelerador de solução Connected Factory lendo os seguintes artigos:

* [Permissões no site azureiotsolutions.com][lnk-permissions]
* [Perguntas Frequentes sobre Factory Conectado](iot-accelerators-faq-cf.md)
* [perguntas frequentes][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md