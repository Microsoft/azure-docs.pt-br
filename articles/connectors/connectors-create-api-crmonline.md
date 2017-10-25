---
title: "Conectar-se ao Dynamics 365 (online) do Aplicativo Lógico do Azure | Microsoft Docs"
description: "Criar fluxos de trabalho de aplicativo lógico que gerenciem entidades (online) do Dynamics 365 por meio da API fornecida pelo conector do Dynamics 365"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Conectar-se ao Dynamics 365 de fluxos de trabalho de aplicativo lógico

Com Aplicativos Lógicos, você pode se conectar ao Dynamics 365 (online) e criar fluxos de negócios úteis que criam registros, atualizam itens ou retornam uma lista de registros. Com o conector do Dynamics 365, você pode:

* Compilar seu fluxo de negócios com base nos dados que obtém do Dynamics 365 (online).
* Usar ações que obtêm uma resposta e disponibilizar a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365 (online), você pode enviar um e-mail usando o Office 365.

Este tópico mostra como criar um aplicativo lógico que cria uma tarefa no Dynamics 365 sempre que um novo cliente em potencial é criado no Dynamics 365.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure.
* Uma conta do Dynamics 365 (online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Criar uma tarefa sempre que um novo cliente em potencial é criado no Dynamics 365

1.  [Entrar no Azure](https://portal.azure.com).

2.  Na caixa de pesquisa do Azure, digite `Logic apps` e pressione ENTER.

      ![Localizar aplicativos lógicos](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  Em **Aplicativos lógicos**, clique em **Adicionar**.

      ![Adicionar LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Para criar o aplicativo lógico, complete os campos **Nome**, **Assinatura**, **Grupo de Recursos** e **Local** e, em seguida, clique em **Criar**.

5.  Selecione o novo aplicativo lógico. Quando você receber a notificação **Implantação bem-sucedida**, clique em **Atualizar**.

6.  Em **Ferramentas de Desenvolvimento**, clique em **Designer de Aplicativo Lógico**. Na lista de modelos, clique em **Aplicativo Lógico em Branco**.

7.  Na caixa de pesquisa, digite `Dynamics 365`. Na lista de disparadores Dynamics 365, selecione **Dynamics 365 – Quando um registro é criado**.

8.  Se for solicitado que você entre no Dynamics 365, faça isso agora.

9.  Nos detalhes do gatilho, insira as seguintes informações:

  * **Nome da organização**. Selecione a instância do Dynamics 365 que você deseja que o aplicativo lógico ouça.

  * **Nome da entidade**. Selecione a entidade à qual você deseja escutar. Esse evento atua como um gatilho para iniciar o aplicativo lógico. 
  Neste passo a passo, **Clientes em potencial** está selecionado.

  * **Com que frequência você deseja verificar os itens?** Esses valores definem com que frequência o aplicativo lógico verifica atualizações relacionadas ao gatilho. A configuração padrão é verificar se há atualizações a cada três minutos.

    * **Frequência**. Selecione segundos, minutos, horas ou dias.

    * **Intervalo**. Insira o número de segundos, minutos, horas ou dias que você deseja que passem antes da próxima verificação.

      ![Detalhes do Gatilho de Aplicativo Lógico](./media/connectors-create-api-crmonline/trigger-details.png)

10. Clique na caixa **Nova etapa** e depois clique em **Adicionar uma ação**.

11. Na caixa de pesquisa, digite `Dynamics 365`. Na lista de ações, selecione **Dynamics 365 – Criar um novo registro**.

12. Insira as seguintes informações:

    * **Nome da organização**. Selecione a instância do Dynamics 365 na qual você deseja que o fluxo crie o registro. 
    Observe que essa instância não precisa ser a mesma instância da qual o evento é disparado.

    * **Nome da entidade**. Selecione a entidade que você deseja que crie um registro quando o evento é disparado. 
    Neste passo a passo, **Tarefas** está selecionado.

13. Clique na caixa **Assunto** que é exibida. Na lista de conteúdo dinâmico que aparece, você pode selecionar qualquer um destes campos:

    * **Sobrenome**. Selecionar este campo inserirá o sobrenome do lead no campo Assunto da tarefa quando a tarefa de registro for criada.
    * **Tópico**. Selecionar este campo inserirá o campo Tópico do lead no campo Assunto da tarefa quando a tarefa de registro for criada. 
    Clique em **Tópico** para adicioná-lo à caixa **Assunto**.

      ![Criar novos detalhes do registro do Aplicativo Lógico](./media/connectors-create-api-crmonline/create-record-details.png)

14. Clique em **Salvar** na barra de ferramentas do Designer de Aplicativo Lógico.

    ![Barra de ferramentas do Designer do Aplicativo Lógico Salvar](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Para iniciar o Aplicativo Lógico, clique em **Executar**.

    ![Barra de ferramentas do Designer do Aplicativo Lógico Salvar](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Agora, crie um registro de cliente em potencial no Dynamics 365 para Vendas e veja seu fluxo em ação!

## <a name="set-advanced-options-for-a-logic-app-step"></a>Definir opções avançadas para uma etapa de aplicativo lógico

Para especificar como filtrar dados em uma etapa do aplicativo lógico, clique em **Mostrar opções avançadas** nessa etapa e, em seguida, adicione uma consulta de filtro ou order by.

Por exemplo, você pode usar uma consulta de filtro para obter apenas contas ativas e ordenar pelo nome da conta. Para fazer isso, insira a consulta de filtro OData `statuscode eq 1` e selecione **Nome da Conta** no painel de conteúdo dinâmico. Mais informações: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) e [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Opções avançadas de aplicativo lógico](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Práticas recomendadas ao usar o opções avançadas

Observe que, ao adicionar um valor a um campo, você deve corresponder ao tipo de campo se digitar um valor ou selecionar um valor da lista de conteúdo dinâmico.

Tipo de campo  |Como usar  |Onde encontrar  |Nome  |Tipo de dados  
---------|---------|---------|---------|---------
Campos de texto|Campos de texto exigem uma única linha de texto ou conteúdo dinâmico que seja um campo de tipo de texto. Exemplos incluem os campos Categoria e Subcategoria.|Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |categoria |Linha única de texto        
Campos de número inteiro | Alguns campos exigem conteúdo inteiro ou dinâmico que seja um campo de tipo inteiro. Exemplos incluem Porcentagem concluída e Duração. |Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |percentcomplete |Número inteiro         
Campos de data | Alguns campos exigem a data inserida no formato dd/mm/aaaa ou conteúdo dinâmico que seja um campo do tipo data. Exemplos incluem Criado em, Data de início, Início real, Último tempo de espera, Término real e Data de vencimento. | Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |createdon |Data e hora
Campos que exigem um registro de ID e tipo de pesquisa |Alguns campos que fazem referência a outro registro de entidade exigem a ID do registro e o tipo de pesquisa. |Configurações > Personalizações > Personalizar o sistema > Entidades > Conta > Campos  | accountid  | Chave primária

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Mais exemplos de campos que exigem uma ID de registro e tipo de pesquisa
Expandindo a tabela anterior, aqui há mais exemplos de campos que não funcionam com valores selecionados da lista de conteúdo dinâmica. Em vez disso, esses campos exigem que uma ID de registro e o tipo de pesquisa sejam inseridos nos campos no PowerApps.  
* Proprietário e Tipo de proprietário. O campo Proprietário deve ser uma ID de registro de usuário ou de equipe válida. O Tipo de proprietário deve ser **systemusers** ou **equipes**.
* Cliente e Tipo de cliente. O campo Cliente deve ser uma ID de registro de contato ou conta válida. O Tipo de proprietário deve ser **contas** ou **contatos**.
* Referente e Referente ao tipo. O campo Referente deve ser uma ID de registro válida, como uma ID de registro de contato ou conta. Referente ao tipo deve ser o tipo de pesquisa para o registro, como **contas** ou **contatos**.

O seguinte exemplo de ação de criação de tarefa adiciona um registro de conta que corresponde à ID de registro que a adiciona ao campo referente da tarefa.

![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid-type-account.png)

Este exemplo também atribui a tarefa a um usuário específico com base na ID de registro do usuário.

![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid-type-user.png)

Para localizar uma ID de registro, consulte a seção a seguir: *Localizar a ID de registro*

## <a name="find-the-record-id"></a>Localize a ID de registro

1. Abra um registro, como um registro de conta.

2. Na barra de ferramentas Ações, clique em **Pop Out** ![registro de pop-up](./media/connectors-create-api-crmonline/popout-record.png).
Como alternativa, na barra de ferramentas Ações, para copiar a URL completa no seu programa de email padrão, clique em **ENVIAR LINK POR EMAIL**.

   A ID de registro é exibida entre os caracteres de codificação % 7b e %7 da URL.

   ![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Solucionar problemas
Para solucionar uma falha de uma etapa em um aplicativo lógico, exiba os detalhes de status do evento.

1. Em **Aplicativos Lógicos**, clique no seu aplicativo lógico e, em seguida, em **Visão geral**. 

   A área Resumo é exibida, fornecendo o status de execução do aplicativo lógico. 

   ![Status de execução do aplicativo lógico](./media/connectors-create-api-crmonline/tshoot1.png)

2. Se houver execuções com falha, clique no evento com falha sobre o qual você deseja exibir mais informações. Para expandir uma etapa com falha, clique nessa etapa.

   ![Expandir etapa com falha](./media/connectors-create-api-crmonline/tshoot2.png)

   Os detalhes da etapa aparecem e podem ajudar a diagnosticar a causa da falha.

   ![Detalhes da etapa com falha](./media/connectors-create-api-crmonline/tshoot3.png)

Para obter mais informações sobre como solucionar problemas de aplicativos lógicos, consulte [Diagnosticando falhas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/crm/). 

## <a name="next-steps"></a>Próximas etapas
Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).
