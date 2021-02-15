---
title: Conectar-se a um Oracle Database
description: Inserir e gerenciar registros com APIs REST do Oracle Database e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674810"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introdução ao conector do Banco de Dados Oracle

Com o conector do Banco de Dados Oracle, você cria fluxos de trabalho organizacionais que usam os dados em seu banco de dados existente. Esse conector pode se conectar ao Banco de Dados Oracle local, ou a uma máquina virtual do Azure com o Banco de Dados Oracle instalado. Com esse conector, você pode:

* Compile o fluxo de trabalho adicionando um novo cliente a um banco de dados de clientes ou atualizando um pedido em um banco de dados de pedidos.
* Use as ações para obter uma linha de dados, inserir uma nova linha e até mesmo excluir. Por exemplo, quando um registro é criado no Dynamics CRM Online (um gatilho), insira uma linha em um Banco de Dados Oracle (uma ação). 

Este conector não é compatível com os seguintes itens:

* Exibições 
* Qualquer tabela com chaves compostas
* Tipos de objeto aninhados em tabelas
* Funções de banco de dados com valores não escalares

Este artigo mostra como usar o conector do Banco de Dados Oracle um aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Versões do Oracle com suporte: 
    * Oracle 9 e versões posteriores
    * Software cliente do Oracle 8.1.7 e posteriores

* Instalar o gateway de dados local. [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md) lista as etapas. O gateway é necessário para se conectar ao Banco de Dados Oracle local, ou uma VM do Azure com o Banco de Dados Oracle instalado. 

    > [!NOTE]
    > O gateway de dados local atua como uma ponte e fornece transferência de dados segura entre dados locais (dados que não estão na nuvem) e seu aplicativo lógico. O mesmo gateway pode ser usado com vários serviços e várias fontes de dados.  Assim, você só precisará instalar o gateway uma vez.

