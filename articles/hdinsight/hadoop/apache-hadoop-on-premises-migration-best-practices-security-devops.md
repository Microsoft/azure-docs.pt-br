---
title: Migrar clusters do Apache Hadoop local para o Azure HDInsight – segurança e melhores práticas de DevOps
description: Saiba mais sobre as melhores práticas de segurança e de DevOps para a migração de clusters locais do Hadoop para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1d13b52d253562a24946e6df2fc069f41b485fef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095363"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrar clusters do Apache Hadoop local para o Azure HDInsight – segurança e melhores práticas de DevOps

Este artigo fornece recomendações de segurança e DevOps nos sistemas do Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Proteger e controlar o cluster com o Enterprise Security Package

O ESP (Enterprise Security Package) é compatível com a autenticação baseada no Active Directory, suporte multiusuário e controle de acesso baseado em função. Com a opção ESP selecionada, o cluster do HDInsight ingressa no domínio do Active Directory. Dessa maneira, o admin corporativo pode configurar o RBAC (controle de acesso baseado em função) para a segurança do Apache Hive usando o Apache Ranger. O administrador também poderá auditar o acesso a dados pelos funcionários e todas as alterações feitas nas políticas de controle de acesso.

O ESP está disponível nos seguintes tipos de cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e Apache Interactive Query (Hive LLAP). 

Use as seguintes etapas para implantar o cluster do HDInsight ingressado no domínio:

- Implante o AAD (Azure Active Directory) passando o nome de domínio.
- Implante o AAD DS (Azure Active Directory Domain Services).
- Crie a Rede Virtual obrigatória e a sub-rede.
- Implante uma VM na Rede Virtual para gerenciar o AAD DS.
- Ingresse a VM no domínio.
- Instale o AD e as ferramentas de DNS.
- Faça com que o Administrador do AAD DS crie uma UO (unidade organizacional).
- Habilite o LDAPS para o AAD DS.
- Crie uma conta de serviço no Azure Active Directory com permissão de administrador delegada para leitura e gravação na UO. Depois, essa conta de serviço poderá ingressar computadores no domínio e colocar entidades de segurança do computador na UO. Também poderá criar entidades de serviço na UO especificadas por você durante a criação do cluster.


    > [!Note]
    > A conta de serviço não precisa ser uma conta do administrador de domínio do AD.


- Implante o cluster ESP do HDInsight configurando os seguintes parâmetros:
    - **Nome de domínio**: O nome de domínio associado ao Azure AD DS.
    - **Nome de usuário do domínio**: A conta de serviço no domínio gerenciado do Azure AD DS DC que você criou na seção anterior, por exemplo: `hdiadmin@contoso.onmicrosoft.com`. Esse usuário de domínio será o administrador deste cluster HDInsight.
    - **Senha do domínio**: A senha da conta de serviço.
    - **Unidade Organizacional**: O nome diferenciado da UO que você quer usar com o cluster do HDInsight, por exemplo: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Se essa UO não existir, o cluster do HDInsight tentará criá-la usando os privilégios da conta de serviço.
    - **URL do LDAPS**: por exemplo, `ldaps://contoso.onmicrosoft.com:636`.
    - **Acessar grupo de usuários**: Os grupos de segurança cujos usuários você deseja sincronizar com o cluster, por exemplo: `HiveUsers`. Se você quiser especificar vários grupos de usuários, separe-os por ponto e vírgula ";". Os grupos precisam existir no diretório antes da criação do cluster do ESP.

Para obter mais informações, consulte os seguintes artigos:

- [Uma introdução à segurança do Apache Hadoop com clusters HDInsight ingressados no domínio](../domain-joined/apache-domain-joined-introduction.md)

- [Planeje clusters do Apache Hadoop associados ao domínio do Azure no HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configurar um cluster do HDInsight ingressado no domínio usando o Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizar usuários do Azure Active Directory para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configurar políticas do Apache Hive no HDInsight associado ao domínio](../domain-joined/apache-domain-joined-run-hive.md)
- [Executar o Apache Oozie em clusters HDInsight Hadoop ingressados no domínio](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementar a segurança empresarial de ponta a ponta

A segurança empresarial de ponta a ponta pode ser alcançada usando os seguintes controles:

- **Pipeline de dados privado e protegido (segurança no nível do perímetro)**
    - É possível obter segurança no nível do perímetro por meio de Redes Virtual do Azure, do Grupo de Segurança de Rede e do serviço de Gateway.

- **Autenticação e autorização acesso a dados**
    - Criar clusters do HDInsight ingressados no domínio usando o Azure Active Directory Domain Services. (Enterprise Security Package).
    - Usar o Ambari para fornecer acesso baseado em função para recursos de cluster a usuários do AD.
    - Usar o Apache Ranger para definir políticas de controle de acesso para o Hive no nível da tabela/coluna/linha.
    - O acesso a SSH no cluster pode ser restringido somente ao administrador.

- **Auditoria**
    - Exibir e gerar relatórios sobre todos os acessos aos recursos de cluster e dados do HDInsight.
    - Exibir e gerar relatórios sobre todas as alterações nas políticas de controle de acesso.

- **Criptografia**
    - Criptografia transparente no servidor usando chaves gerenciadas pela Microsoft ou pelo cliente.
    - Criptografia em trânsito usando criptografia do lado do cliente, https e TLS.

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral das Redes Virtuais do Azure](../../virtual-network/virtual-networks-overview.md)
- [Visão geral dos Grupos de Segurança de Rede do Azure](../../virtual-network/security-overview.md)
- [Emparelhamento de Rede Virtual do Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guia de segurança do Armazenamento do Azure](../../storage/common/storage-security-guide.md)
- [Criptografia do Serviço de Armazenamento do Azure em repouso](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Usar o monitoramento e os alertas

Para saber mais, confira o artigo:

[Visão Geral do Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Atualizar clusters

Atualize regularmente para a versão mais recente do HDInsight para aproveitar os recursos mais recentes. As etapas a seguir podem ser usadas para atualizar o cluster para a versão mais recente:

1. Crie um novo cluster TEST do HDInsight usando a versão mais recente disponível do serviço.
1. Teste no novo cluster para se certificar de que os trabalhos e as cargas de trabalho funcionem conforme o esperado.
1. Modifique trabalhos, aplicativos ou cargas de trabalho conforme o necessário.
1. Faça backup de dados transitórios armazenados localmente em nós do cluster.
1. Exclua o cluster existente.
1. Crie um cluster da versão mais recente do HDInsight na mesma sub-rede VNET usando os mesmos dados padrão e o armazenamento de metadados do cluster anterior.
1. Importe o backup de todos os dados transitórios.
1. Inicie os trabalhos/continue processando usando o novo cluster.

Para saber mais, confira o artigo: [Atualizar o cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Aplicar patch no sistema operacional do cluster

Como um serviço Hadoop gerenciado, o HDInsight se encarrega da aplicação de patch no SO das VMs usadas por clusters HDInsight.

Para saber mais, confira o artigo: [Aplicação de patch do SO no HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Após a migração

1. **Corrigir os aplicativos** – faça as alterações necessárias nos trabalhos, processos e scripts de forma iterativa.
2. **Executar testes** – execute testes funcionais e de desempenho de forma iterativa.
3. **Otimizar** – resolva os problemas de desempenho com base nos resultados de teste anterior e, em seguida, teste novamente para confirmar as melhorias de desempenho.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
