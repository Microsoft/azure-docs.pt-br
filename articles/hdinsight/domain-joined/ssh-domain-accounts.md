---
title: Gerenciar o acesso SSH para contas de domínio no Azure HDInsight
description: Etapas para gerenciar o acesso SSH para contas do Azure AD no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946746"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gerenciar o acesso SSH para contas de domínio no Azure HDInsight

Em clusters seguros, por padrão, todos os usuários de domínio no [Azure AD DS](../../active-directory-domain-services/overview.md) são permitidos para o [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) nos nós de cabeçalho e borda. Esses usuários não fazem parte do grupo sudoers e não obtêm acesso à raiz. O usuário SSH criado durante a criação do cluster terá acesso à raiz.

## <a name="manage-access"></a>Gerenciar acesso

Para modificar o acesso SSH a usuários ou grupos específicos, atualize `/etc/ssh/sshd_config` em cada um dos nós.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra o `ssh_confi` arquivo g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modifique o `sshd_config` arquivo conforme desejado. Se você restringir os usuários a determinados grupos, as contas locais não poderão fazer SSH nesse nó. A seguir, há apenas um exemplo de sintaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Em seguida, salve as alterações: **Ctrl + X**, **Y**, **Enter**.

1. Reinicie o sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Repita as etapas acima para cada nó.

## <a name="ssh-authentication-log"></a>Log de autenticação SSH

O log de autenticação SSH é gravado no `/var/log/auth.log` . Se você vir falhas de logon por meio de SSH para contas locais ou de domínio, será necessário passar pelo log para depurar os erros. Geralmente, o problema pode estar relacionado a contas de usuário específicas e geralmente é uma boa prática tentar outras contas de usuário ou SSH usando o usuário SSH padrão (conta local) e, em seguida, tentar um kinit.

## <a name="ssh-debug-log"></a>Log de depuração SSH

Para habilitar o log detalhado, será necessário reiniciar `sshd` com a `-d` opção. Como `/usr/sbin/sshd -d` você também pode executar `sshd` em uma porta personalizada (como 2222) para não ter que interromper o daemon principal do SSH. Você também pode usar `-v` a opção com o cliente SSH para obter mais logs (exibição do lado do cliente das falhas).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters HDInsight com Enterprise Security Package](./apache-domain-joined-manage.md)
* [Conectar ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
