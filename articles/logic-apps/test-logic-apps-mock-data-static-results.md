---
title: Testar aplicativos lógicos usando dados fictícios
description: Configurar resultados estáticos para testar aplicativos lógicos com dados fictícios sem afetar os ambientes de produção
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027696"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testar aplicativos lógicos com dados fictícios Configurando resultados estáticos

Ao testar seus aplicativos lógicos, talvez você não esteja pronto para realmente chamar ou acessar aplicativos, serviços e sistemas por vários motivos. Geralmente nesses cenários, talvez seja necessário executar caminhos de condição diferentes, forçar erros, fornecer corpos de resposta de mensagem específicos ou até mesmo tentar ignorar algumas etapas. Ao configurar resultados estáticos para uma ação em seu aplicativo lógico, você pode simular dados de saída dessa ação. A habilitação de resultados estáticos em uma ação não executa a ação, mas retorna os dados fictícios.

Por exemplo, se você configurar resultados estáticos para a ação enviar email do Outlook 365, o mecanismo de aplicativos lógicos retornará apenas os dados fictícios que você especificou como resultados estáticos, em vez de chamar o Outlook e enviar um email.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurar resultados estáticos

1. Se você ainda não fez isso, na [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativos lógicos.

1. Na ação em que você deseja configurar resultados estáticos, siga estas etapas: 

   1. No canto superior direito da ação, escolha o botão de reticências (*...*) e selecione **resultado estático**, por exemplo:

      ![Selecione "resultado estático" > "Habilitar resultado estático"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolha **habilitar resultado estático**. Para as propriedades obrigatórias (*), especifique os valores de saída fictícios que você deseja retornar para a resposta da ação.

      Por exemplo, aqui estão as propriedades necessárias para a ação HTTP:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Status** | O status da ação a ser retornado |
      | **Código de status** | O código de status específico a ser retornado |
      | **Cabeçalhos** | O conteúdo do cabeçalho a ser retornado |
      |||

      ![Selecione "Habilitar resultado estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para inserir os dados fictícios no formato JavaScript Object Notation (JSON), escolha **alternar para o modo JSON** ( ![ escolha "alternar para o modo JSON" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ).

   1. Para propriedades opcionais, abra a lista **Selecionar campos opcionais** e selecione as propriedades que você deseja simular.

      ![Selecionar propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando estiver pronto para salvar, escolha **concluído**.

   No canto superior direito da ação, a barra de título agora mostra um ícone de Beaker de teste ( ![ ícone para resultados estáticos ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), que indica que você habilitou resultados estáticos.

   ![Ícone mostrando os resultados estáticos habilitados](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para encontrar execuções anteriores que usam dados fictícios, consulte [Localizar execuções que usam resultados estáticos](#find-runs-mock-data) mais adiante neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se seu aplicativo lógico tiver uma execução anterior com saídas, você poderá reutilizar como saídas de simulação, poderá copiar e colar as saídas dessa execução.

1. Se você ainda não fez isso, na [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativos lógicos.

1. No menu principal do aplicativo lógico, selecione **visão geral**.

1. Na seção **histórico de execuções** , selecione a execução do aplicativo lógico que você deseja.

1. No fluxo de trabalho do aplicativo lógico, localize e expanda a ação que tem as saídas que você deseja.

1. Escolha o link **Mostrar saídas brutas** .

1. Copie o objeto JavaScript Object Notation completo (JSON) ou a subseção específica que você deseja usar, por exemplo, a seção de saídas ou até apenas a seção de cabeçalhos.

1. Siga as etapas para abrir a caixa de **resultado estático** para sua ação em [Configurar resultados estáticos](#set-up-static-results).

1. Depois que a caixa de **resultado estático** for aberta, escolha uma das etapas:

   * Para colar um objeto JSON completo, escolha **alternar para o modo JSON** ( ![ escolha "alternar para o modo JSON" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ):

     ![Escolha "alternar para o modo JSON" para o objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para colar apenas uma seção JSON, ao lado do rótulo dessa seção, escolha **alternar para o modo JSON** para essa seção, por exemplo:

     ![Escolha "alternar para o modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor de JSON, Cole o JSON copiado anteriormente.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, escolha **Concluído**. Ou, para retornar ao designer, escolha **alternar modo do editor** ( ![ escolha "alternar modo do editor" ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Localizar execuções que usam resultados estáticos

O histórico de execuções do seu aplicativo lógico identifica as execuções em que as ações usam resultados estáticos. Para encontrar essas execuções, siga estas etapas:

1. No menu principal do aplicativo lógico, selecione **visão geral**. 

1. No painel direito, em **histórico de execuções**, localize a coluna **resultados estáticos** . 

   Qualquer execução que inclua ações com resultados tem a coluna **resultados estáticos** definida como **habilitado**, por exemplo:

   ![Histórico de execuções-coluna de resultados estáticos](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para exibir as ações que usam resultados estáticos, selecione a execução desejada onde a coluna de **resultados estáticos** está definida como **habilitada**.

   As ações que usam resultados estáticos mostram o ícone testar Beaker ( ![ ícone para resultados estáticos ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), por exemplo:

   ![Histórico de execuções-ações que usam resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desabilitar resultados estáticos

Desativar os resultados estáticos não retira os valores de sua última configuração. Assim, quando você ativar os resultados estáticos da próxima vez, poderá continuar usando os valores anteriores.

1. Localize a ação na qual você deseja desabilitar as saídas estáticas. No canto superior direito da ação, escolha o ícone testar Beaker ( ![ ícone para resultados estáticos ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![Captura de tela mostra uma ação H T P em que é possível selecionar o ícone testar Beaker.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolha **desabilitar resultado estático**  >  **concluído**.

   ![Captura de tela mostra a opção Desabilitar resultado estático que você pode selecionar.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referência

Para obter mais informações sobre essa configuração em suas definições de fluxo de trabalho subjacentes, consulte [resultados estáticos-referência de esquema para a linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#static-results) e [definições de configuração de runtimeConfiguration. staticResult-Runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)