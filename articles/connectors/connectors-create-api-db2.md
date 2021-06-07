---
title: Acessar e gerenciar recursos do IBM DB2
description: Leia, edite, atualize e gerencie recursos do IBM DB2 criando fluxos de trabalho automatizados usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967394"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Acessar e gerenciar recursos do IBM DB2 usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector IBM DB2](/connectors/db2/), você pode criar tarefas e fluxos de trabalho automatizados com base nos recursos armazenados em seu banco de dados DB2. Seus fluxos de trabalho podem se conectar aos recursos em seu banco de dados, ler e listar as tabelas de banco de dados, adicionar linhas, alterá-las, excluí-las e muito mais. É possível incluir ações em seus aplicativos lógicos que obtêm respostas do seu banco de dados e disponibilizam a saída para outras ações.

Este artigo mostra como você pode criar um aplicativo lógico que realiza várias operações de banco de dados. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plataformas e versões com suporte

O conector DB2 inclui um cliente Microsoft que se comunica com servidores DB2 remotos em uma rede TCP/IP. Você pode usar esse conector para acessar bancos de dados de nuvem, como IBM DB2 para Windows, em execução na virtualização do Azure. Também é possível acessar bancos de dados DB2 locais após [instalar e configurar o gateway de dados local](../logic-apps/logic-apps-gateway-connection.md).

O conector IBM DB2 dá suporte a essas plataformas e versões do IBM DB2, juntamente com os produtos compatíveis com IBM DB2 que dão suporte à DRDA (Distributed Relacionative Database Architecture) versões 10 e 11:

| Plataforma | Versão | 
|----------|---------|
| IBM DB2 para z/OS | 12, 11,1, 10,1 |
| IBM DB2 para i | 7.3, 7.2, 7.1 |
| IBM DB2 para LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operações de banco de dados com suporte

O conector do IBM DB2 é compatível com estas operações de banco de dados, que mapeiam para as ações correspondentes no conector:

| Operação de banco de dados | Ação do conector |
|--------------------|------------------|
| Listar tabelas do banco de dados | Obter tabelas |
| Ler uma linha usando SELECT | Obter linha |
| Ler todas as linhas usando SELECT | Obter linhas |
| Adicionar uma linha usando INSERT | Inserir linha |
| Editar uma linha usando UPDATE | Atualizar linha |
| Remover uma linha usando DELETE | Excluir linha |
|||

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um banco de dados do IBM DB2, baseado em nuvem ou local

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar o banco de dados DB2. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho **Recorrência**.
Os exemplos neste artigo usam o gatilho **Recorrência**.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adicionar ação do DB2 – Obter tabelas

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. No gatilho, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira "db2" como seu filtro. Para esse exemplo, na lista de ações, selecione esta ação: **Obter tabelas (versão prévia)**

   ![Ação selecionar](./media/connectors-create-api-db2/select-db2-action.png)

   Agora você deverá fornecer os detalhes da conexão para seu banco de dados DB2.