* Instale o Cliente Oracle no computador onde você instalou o gateway de dados local.  Instale o Provedor de Dados do Oracle de 64 bits para .NET a partir do Oracle:  

  [ODAC 12c Release 4 (12.1.0.2.4) de 64 bits para Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se o cliente Oracle não estiver instalado, ocorrerá um erro ao tentar criar ou usar a conexão. Consulte os erros comuns neste artigo.


## <a name="add-the-connector"></a>Adicione o conector

> [!IMPORTANT]
> Esse conector não tem gatilhos. Ele tem somente ações. Então, quando você criar seu aplicativo lógico, adicione outro gatilho para iniciar seu aplicativo lógico, como **Agenda - Recorrência** ou **Solicitação / Resposta - Resposta** . 

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco.

2. No início de seu aplicativo lógico, selecione o gatilho **Solicitação / Resposta - Solicitação** : 

    ![Uma caixa de diálogo tem uma caixa para pesquisar todos os gatilhos. Há também um único gatilho mostrado, "solicitação/resposta-solicitação", com um botão de seleção.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Clique em **Salvar** . Quando você salva, a URL de uma solicitação é gerada automaticamente. 

4. Selecione **Nova etapa** e selecione **Adicionar uma ação** . Digite `oracle` para ver as ações disponíveis: 

    ![Uma caixa de pesquisa contém "Oracle". A pesquisa produz um clique rotulada como "Oracle Database". Há uma página com guias, uma guia mostrando "TRIGGERs (0)", outra mostrando "Actions (6)". Seis ações são listadas. O primeiro deles é "obter a visualização de linha".](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Essa também é a maneira mais rápida de ver os gatilhos e ações disponíveis para qualquer conector. Digite parte do nome do conector, como `oracle`. O designer lista todos os gatilhos e ações. 

5. Selecione uma das ações, como **Banco de Dados Oracle - Obter linhas** . Selecione **Conectar por meio do gateway de dados local** . Insira o nome do servidor Oracle, o método de autenticação, o nome de usuário, a senha e selecione o gateway:

    ![A caixa de diálogo é intitulada "Oracle Database-obter linha". Há uma caixa, marcada, rotulada "conectar via gateway de dados local". Abaixo, estão as cinco outras caixas de texto.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Após a conexão, selecione uma tabela na lista e insira a ID da linha à tabela. Você precisa saber o identificador da tabela. Se você não souber, contate o administrador do banco de dados Oracle e obtenha a saída de `select * from yourTableName`. Isso lhe dará as informações de identificação necessárias para continuar.

    No exemplo a seguir, os dados do trabalho retornam de um banco de dados de Recursos Humanos: 

    ![A caixa de diálogo intitulada "obter linha (visualização)" tem duas caixas de texto: "nome da tabela", que contém "trabalhos de R H" e tem uma lista suspensa e "linha i d", que contém "S A _ REP".](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Nesta próxima etapa, use qualquer um dos outros conectores para compilar seu fluxo de trabalho. Se você quiser testar a obtenção de dados do Oracle, envie um email com os dados da Oracle usando um dos conectores de envio de email, como o Outlook do Office 365. Use os tokens dinâmicos da tabela do Oracle para criar o `Subject` e o `Body` de seu email:

    ![Há duas caixas de diálogo. A caixa "enviar um email" tem caixas para especificar o endereço "corpo", "assunto" e "para" do email. A caixa de diálogo "adicionar conteúdo dinâmico" fornece uma pesquisa de conteúdo dinâmico dos aplicativos e serviços do fluxo.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Salve** seu aplicativo lógico e selecione **Executar** . Feche o designer e examine o histórico de execuções do status. Se ele falhar, selecione a linha da mensagem com falha. O designer abre e mostra qual etapa falhou, e também mostra as informações do erro. Se for bem-sucedido, você receberá um e-mail com as informações que adicionou.


### <a name="workflow-ideas"></a>Ideias de fluxo de trabalho

* Você deseja monitorar a hashtag #oracle e colocar os tweets em um banco de dados para que possam ser consultados e usados em outros aplicativos. Em um aplicativo lógico, adicione o gatilho `Twitter - When a new tweet is posted` e insira a hashtag **#oracle** . Em seguida, adicione a ação `Oracle Database - Insert row` e selecione sua tabela:

    ![A caixa de diálogo "quando um novo tweet é Postado" mostra "hashtag Oracle" como texto de pesquisa e permite que você especifique a frequência de verificação. Essa caixa de diálogo leva à caixa de diálogo "Oracle Database" que permite que você selecione a ação.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* As mensagens são enviadas a uma fila do Barramento de Serviço. Obtenha essas mensagens e coloque-as em um banco de dados. Em um aplicativo lógico, adicione o gatilho `Service Bus - when a message is received in a queue` e selecione a fila. Em seguida, adicione a ação `Oracle Database - Insert row` e selecione sua tabela:

    ![O "quando uma mensagem é recebida..." a caixa de diálogo mostra "Orders" como o "nome da fila" e permite que você especifique a frequência de verificação. Essa caixa leva à caixa de diálogo "Inserir linha (visualização)" que permite selecionar "nome da tabela".](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Erros comuns

#### <a name="error-cannot-reach-the-gateway"></a>**Erro** : Não é possível acessar o Gateway

**Causa** : o gateway de dados local não é capaz de se conectar à nuvem. 

**Atenuação** : verifique se o gateway está em execução no computador local em que ele foi instalado e se ele pode se conectar à Internet.    Recomendamos a não instalação do gateway em um computador que pode ser desativado ou suspenso.  Você também pode reiniciar o serviço de gateway de dados local (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Erro** : o provedor que está sendo usado é preterido: 'System.Data.OracleClient exige software cliente Oracle versão 8.1.7 ou posterior.'. Consulte [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) para instalar o provedor oficial.

**Causa** : o SDK do cliente da Oracle não está instalado no computador em que o gateway de dados local está em execução.  

**Resolução:** baixe e instale o SDK do cliente da Oracle no mesmo computador que o gateway de dados local.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Erro** : a tabela '[Nome_da_tabela]' não define colunas de chave

**Causa** : a tabela não tem uma chave primária.  

**Resolução:** o conector do Oracle Database exige o uso de uma tabela com uma coluna de chave primária.
 
## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/oracle/). 

## <a name="get-some-help"></a>Obtenha ajuda

A [página de perguntas e respostas da Microsoft sobre Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html) é um ótimo lugar para fazer perguntas, responder a dúvidas e saber o que os outros usuários dos Aplicativos Lógicos estão fazendo. 

Você pode ajudar a melhorar os Aplicativos Lógicos e os conectores vitando e enviando suas ideias em [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) e explore os conectores disponíveis nos Aplicativos Lógicos em nossa [Lista de APIs](apis-list.md).