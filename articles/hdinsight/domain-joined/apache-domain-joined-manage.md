---
title: Gerenciar clusters de Enterprise Security Package-Azure HDInsight
description: Saiba como gerenciar clusters do Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: b0f8ba4adfa4b08c23d3f69fa4e2b01f4580bb19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742204"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gerenciar clusters HDInsight com Enterprise Security Package

Saiba mais sobre usuários e funções no ESP (Enterprise Security Package) do HDInsight e como gerenciar clusters ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Use o VSCode para vincular ao cluster ingressado no domínio

É possível vincular um cluster normal usando o nome de usuário gerenciado do Apache Ambari, além de vincular um cluster de segurança do Apache Hadoop, usando o nome de usuário de domínio (como: `user1@contoso.com`).

1. Abra [Visual Studio Code](https://code.visualstudio.com/). Verifique se a extensão de [Ferramentas do hive do Spark &](../hdinsight-for-vscode.md) está instalada.

1. Siga as etapas em [vincular um cluster](../hdinsight-for-vscode.md#link-a-cluster) para Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Use o IntelliJ para vincular ao cluster ingressado no domínio

É possível vincular um cluster normal usando o nome de usuário gerenciado Ambari, além de vincular um cluster hadoop de segurança usando o nome de usuário do domínio (como: `user1@contoso.com`).

1. Abra o IntelliJ IDEA. Verifique se todos os [pré-requisitos](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) foram atendidos.

1. Siga as etapas em [vincular um cluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) para IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Use o Eclipse para vincular ao cluster ingressado no domínio

É possível vincular um cluster normal usando o nome de usuário gerenciado Ambari, além de vincular um cluster hadoop de segurança usando o nome de usuário do domínio (como: `user1@contoso.com`).

1. Abra o Eclipse. Verifique se todos os [pré-requisitos](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) foram atendidos.

1. Siga as etapas em [vincular um cluster para o](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acessar os clusters com Enterprise Security Package

O Pacote de Segurança Enterprise (anteriormente conhecido como HDInsight Premium) fornece acesso de vários usuário ao cluster, onde a autenticação é feita pelo Active Directory e a autorização pelo Apache Ranger e ACLs de armazenamento (ACLs do ADLS). A autorização fornece limites de segurança entre vários usuários e permite que somente usuários privilegiados tenham acesso aos dados com base nas políticas de autorização.

Segurança e isolamento de usuários são importantes para um cluster de HDInsight com o Pacote de Segurança Enterprise. Para atender a esses requisitos, o acesso do SSH ao cluster com Enterprise Security Package tem suporte para o usuário local selecionado no momento da criação do cluster, bem como os usuários disponíveis no AAD-DS (ou seja, Kerberos). A tabela a seguir mostra os métodos de acesso recomendados para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de Acesso|
|--------|--------|-------------|
|Apache Hadoop|Hive – trabalhos/consultas interativas    |<ul><li>[Beeline](#beeline)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Trabalhos/consultas interativas, PySpark interativo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Cenários em lote – envio de Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Consulta Interativa (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualquer|Instalar Aplicativo Personalizado|<ul><li>[Ações de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter não está instalado/com suporte no  Enterprise Security Package.

Usar as APIs padrão ajuda da perspectiva de segurança. Você também obtém os seguintes benefícios:

- **Gerenciamento** – você pode gerenciar seu código e automatizar trabalhos usando APIs padrão – Livy, HS2 etc.
- **Auditoria** – com o ssh, não há como auditar, que os usuários SSH para o cluster. Esse não seria o caso quando os trabalhos são construídos por meio de pontos de extremidade padrão, pois eles seriam executados no contexto do usuário.

### <a name="use-beeline"></a><a name="beeline"></a>Use Beeline

Instale o Beeline no seu computador e conecte-se pela internet pública, use os seguintes parâmetros:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se você tiver Beeline instalado localmente e conectar-se pela Rede Virtual do Azure, use os seguintes parâmetros:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento Gerenciar HDInsight usando a API de REST do Ambari.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Usuários de clusters HDInsight com ESP

Um cluster HDInsight não ESP tem duas contas de usuário criadas durante a criação do cluster:

- **Administração do Ambari**: essa conta também é conhecida como *usuário do Hadoop* ou *usuário HTTP*. Essa conta pode ser usada para entrar no Ambari em `https://CLUSTERNAME.azurehdinsight.net` . Ele também pode ser usado para executar consultas em exibições do Ambari, executar trabalhos por meio de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio) e autenticar com o driver ODBC do hive e as ferramentas de BI (por exemplo, Excel, Power BI ou tableau).

Um cluster HDInsight com ESP tem três novos usuários além do Ambari Admin.

- **Administração do Ranger**: essa é a conta de administrador local do Apache Ranger. Não é um usuário de domínio do Active Directory. Essa conta pode ser usada para configurar políticas e tornar outros usuários administradores ou administradores delegados (de modo que os usuários possam gerenciar políticas). Por padrão, o nome de usuário é *administrador* e a senha é a mesma que a senha de administrador do Ambari. A senha pode ser atualizada da página Configurações no Ranger.

- **Usuário de domínio do administrador do cluster**: essa conta é um usuário do Domínio do Active Directory designado como o administrador do cluster do Hadoop, incluindo o Ambari e o Ranger. Você deve fornecer as credenciais deste usuário durante a criação do cluster. Esse usuário tem os seguintes privilégios:
    - Adicionar computadores ao domínio e colocá-los na UO que você especificar durante a criação do cluster.
    - Criar entidades de serviço na UO que você especificar durante a criação do cluster.
    - Criar entradas de DNS reverso.

    Observe que os usuários do AD também têm esses privilégios.

    Há alguns pontos de extremidade dentro do cluster (por exemplo, Templeton) que não são gerenciados pelo Ranger e, portanto, não são seguros. Esses pontos de extremidade estão bloqueados para todos os usuários, exceto para o usuário de domínio do administrador do cluster.

- **Regular**: durante a criação do cluster, você poderá fornecer vários grupos do Active Directory. Os usuários nesses grupos são sincronizados ao Ranger e ao Ambari. Esses usuários são usuários de domínio e têm acesso apenas aos pontos de extremidade gerenciados pelo Ranger (por exemplo, Hiveserver2). Todas as políticas de RBAC e a auditoria serão aplicáveis a esses usuários.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Funções dos clusters HDInsight com ESP

O Enterprise Security Package do HDInsight tem as seguintes funções:

- Administrador do cluster
- Operador do cluster
- Administrador de serviços
- Operador de serviço
- Usuário do cluster

**Para ver as permissões dessas funções**

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, selecione **funções**.
3. Selecione o ponto de interrogação azul para ver as permissões:

    ![Permissões de funções do HDInsight do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a interface do usuário do Ambari Management

1. Navegue até `https://CLUSTERNAME.azurehdinsight.net/` onde ClusterName é o nome do cluster.
1. Entre no Ambari usando o nome de usuário e a senha do domínio do administrador de cluster.
1. Selecione o menu suspenso **admin** no canto superior direito e, em seguida, selecione **gerenciar Ambari**.

    ![Gerenciar Apache Ambari do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do usuário é semelhante a:

    ![Interface do usuário de gerenciamento do Apache HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os usuários de domínio sincronizados do Active Directory

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, selecione **usuários**. Você deverá ver todos os usuários sincronizados do seu Active Directory com o cluster do HDInsight.

    ![Usuários listados na interface do usuário de gerenciamento do Ambari do HDInsight do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados do Active Directory

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, selecione **grupos**. Você deverá ver todos os grupos sincronizados do seu Active Directory no cluster do HDInsight.

    ![Grupos de listas de interface do usuário de gerenciamento do Ambari do HDInsight do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de exibições do Hive

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, selecione **exibições**.
3. Selecione **Hive** para mostrar os detalhes.

    ![Exibições de Hive da interface do usuário de gerenciamento do Ambari do HDInsight do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selecione o link **exibição do hive** para configurar exibições do hive.
5. Role para baixo até a seção **Permissões**.

    ![Permissões de configuração das Exibições de Hive na interface do usuário de gerenciamento do Ambari do HDInsight do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selecione **Adicionar usuário** ou **Adicionar grupo** e, em seguida, especifique os usuários ou grupos que podem usar exibições do hive.

## <a name="configure-users-for-the-roles"></a>Configurar usuários para as funções

 Para ver uma lista de funções e suas permissões, confira Funções dos clusters HDInsight com ESP.

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, selecione **funções**.
3. Selecione **Adicionar usuário** ou **Adicionar grupo** para atribuir usuários e grupos a diferentes funções.

## <a name="next-steps"></a>Próximas etapas

- Para configurar um cluster HDInsight com Enterprise Security Package, confira [Configurar clusters HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).
- Para configurar as políticas do Hive e executar as consultas do Hive, confira [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).