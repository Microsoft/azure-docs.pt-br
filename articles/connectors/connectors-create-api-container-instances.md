---
title: Implantar & gerenciar instâncias de contêiner do Azure
description: Automatizar tarefas e fluxos de trabalho que criam e gerenciam implantações de contêiner em instâncias de contêiner do Azure usando aplicativos lógicos do Azure
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76046286"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Implantar e gerenciar instâncias de contêiner do Azure usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector de instância de contêiner do Azure, você pode configurar tarefas automatizadas e fluxos de trabalho que implantam e gerenciam [grupos de contêineres](../container-instances/container-instances-container-groups.md). O conector da instância de contêiner dá suporte às seguintes ações:

* Criar ou excluir um grupo de contêineres
* Obter as propriedades de um grupo de contêineres
* Obter uma lista de grupos de contêineres
* Obter os logs de uma instância de contêiner

Use essas ações em seus aplicativos lógicos para tarefas como a execução de uma carga de trabalho de contêiner em resposta a um gatilho de aplicativos lógicos. Você também pode fazer com que outras ações usem a saída das ações de instância de contêiner. 

Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, use um gatilho separado, como um gatilho de **recorrência** para executar uma carga de trabalho de contêiner em um agendamento regular. Ou talvez você precise disparar uma implantação de grupo de contêineres após um evento, como a chegada de um email do Outlook. 

Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) e [como criar e gerenciar instâncias de contêiner](../container-instances/container-instances-quickstart.md)

* O aplicativo lógico no qual você deseja acessar as instâncias de contêiner. Para usar uma ação, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="add-a-container-instance-action"></a>Adicionar uma ação de instância de contêiner

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

     -ou-

   * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "instância do contêiner" como filtro. Na lista ações, selecione a ação de conector da instância de contêiner do Azure desejada.

1. Forneça um nome para a conexão. 

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

  Por exemplo, selecione **Criar grupo de contêineres** e insira as propriedades de um grupo de contêineres e uma ou mais instâncias de contêiner no grupo, conforme mostrado na imagem a seguir (detalhes parciais):

  ![Criar grupo de contêineres](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/aci/) do conector ou a referência do grupo de contêineres [YAML](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Próximas etapas

* Consulte um [aplicativo lógico de exemplo](https://github.com/Azure-Samples/aci-logicapps-integration) que executa um contêiner em instâncias de contêiner do Azure para analisar o texto de email ou do Twitter

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)