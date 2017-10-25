---
title: "Adicionar o conector do Banco de Dados Oracle em seus Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Usar o conector do Banco de Dados Oracle em um aplicativo lógico"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: cc64441617eb5e7d5e70c1cf5c491a672428bc51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introdução ao conector do Banco de Dados Oracle

Com o conector do Banco de Dados Oracle, você cria fluxos de trabalho organizacionais que usam os dados em seu banco de dados existente. Esse conector pode se conectar ao Banco de Dados Oracle local, ou a uma máquina virtual do Azure com o Banco de Dados Oracle instalado. Com esse conector, você pode:

* Compile o fluxo de trabalho adicionando um novo cliente a um banco de dados de clientes ou atualizando um pedido em um banco de dados de pedidos.
* Use as ações para obter uma linha de dados, inserir uma nova linha e até mesmo excluir. Por exemplo, quando um registro é criado no Dynamics CRM Online (um gatilho), insira uma linha em um Banco de Dados Oracle (uma ação). 

Este tópico mostra como usar o conector do Banco de Dados Oracle um aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Versões do Oracle com suporte: 
    * Oracle 9 e versões posteriores
    * Software cliente do Oracle 8.1.7 e posteriores

* Instalar o gateway de dados local. [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md) lista as etapas. O gateway é necessário para se conectar ao Banco de Dados Oracle local, ou uma VM do Azure com o Banco de Dados Oracle instalado. 

    > [!NOTE]
    > O gateway de dados local atua como uma ponte e fornece transferência de dados segura entre dados locais (dados que não estão na nuvem) e seu aplicativo lógico. O mesmo gateway pode ser usado com vários serviços e várias fontes de dados. Assim, você só precisará instalar o gateway uma vez.

* Instale o Cliente Oracle no computador onde você instalou o gateway de dados local. Instale o Provedor de Dados do Oracle de 64 bits para .NET a partir do Oracle:  

  [ODAC 12c Release 4 (12.1.0.2.4) de 64 bits para Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorrerá um erro ao tentar criar ou usar a conexão. Consulte os erros comuns neste tópico.


## <a name="add-the-connector"></a>Adicione o conector

> [!IMPORTANT]
> Esse conector não tem gatilhos. Ele tem somente ações. Então, quando você criar seu aplicativo lógico, adicione outro gatilho para iniciar seu aplicativo lógico, como **Agenda - Recorrência** ou **Solicitação / Resposta - Resposta**. 

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco.

2. No início de seu aplicativo lógico, selecione o gatilho **Solicitação / Resposta - Solicitação**: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecione **Salvar**. Quando você salva, a URL de uma solicitação é gerada automaticamente. 

4. Selecione **Nova etapa** e selecione **Adicionar uma ação**. Digite `oracle` para ver as ações disponíveis: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Essa também é a maneira mais rápida de ver os gatilhos e ações disponíveis para qualquer conector. Digite parte do nome do conector, como `oracle`. O designer lista todos os gatilhos e ações. 

5. Selecione uma das ações, como **Banco de Dados Oracle - Obter linhas**. Selecione **Conectar por meio do gateway de dados local**. Insira o nome do servidor Oracle, o método de autenticação, o nome de usuário, a senha e selecione o gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Após a conexão, selecione uma tabela na lista e insira a ID da linha à tabela. Você precisa saber o identificador da tabela. Se você não souber, contate o administrador do banco de dados Oracle e obtenha a saída de `select * from yourTableName`. Isso lhe dará as informações de identificação necessárias para continuar.

    No exemplo a seguir, os dados do trabalho retornam de um banco de dados de Recursos Humanos: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Nesta próxima etapa, use qualquer um dos outros conectores para compilar seu fluxo de trabalho. Se você quiser testar a obtenção de dados do Oracle, envie um email com os dados do Oracle usando um dos conectores de envio de email, como o Office 365 ou o Gmail. Use os tokens dinâmicos da tabela do Oracle para criar o `Subject` e o `Body` de seu email:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Salve** seu aplicativo lógico e selecione **Executar**. Feche o designer e examine o histórico de execuções do status. Se ele falhar, selecione a linha da mensagem com falha. O designer abre e mostra qual etapa falhou, e também mostra as informações do erro. Se for bem-sucedido, você receberá um e-mail com as informações que adicionou.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Você deseja monitorar a hashtag #oracle e colocar os tweets em um banco de dados para que possam ser consultados e usados em outros aplicativos. Em um aplicativo lógico, adicione o gatilho `Twitter - When a new tweet is posted` e insira a hashtag **#oracle**. Em seguida, adicione a ação `Oracle Database - Insert row` e selecione sua tabela:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas a uma fila do Barramento de Serviço. Obtenha essas mensagens e coloque-as em um banco de dados. Em um aplicativo lógico, adicione o gatilho `Service Bus - when a message is received in a queue` e selecione a fila. Em seguida, adicione a ação `Oracle Database - Insert row` e selecione sua tabela:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro**: não é possível acessar o Gateway

**Causa**: o gateway de dados local não é capaz de se conectar à nuvem. 

**Atenuação**: verifique se o gateway está em execução no computador local onde ele foi instalado e se ele pode se conectar à internet.  Recomendamos a não instalação do gateway em um computador que pode ser desativado ou suspenso. Você também pode reiniciar o serviço de gateway de dados local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro**: o provedor que está sendo usado é preterido: 'O System.Data.OracleClient exige o software cliente da Oracle versão 8.1.7 ou posterior.'. Visite [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) para instalar o provedor oficial.

**Causa**: o SDK do cliente da Oracle não está instalado no computador onde o gateway de dados local está em execução.  

**Resolução**: baixe e instale o SDK do cliente da Oracle no mesmo computador que o gateway de dados local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro**: a tabela '[Nome_da_tabela]' não define colunas de chave

**Causa**: a tabela não tem uma chave primária.  

**Resolução**: o conector do Banco de Dados Oracle exige o uso de uma tabela com uma coluna de chave primária.

#### <a name="currently-not-supported"></a>Não há suporte no momento

* Exibições e procedimentos armazenados 
* Qualquer tabela com chaves compostas
* Tipos de objeto aninhados em tabelas
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obtenha ajuda

O [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) é um ótimo lugar para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos estão fazendo. 

Você pode ajudar a melhorar os Aplicativos Lógicos e os conectores vitando e enviando suas ideias em [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) e explore os conectores disponíveis nos Aplicativos Lógicos em nossa [Lista de APIs](apis-list.md).
