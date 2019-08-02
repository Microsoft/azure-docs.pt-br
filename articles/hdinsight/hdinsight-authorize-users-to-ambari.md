---
title: Autorizar usuários para o Ambari Views – Azure HDInsight
description: Como gerenciar permissões de usuários e grupos do Ambari para clusters do HDInsight com o ESP habilitado.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 28f30270ab0a6c057ee583ccebc2a8540980c6cc
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442180"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizar usuários para o Apache Ambari Views

[Empresa Pacote de Segurança (ESP) habilitados aglomerados HDInsight](./domain-joined/hdinsight-security-overview.md) fornecer recursos de nível empresarial, incluindo autenticação Azure com base no Active Directory. Você pode [sincronizar novos usuários](hdinsight-sync-aad-users-to-cluster.md) adicionados aos grupos do Azure AD e que receberam acesso ao cluster, permitindo a esses usuários específicos executar determinadas ações. Trabalhar com usuários, grupos e permissões no [ Apache Ambari ](https://ambari.apache.org/) é suportado para clusters ESP HDInsight e clusters HDInsight padrão.

Active Directory os usuários podem entrar nos nós do cluster usando suas credenciais de domínio. Eles também podem usar suas credenciais de domínio para autenticar interações de cluster com outros terminais aprovados, como [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell e APIs REST.

> [!WARNING]  
> Não altere a senha do watchdog Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar as ações de script ou executar operações de dimensionamento com o cluster.

Se você ainda não fez isso, siga [estas instruções](./domain-joined/apache-domain-joined-configure.md) para provisionar um novo cluster ESP.

## <a name="access-the-ambari-management-page"></a>Acessar a página de gerenciamento do Ambari

Para obter o **página de gerenciamento do Ambari** na [IU da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md), navegue até **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Insira o nome de usuário e a senha do administrador de cluster que você definiu ao criar o cluster. Em seguida, no painel do Ambari, selecione **Gerenciar Ambari** abaixo do menu **admin**:

![Gerenciar Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Conceder permissões às exibição do Hive

O Ambari vem com instâncias de visualização para [Apache Hive](https://hive.apache.org/) e [Apache TEZ](https://tez.apache.org/), entre outros. Para conceder acesso a uma ou mais instâncias de exibição do Hive, acesse a **página de gerenciamento do Ambari**.

1. Na página de gerenciamento, selecione o link **Exibições** no título de menu **Exibições** à esquerda.

    ![Link de exibições](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na página Exibições, expanda a linha **HIVE**. Há uma exibição padrão do Hive que é criada quando o serviço do Hive é adicionado ao cluster. Você também pode criar mais instâncias de exibição do Hive conforme necessário. Selecione uma exibição do Hive:

    ![Exibições – exibição do Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Role até o fim da página Exibições. Na seção *Permissões*, você tem duas opções para conceder as permissões para a exibição aos usuários do domínio:

**Conceder permissão a esses usuários** ![Conceder permissão a esses usuários](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Conceder permissão a esses grupos** ![Conceder permissão a esses grupos](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Para adicionar um usuário, selecione o botão **Adicionar Usuário**.

   * Comece a digitar o nome de usuário e você verá uma lista suspensa de nomes previamente definidos.

     ![Preenchimento automático do usuário](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Selecione ou terminar de digitar o nome de usuário. Para adicionar esse nome de usuário como um novo usuário, selecione o botão **Novo**.

   * Para salvar suas alterações, marque a **caixa de seleção azul**.

     ![Usuário inserido](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Para adicionar um grupo, selecione o botão **Adicionar Grupo**.

   * Comece a digitar o nome do grupo. O processo para selecionar um nome de grupo existente ou adicionar um novo grupo é o mesmo da adição de usuários.
   * Para salvar suas alterações, marque a **caixa de seleção azul**.

     ![Grupo inserido](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Adicionar usuários diretamente a uma exibição é útil quando você deseja atribuir permissões a um usuário para usar essa exibição, mas não deseja que ele seja membro de um grupo que tenha permissões adicionais. Para reduzir a quantidade de sobrecarga administrativa, pode ser mais simples atribuir permissões a grupos.

## <a name="grant-permissions-to-apache-tez-views"></a>Conceder permissões para exibições do Apache TEZ

As instâncias de visualização do [Apache TEZ](https://tez.apache.org/) permitem que os usuários monitorem e depurem todos os trabalhos do Tez, enviados por [consultas do Apache Hive](https://hive.apache.org/) e scripts do [Apache Pig](https://pig.apache.org/). Há uma instância padrão de exibição do Tez que é criada quando o cluster é provisionado.

Para atribuir usuários e grupos a uma instância de exibição do Tez, expanda a linha **TEZ** na página Exibições, conforme descrito anteriormente.

![Exibições – exibição do Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Para adicionar usuários ou grupos, repita as etapas 3 a 5 da seção anterior.

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções

Há cinco funções de segurança para usuários e grupos, listados em ordem decrescente de permissões de acesso:

* Administrador do cluster
* Operador do cluster
* Administrador de Serviços
* Operador de serviço
* Usuário do cluster

Para gerenciar as funções, acesse a **página de gerenciamento do Ambari** e, em seguida, selecione o link **Funções** no grupo de menus *Clusters* à esquerda.

![Link do menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Para ver a lista de permissões concedidas a cada função, clique no ponto de interrogação azul ao lado do cabeçalho da tabela **Funções** na página Funções.

![Link do menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Nessa página, há duas exibições diferentes que você pode usar para gerenciar funções para usuários e grupos: Bloco e Lista.

### <a name="block-view"></a>Exibição Bloco

A exibição Bloco exibe cada função em sua própria linha e fornece as opções **Atribuir funções a esses usuários** e **Atribuir funções a esses grupos**, conforme descrito anteriormente.

![Exibição em bloco das funções](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Exibição Lista

A exibição Lista fornece recursos de edição rápidos em duas categorias: Usuários e Grupos.

* A categoria Usuários da exibição Lista exibe uma lista de todos os usuários, permitindo que você selecione uma função para cada usuário na lista suspensa.

    ![Exibição em lista das funções – usuários](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  A categoria Grupos da exibição Lista exibe todos os grupos e a função atribuída a cada grupo. Em nosso exemplo, a lista de grupos é sincronizada com os grupos do Azure AD especificados na propriedade **Acessar grupo de usuários** das configurações de domínio do cluster. Ver [criar um cluster HDInsight com ESP habilitado](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Exibição em lista das funções – grupos](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na imagem acima, o grupo "hiveusers" tem a função *Usuário de Cluster* atribuída. Essa é uma função somente leitura que permite que os usuários daquele grupo exibam mas não alterem as configurações do serviço e as métricas do cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Fazer logon no Ambari como um usuário somente exibição

Nós atribuímos permissões para exibições do Hive e do Tez ao usuário de domínio do Azure AD "hiveuser1". Quando iniciamos a interface do usuário da Web do Ambari e inserimos as credenciais de domínio do usuário (nome de usuário do Azure AD no formato de email e também a senha), o usuário é redirecionado para a página Ambari Views. Neste local, o usuário pode selecionar qualquer exibição acessível. O usuário não pode visitar nenhuma outra parte do site, inclusive as páginas de painel, serviços, hosts, alertas ou de administrador.

![Usuário somente com exibições](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Fazer logon no Ambari como um usuário de cluster

Atribuímos a função de *Usuário de Cluster* ao usuário de domínio do Azure AD "hiveuser2". Essa função é capaz de acessar o painel e todos os itens de menu. Um usuário de cluster tem menos opções permitidas que um administrador. Por exemplo, o hiveuser2 pode exibir as configurações de cada um dos serviços, mas não pode editá-las.

![Usuário com função de Usuário de Cluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Próximas etapas

* [Configurar políticas do Apache Hive no HDInsight com ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerenciar clusters HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Use o Apache Hive View com o Apache Hadoop no HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Sincronizar usuários do Azure AD com o cluster](hdinsight-sync-aad-users-to-cluster.md)
