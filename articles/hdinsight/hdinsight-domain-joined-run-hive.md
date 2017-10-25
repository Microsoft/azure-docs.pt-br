---
title: "Configurar políticas do Hive no HDInsight associado ao domínio – Azure | Microsoft Docs"
description: Saiba como...
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: de537d5e39dd0d3f75ff802948c7372e4d65d127
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight-preview"></a>Configurar políticas do Hive no HDInsight associado ao domínio (Visualização)
Saiba como configurar políticas do Ranger Apache para o Hive. Neste artigo, você criará duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable fornecido com clusters HDInsight. Depois de configurar as políticas, você usa o Excel e o driver ODBC para conectar-se a tabelas do Hive no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster HDInsight associado a um domínio. Confira [Configurar clusters HDInsight associados ao domínio](hdinsight-domain-joined-configure.md).
* Uma estação de trabalho com Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se à interface do usuário de Administração do Apache Ranger
**Para conectar-se à interface do usuário de Administrador do Ranger**

1. Em um navegador, conecte-se à interface do usuário de Administrador do Ranger. A URL é https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > O Ranger usa credenciais diferentes das do cluster Hadoop. Para evitar que os navegadores usem credenciais do Hadoop armazenadas em cache, use a nova janela de navegador inprivate para se conectar à interface do usuário de Administração do Ranger.
   >
   >
2. Faça logon usando o nome de usuário e a senha de domínio de administrador de cluster:

    ![Home page do Ranger associado ao domínio do HDInsight](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Hive e o Yarn.

## <a name="create-domain-users"></a>Criar usuários de Domínio
Em [Configurar clusters HDInsight associados ao domínio](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad), você criou hiveruser1 e hiveuser2. Você usará a conta de usuário dois neste tutorial.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger
Nesta seção, você criará duas políticas do Ranger para acessar hivesampletable. Você pode dar permissão select em um conjunto diferente de colunas. Ambos os usuários foram criados em [Configurar clusters HDInsight associados ao domínio](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).  Na próxima seção, você testará as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abrir a Interface de Usuário de Administração do Ranger. Confira [Conectar-se à interface do usuário de Administração do Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Clique em **&lt;ClusterName >_hive**, em **Hive**. Você deverá ver duas políticas de pré-configuração.
3. Clique em **Adicionar Nova Política** e insira os seguintes valores:

   * Nome da política: read-hivesampletable-all
   * Hive de Banco de Dados: padrão
   * tabela: hivesampletable
   * Coluna de hive: *
   * Selecione o usuário: hiveuser1
   * Permissões: selecionar

     ![Configurar política do Hive do Ranger associada ao domínio do HDInsight](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Se um usuário de domínio não estiver populado em Selecionar Usuário, aguarde alguns instantes para que o Ranger seja sincronizado com o AAD.
     >
     >
4. Clique em **Adicionar** para salvar a política.
5. Repita as duas últimas etapas para criar outra política com as seguintes propriedades:

   * Nome da política: read-hivesampletable-devicemake
   * Hive de Banco de Dados: padrão
   * tabela: hivesampletable
   * Coluna do hive: clientid, devicemake
   * Selecione o usuário: hiveuser2
   * Permissões: selecionar

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive
As instruções podem ser encontradas em [Criar fonte de dados ODBC do Hive](hdinsight-connect-excel-hive-odbc-driver.md).  

    Propriedade|Descrição
    ---|---
    Nome da fonte de dados|Forneça um nome para a sua fonte de dados
    Host|Digite &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, meu_Cluster_HDI.azurehdinsight.net
    Port|Use <strong>443</strong>. (Essa porta foi alterada de 563 para 443.)
    Banco de dados|Use <strong>Padrão</strong>.
    Tipo de servidor Hive|Selecione <strong>Servidor Hive 2</strong>
    Mecanismo|Selecione <strong>Serviço do Azure HDInsight</strong>
    Caminho HTTP|Deixe em branco.
    Nome de usuário|Digite hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se ele for diferente.
    Senha|Digite a senha para hiveuser1.
    </table>

Clique em **Testar** antes de salvar a fonte de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel
Na última seção, você configurou duas políticas.  hiveuser1 tem a permissão select em todas as colunas e hiveuser2 tem a permissão select em duas colunas. Nesta seção, você representa os dois usuários para importar dados para o Excel.

1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **Dados**, clique em **De Outras Fontes de Dados** e clique em **Do Assistente de Conexão de Dados** para iniciar o **Assistente de Conexão de Dados**.

    ![Assistente de conexão de dados][img-hdi-simbahiveodbc.excel.dataconnection]
3. Selecione **ODBC DSN** como a fonte de dados e clique em **Avançar**.
4. Em Fontes de dados ODBC, selecione o nome da fonte de dados criada na etapa anterior e clique em **Avançar**.
5. Digite novamente a senha para o cluster no assistente e clique em **OK**. Aguarde até que a caixa de diálogo **Selecionar Banco de Dados e Tabela** seja aberta. Isso pode levar alguns segundos.
6. Selecione **hivesampletable** e clique em **Avançar**.
7. Clique em **Concluir**.
8. No diálogo **Importar Dados** , você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos.
9. Clique na guia **Definição**. O texto do comando é:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   De acordo com as políticas do Ranger definidas por você, hiveuser1 tem permissão select em todas as colunas.  Então, essa consulta funciona com as credenciais de hiveuser1, mas não funciona com as credenciais de hiveuser2.

   ![Propriedades de conexão][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Clique em **OK** para fechar o diálogo Propriedades da Conexão.
11. Clique em **OK** para fechar a caixa de diálogo **Importar Dados**.  
12. Digite novamente a senha para hiveuser1 e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel. Quando estiver pronto, você deverá ver 11 colunas de dados.

Para testar a segunda política (read-hivesampletable-devicemake) que você criou na seção anterior

1. Adicione uma nova planilha no Excel.
2. Siga o último procedimento para importar os dados.  A única alteração que você fará é usar as credenciais de hiveuser2 em vez de hiveuser1. Isso falhará porque hiveuser2 só tem permissão para ver duas colunas. Você deverá receber o seguinte erro:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Siga o mesmo procedimento para importar dados. Desta vez, use as credenciais de hiveuser2 e também modifique a instrução select from:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    para:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Quando estiver pronto, você deverá ver duas colunas de dados importados.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight associado a um domínio, confira [Configurar clusters HDInsight associados a domínio](hdinsight-domain-joined-configure.md).
* Para gerenciar um cluster HDInsight associado a um domínio, confira [Gerenciar clusters HDInsight associados a domínio](hdinsight-domain-joined-manage.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Para conectar o Hive usando o JDBC Hive, confira [Conectar ao Hive no Azure HDInsight usando o driver JDBC do Hive](hdinsight-connect-hive-jdbc-driver.md)
* Para conectar o Excel ao Hadoop usando o ODBC do Hive, confira [Conectar o Excel ao Hadoop com a unidade ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md)
* Para conectar o Excel ao Hadoop usando o Power Query, confira [Conectar o Excel ao Hadoop usando o Power Query](hdinsight-connect-excel-power-query.md)
