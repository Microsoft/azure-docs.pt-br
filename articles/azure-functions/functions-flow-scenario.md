---
title: Chamar uma função do Azure do Microsoft Flow | Microsoft Docs
description: Crie um conector personalizado e, em seguida, chame uma função usando esse conector.
services: functions
keywords: aplicativos de nuvem, serviços de nuvem, Microsoft Flow, processos de negócios, aplicativo de negócios
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 31e18285bf6211e73d994e037a91adc396972715
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106963"
---
# <a name="call-a-function-from-microsoft-flow"></a>Chamar uma função do Microsoft Flow

O [Microsoft Flow](https://flow.microsoft.com/) facilita a automação de fluxos de trabalho e processos de negócios entre seus aplicativos e serviços favoritos. Desenvolvedores profissionais podem usar o Azure Functions para estender os recursos do Microsoft Flow e, ao mesmo tempo, proteger os construtores de fluxo dos detalhes técnicos.

Crie um aplicativo neste tópico com base em um cenário de manutenção para turbinas eólicas. Este tópico mostra como chamar a função que você definiu em [Criar uma definição de OpenAPI para uma função](functions-openapi-definition.md). A função determina se um reparo de emergência em uma turbina eólica é econômico. Se for economicamente viável, o fluxo envia um email para recomendar o reparo.

Para obter informações sobre como chamar a mesma função do PowerApps, consulte [Chamar uma função do PowerApps](functions-powerapps-scenario.md).

Neste tópico, você aprenderá a:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exportar uma definição de API.
> * Adicionar uma conexão à API.
> * Crie um fluxo para enviar email se um reparo for econômico.
> * Execute o fluxo.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma [conta do Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) ativa com as mesmas credenciais de entrada da sua conta do Azure. 
+ SharePoint, que você pode usar como uma fonte de dados para este fluxo. Inscreva-se para [uma versão de avaliação do Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) se ainda não tiver o SharePoint.
+ Conclua o tutorial [Criar uma definição de OpenAPI para uma função](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Criar uma lista do SharePoint
Você começa criando uma lista que usa como uma fonte de dados para o fluxo. A lista tem as colunas a seguir.

| Coluna de lista     | Tipo de Dados           | Observações                                    |
|-----------------|---------------------|------------------------------------------|
| **Título**           | Linha única de texto | Nome da turbina                      |
| **LastServiceDate** | Data                |                                          |
| **MaxOutput**       | Número              | Saída de turbina, em KwH            |
| **ServiceRequired** | Sim/Não              |                                          |
| **EstimatedEffort** | Número              | Tempo estimado para o reparo, em horas |

1. No site do SharePoint, clique ou toque em **Novo** e, em seguida, **Lista**.

    ![Criar uma nova lista do SharePoint](./media/functions-flow-scenario/new-list.png)

2. Insira o nome `Turbines` e clique ou toque em **Criar**.

    ![Especificar um nome para a nova lista](./media/functions-flow-scenario/create-list.png)

    A lista **Turbinas** é criada, com o campo **Título** padrão.

    ![Lista de turbinas](./media/functions-flow-scenario/initial-list.png)

3. Clique ou toque no ![ícone Novo item](./media/functions-flow-scenario/icon-new.png) e, em seguida, em **Data**.

    ![Adicionar linha única de campo de texto](./media/functions-flow-scenario/add-column.png)

4. Insira o nome `LastServiceDate` e clique ou toque em **Criar**.

    ![Criar coluna LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Repita as duas últimas etapas para as outras três colunas na lista:

    1. **Número** > "MaxOutput"

    2. **Sim/Não** > "ServiceRequired"

    3. **Número** > "EstimatedEffort"

É isso por enquanto – você deve ter uma lista vazia que se parece com a imagem a seguir. Você adiciona dados à lista depois de criar o fluxo.

![Lista vazia](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Adicionar uma conexão à API
A API personalizada (também conhecida como um conector personalizado) está disponível no Microsoft Flow, mas você deve fazer uma conexão com a API antes de poder usá-la em um fluxo.

1. Em [flow.microsoft.com](https://flow.microsoft.com), clique no ícone de engrenagem (no canto superior direito) e clique em **Conexões**.

    ![Conexões de fluxo](media/functions-flow-scenario/flow-connections.png)

1. Clique em **Criar Conexão**, role para baixo até o conector **Reparo da Turbina** e clique nele.

    ![Criar conexão](media/functions-flow-scenario/create-connection.png)

1. Insira a chave de API e clique em **Criar conexão**.

    ![Inserir a chave de API e criar](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Se você compartilhar seu fluxo com outras pessoas, cada pessoa que trabalha ou usa o fluxo também deverá inserir a chave de API para conectar-se à API. Esse comportamento pode ser alterado no futuro e atualizaremos este tópico para refletir isso.


## <a name="create-a-flow"></a>Criar um fluxo

Agora você está pronto para criar um fluxo que usa o conector personalizado e a lista do SharePoint que você criou.

### <a name="add-a-trigger-and-specify-a-condition"></a>Adicione um gatilho e especifique uma condição

Você primeiro cria um fluxo de espaço em branco (sem um modelo) e adiciona um *gatilho* que é acionado quando um item é criado na lista do SharePoint. Então adicione uma *condição* para determinar o que acontece em seguida.

1. Em [flow.microsoft.com](https://flow.microsoft.com), clique em **Meus Fluxos** e, em seguida, em **Criar do zero**.

    ![Criar do zero](media/functions-flow-scenario/create-from-blank.png)

2. Clique no gatilho do SharePoint **Quando um item é criado**.

    ![Escolher um gatilho](media/functions-flow-scenario/choose-trigger.png)

    Se você ainda não tiver entrado no SharePoint, será solicitado a fazê-lo.

3. Para **Endereço do site**, digite o nome do site do SharePoint e, para **Nome da lista**, insira a lista que contém os dados da turbina.

    ![Escolher um gatilho](media/functions-flow-scenario/site-list.png)

4. Clique em **Nova etapa** e, em seguida, **Adicionar uma condição**.

    ![Adicione uma condição](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow adiciona duas ramificações ao fluxo: **Em caso afirmativo** e **se nenhum**. Você adiciona etapas a uma ou ambas as ramificações depois de definir a condição que você deseja corresponder.

    ![Ramificações de condição](media/functions-flow-scenario/condition-branches.png)

5. No cartão **Condição**, clique na primeira caixa e selecione **ServiceRequired** na caixa de diálogo **Conteúdo dinâmico**.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition1-field.png)

6. Digite um valor de `True` para a condição.

    ![Insira true para a condição](media/functions-flow-scenario/condition1-yes.png)

    O valor é exibido como `Yes` ou `No` na lista do SharePoint, mas é armazenado como um *booliano*, `True` ou `False`. 

7. Clique em **Criar fluxo** na parte superior da página. Clique em **Atualizar Fluxo** periodicamente.

Para qualquer item criado na lista, o fluxo verifica se o campo **ServiceRequired** é definido como `Yes`, em seguida, vai para a ramificação **Se sim** ou **Se não**, conforme apropriado. Para economizar tempo, neste tópico, você somente especifica ações para a ramificação **Se sim**.

### <a name="add-the-custom-connector"></a>Adicionar o conector personalizado

Agora, você adiciona o conector personalizado que chama a função no Azure. Você adiciona o conector personalizado ao fluxo como um conector padrão. 

1. Na ramificação **se Sim**, clique em **Adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Na caixa de diálogo **Escolher uma ação**, pesquise `Turbine Repair` e, em seguida, selecione a ação **Reparo da turbina – calcular custos**.

    ![Escolher uma ação](media/functions-flow-scenario/choose-turbine-repair.png)

    A imagem a seguir mostra o cartão que é adicionado ao fluxo. Os campos e as descrições são provenientes de definição de OpenAPI para o conector.

    ![Calcula os padrões de custos](media/functions-flow-scenario/calculates-costs-default.png)

3. No cartão **Calcular custos**, use a caixa de diálogo **Conteúdo dinâmico** para selecionar entradas para a função. O Microsoft Flow mostra os campos numéricos, mas não o campo de data, pois a definição de OpenAPI especifica que **Horas** e **Capacidade** são numéricas.

    Para **Horas**, selecione **EstimatedEffort** e, para **Capacidade**, selecione **MaxOutput**.

    ![Escolher uma ação](media/functions-flow-scenario/calculates-costs-fields.png)

     Agora você adiciona outra condição baseada na saída da função.

4. Na parte inferior da ramificação **Se sim**, clique em **Mais** e, em seguida, **Adicionar uma condição**.

    ![Adicione uma condição](media/functions-flow-scenario/condition2-add.png)

5. No cartão **Condição 2**, clique na primeira caixa e selecione **Mensagem** na caixa de diálogo **Conteúdo dinâmico**.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition2-field.png)

6. Insira um valor de `Yes`. O fluxo vai para a próxima ramificação **Se sim** ou **Se não** com base em se a mensagem retornada pela função é sim (fazer o reparo) ou não (não fazer o reparo). 

    ![Insira Sim para condição](media/functions-flow-scenario/condition2-yes.png)

O fluxo agora deve se parecer com a imagem a seguir.

![Insira Sim para condição](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Enviar email com base nos resultados de função

Nesse ponto no fluxo, a função retornou um valor de **Mensagem** de `Yes` ou `No` da função, bem como outras informações sobre os custos e o potencial de receita. Na ramificação **Se sim** da segunda condição, você enviará um email, mas poderia fazer várias coisas, como write-back na lista do SharePoint ou iniciar um [processo de aprovação](https://flow.microsoft.com/documentation/modern-approvals/).

1. Na ramificação **Se sim** da segunda condição, clique em **Adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Na caixa de diálogo **Escolher uma ação**, pesquise `email` e, em seguida, selecione uma ação de enviar email com base no sistema de email que você usa (neste caso, o Outlook).

    ![Enviar um email do Outlook](media/functions-flow-scenario/outlook-send-email.png)

3. No cartão **Enviar um email**, componha um email. Insira um nome válido na sua organização para o campo **Para**. Na imagem abaixo, você pode ver que os outros campos são uma combinação de texto e tokens da caixa de diálogo **Conteúdo dinâmico**. 

    ![Campos de email](media/functions-flow-scenario/email-fields.png)

    O token **Título** vem da lista do SharePoint, e **CostToFix** e **RevenueOpportunity** são retornados pela função.

    O fluxo concluído deve parecer com a imagem a seguir (deixamos de fora a primeira ramificação **Se não** para economizar espaço).

    ![Fluxo completo](media/functions-flow-scenario/complete-flow.png)

4. Clique em **Atualizar Fluxo** na parte superior da página, em seguida, clique em **Concluído**.

## <a name="run-the-flow"></a>Executar o fluxo

Agora que o fluxo está concluído, você adiciona uma linha à lista do SharePoint e vê como o fluxo responde.

1. Volte para a lista do SharePoint e clique em **Edição Rápida**.

    ![Edição rápida](media/functions-flow-scenario/quick-edit.png)

2. Insira os valores a seguir na grade de edição.

    | Coluna de lista     | Value           |
    |-----------------|---------------------|
    | **Título**           | Turbina 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Sim |
    | **EstimatedEffort** | 10 |

3. Clique em **Concluído**.

    ![Edição rápida concluída](media/functions-flow-scenario/quick-edit-done.png)

    Quando você adiciona o item, ele aciona o fluxo, que você verá em seguida.

4. Em [flow.microsoft.com](https://flow.microsoft.com), clique em **Meus Fluxos** e, em seguida, clique no fluxo que você criou.

    ![Meus fluxos](media/functions-flow-scenario/my-flows.png)

5. Em **HISTÓRICO DE EXECUÇÃO**, clique na execução do fluxo.

    ![Histórico da execução](media/functions-flow-scenario/run-history.png)

    Se a execução tiver sido bem-sucedida, você poderá examinar as operações de fluxo na próxima página. Se a execução falhar por algum motivo, a próxima página fornecerá informações de solução de problemas.

6. Expanda os cartões para ver o que ocorreu durante o fluxo. Por exemplo, expanda o cartão **Calcular custos** para ver as entradas e saídas da função. 

    ![Calcula entradas e saídas de custos](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Verifique a conta de email da pessoa que você especificou no campo **Para** do cartão **Enviar um email**. O email enviado do fluxo de deve se parecer com a imagem a seguir.

    ![Email de fluxo](media/functions-flow-scenario/flow-email.png)

    Você pode ver como os tokens foram substituídos pelos valores corretos da lista do SharePoint e a função.

## <a name="next-steps"></a>Próximas etapas
Neste tópico, você aprendeu a:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exportar uma definição de API.
> * Adicionar uma conexão à API.
> * Crie um fluxo para enviar email se um reparo for econômico.
> * Execute o fluxo.

Para saber mais sobre o Microsoft Flow, consulte [Introdução ao Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Para saber mais sobre outros cenários interessantes que usam Azure Functions, consulte [Chamar uma função do PowerApps](functions-powerapps-scenario.md) e [Criar uma função que se integre aos Aplicativos Lógicos do Azure](functions-twitter-email.md).
