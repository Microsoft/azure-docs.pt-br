---
title: Conectar-se ao banco de dados do IBM Informix
description: Automatizar tarefas e fluxos de trabalho que gerenciam os recursos armazenados no IBM Informix usando o aplicativo lógico do Azure
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761637"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gerenciar recursos do banco de dados IBM Informix usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector do Informix](/connectors/informix/), você pode criar tarefas automatizadas e fluxos de trabalho que gerenciam recursos em um banco de dados IBM Informix. Esse conector inclui um cliente da Microsoft que se comunica com computadores remotos do servidor Informix em uma rede TCP/IP, incluindo bancos de dados baseados em nuvem, como o IBM Informix para Windows em execução na virtualização do Azure e bancos de dados locais quando você usa o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Você pode se conectar a essas plataformas e versões da Informix se elas estiverem configuradas para dar suporte a conexões de cliente DRDA (arquitetura de banco de dados relacional distribuído):

* IBM Informix 12.1
* IBM Informix 11.7

Este tópico mostra como usar o Conector em um aplicativo lógico para processar as operações do banco de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Para bancos de dados locais, [faça o download e instale o gateway de dados](../logic-apps/logic-apps-gateway-install.md) local em um computador local e, em seguida, [crie um recurso de gateway de dados do Azure no portal do Azure](../logic-apps/logic-apps-gateway-connection.md).

