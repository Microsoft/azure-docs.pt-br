---
title: "Adicionar o conector do DB2 nos Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do conector do DB2 com parâmetros da API REST"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: 1c6b010c-beee-496d-943a-a99e168c99aa
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: 4501b3d9a2fdc00582596cb907f7130591e4782e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-db2-connector"></a>Introdução ao Conector do DB2
O conector da Microsoft para DB2 conecta os Aplicativos Lógicos aos recursos armazenados em um banco de dados DB2 da IBM. Este conector inclui um cliente Microsoft para se comunicar com computadores de servidores DB2 remotos em uma rede TCP/IP. Isso inclui bancos de dados de nuvem, como IBM Bluemix dashDB ou IBM DB2 para Windows em execução na virtualização do Azure e bancos de dados locais usando o gateway de dados local. Veja a [lista](connectors-create-api-db2.md#supported-db2-platforms-and-versions) de plataformas e versões do IBM DB2 com suporte (neste tópico).

O Conector DB2 oferece suporte às seguintes operações do banco de dados:

* Listar tabelas do banco de dados
* Ler uma linha usando SELECT
* Ler todas as linhas usando SELECT
* Adicionar uma linha usando INSERT
* Alterar uma linha usando UPDATE
* Remover uma linha usando DELETE

Este tópico mostra como usar o Conector em um aplicativo lógico para processar as operações do banco de dados.

Para saber mais sobre os Aplicativos Lógicos, consulte como [criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Ações disponíveis
O conector DB2 dá suporte às seguintes ações do aplicativo lógico:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Listar tabelas
A criação de um aplicativo lógico para qualquer operação é composta de várias etapas executadas por meio do portal do Microsoft Azure.

No aplicativo lógico, você pode adicionar uma ação para listar tabelas em um banco de dados DB2. A ação instrui o conector a processar uma instrução de esquema do DB2, como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `Db2getTables`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**.  
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite `db2` na caixa de edição **Procurar mais ações** e selecione **DB2 - Obter tabelas (Visualização)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. No painel de configuração **DB2 - Obter tabelas**, selecione **caixa de seleção** para habilitar **Conectar via gateway de dados local**. Observe que as configurações mudam de nuvem para local.
   
   * Digite o valor para **Servidor**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `ibmserver01:50000`.
   * Digite o valor para o **Banco de Dados**. Por exemplo, digite `nwind`.
   * Selecione o valor para a **Autenticação**. Por exemplo, selecione **Básica**.
   * Digite o valor para o **Nome de Usuário**. Por exemplo, digite `db2admin`.
   * Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
   * Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
7. Selecione **Criar** e, em seguida, **Salvar**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. Na folha **Db2getTables**, na lista **Todas as execuções** em **Resumo**, selecione o primeiro item listado (a execução mais recente).
9. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, clique em **Get_tables**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar as conexões
Este conector dá suporte às conexões com os bancos de dados hospedados no local e na nuvem usando as propriedades de conexão a seguir. 

| Propriedade | Descrição |
| --- | --- |
| Servidor |Obrigatório. Aceita um valor da cadeia de caracteres que representa um endereço TCP/IP ou alias no formato IPv4 ou IPv6, seguido de (delimitado por dois pontos) um número de porta TCP/IP. |
| Banco de Dados |Obrigatório. Aceita um valor da cadeia de caracteres que representa um Nome do Banco de Dados Relacional (RDBNAM) DRDA. O DB2 para z/OS aceita uma cadeia de caracteres de 16 bytes (o banco de dados é conhecido como um IBM DB2 para o local do z/OS). O DB2 para i5/OS aceita uma cadeia de caracteres de 18 bytes (o banco de dados é conhecido como um IBM DB2 para o banco de dados relacional i). O DB2 para LUW aceita uma cadeia de caracteres de 8 bytes. |
| Autenticação |Opcional. Aceita um valor de item de lista, Básica ou Windows (kerberos). |
| Nome de Usuário |Obrigatório. Aceita um valor de cadeia de caracteres. O DB2 para z/OS aceita uma cadeia de caracteres de 8 bytes. O DB2 para i aceita uma cadeia de caracteres de 10 bytes. O DB2 para Linux ou UNIX aceita uma cadeia de caracteres de 8 bytes. O DB2 para Windows aceita uma cadeia de caracteres de 30 bytes. |
| Senha |Obrigatório. Aceita um valor de cadeia de caracteres. |
| Gateway |Obrigatório. Aceita um valor de item da lista, que representa o gateway de dados local definido para os Aplicativos Lógicos no grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar a conexão de gateway local
Este conector pode acessar um banco de dados DB2 local usando o gateway local. Consulte os tópicos do gateway para obter mais informações. 

1. No painel de configuração **Gateways**, marque a **caixa de seleção** para habilitar **Conectar via gateway**. Observe que as configurações mudam de nuvem para local.
2. Digite o valor para **Servidor**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `ibmserver01:50000`.
3. Digite o valor para o **Banco de Dados**. Por exemplo, digite `nwind`.
4. Selecione o valor para a **Autenticação**. Por exemplo, selecione **Básica**.
5. Digite o valor para o **Nome de Usuário**. Por exemplo, digite `db2admin`.
6. Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
7. Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
8. Selecione **Criar** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a conexão de nuvem
Este conector pode acessar um banco de dados DB2 de nuvem. 

1. No painel de configuração **Gateways**, deixe a **caixa de seleção** desabilitada (não clicada) **Conectar via gateway**. 
2. Digite o valor para o **Nome da conexão**. Por exemplo, digite `hisdemo2`.
3. Digite o valor para o **Nome do servidor DB2**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `hisdemo2.cloudapp.net:50000`.
4. Digite o valor para o **Nome do banco de dados DB2**. Por exemplo, digite `nwind`.
5. Digite o valor para o **Nome de Usuário**. Por exemplo, digite `db2admin`.
6. Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
7. Selecione **Criar** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Buscar todas as linhas usando SELECT
Você pode definir uma ação de aplicativo lógico para buscar todas as linhas em uma tabela do DB2. Isso instrui o conector a processar uma instrução SELECT de DB2, como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `Db2getRows`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite `db2` na caixa de edição **Procurar mais ações** e então selecione **DB2 - Obter linhas (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**.
7. No painel de configuração **Conexões**, selecione **Criar novo**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. No painel de configuração **Gateways**, deixe a **caixa de seleção** desabilitada (não clicada) **Conectar via gateway**.
   
   * Digite o valor para o **Nome da conexão**. Por exemplo, digite `HISDEMO2`.
   * Digite o valor para o **Nome do servidor DB2**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `HISDEMO2.cloudapp.net:50000`.
   * Digite o valor para o **Nome do banco de dados DB2**. Por exemplo, digite `NWIND`.
   * Digite o valor para o **Nome de Usuário**. Por exemplo, digite `db2admin`.
   * Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
9. Selecione **Criar** para continuar.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
12. Selecione **Salvar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. Na folha **Db2getRows**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
14. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha usando INSERT
Você pode definir uma ação de aplicativo lógico para adicionar uma linha a uma tabela do DB2. Essa ação instrui o conector a processar uma instrução INSERT do DB2, como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `Db2insertRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **db2** na caixa de edição **Procurar mais ações** e selecione **DB2 - Inserir linha (Visualização)**.
6. Na ação **DB2 - Inserir linha (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configuração **Conexões** , clique para selecionar uma conexão. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, digite `Area 99999` e digite `102` para **REGIONID**. 
10. Selecione **Salvar**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. Na folha **Db2insertRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a nova linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Buscar uma linha usando SELECT
Você pode definir uma ação de aplicativo lógico para buscar uma linha em uma tabela do DB2. Essa ação instrui o conector para processar uma instrução SELECT WHERE do DB2, como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome** (por exemplo, "**Db2getRow**"), **Assinatura**, **Grupo de recursos**,**Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**. 
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **db2** na caixa de edição **Procurar mais ações** e selecione **DB2 - Obter linhas (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
11. Selecione **Salvar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. Na folha **Db2getRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
13. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha usando UPDATE
Você pode definir uma ação de aplicativo lógico para alterar uma linha em uma tabela do DB2. Essa ação instrui o conector a processar uma instrução UPDATE do DB2, como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `Db2updateRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **db2** na caixa de edição **Procurar mais ações** e selecione **DB2 - Atualizar linha (Visualização)**.
6. Na ação **DB2 - Atualizar linha (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, digite `Updated 99999` e digite `102` para **REGIONID**. 
10. Selecione **Salvar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. Na folha **Db2updateRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a nova linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha usando DELETE
Você pode definir uma ação de aplicativo lógico para remover uma linha de uma tabela do DB2. Essa ação instrui o conector a processar uma instrução DELETE do DB2, como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `Db2deleteRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**. 
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, selecione **db2** na caixa de edição **Procurar mais ações** e selecione **DB2 - Excluir linha (Visualização)**.
6. Na ação **DB2 - Excluir linha (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Selecione **Salvar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. Na folha **Db2deleteRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a linha excluída.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Plataformas e versões com suporte do DB2
Este conector dá suporte às plataformas e versões do IBM DB2 a seguir, bem como os produtos IBM DB2 compatíveis (por exemplo, IBM Bluemix dashDB) que oferecem suporte às versões 10 e 11 do SQL Access Manager (SQLAM) da Distributed Relational Database Architecture (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/db2/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