1. Siga as etapas para criar conexões para [bancos de dados de nuvem](#cloud-connection) ou [bancos de dados locais](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Conectar-se ao DB2 de nuvem

Para configurar sua conexão, forneça estes detalhes de conexão quando solicitados. Escolha **Criar** e salve seu aplicativo lógico:

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Conectar-se por meio do gateway local** | Não | Aplica-se somente para conexões locais. |
| **Nome da conexão** | Sim | O nome da sua conexão, por exemplo, "MyLogicApp-DB2-connection" |
| **Servidor** | Sim | O endereço ou alias dois pontos número da porta para seu servidor DB2, por exemplo, "myDB2server.cloudapp.net:50000" <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um endereço TCP/IP ou alias, em formato IPv4 ou IPv6, seguido de dois pontos e um número da porta TCP/IP. |
| **Backup de banco de dados** | Sim | O nome do seu banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um RDBNAM (Nome do banco de dados relacional DRDA): <p>– O DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes e que o banco de dados é conhecido como um local "IBM DB2 para z/OS". <br>– O DB2 para i aceita uma cadeia de caracteres de 18 bytes em que o banco de dados é conhecido como um banco de dados relacional "IBM DB2 para i". <br>– O DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Nome de usuário** | Sim | Seu nome de usuário para o banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres cujo comprimento baseia-se no banco de dados específico: <p><p>– O DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para i aceita uma cadeia de caracteres de 10 bytes. <br>– O DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para Windows aceita uma cadeia de caracteres de 30 bytes. |
| **Senha** | Sim | Sua senha do banco de dados |
||||

Por exemplo:

![Detalhes de conexão para bancos de dados baseados em nuvem](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Conectar-se ao DB2 local

Antes de criar sua conexão, é necessário já ter o gateway de dados local instalado. Caso contrário, não será possível configurar sua conexão. Se você já tiver sua instalação de gateway, continue fornecendo esses detalhes de conexão e escolha **Criar**.

| Propriedade | Obrigatório | Descrição |
|----------|----------|-------------|
| **Conectar-se por meio do gateway local** | Sim | Aplica-se quando você desejar uma conexão local e mostra as propriedades da conexão local. |
| **Nome da conexão** | Sim | O nome da sua conexão, por exemplo, "MyLogicApp-DB2-connection" | 
| **Servidor** | Sim | O endereço ou alias dois pontos número da porta para seu servidor DB2, por exemplo, "myDB2server:50000" <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um endereço TCP/IP ou alias, em formato IPv4 ou IPv6, seguido de dois pontos e um número da porta TCP/IP. |
| **Backup de banco de dados** | Sim | O nome do seu banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres que representa um RDBNAM (Nome do banco de dados relacional DRDA): <p>– O DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes e que o banco de dados é conhecido como um local "IBM DB2 para z/OS". <br>– O DB2 para i aceita uma cadeia de caracteres de 18 bytes em que o banco de dados é conhecido como um banco de dados relacional "IBM DB2 para i". <br>– O DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| **Autenticação** | Sim | O tipo de autenticação para sua conexão, por exemplo, "Básica" <p><p>**Observação**: selecione esse valor na lista, que inclui Básica ou Windows (Kerberos). |
| **Nome de usuário** | Sim | Seu nome de usuário para o banco de dados <p><p>**Observação**: esse valor é uma cadeia de caracteres cujo comprimento baseia-se no banco de dados específico: <p><p>– O DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para i aceita uma cadeia de caracteres de 10 bytes. <br>– O DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. <br>– O DB2 para Windows aceita uma cadeia de caracteres de 30 bytes. |
| **Senha** | Sim | Sua senha do banco de dados |
| **Gateway** | Sim | O nome do seu gateway de dados local instalado <p><p>**Observação**: selecione esse valor na lista, que inclui todos os gateways de dados instalados dentro do grupo de recursos e de assinatura do Azure. |
||||

Por exemplo:

![Detalhes da conexão para bancos de dados locais](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Exibir tabelas de saída

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

   ![Exibir histórico de execuções](./media/connectors-create-api-db2/run-history.png)

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Obter tabelas**.

   ![Expandir ação](./media/connectors-create-api-db2/expand-action-step.png)

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem uma lista de tabelas.

   ![Exibir tabelas de saída](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obter linha

Para efetuar fetch de um registro em uma tabela do banco de dados DB2, use a ação **Obter linha** em seu aplicativo lógico. Essa ação executa uma instrução `SELECT WHERE` do DB2, por exemplo, `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Se você nunca usou ações do DB2 antes em seu aplicativo lógico, examine essas etapas na seção [Adicionar ação do DB2 – Obter tabelas](#add-db2-action), mas adicione a ação **Obter linha** e volte aqui para continuar.

   Após adicionar a ação **Obter linha**, veja como seu aplicativo lógico de exemplo é exibido:

   ![Ação Obter linha](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Especifique valores para todas as propriedades obrigatórias (*). Após selecionar uma tabela, a ação mostrará as propriedades relevantes específicas para os registros nessa tabela.

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela que tem o registro desejado, como "AREA" neste exemplo |
   | **ID de área** | Sim | A ID do registro desejado, como "99999" neste exemplo |
   ||||

   ![Captura de tela que mostra a ação "obter linha (visualização)" com a lista "nome de tabela" aberta e o valor de "área" selecionado.](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

### <a name="view-output-row"></a>Exibir linha de saída

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Obter linha**.

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem a linha especificada.

   ![Exibir linha de saída](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obter linhas

Para efetuar fetch de todos os registros em uma tabela do banco de dados DB2, use a ação **Obter linhas** em seu aplicativo lógico. Essa ação executa uma instrução `SELECT` do DB2, por exemplo, `SELECT * FROM AREA`.

1. Se você nunca usou ações do DB2 antes em seu aplicativo lógico, examine essas etapas na seção [Adicionar ação do DB2 – Obter tabelas](#add-db2-action), mas adicione a ação **Obter linhas** e volte aqui para continuar.

   Após adicionar a ação **Obter linhas**, veja como seu aplicativo lógico de exemplo é exibido:

   ![Ação Obter linhas](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Abra a lista **Nome da tabela** e, em seguida, selecione a tabela desejada, que é "AREA" neste exemplo:

   ![Captura de tela que mostra a ação "obter linha (visualização)" com o valor "área" selecionado na lista "nome da tabela".](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Para especificar um filtro ou uma consulta para resultados, escolha **Mostrar opções avançadas**.

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

### <a name="view-output-rows"></a>Exibir linhas de saída

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Obter linhas**.

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem todos os registros em sua tabela especificada.

   ![Exibir linhas de saída](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Inserir linha

Para adicionar um único registro a uma tabela do banco de dados DB2, use a ação **Inserir linha** em seu aplicativo lógico. Essa ação executa uma instrução `INSERT` do DB2, por exemplo, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Se você nunca usou ações do DB2 antes em seu aplicativo lógico, examine essas etapas na seção [Adicionar ação do DB2 – Obter tabelas](#add-db2-action), mas adicione a ação **Inserir linha** e volte aqui para continuar.

   Após adicionar a ação **Inserir linha**, veja como seu aplicativo lógico de exemplo é exibido:

   ![Ação Inserir linha](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Especifique valores para todas as propriedades obrigatórias (*). Após selecionar uma tabela, a ação mostrará as propriedades relevantes específicas para os registros nessa tabela.

   Para esse exemplo, estas são as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela na qual adicionar o registro, como "AREA" |
   | **ID de área** | Sim | A ID da área a ser adicionada, como "99999" |
   | **Descrição da área** | Sim | A descrição da área a ser adicionada, como "Área 99999" |
   | **ID da região** | Sim | A ID da região a ser adicionada, como "102" |
   |||| 

   Por exemplo:

   ![Captura de tela que mostra o designer de aplicativos lógicos com a ação "Inserir linha (visualização)" e os valores de propriedade de exemplo.](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

### <a name="view-insert-row-outputs"></a>Exibir saídas de linha de inserção

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Inserir linha**.

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem os registros adicionados à sua tabela especificada.

   ![Exibir a saída com a linha inserida](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Atualizar linha

Para atualizar um único registro em uma tabela do banco de dados DB2, use a ação **Atualizar linha** em seu aplicativo lógico. Essa ação executa uma instrução `UPDATE` do DB2, por exemplo, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Se você nunca usou ações do DB2 antes em seu aplicativo lógico, examine essas etapas na seção [Adicionar ação do DB2 – Obter tabelas](#add-db2-action), mas adicione a ação **Atualizar linha** e volte aqui para continuar.

   Após adicionar a ação **Atualizar linha**, veja como seu aplicativo lógico de exemplo é exibido:

   ![Ação Atualizar linha](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Especifique valores para todas as propriedades obrigatórias (*). Após selecionar uma tabela, a ação mostrará as propriedades relevantes específicas para os registros nessa tabela.

   Para esse exemplo, estas são as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela na qual atualizar o registro, como "AREA" |
   | **ID da linha** | Sim | A ID do registro a ser atualizado, como "99999" |
   | **ID de área** | Sim | A nova ID de área, como "99999" |
   | **Descrição da área** | Sim | A nova descrição de área, como "99999 atualizada" |
   | **ID da região** | Sim | A nova ID da região, como "102" |
   ||||

   Por exemplo:

   ![Captura de tela que mostra o designer de aplicativos lógicos com a ação "atualizar linha (visualização)" onde você seleciona uma tabela.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

### <a name="view-update-row-outputs"></a>Exibir saídas de linha de atualização

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Atualizar linha**.

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas incluem o registro atualizado em sua tabela especificada.

   ![Exibir a saída com a linha atualizada](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Excluir linha

Para excluir um único registro de uma tabela do banco de dados DB2, use a ação **Excluir linha** em seu aplicativo lógico. Essa ação executa uma instrução `DELETE` do DB2, por exemplo, `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Se você nunca usou ações do DB2 antes em seu aplicativo lógico, examine essas etapas na seção [Adicionar ação do DB2 – Obter tabelas](#add-db2-action), mas adicione a ação **Excluir linha** e volte aqui para continuar.

   Após adicionar a ação **Excluir linha**, veja como seu aplicativo lógico de exemplo é exibido:

   ![Ação Excluir linha](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Especifique valores para todas as propriedades obrigatórias (*). Após selecionar uma tabela, a ação mostrará as propriedades relevantes específicas para os registros nessa tabela.

   Para esse exemplo, estas são as propriedades:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da tabela** | Sim | A tabela na qual excluir o registro, como "AREA" |
   | **ID da linha** | Sim | A ID do registro a ser excluído, como "99999" |
   ||||

   Por exemplo:

   ![Captura de tela que mostra o designer de aplicativos lógicos com a ação "Excluir linha (visualização)" onde você seleciona uma tabela a ser excluída.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

### <a name="view-delete-row-outputs"></a>Exibir saídas de linhas de exclusão

Para testar manualmente o aplicativo lógico, na barra de ferramentas do designer, escolha **Executar**. Depois que o aplicativo lógico concluir a execução, será possível exibir a saída da execução.

1. No menu do aplicativo lógico, selecione **Visão geral**.

1. Em **Resumo**, no **Histórico de execuções**, selecione a execução mais recente, que é o primeiro item na lista.

1. Em **Execução do aplicativo lógico**, agora é possível examinar o status, entradas e saídas para cada etapa em seu aplicativo lógico.
Expanda a ação **Excluir linha**.

1. Para exibir as entradas, escolha **Mostrar entradas brutas**.

1. Para exibir as saídas, escolha **Mostrar saídas brutas**.

   As saídas não incluem mais o registro excluído de sua tabela especificada.

   ![Exibir a saída sem a linha excluída](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/db2/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

