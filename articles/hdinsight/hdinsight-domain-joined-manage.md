---
title: "Gerenciar clusters do HDInsight ingressados no domínio – Azure | Microsoft Docs"
description: "Saiba como gerenciar clusters do HDInsight Ingressado no Domínio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 9b56ce6cc5bdd3b8d48d047751e978cad08598e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>Gerenciar clusters do HDInsight Ingressado no Domínio (Preview)
Conheça os usuários e as funções do HDInsight Ingressado no Domínio e como gerenciar seus clusters.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Usuários dos clusters do HDInsight Ingressado no Domínio
Um cluster do HDInsight que não for ingressado no domínio terá duas contas de usuário que serão criadas durante a elaboração do cluster:

* **Administração do Ambari**: essa conta também é conhecida como *usuário do Hadoop* ou *usuário HTTP*. Essa conta pode ser usada para fazer logon no Ambari em https://&lt;clustername>.azurehdinsight.net. Ela também pode ser usada para executar consultas nas exibições do Ambari, para executar trabalhos por meio de ferramentas externas (ou seja, PowerShell, Templeton, Visual Studio) e para autenticar com o driver ODBC do Hive e com ferramentas de BI (por exemplo, Excel, PowerBI ou Tableau).
* **Usuário do SSH**: essa conta pode ser usada com o SSH e executar comandos sudo. Ela tem privilégios raiz sobre as VMs do Linux.

Um cluster do HDInsight ingressado no domínio tem três novos usuários, além da Administração do Ambari e do Usuário do SSH.

* **Administração do Ranger**: essa é a conta de administrador local do Apache Ranger. Ela não é um usuário do Domínio do Active Directory. Essa conta pode ser usada para configurar políticas e tornar outros usuários administradores ou administradores delegados (de modo que os usuários possam gerenciar políticas). Por padrão, o nome de usuário é *administrador* e a senha é a mesma que a senha de administrador do Ambari. A senha pode ser atualizada da página Configurações no Ranger.
* **Usuário de domínio do administrador do cluster**: essa conta é um usuário do Domínio do Active Directory designado como o administrador do cluster do Hadoop, incluindo o Ambari e o Ranger. Você deve fornecer as credenciais do usuário durante a criação do cluster. Esse usuário tem os seguintes privilégios:

  * Adicionar computadores ao domínio e colocá-los na UO que você especificar durante a criação do cluster.
  * Criar entidades de serviço na UO que você especificar durante a criação do cluster.
  * Criar entradas de DNS reverso.

    Observe que os usuários do AD também têm esses privilégios.

    Há alguns pontos de extremidade do cluster (por exemplo, Templeton) que não são gerenciados pelo Ranger e, portanto, não são seguros. Esses pontos de extremidade estão bloqueados para todos os usuários, exceto para o usuário de domínio do administrador do cluster.
* **Regular**: durante a criação do cluster, você poderá fornecer vários grupos do Active Directory. Os usuários nesses grupos serão sincronizados ao Ranger e ao Ambari. Esses usuários são usuários de domínio e eles terão acesso apenas aos pontos de extremidade gerenciados pelo Ranger (por exemplo, Hiveserver2). Todas as políticas de RBAC e a auditoria serão aplicáveis a esses usuários.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Funções dos clusters do HDInsight Ingressado no Domínio
O HDInsight Ingressado no Domínio tem as seguintes funções:

* Administrador do cluster
* Operador do cluster
* Administrador de serviços
* Operador de serviço
* Usuário do cluster

**Para ver as permissões dessas funções**

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Funções**.
3. Clique no ponto de interrogação azul para ver as permissões:

    ![Permissões das funções do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a interface do usuário do Ambari Management
1. Entre no [portal do Azure](https://portal.azure.com).
2. Abra seu cluster do HDInsight em uma folha. Confira [Listar e mostrar clusters](hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Clique em **Painel** no menu superior para abrir o Ambari.
4. Faça logon no Ambari usando o nome de usuário e a senha de domínio de administrador do cluster.
5. Clique no menu suspenso **Administrador** no canto superior direito e, em seguida, clique em **Gerenciar o Ambari**.

    ![Ambari gerenciado pelo HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do usuário é semelhante a:

    ![Interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os usuários de domínio sincronizados do Active Directory
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Usuários**. Você deverá ver todos os usuários sincronizados do seu Active Directory com o cluster do HDInsight.

    ![Usuários listados na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados do Active Directory
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Grupos**. Você deverá ver todos os grupos sincronizados do seu Active Directory no cluster do HDInsight.

    ![Grupos listados na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de exibições do Hive
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Exibições**.
3. Clique em **HIVE** para mostrar os detalhes.

    ![Exibições do Hive na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Clique no link **Exibição do Hive** para configurar as exibições do Hive.
5. Role para baixo até a seção **Permissões**.

    ![Permissões de configuração das exibições do Hive na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Clique em **Adicionar Usuário** ou **Adicionar Grupo** e, em seguida, especifique os usuários ou grupos que podem usar as exibições do Hive.

## <a name="configure-users-for-the-roles"></a>Configurar usuários para as funções
 Para ver uma lista de funções e suas permissões, confira [Roles of Domain-joined HDInsight clusters](#roles-of-domain---joined-hdinsight-clusters) (Funções dos clusters do HDInsight Ingressado no Domínio).

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Funções**.
3. Clique em **Adicionar Usuário** ou **Adicionar Grupo** para atribuir usuários e grupos a funções diferentes.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster do HDInsight ingressado no domínio, confira [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configurar clusters do HDInsight Ingressado no Domínio).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
