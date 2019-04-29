---
title: Conectar ao banco de dados IBM Informix – Aplicativos Lógicos do Azure | Microsoft Docs
description: Gerenciar recursos com as APIs REST do IBM Informix e os Aplicativos Lógicos do Azure
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690740"
---
# <a name="get-started-with-the-informix-connector"></a>Introdução ao conector do Informix
O Microsoft Connector para Informix conecta os Aplicativos Lógicos aos recursos armazenados em um banco de dados IBM Informix. O conector Informix inclui um cliente Microsoft para se comunicar com computadores de servidores Informix remotos em uma rede TCP/IP. Isso inclui bancos de dados de nuvem, como o IBM Informix para Windows em execução na virtualização do Azure e bancos de dados locais usando o gateway de dados local. Veja a [lista](connectors-create-api-informix.md#supported-informix-platforms-and-versions) de plataformas e versões do IBM Informix com suporte (neste tópico).

O conector oferece suporte às seguintes operações de banco de dados:

* Listar tabelas do banco de dados
* Ler uma linha usando SELECT
* Ler todas as linhas usando SELECT
* Adicionar uma linha usando INSERT
* Alterar uma linha usando UPDATE
* Remover uma linha usando DELETE

Este tópico mostra como usar o Conector em um aplicativo lógico para processar as operações do banco de dados.

Para saber mais sobre os Aplicativos Lógicos, consulte como [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Ações disponíveis
Este conector dá suporte às seguintes ações do aplicativo lógico:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Listar tabelas
A criação de um aplicativo lógico para qualquer operação é composta de várias etapas executadas por meio do portal do Microsoft Azure.

No aplicativo lógico, você pode adicionar uma ação para listar tabelas em um banco de dados Informix. Essa ação instrui o conector a processar uma instrução de esquema do Informix, como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `InformixgetTables`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**.  
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Obter tabelas (Visualização)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. No painel de configuração **Informix - Obter tabelas**, marque **caixa de seleção** para habilitar **Conectar via gateway de dados local**. Observe que as configurações mudam de nuvem para local.
   
   * Digite o valor para **Servidor**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `ibmserver01:9089`.
   * Digite o valor para o **Banco de Dados**. Por exemplo, digite `nwind`.
   * Selecione o valor para a **Autenticação**. Por exemplo, selecione **Básica**.
   * Digite o valor para o **Nome de Usuário**. Por exemplo, digite `informix`.
   * Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
   * Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
7. Selecione **Criar** e, em seguida, **Salvar**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Na folha **InformixgetTables**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
9. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, clique em **Get_tables**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar as conexões
Este conector dá suporte às conexões com os bancos de dados no local e na nuvem usando as propriedades de conexão a seguir. 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Servidor |Obrigatório. Aceita um valor da cadeia de caracteres que representa um endereço TCP/IP ou alias no formato IPv4 ou IPv6, seguido de (delimitado por dois pontos) um número de porta TCP/IP. |
| Banco de Dados |Obrigatório. Aceita um valor da cadeia de caracteres que representa um Nome do Banco de Dados Relacional (RDBNAM) DRDA. O Informix aceita uma cadeia de caracteres de 128 bytes (o banco de dados é conhecido como um nome de banco de dados IBM Informix (dbname)). |
| Autenticação |Opcional. Aceita um valor de item de lista, Básica ou Windows (kerberos). |
| Nome de Usuário |Obrigatório. Aceita um valor de cadeia de caracteres. |
| Senha |Obrigatório. Aceita um valor de cadeia de caracteres. |
| Gateway |Obrigatório. Aceita um valor de item da lista, que representa o gateway de dados local definido para os Aplicativos Lógicos no grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar a conexão de gateway local
Este conector pode acessar um banco de dados do Informix local por meio do gateway de dados local. Consulte os tópicos do gateway para obter mais informações. 

1. No painel de configuração **Gateways**, marque a **caixa de seleção** para habilitar **Conectar via gateway**. Veja as configurações mudarem de nuvem para local.
2. Digite o valor para **Servidor**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `ibmserver01:9089`.
3. Digite o valor para o **Banco de Dados**. Por exemplo, digite `nwind`.
4. Selecione o valor para a **Autenticação**. Por exemplo, selecione **Básica**.
5. Digite o valor para o **Nome de Usuário**. Por exemplo, digite `informix`.
6. Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
7. Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
8. Selecione **Criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a conexão de nuvem
Este conector pode acessar uma nuvem de banco de dados Informix. 

1. No painel de configuração **Gateways**, deixe a **caixa de seleção** desabilitada (não clicada) **Conectar via gateway**. 
2. Digite o valor para o **Nome da conexão**. Por exemplo, digite `hisdemo2`.
3. Digite o valor para o **Nome do servidor Informix**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `hisdemo2.cloudapp.net:9089`.
4. Digite o valor para **nome do banco de dados Informix**. Por exemplo, digite `nwind`.
5. Digite o valor para o **Nome de Usuário**. Por exemplo, digite `informix`.
6. Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
7. Selecione **Criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Buscar todas as linhas usando SELECT
Você pode criar uma ação de aplicativo lógico para buscar todas as linhas na tabela do Informix. Essa ação instrui o conector a processar uma instrução SELECT do Informix, como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome** (por exemplo, "**InformixgetRows**"), **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Obter linhas (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**.
7. No painel de configuração **Conexões**, selecione **Criar novo**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. No painel de configuração **Gateways**, deixe a **caixa de seleção** desabilitada (não clicada) **Conectar via gateway**.
   
   * Digite o valor para o **Nome da conexão**. Por exemplo, digite `HISDEMO2`.
   * Digite o valor para o **Nome do servidor Informix**, na forma de número da porta com dois pontos para o endereço ou alias. Por exemplo, digite `HISDEMO2.cloudapp.net:9089`.
   * Digite o valor para **nome do banco de dados Informix**. Por exemplo, digite `NWIND`.
   * Digite o valor para o **Nome de Usuário**. Por exemplo, digite `informix`.
   * Digite o valor para a **Senha**. Por exemplo, digite `Password1`.
9. Selecione **Criar** para continuar.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
12. Clique em **Salvar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Na folha **InformixgetRows**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
14. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha usando INSERT
Você pode criar uma ação de aplicativo lógico para adicionar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução INSERT do Informix, como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `InformixinsertRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Inserir linha (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configuração **Conexões** , selecione uma conexão. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, digite `Area 99999` e digite `102` para **REGIONID**. 
10. Clique em **Salvar**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Na folha **InformixinsertRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Buscar uma linha usando SELECT
Você pode criar uma ação de aplicativo lógico para buscar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução SELECT WHERE do Informix, como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `InformixgetRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Obter linhas (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
11. Clique em **Salvar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Na folha **InformixgetRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
13. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha usando UPDATE
Você pode criar uma ação de aplicativo lógico para alterar uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução UPDATE do Informix, como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `InformixupdateRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Atualizar linha (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, digite `Updated 99999` e digite `102` para **REGIONID**. 
10. Clique em **Salvar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Na folha **InformixupdateRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha usando DELETE
Você pode criar uma ação de aplicativo lógico para remover uma linha em uma tabela do Informix. Essa ação instrui o conector a processar uma instrução DELETE do Informix, como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico
1. Na **tela inicial do Azure**, selecione **+** (sinal de mais), **Web + Móvel**, e então, **Aplicativo Lógico**.
2. Insira o **Nome**, como `InformixdeleteRow`, **Assinatura**, **Grupo de recursos**, **Local** e **Plano do Serviço de Aplicativo**. Escolha **Fixar no painel** e selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um gatilho e uma ação
1. No **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** na lista **Modelos**.
2. Na lista de **gatilhos**, selecione **Recorrência**. 
3. No gatilho **Recorrência**, clique em **Editar**, escolha a lista suspensa **Frequência** para selecionar **Dia** e defina o **Intervalo** como **7**. 
4. Selecione a caixa **+ Nova etapa** e escolha **Adicionar uma ação**.
5. Na lista de **ações**, digite **informix** na caixa de edição **Procurar mais ações** e selecione **Informix - Excluir linha (Visualização)**.
6. Na ação **Obter linhas (Visualização)**, selecione **Alterar conexão**. 
7. No painel de configurações de **Conexões** , selecione uma conexão existente. Por exemplo, selecione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Na lista **Nome da tabela**, selecione a **seta para baixo** e selecione **AREA**.
9. Insira valores para todas as colunas necessárias (confira o asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Clique em **Salvar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Na folha **InformixdeleteRow**, na lista **Todas as execuções** em **Resumo**, selecione o item listado primeiro (a execução mais recente).
12. Na folha **Execução do aplicativo lógico**, selecione **Detalhes da Execução**. Na lista **Ação**, selecione **Get_rows**. Confira o valor de **Status**, que deve ser **Êxito**. Selecione o **link Entradas** para exibir as entradas. Selecione o **link Saídas**e exiba as saídas, que deverão incluir a linha excluída.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Versões e plataformas Informix com suporte
Este conector dá suporte às versões do IBM Informix à seguir quando configurado para dar suporte a conexões a conexões de cliente DRDA (Distributed Relational Database Architecture).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/informix/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

