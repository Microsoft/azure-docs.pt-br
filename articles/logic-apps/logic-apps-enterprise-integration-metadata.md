---
title: Gerenciar metadados de artefato de contas de integração – Aplicativos Lógicos do Azure | Microsoft Docs
description: Adicionar ou obter metadados de artefato de contas de integração nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846090"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Gerenciar metadados de artefato em contas de integração a Aplicativos Lógicos do Azure e o Enterprise Integration Pack

É possível definir os metadados personalizados para artefatos em contas de integração e obter esses metadados durante o tempo de execução do aplicativo lógico para ser usado. Por exemplo, é possível fornecer metadados para artefatos, como parceiros, contratos, esquemas e mapas: todos armazenam metadados usando pares chave-valor. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica que tem os artefatos em que você deseja adicionar os metadados, por exemplo: 

  * [Parceiro](logic-apps-enterprise-integration-partners.md)
  * [Contrato](logic-apps-enterprise-integration-agreements.md)
  * [Esquema](logic-apps-enterprise-integration-schemas.md)
  * [Map](logic-apps-enterprise-integration-maps.md)

* Um aplicativo lógico que está vinculado à conta de integração e aos metadados de artefato que você deseja usar. Se o aplicativo lógico ainda não estiver vinculado, saiba [como vincular aplicativos lógicos a contas de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Adicione o gatilho e as ações que deseja usar para gerenciar os metadados de artefato. Ou, apenas para testar, adicione um gatilho, como **Solicitação** ou **HTTP** ao seu aplicativo lógico.

## <a name="add-metadata-to-artifacts"></a>Adicionar metadados a artefatos

1. Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com suas credenciais da conta do Azure. Localize e abra a conta de integração.

1. Selecione o artefato ao qual você deseja adicionar os metadados e escolha **Editar**. Insira os detalhes de metadados desse artefato, por exemplo:

   ![Inserir metadados](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Quando terminar, escolha **OK**.

1. Para exibir esses metadados na definição JavaScript Object Notation (JSON) para a conta de integração, escolha **Editar como JSON** para que o editor de JSON se abra: 

   ![JSON para metadados de parceiro](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Obter metadados de artefato

1. No portal do Azure, abra o aplicativo lógico que está vinculado à conta de integração que você deseja. 

1. No Designer do Aplicativo Lógico, se você estiver adicionando a etapa para a obtenção de metadados, abaixo do gatilho ou da última ação no fluxo de trabalho, escolha **Nova etapa** > **Adicionar uma ação**. 

1. Na caixa de pesquisa, digite “conta de integração”. Na caixa de pesquisa, escolha **Tudo**. Na lista de ações, selecione esta ação: **Pesquisa de artefato da conta de integração: conta de integração**

   ![Selecione "Pesquisa de Artefato da Conta de Integração"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Forneça estas informações para o artefato que você deseja localizar:

   | Propriedade | Necessário | Value | DESCRIÇÃO | 
   |----------|---------|-------|-------------| 
   | **Tipo de Artefato** | Sim | **Esquema**, **Mapa**, **Parceiro**, **Contrato** ou um tipo personalizado | O tipo do artefato que você deseja | 
   | **Nome do Artefato** | Sim | <*artifact-name*> | O nome do artefato que você deseja | 
   ||| 

   Por exemplo, imagine que você deseje obter os metadados para um artefato de parceiro comercial:

   ![Selecionar o tipo de artefato e digitar um nome de artefato](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adicione a ação que você deseja para lidar com esses metadados, por exemplo:

   1. Abaixo da ação **Pesquisa de artefato da conta de integração**, escolha **Próxima etapa**e selecione **Adicionar uma ação**. 

   1. Na caixa de pesquisa, digite "http". Abaixo da caixa de pesquisa, escolha **Internos**e selecione a ação: **HTTP - HTTP**

      ![Adicionar ação HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Forneça as informações para os metadados do artefato que você deseja gerenciar. 

      Por exemplo, imagine que você deseje obter os metadados `routingUrl` que foram adicionados anteriormente neste tópico. Aqui estão os valores de propriedade que você poderá especificar: 

      | Propriedade | Necessário | Value | DESCRIÇÃO | 
      |----------|----------|-------|-------------| 
      | **Método** | Sim | <*operation-to-run*> | A operação de HTTP a ser executada no artefato. Por exemplo, essa ação HTTP usa p método **GET**. | 
      | **URI** | Sim | <*metadata-location*> | Para acessar o valor de metadados `routingUrl` do artefato recuperado, você poderá usar uma expressão, por exemplo: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Cabeçalhos** | Não  | <*header-values*> | Qualquer cabeçalho de saídas do gatilho que você deseja passar para a ação HTTP. Por exemplo, para passar o valor de propriedade `headers` do gatinho: é possível usar uma expressão: <p>`@triggeroutputs()['headers']` | 
      | **Corpo** | Não  | <*conteúdo do corpo*> | Qualquer outro conteúdo que você deseje passar por meio da propriedade `body` da ação HTTP. Este exemplo passa os valores `properties` do artefato para a ação HTTP: <p>1. Clique dentro da propriedade **Corpo** para que seja exibida a lista de conteúdo dinâmico. Se nenhuma propriedade aparecer, escolha **Ver mais**. <br>2. Na lista de conteúdo dinâmico, em **Pesquisa de artefato da conta de integração**, selecione **Propriedades**. | 
      |||| 

      Por exemplo: 

      ![Especificar valores e expressões para a ação HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Para verificar as informações fornecidas para a ação HTTP, exiba a definição JSON do aplicativo lógico. Na barra de ferramentas do Designer de Aplicativo Lógico, escolha **Exibição de código** para que a definição JSON do aplicativo apareça, por exemplo:

      ![Definição JSON do aplicativo lógico](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Depois que você alternar de volta para o Designer do aplicativo lógico, todas as expressões que você usou aparecem agora resolvidas, por exemplo:

      ![Expressões resolvidas no Designer de aplicativos lógicos](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre contratos](logic-apps-enterprise-integration-agreements.md)
