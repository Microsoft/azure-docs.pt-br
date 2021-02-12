---
title: Políticas de Apache Hive no Apache Ranger – Azure HDInsight
description: Aprenda a configurar as políticas do Apache Ranger para o Hive no serviço do Azure HDInsight com o Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 8ebc03d0847414730c51b899be4cf6586d064696
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932232"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurar políticas do Apache Hive no HDInsight com o Enterprise Security Package

Aprenda a configurar as políticas do Apache Ranger para o Apache Hive. Neste artigo, você criará duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable fornecido com clusters HDInsight. Depois de configurar as políticas, use o Excel e o driver ODBC para se conectar a tabelas Hive no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster HDInsight com Enterprise Security Package. Confira [Configurar clusters do HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Uma estação de trabalho com aplicativos Microsoft 365 para Enterprise, Office 2016, Office 2013 Professional Plus, Excel 2013 standalone ou Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se à interface do usuário de Administração do Apache Ranger
**Para conectar-se à interface do usuário de Administrador do Ranger**

1. Em um navegador, navegue até a interface do usuário do administrador do Ranger em `https://CLUSTERNAME.azurehdinsight.net/Ranger/` onde ClusterName é o nome do cluster.

   > [!NOTE]  
   > O Ranger usa credenciais diferentes das utilizadas pelo cluster Apache Hadoop. Para evitar que os navegadores usem credenciais do Hadoop armazenadas em cache, use a nova janela de navegador InPrivate para se conectar à interface do usuário de Administração do Ranger.

2. Faça logon usando o nome de usuário e a senha de domínio de administrador de cluster:

    ![Home page do HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Hive e o Yarn.

## <a name="create-domain-users"></a>Criar usuários de Domínio

Confira [Criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp), para obter informações sobre como criar hiveruser1 e hiveuser2. Você usa as duas contas de usuário neste artigo.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Nesta seção, você criará duas políticas do Ranger para acessar hivesampletable. Você pode dar permissão select em um conjunto diferente de colunas. Ambos os usuários foram criados em [Criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Na próxima seção, você testará as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abrir a Interface de Usuário de Administração do Ranger. Confira Conectar-se à interface do usuário de Administração do Apache Ranger.
2. Selecione **CLUSTERNAME_Hive**, em **Hive**. Você deverá ver duas políticas de pré-configuração.
3. Selecione **Adicionar nova política** e, em seguida, insira os seguintes valores:

    |Propriedade |Valor |
    |---|---|
    |Nome da política|Read-hivesampletable-All|
    |Banco de dados do hive|padrão|
    |tabela|hivesampletable|
    |Coluna do hive|*|
    |Selecionar usuário|hiveuser1|
    |Permissões|select|

    ![Configuração de políticas de hive do HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Se um usuário de domínio não estiver populado em Selecionar Usuário, aguarde alguns instantes para que o Ranger seja sincronizado com o AAD.

4. Selecione **Adicionar** para salvar a política.

5. Repita as duas últimas etapas para criar outra política com as seguintes propriedades:

    |Propriedade |Valor |
    |---|---|
    |Nome da política|Leia-hivesampletable-devicemake|
    |Banco de dados do hive|padrão|
    |tabela|hivesampletable|
    |Coluna do hive|ClientID, devicemake|
    |Selecionar usuário|hiveuser2|
    |Permissões|select|

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive

As instruções podem ser encontradas em [Criar fonte de dados ODBC do Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriedade  |Descrição |
 | --- | --- |
 | Nome da Fonte de Dados | Forneça um nome para a sua fonte de dados |
 | Host | Insira CLUSTERNAME.azurehdinsight.net. Por exemplo, meu_Cluster_HDI.azurehdinsight.net |
 | Porta | Use **443**. (Essa porta foi alterada de 563 para 443.) |
 | Banco de dados | Usar **padrão**. |
 | Tipo de servidor Hive | Selecione **Servidor Hive 2** |
 | Mecanismo | Selecione **Serviço do Azure HDInsight** |
 | Caminho HTTP | Deixe em branco. |
 | Nome do Usuário | Insira hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se ele for diferente. |
 | Senha | Digite a senha para hiveuser1. |

Clique em **Testar** antes de salvar a fonte de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel

Na última seção, você configurou duas políticas.  hiveuser1 tem a permissão select em todas as colunas e hiveuser2 tem a permissão select em duas colunas. Nesta seção, você representa os dois usuários para importar dados para o Excel.

1. Abra uma pasta de trabalho nova ou existente no Excel.

1. Na guia **Dados**, navegue até **Obter Dados** > **De Outras Fontes** > **Do ODBC** para abrir a janela **Do ODBC**.

    ![Abrir o assistente de conexão de dados](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Na lista suspensa, selecione o nome da fonte de dados que você criou na última seção e, em seguida, selecione **OK**.

1. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **Windows** no menu à esquerda. Em seguida, selecione **conectar** para abrir a janela **navegador** .

1. Aguarde até que a caixa de diálogo **Selecionar Banco de Dados e Tabela** seja aberta. Isso pode levar alguns segundos.

1. Selecione **hivesampletable** e, em seguida, selecione **Avançar**.

1. Selecione **Concluir**.

1. No diálogo **Importar Dados** , você pode alterar ou especificar a consulta. Para fazer isso, selecione **Propriedades**. Isso pode levar alguns segundos.

1. Selecione a guia **definição** . O texto do comando é:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   De acordo com as políticas do Ranger definidas por você, hiveuser1 tem permissão select em todas as colunas.  Portanto, essa consulta funciona com as credenciais do hiveuser1, mas essa consulta não funciona com as credenciais do hiveuser2.

1. Selecione **OK** para fechar a caixa de diálogo Propriedades da conexão.

1. Selecione **OK** para fechar a caixa de diálogo **importar dados** .  

1. Digite novamente a senha para hiveuser1 e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel. Quando terminar, você deverá ver 11 colunas de dados.

Para testar a segunda política (read-hivesampletable-devicemake) que você criou na seção anterior

1. Adicione uma nova planilha no Excel.
2. Siga o último procedimento para importar os dados.  A única alteração que você fará é usar as credenciais de hiveuser2 em vez de hiveuser1. Isso falhará porque hiveuser2 só tem permissão para ver duas colunas. Você deverá receber o seguinte erro:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Siga o mesmo procedimento para importar dados. Desta vez, use as credenciais de hiveuser2 e também modifique a instrução select from:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    para:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Quando terminar, você deverá ver duas colunas de dados importadas.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um cluster HDInsight com Enterprise Security Package, confira [Configurar clusters HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Para gerenciar um cluster HDInsight com ESP, confira [Gerenciar clusters HDInsight com ESP](apache-domain-joined-manage.md).
* Para executar consultas Hive usando SSH em clusters HDInsight com ESP, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Para conectar o Hive usando o JDBC do Hive, confira [Conectar-se ao Apache Hive no Azure HDInsight usando o driver JDBC do Hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para conectar o Excel ao Hadoop usando o ODBC do Hive, confira [Conectar o Excel ao Apache Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para conectar o Excel ao Hadoop usando o Power Query, confira [Conectar o Excel ao Apache Hadoop usando o Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)