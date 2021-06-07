---
title: Conectar-se ao Common Data Service (Microsoft inverso)
description: Criar e gerenciar registros Common Data Service (Microsoft dataverso) usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382337"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Criar e gerenciar registros em Common Data Service (Microsoft dataverso) usando aplicativos lógicos do Azure

> [!NOTE]
> Em novembro de 2020, Common Data Service foi renomeado para o Microsoft dataverso.

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector de Common Data Service](/connectors/commondataservice/), você pode criar fluxos de trabalho automatizados que gerenciam registros em seu Common Data Service, agora banco de dados [do Microsoft dataverso](/powerapps/maker/common-data-service/data-platform-intro) . Esses fluxos de trabalho podem criar registros, atualizar registros e executar outras operações. Você também pode obter informações do banco de dados do dataverso e disponibilizar a saída para outras ações a serem usadas em seu aplicativo lógico. Por exemplo, quando um registro é atualizado no banco de dados do dataverso, você pode enviar um email usando o conector do Outlook do Office 365.

Este artigo mostra como você pode criar um aplicativo lógico que cria um registro de tarefa sempre que um novo registro de Lead é criado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [ambiente de Common Data Service](/power-platform/admin/environments-overview), que é um espaço em que sua organização armazena, gerencia e compartilha dados de negócios e um banco de Common Data Service. Para obter mais informações, consulte estes recursos:<p>

  * [Aprenda: introdução ao Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Plataforma de energia-visão geral dos ambientes](/power-platform/admin/environments-overview)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) e o aplicativo lógico de onde você deseja acessar os registros no banco de dados do dataverso. Para iniciar seu aplicativo lógico com um gatilho Common Data Service, você precisa de um aplicativo lógico em branco. Se você for novo no aplicativo lógico do Azure, examine [início rápido: criar seu primeiro fluxo de trabalho usando os aplicativos lógicos do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Adicionar Common Data Service gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Para este exemplo, adicione o gatilho Common Data Service que é acionado quando um novo registro é criado.

1. No [Portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na caixa de pesquisa, insira `common data service`. Neste exemplo, na lista de gatilhos, selecione este gatilho: **Quando um registro é criado**

   ![Selecione o gatilho "quando um registro é criado"](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Se solicitado, entre no Common Data Service.

1. No gatilho, forneça informações sobre o ambiente em que você deseja monitorar novos registros "leads", por exemplo:

   ![Informações de gatilho para o ambiente a ser monitorado](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Ambiente** | Sim | O ambiente a ser monitorado, por exemplo, "produção de vendas da Fabrikam". Para obter mais informações, consulte [visão geral da plataforma de energia – ambientes](/power-platform/admin/environments-overview). |
   | **Nome da entidade** | Sim | A entidade a ser monitorada, por exemplo, "leads" |
   | **Escopo** | Sim | A origem que criou o novo registro, por exemplo, um usuário em sua unidade de negócios ou qualquer usuário em sua organização. Este exemplo usa "unidade de negócios". |
   ||||

## <a name="add-common-data-service-action"></a>Adicionar ação de Common Data Service

Agora, adicione uma ação Common Data Service que cria um registro de tarefa para um novo registro "leads".

1. No gatilho **quando um registro é criado** , selecione **nova etapa**.

1. Na caixa de pesquisa, insira `common data service`. Na lista de ações, selecione esta ação: **Criar um novo registro**

   ![Selecione a ação "criar um novo registro"](./media/connect-common-data-service/select-create-new-record-action.png)

1. Na ação, forneça as informações sobre o ambiente em que você deseja criar o novo registro de tarefa. Se disponível, outras propriedades também serão exibidas com base na entidade que você selecionou para esta ação, por exemplo:

   ![Informações de ação para o ambiente onde criar o registro](./media/connect-common-data-service/create-new-record-action-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da organização** | Sim | O ambiente no qual você deseja criar o registro, que não precisa ser o mesmo ambiente em seu gatilho, mas é "produção de vendas da Fabrikam" neste exemplo |
   | **Nome da entidade** | Sim | A entidade na qual você deseja criar o registro, por exemplo, “Tarefas” |
   | **Entidade** | Sim, com base na entidade selecionada neste exemplo | Uma breve descrição sobre o objetivo desta tarefa |
   ||||

   1. Para a propriedade **Subject** , digite este texto com um espaço à direita:

      `Follow up with new lead:`

   1. Mantenha o ponteiro dentro da caixa de **assunto** para que a lista de conteúdo dinâmico permaneça visível.
   
   1. Na lista, na seção **quando um registro é criado** , selecione as saídas do gatilho que você deseja incluir no registro de tarefa, por exemplo:

      ![Selecionar saídas do gatilho a serem usadas no registro de tarefa](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Saída do gatilho | Descrição |
      |----------------|-------------|
      | **First Name** | O nome do registro de Lead a ser usado como o contato principal no registro de tarefa |
      | **Sobrenome** | O sobrenome do registro de Lead a ser usado como o contato principal no registro de tarefa |
      | **Descrição** | Outras saídas a serem incluídas no registro de tarefa, como endereço de email e número de telefone comercial |
      |||

   Quando você terminar, a ação poderá ser semelhante a este exemplo:

   ![Ação "criar um novo registro" concluída](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

1. Para iniciar manualmente o aplicativo lógico, na barra de ferramentas do designer, selecione **executar**. Para testar seu aplicativo lógico, crie um novo registro "leads".

## <a name="trigger-only-on-updated-attributes"></a>Disparar somente em atributos atualizados

Para gatilhos que são executados quando os registros são atualizados, como a ação **quando um registro é atualizado** , você pode usar atributos de filtro para que seu aplicativo lógico seja executado somente quando os atributos especificados forem atualizados. Esse recurso ajuda a impedir a execução de aplicativos lógicos desnecessários.

1. No gatilho, na lista **Adicionar novo parâmetro** , selecione filtros de **atributo**.

   ![Captura de tela que mostra a ação "quando um registro é atualizado" e a lista "Adicionar novo parâmetro" aberta com a propriedade "filtros de atributo" selecionada.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Para cada **item de filtros de atributo**, selecione o atributo que você deseja monitorar para as atualizações, por exemplo:

   ![Adicionar a propriedade "filtros de atributo"](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Listar registros com base em um filtro

Para ações que retornam registros, como a ação **listar registros** , você pode usar uma consulta ODATA que retorna registros com base no filtro especificado. Por exemplo, você tem a lista de ações somente os registros de contas ativas.

1. Na ação, abra a lista **Adicionar novo parâmetro** e selecione a propriedade **Filtrar consulta** .

   ![Adicionar a propriedade "filtrar consulta"](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Na propriedade de **consulta de filtro** que agora aparece na ação, insira esta consulta de filtro ODATA: `statuscode eq 1`

   ![Inserir consulta de filtro ODATA para filtrar registros](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Para obter mais informações sobre `$filter` Opções de consulta do sistema, consulte [Common Data Service-resultados do filtro](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Listar registros com base em um pedido

Para ações que retornam registros, como a ação **listar registros** , você pode usar uma consulta ODATA que retorna registros em uma ordem especificada, que varia de acordo com os registros que a ação retorna. Por exemplo, você pode fazer com que a ação liste os registros com base no nome da conta.

1. Na ação, abra a lista **Adicionar novo parâmetro** e selecione a propriedade **ordenar por** .

   ![Adicionar a propriedade "Order By"](./media/connect-common-data-service/list-records-action-order-by.png)

1. Na propriedade **order by** que agora aparece na ação, insira esta consulta de filtro ODATA: `name`

   ![Inserir consulta de filtro ODATA para ordenar registros](./media/connect-common-data-service/list-records-action-order-by-value.png)

Para obter mais informações sobre `$orderby` Opções de consulta do sistema, consulte [resultados de ordem de Common Data Service](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Tipos de dados de campo

Independentemente de você inserir um valor manualmente ou selecionar um valor da lista de conteúdo dinâmico para um campo em um gatilho ou ação, o tipo de dados do valor deve corresponder ao tipo de dados exigido do campo.

Esta tabela descreve alguns tipos de campo e os tipos de dados que esses campos exigem para seus valores.

| Campo | Tipo de dados | Descrição |
|-------|-----------|-------------|
| Campo de texto | Linha única de texto | Requer uma única linha de texto ou conteúdo dinâmico que tenha o tipo de dados text, por exemplo, essas propriedades: <p><p>- **Ndescrição** <br>- **Categorias** |
| Campo de inteiro | Número inteiro | Requer um inteiro ou conteúdo dinâmico que tenha o tipo de dados Integer, por exemplo, essas propriedades: <p><p>- **Porcentagem concluída** <br>- **Permanência** |
| Campo de data | Data e hora | Requer uma data no formato MM/DD/YYY ou conteúdo dinâmico que tenha o tipo de dados Date, por exemplo, essas propriedades: <p><p>- **Criado em** <br>- **Data de início** <br>- **Início real** <br>- **Fim real** <br>- **Data de conclusão** |
| Campo que faz referência a outro registro de entidade | Chave primária | Requer uma ID de registro, como um GUID, e um tipo de pesquisa, o que significa que os valores da lista de conteúdo dinâmico não funcionarão, por exemplo, essas propriedades: <p><p>- **Proprietário**: deve ser uma ID de usuário válida ou uma ID de registro de equipe. <br>- **Tipo de proprietário**: deve ser um tipo de pesquisa como `systemusers` ou `teams` , respectivamente. <p><p>- **Sobre**: deve ser uma ID de registro válida, como uma ID de conta ou uma ID de registro de contato. <br>- **Referente ao tipo**: deve ser um tipo de pesquisa como `accounts` ou `contacts` , respectivamente. <p><p>- **Cliente**: deve ser uma ID de registro válida, como uma ID de conta ou ID de registro de contato. <br>- **Tipo de cliente**: deve ser o tipo de pesquisa, como `accounts` ou `contacts` , respectivamente. |
||||

Este exemplo mostra como a ação **criar um novo registro** cria um novo registro de "tarefas" associado a outros registros de entidade, especificamente um registro de usuário e um registro de conta. A ação especifica as IDs e os tipos de pesquisa para esses registros de entidade usando valores que correspondem aos tipos de dados esperados para as propriedades relevantes.

* Com base na propriedade **Owner** , que especifica uma ID de usuário e a propriedade de **tipo Owner** , que especifica o `systemusers` tipo de pesquisa, a ação associa o novo registro "Tasks" a um usuário específico.

* Com base na propriedade **referente** , que especifica uma ID de registro e a propriedade de **tipo sobre** , que especifica o `accounts` tipo de pesquisa, a ação associa o novo registro "tarefas" a uma conta específica.

![Criar registro de "tarefas" associado a IDs e tipos de pesquisa](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Referência de conector

Para obter informações técnicas com base na descrição do Swagger do conector, como gatilhos, ações, limites e outros detalhes, consulte a [página de referência do conector](/connectors/commondataservice/).

## <a name="troubleshooting-problems"></a>Solucionando problemas

### <a name="calls-from-multiple-environments"></a>Chamadas de vários ambientes

Ambos os conectores, Common Data Service e Common Data Service (ambiente atual), armazenam informações sobre os fluxos de trabalho do aplicativo lógico que precisam e recebem notificações sobre alterações de entidade usando a `callbackregistrations` entidade em seu Microsoft dataverso. Se você copiar uma organização do dataverso, qualquer webhook também será copiado. Se você copiar sua organização antes de desabilitar os fluxos de trabalho mapeados para sua organização, todos os WebHooks copiados também apontarão para os mesmos aplicativos lógicos, que, por sua vez, receberão notificações de várias organizações.

Para interromper notificações indesejadas, exclua o registro de retorno de chamada da organização que envia essas notificações seguindo estas etapas:

1. Identifique a organização do dataverso de onde você deseja remover as notificações e entre nessa organização.

1. No navegador Chrome, localize o registro de retorno de chamada que você deseja excluir seguindo estas etapas:

   1. Examine a lista genérica para todos os registros de retorno de chamada no seguinte URI do OData para que você possa exibir os dados dentro da `callbackregistrations` entidade:

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > Se nenhum valor for retornado, talvez você não tenha permissões para exibir esse tipo de entidade ou talvez não esteja conectado à organização correta.

   1. Filtre o nome lógico da entidade disparando `entityname` e o evento de notificação que corresponde ao fluxo de trabalho do aplicativo lógico (mensagem). Cada tipo de evento é mapeado para o inteiro da mensagem da seguinte maneira:

      | Tipo de evento | Número inteiro da mensagem |
      |------------|-----------------|
      | Criar | 1 |
      | Excluir | 2 |
      | Atualizar | 3 |
      | CreateOrUpdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      Este exemplo mostra como você pode filtrar as `Create` notificações em uma entidade chamada `nov_validation` usando o seguinte URI de OData para uma organização de exemplo:

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![Captura de tela que mostra a janela do navegador e o URI do OData na barra de endereços.](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > Se existirem vários gatilhos para a mesma entidade ou evento, você poderá filtrar a lista usando filtros adicionais, como `createdon` os `_owninguser_value` atributos e. O nome do usuário proprietário aparece em `/api/data/v9.0/systemusers({id})` .

   1. Depois de encontrar a ID para o registro de retorno de chamada que você deseja excluir, siga estas etapas:
   
      1. No navegador Chrome, abra o Chrome Ferramentas para Desenvolvedores (teclado: F12).

      1. Na janela, na parte superior, selecione a guia **console** .

      1. No prompt de linha de comando, digite este comando, que envia uma solicitação para excluir o registro de retorno de chamada especificado:

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > Certifique-se de fazer a solicitação de uma página de UCI (interface do cliente não unificada), por exemplo, da própria página de resposta do OData ou da API. Caso contrário, a lógica no arquivo de app.js pode interferir nessa operação.

   1. Para confirmar que o registro de retorno de chamada não existe mais, verifique a lista de registros de retorno de chamada.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [outros conectores para Aplicativos Lógicos do Azure](../connectors/apis-list.md)
