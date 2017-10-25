---
title: "Gerenciar permissões de usuário nos níveis de arquivo e de pasta – Azure HDInsight| Microsoft Docs"
description: "Como gerenciar permissões de arquivo e de pasta para clusters HDInsight ingressados no domínio."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Gerenciar permissões de usuário nos níveis de arquivo e de pasta

Os [clusters HDInsight ingressados no domínio](hdinsight-domain-joined-introduction.md) usam a autenticação forte com usuários do Azure AD (Azure Active Directory) e também usam políticas *RBAC* (controle de acesso baseado em função) para vários serviços, como o YARN e o Hive. Se o armazenamento de dados padrão do seu cluster for o Armazenamento do Microsoft Azure ou o WASB (Microsoft Azure Storage Blob), você também poderá impor permissões de nível de pasta e arquivo. Você pode usar o Apache Ranger para controlar o acesso aos arquivos do cluster de seus grupos e usuários sincronizados do Azure AD.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

A instância do Apache Ranger para clusters HDInsight ingressados no domínio vem pré-configurada com o serviço Ranger-WASB. O serviço Ranger-WASB é um mecanismo de gerenciamento de política que é semelhante ao Ranger-HDFS, mas com uma imposição diferente das políticas de acesso do Ranger. No serviço Ranger-WASB, se uma solicitação de recurso de entrada não tiver uma política de correspondência do Ranger, a resposta padrão será DENY. O serviço Ranger não transmite a verificação de permissão para o WASB.

## <a name="permission-and-policy-model"></a>Modelo de política e permissão

As solicitações de acesso a recursos são avaliadas usando o seguinte fluxo:

![Fluxo de avaliação de política do Apache Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

As regras de DENY são avaliadas primeiro, seguidas pelas regras de ALLOW. No final da correspondência, se não houver políticas correspondentes, DENY será retornado.

### <a name="user-variable"></a>Variável USER

Você pode usar a variável `{USER}` durante a atribuição de políticas para um usuário acessar um subdiretório `/{username}`, por exemplo:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

A política acima concede aos usuários o acesso a sua própria subpasta no diretório `/app-logs/`. Veja como é essa política na interface do usuário do Ranger:

![Exemplo de uso de variável USER](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Exemplos de modelo de política

A tabela a seguir lista alguns exemplos de como o modelo de política funciona:

| Política do Ranger | Sistema de arquivos existente | Solicitação do usuário | Result |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, criar o arquivo /data/finance/mydatafile.txt | ALLOW – a pasta intermediária "finance" é criada, devido a uma verificação de ancestral |
| /data/finance/, bob, WRITE | /data | alice, criar o arquivo /data/finance/mydatafile.txt | DENY – nenhuma política de correspondência |
| /data/finance*, bob, WRITE | /data | bob, criar o arquivo /data/finance/mydatafile.txt | ALLOW – nesse caso, a política recursiva opcional (`*`) está presente; consulte [Curingas](#wildcards) |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, criar o arquivo /data/finance/mydatafile.txt | DENY – a verificação de ancestral em "/data" falha porque não há nenhuma política |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, criar o arquivo /data/finance/mydatafile.txt | DENY – nenhuma política de verificação de ancestral em "/data/finance" |

As permissões são necessárias no nível da pasta ou no nível de arquivo, com base no tipo de operação. Por exemplo, uma chamada de "ler/abrir" requer acesso de leitura no nível de arquivo, enquanto que uma chamada de "criar" requer permissões no nível de pasta ancestral.

### <a name="wildcards-"></a>Curingas (*)

Quando um curinga (`*`) está presente no caminho de uma política, ele se aplica ao caminho e toda a sua subárvore. Essa recursão é o mesmo que usar um `recurse-flag`. No Ranger-WASB, o curinga indica tanto a recursão quanto a correspondência de nome parcial.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Gerenciar permissões de nível de pasta e de arquivo com o Apache Ranger

Se você ainda não tiver feito isso, siga [estas instruções](hdinsight-domain-joined-configure.md) para provisionar um novo cluster ingressado no domínio.

Abra o Ranger-WASB navegando até `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Insira o nome de usuário e a senha do administrador de cluster que você definiu ao criar seu cluster.

Depois de entrar, você verá o painel do Ranger:

![Painel do Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Para exibir as permissões atuais de arquivo e de pasta para a conta de Armazenamento do Azure associada ao seu cluster, clique no link ***CLUSTERNAME*_wasb** na caixa de controle WASB.

![Painel do Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

A lista atual de políticas é exibida. Várias políticas comuns são incluídas como um ponto de partida. Verifique os detalhes de cada política para ver usos de exemplo.

Para cada política, você pode ver se a política está habilitada, se o log de auditoria está configurado e todos os grupos e usuários atribuídos. Há dois botões de ação para cada política: Editar e Excluir.

![Lista de políticas](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Adicionando uma nova política

1. Na parte superior direita da página de políticas WASB, selecione **Adicionar Nova Política**.

    ![Adicionar nova política](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Insira um **Nome de Política** descritivo. Especifique a **Conta de Armazenamento** do Azure do seu cluster (*ACCOUNT_NAME*.blob.core.windows.net) e o **Contêiner da Conta de Armazenamento** especificado quando você criou seu cluster. Insira o **Caminho relativo** (relativo ao cluster) para o arquivo ou a pasta acessada.

    ![Formulário Nova Política](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Abaixo do formulário, especifique as suas **Condições de permissão** para esse novo recurso. Selecione usuários e grupos aplicáveis e defina as respectivas permissões. No exemplo a seguir, estamos permitindo que todos os usuários do grupo `sales` tenham acesso de leitura/gravação.

    ![Permitir vendas](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Selecione **Salvar**.

### <a name="example-policy-conditions"></a>Condições de política de exemplo

O [fluxo de avaliação de política](#permission-and-policy-model) do Apache Ranger permite que você especifique qualquer combinação condições de permitir e negar a fim de atender às suas necessidades. Veja alguns exemplos:

1. Permitir todos os usuários de vendas, mas nenhum estagiário:

    ![Permitir vendas, negar estagiários](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Permitir todos os usuários de vendas e negar todos os estagiários, exceto um estagiário denominado "hiveuser3", que deve ter acesso de Leitura:

    ![Permitir vendas, negar estagiários, exceto hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Próximas etapas

* [Configurar políticas do Hive no HDInsight ingressado no domínio](hdinsight-domain-joined-run-hive.md)
* [Gerenciar clusters HDInsight ingressados no domínio](hdinsight-domain-joined-manage.md)
* [Gerenciar Ambari – autorizar usuários para o Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