* O aplicativo lógico em que você precisa ter acesso ao seu banco de dados Informix. Esse conector fornece apenas ações, de modo que seu aplicativo lógico já deve começar com um gatilho, por exemplo, o [gatilho de recorrência](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Adicionar uma ação de Informix

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. Na etapa em que você deseja adicionar a ação da Informix, selecione **nova etapa**.

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `informix` como o filtro. Na lista de ações, selecione a ação desejada, por exemplo:

   ![Selecione a ação da Informix a ser executada](./media/connectors-create-api-informix/select-informix-connector-action.png)

   O conector fornece essas ações, que executam as operações de banco de dados correspondentes:

   * Obter tabelas-listar tabelas de banco de dados usando uma `CALL` instrução
   * Obter linhas – ler todas as linhas usando uma `SELECT *` instrução
   * Obter linha-ler uma linha usando uma `SELECT WHERE` instrução
   * Adicionar uma linha usando uma `INSERT` instrução
   * Editar uma linha usando uma `UPDATE` instrução
   * Excluir uma linha usando uma `DELETE` instrução

1. Se você for solicitado a fornecer detalhes de conexão para seu banco de dados Informix, siga as [etapas para criar a conexão](#create-connection)e continue com a próxima etapa.

1. Forneça as informações para a ação selecionada:

   | Ação | Descrição | Propriedades e descrições |
   |--------|-------------|-----------------------------|
   | **Obter tabelas** | Listar tabelas de banco de dados executando uma instrução de chamada Informix. | Nenhum |
   | **Obter linhas** | Busque todas as linhas na tabela especificada executando uma `SELECT *` instrução Informix. | **Nome da tabela**: o nome da tabela Informix que você deseja <p><p>Para adicionar outras propriedades a essa ação, selecione-as na lista **Adicionar novo parâmetro** . Para obter mais informações, consulte o [tópico de referência do conector](/connectors/informix/). |
   | **Obter linha** | Busque uma linha da tabela especificada executando uma `SELECT WHERE` instrução Informix. | - **Nome da tabela**: o nome da tabela Informix que você deseja <br>- **ID da linha**: a ID exclusiva da linha, por exemplo, `9999` |
   | **Inserir linha** | Adicione uma linha à tabela Informix especificada executando uma `INSERT` instrução Informix. | - **Nome da tabela**: o nome da tabela Informix que você deseja <br>- **Item**: a linha com os valores a serem adicionados |
   | **Atualizar linha** | Altere uma linha na tabela Informix especificada executando uma `UPDATE` instrução Informix. | - **Nome da tabela**: o nome da tabela Informix que você deseja <br>- **ID da linha**: a ID exclusiva da linha a ser atualizada, por exemplo, `9999` <br>- **Row**: a linha com os valores atualizados, por exemplo, `102` |
   | **Excluir linha** | Remova uma linha da tabela Informix especificada executando uma `DELETE` instrução Informix. | - **Nome da tabela**: o nome da tabela Informix que você deseja <br>- **ID da linha**: a ID exclusiva da linha a ser excluída, por exemplo, `9999` |
   ||||

1. Salve seu aplicativo lógico. Agora, [teste seu aplicativo lógico](#test-logic-app) ou continue criando seu aplicativo lógico.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Conectar-se ao Informix

1. Se o seu aplicativo lógico se conectar a um banco de dados local, selecione **conectar por meio do gateway do data local**.

1. Forneça essas informações de conexão e, em seguida, selecione **criar**.

   | Propriedade | Propriedade JSON | Obrigatório | Valor de exemplo | Descrição |
   |----------|---------------|----------|---------------|-------------|
   | Nome da conexão | `name` | Sim | `informix-demo-connection` | O nome a ser usado para a conexão com o banco de dados Informix |
   | Servidor | `server` | Sim | Nuvem `informixdemo.cloudapp.net:9089` <br>-Local: `informixdemo:9089` | O endereço TCP/IP ou alias que está no formato IPv4 ou IPv6, seguido por dois-pontos e um número de porta TCP/IP |
   | Banco de dados | `database` | Sim | `nwind` | O nome do banco de dados relacional DRDA (RDBNAM) ou o nome do banco de dados Informix (dbname). A Informix aceita uma cadeia de caracteres de 128 bytes. |
   | Autenticação | `authentication` | Somente local | **Básico** ou **Windows** (Kerberos) | O tipo de autenticação exigido pelo seu banco de dados Informix. Essa propriedade só aparece quando você seleciona **conectar por meio do gateway de dados local**. |
   | Nome de Usuário | `username` | Não | <*nome de usuário do banco de dados*> | Um nome de usuário para o banco de dados |
   | Senha | `password` | Não | <*banco de dados-senha*> | Uma senha para o banco de dados |
   | Gateway | `gateway` | Somente local | -<*Azure-assinatura*> <br>-<*Azure-local-data-gateway-Resource*> | A assinatura do Azure e o nome do recurso do Azure para o gateway de dados local que você criou na portal do Azure. As propriedades e as subpropriedades de **Gateway** só aparecem quando você seleciona **conectar por meio do gateway de dados local**. |
   ||||||

   Por exemplo:

   * **Banco de dados de nuvem**

     ![Informações de conexão do banco de dados de nuvem](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Banco de dados local**

     ![Informações de conexão de banco de dados local](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Salve seu aplicativo lógico.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer do aplicativo lógico, selecione **executar**. Depois que o aplicativo lógico for executado, você poderá exibir as saídas dessa execução.

1. No menu do seu aplicativo lógico, selecione **visão geral**. No painel Visão geral, em **Summary**  >  **histórico de execuções**de resumo, selecione a execução mais recente.

1. Em **execução do aplicativo lógico**, selecione **detalhes da execução**.

1. Na lista ações, selecione a ação com as saídas que você deseja exibir, por exemplo, **Get_tables**.

   Se a ação tiver sido bem-sucedida, sua propriedade **status** será marcada como **bem-sucedida**.

1. Para exibir as entradas, em **link de entradas**, selecione o link de URL. Para exibir as saídas, no link de **link de saídas** , selecione o link de URL. Aqui estão algumas saídas de exemplo:

   * **Get_tables** mostra uma lista de tabelas:

     ![Saídas da ação "obter tabelas"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** mostra uma lista de linhas:

     ![Saídas da ação "obter linhas"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** mostra a linha especificada:

     ![Saídas da ação "obter linha"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** mostra a nova linha:

     ![Saídas da ação "Inserir linha"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** mostra a linha atualizada:

     ![Saídas da ação "atualizar linha"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** mostra a linha excluída:

     ![Saídas da ação "Excluir linha"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição do Swagger do conector, examine a [página de referência do conector](/connectors/informix/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](apis-list.md)
