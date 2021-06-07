---
title: Sincronizar Azure Active Directory usuários com o cluster HDInsight
description: Sincronizar usuários autenticados do Azure Active Directory para um cluster HDInsight.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 2a753a33e9ddf16cc277ab10c1f91049a6382066
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871938"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizar usuários do Azure Active Directory para um cluster HDInsight

Os [clusters HDInsight com Enterprise Security Package (ESP)](./domain-joined/hdinsight-security-overview.md) podem usar a autenticação forte com usuários do Azure Active Directory (Azure AD), bem como usar políticas do Azure *RBAC (controle de acesso baseado em função) do* Azure. Ao adicionar usuários e grupos ao Azure AD, você pode sincronizar os usuários que precisam de acesso ao cluster.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [crie um cluster do HDInsight com o Enterprise Security Package](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="add-new-azure-ad-users"></a>Adicionar novos usuários do Azure AD

Para exibir seus hosts, abra a interface do usuário do Ambari Web. Cada nó será atualizado com novas configurações de atualização automática.

1. No [portal do Azure](https://portal.azure.com), navegue até o diretório do Azure ad associado ao seu cluster ESP.

2. Selecione **Todos os usuários** no menu à esquerda, selecione **Novo usuário**.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png" alt-text="portal do Azure todos os usuários e grupos":::

3. Preencha o formulário de novo usuário. Selecione grupos que você criou para a atribuição de permissões com base em cluster. Neste exemplo, crie um grupo chamado "HiveUsers", aos quais você pode atribuir novos usuários. As [instruções de exemplo](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) para criar um cluster ESP incluem a inclusão de dois grupos, `HiveUsers` e `AAD DC Administrators`.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png" alt-text="Painel do usuário do portal do Azure selecionar grupos":::

4. Selecione **Criar**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Use a API REST do Apache Ambari para sincronizar os usuários

Os grupos de usuários especificados durante o processo de criação de cluster estão sincronizados no momento. A sincronização de usuário ocorre automaticamente uma vez a cada hora. Para sincronizar os usuários imediatamente, ou para sincronizar um grupo diferente de grupos especificados durante a criação do cluster, use a API REST do Ambari.

O método a seguir usa o POST com a API REST do Ambari. Para obter mais informações, consulte [Gerenciar clusters do HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Use o [comando ssh](hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois de autenticar, insira o seguinte comando:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    A resposta será parecida com esta:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Para ver o status da sincronização, execute um novo comando `curl`:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    A resposta será parecida com esta:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Esse resultado mostra que o status está **concluído**, um novo usuário foi criado e o usuário recebeu uma associação. Neste exemplo, o usuário é atribuído ao grupo LDAP sincronizado "HiveUsers", já que o usuário foi adicionado ao mesmo grupo no Azure AD.

    > [!NOTE]  
    > O método anterior sincroniza apenas os grupos do Azure AD especificados na propriedade **Access User Group** das configurações de domínio durante a criação do cluster. Para saber mais, veja [Criar um cluster HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifique se o usuário recém-adicionado do Azure AD

Abra a [Interface do Usuário do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para verificar se o novo usuário do Azure AD foi adicionado. Acesse a interface do usuário da Web do amAmbari navegando até **`https://CLUSTERNAME.azurehdinsight.net`** . Insira o nome de usuário de administrador do cluster.

1. No painel do Ambari, selecione **Gerenciar Ambari** abaixo do menu **admin**.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png" alt-text="Painel do Apache Ambari gerenciar Ambari":::

2. Selecione **usuários** abaixo do grupo de menu **Gerenciamento de grupo de usuário +** no lado esquerdo da página.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png" alt-text="Menu usuários e grupos do HDInsight":::

3. O novo usuário deve ser listado na tabela de usuários. O tipo é definido como `LDAP` em vez de `Local`.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png" alt-text="Visão geral da página de usuários do HDInsight AAD":::

## <a name="log-in-to-ambari-as-the-new-user"></a>Faça logon Ambari como o novo usuário

Quando o novo usuário (ou qualquer outro usuário de domínio) faz logon no Ambari, usa suas credenciais de nome e o domínio de usuário completo do Azure AD.  Ambari exibe um alias do usuário, que é o nome de exibição do usuário no Azure AD.
O novo usuário de exemplo tem o nome de usuário `hiveuser3@contoso.com`. No Ambari, esse novo usuário é exibido como `hiveuser3`, mas o usuário faz logon em Ambari como `hiveuser3@contoso.com`.

## <a name="see-also"></a>Confira também

* [Configurar políticas do Apache Hive no HDInsight com ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerenciar clusters do HDInsight com ESP](./domain-joined/apache-domain-joined-manage.md)
* [Autorizar usuários para o Apache Ambari](hdinsight-authorize-users-to-ambari.md)