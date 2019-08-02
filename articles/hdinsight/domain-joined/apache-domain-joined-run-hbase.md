---
title: Tutorial - Configurar políticas do Apache HBase no HDInsight com o Enterprise Security Package - Azure
description: Tutorial - Aprenda a configurar as políticas do Apache Ranger para HBase no Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 04592ba307cd696c20778d4a79f03be2eb0ac987
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274401"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Tutorial: Configurar políticas do Apache HBase no HDInsight com o Enterprise Security Package

Saiba como configurar políticas do Apache Ranger para clusters Apache HBase do ESP (Enterprise Security Package). Os clusters ESP estão conectados a um domínio, permitindo que os usuários se autentiquem com credenciais de domínio. Neste tutorial, você cria duas políticas do Ranger para restringir o acesso a famílias de colunas diferentes em uma tabela do HBase.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie usuários de domínio
> * Criar políticas do Ranger
> * Criar tabelas em um cluster HBase
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Entre no [Portal do Azure](https://portal.azure.com/).

* Crie um [cluster HBase do HDInsight com o Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se à interface do usuário de Administração do Apache Ranger

1. Em um navegador, conecte-se à interface de usuário do Ranger Admin usando o URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Lembre-se de alterar `<ClusterName>` para o nome do seu cluster HBase.

    > [!NOTE]  
    > Credenciais do Ranger não são as mesmas credenciais do cluster do Hadoop. Para impedir que os navegadores usem credenciais do Hadoop em cache, use uma nova janela do navegador InPrivate para se conectar à interface de administração do Ranger Admin.

2. Entre usando suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não são iguais às credenciais de cluster do HDInsight nem às credenciais SSH do nó do Linux HDInsight.

## <a name="create-domain-users"></a>Crie usuários de domínio

Visite [Criar um cluster do HDInsight com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) para saber como criar os usuários de domínio **sales_user1** e **marketing_user1**. Em um cenário de produção, os usuários do domínio vêm do seu locatário do Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Criar tabelas do HBase e importar dados de exemplo

Você pode usar o SSH para se conectar a clusters do HBase e, em seguida, usar o [Shell do Apache HBase](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas do HBase, inserir dados e consultar dados. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Para usar o shell do HBase

1. No SSH, execute este comando HBase:
   
    ```bash
    hbase shell
    ```

2. Crie uma tabela do HBase `Customers` com famílias de duas colunas: `Name` e `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Insira alguns dados:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Exiba o conteúdo da tabela:
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![Shell do HBase do Hadoop HDInsight](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Crie uma política do Ranger para **sales_user1** e **marketing_user1**.

1. Abra o **interface do usuário administrador do Ranger**. Clique em **\<ClusterName>_hbase** em **HBase**.

   ![Interface de usuário de administração do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. A tela **Lista de Políticas** exibirá todas as políticas do Ranger criadas para esse cluster. Uma política previamente configurada pode estar listada. Clique em **Adicionar Nova Política**.

    ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Na página **Criar Política**, insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  sales_customers_name_contact   |
   |Tabela do HBase   |  Clientes |
   |Família de colunas do HBase   |  Nome, Contato |
   |Coluna do HBase   |  * |
   |Selecionar grupo  | |
   |Selecionar usuário  | sales_user1 |
   |Permissões  | Ler |

   Os curingas a seguir podem ser incluídos no nome do tópico:

   * `*` indica zero ou mais ocorrências de caracteres.
   * `?` indica um caractere único.

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Aguarde alguns instantes para que o Ranger sincronize com o Azure AD se um usuário do domínio não for preenchido automaticamente para **Selecionar usuário**.

4. Clique em **Adicionar** para salvar a política.

5. Clique em **adicionar nova política** e, em seguida, insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  marketing_customers_contact   |
   |Tabela do HBase   |  Clientes |
   |Família de colunas do HBase   |  Contato |
   |Coluna do HBase   |  * |
   |Selecionar grupo  | |
   |Selecionar usuário  | marketing_user1 |
   |Permissões  | Ler |

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Clique em **Adicionar** para salvar a política.

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas do Ranger configuradas, **sales_user1** pode exibir todos os dados das colunas nas famílias de colunas `Name` e `Contact`. **marketing_user1** só pode exibir dados na família de colunas `Contact`.

### <a name="access-data-as-salesuser1"></a>Acessar dados como sales_user1

1. Abra uma nova conexão de SSH ao cluster. Use o seguinte comando para entrar no cluster:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Use o comando kinit para alternar para o contexto de nosso usuário desejado.

   ```bash
   kinit sales_user1
   ```

2. Abra o shell do HBase e verifique a tabela `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Observe que o usuário de vendas pode exibir todas as colunas da tabela `Customers`, incluindo as duas colunas na família de colunas `Name`, bem como as cinco colunas na família de colunas `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Acessar dados como marketing_user1

1. Abra uma nova conexão de SSH ao cluster. Use o seguinte comando para entrar como **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Use o comando kinit para alternar para o contexto de nosso usuário desejado

   ```bash
   kinit marketing_user1
   ```

2. Abra o shell do HBase e verifique a tabela `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Observe que o usuário de marketing pode exibir somente as cinco colunas da família de colunas `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Exiba os eventos de auditoria de acesso da interface do usuário do Ranger.

   ![Auditoria de políticas do Ranger da interface do usuário](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não for continuar usando este aplicativo, exclua o cluster do HBase que você criou seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Na caixa **Pesquisar** na parte superior, digite **HDInsight**. 
1. Selecione **Clusters do HDInsight** em **Serviços**.
1. Na lista de clusters do HDInsight que aparece, clique em **…** ao lado do cluster que você criou para este tutorial. 
1. Clique em **Excluir**. Clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
