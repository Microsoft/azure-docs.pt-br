---
title: Uma introdução à segurança do Apache Hadoop com Enterprise Security Package
description: Saiba como o Enterprise Security Package dá suporte aos quatro pilares da segurança empresarial.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: deff2aa3cd475a3ed37adc4408a133ed1fd0757e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113199"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Uma introdução à segurança do Apache Hadoop com Enterprise Security Package

No passado, o Azure HDInsight dava suporte apenas a um único usuário: o administrador local. Isso funcionava bem para equipes de aplicativos ou departamentos menores. À medida que as cargas de trabalho com base no Apache Hadoop ganharam mais popularidade no setor empresarial, a necessidade de funcionalidades de nível empresarial, como autenticação baseada no Azure Active Directory, suporte a vários usuários e controle de acesso baseado em função, tornou-se cada vez mais importante. 

Você pode criar um cluster do HDInsight com o ESP (Enterprise Security Package) que é ingressado em um domínio do Active Directory. Em seguida, você pode configurar uma lista de funcionários da empresa que podem se autenticar através do Active Directory do Azure para entrar no cluster HDInsight. Ninguém de fora da empresa podem entrar ou acessar o cluster HDInsight. 

O administrador corporativo pode configurar o RBAC (controle de acesso baseado em função) para segurança do Apache Hive, usando o [Apache Ranger](https://hortonworks.com/apache/ranger/). Configurar RBAC restringe o acesso a dados somente para o que é necessário. Finalmente, o administrador pode auditar o acesso aos dados pelos funcionários e quaisquer alterações feitas para acessar as políticas de controle. O administrador pode, então, alcançar um alto grau de governança dos recursos corporativos.

> [!NOTE]  
> O Apache Oozie agora está habilitado em clusters do ESP. Para acessar a IU da Web do Oozie, os usuários devem habilitar o [túnel](../hdinsight-linux-ambari-ssh-tunnel.md).

A segurança empresarial contém quatro pilares principais: segurança de perímetro, autenticação, autorização e criptografia.

![Benefícios dos clusters do HDInsight do Enterprise Security Package nos quatro pilares da segurança empresarial](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Segurança de perímetro
A segurança de perímetro no HDInsight é obtida por meio de redes virtuais e do serviço do Gateway de VPN do Azure. Um admin corporativo pode criar um cluster ESP dentro de uma rede virtual e usar grupos de segurança de rede (regras de firewall) para restringir o acesso à rede virtual. Somente os endereços IP definidos nas regras de firewall de entrada poderão comunicar-se com o cluster HDInsight. Essa configuração fornece segurança de perímetro.

Outra camada de segurança de perímetro é obtida através do serviço de Gateway de VPN. O gateway atua como primeira linha de defesa para qualquer solicitação recebida no cluster HDInsight. Ele aceita a solicitação, valida-a e somente então permitirá que a solicitação passe para os outros nós no cluster. Dessa maneira, o gateway fornece segurança de perímetro para outros nós de nome e dados no cluster.

## <a name="authentication"></a>Authentication
Um admin corporativo pode criar um cluster do HDInsight com ESP em uma [rede virtual](https://azure.microsoft.com/services/virtual-network/). Todos os nós do cluster HDInsight são ingressados no domínio que a empresa gerencia. Isso é obtido por meio do uso do [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Com essa configuração, funcionários da empresa podem entrar para os nós de cluster usando suas credenciais de domínio. Além disso, podem usar suas credenciais de domínio para autenticação com outros pontos de extremidade aprovados como Apache Ambari Views, ODBC, JDBC, PowerShell e API REST para interagir com o cluster. O administrador tem controle total sobre a limitação do número de usuários que interagem com o cluster por meio desses pontos de extremidade.

## <a name="authorization"></a>Autorização
Uma melhor prática que a maioria das empresas segue é garantir que nem todos os funcionários tenham acesso a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controle de acesso baseadas em função para os recursos do cluster. 

Por exemplo, o administrador pode configurar o [Apache Ranger](https://hortonworks.com/apache/ranger/) para definir políticas de controle de acesso para o Hive. Essa funcionalidade garante que os funcionários possam acessar apenas os dados que precisam para ter êxito nos trabalhos. O acesso SSH ao cluster também é restrito apenas ao administrador.

## <a name="auditing"></a>Auditoria
A auditoria de todo acesso aos recursos do cluster e aos dados é necessária para rastrear o acesso não autorizado ou não intencional dos recursos. É tão importante quanto proteger os recursos de cluster HDInsight de usuários não autorizados e proteger os dados. 

O administrador pode exibir e relatar todo o acesso aos recursos e dados de cluster do HDInsight. O administrador também pode exibir e relatar todas as alterações nas políticas de controle de acesso criadas nos pontos de extremidade com suporte pelo Apache Ranger. 

Um cluster do HDInsight com ESP usa a interface do usuário familiar do Apache Ranger para pesquisar logs de auditoria. No back-end, o Ranger usa o [Apache Solr](https://hortonworks.com/apache/solr/) para armazenar e pesquisar os logs.

## <a name="encryption"></a>Criptografia
A proteção de dados é importante para atender aos requisitos de segurança e conformidade da organização. Além de restringir o acesso a dados de funcionários não autorizados, você deve criptografá-lo. 

Ambos os armazenamentos de dados para clusters HDInsight, Armazenamento de Blobs do Azure e Azure Data Lake Storage Gen1/Gen2, dão suporte à [criptografia de dados](../../storage/common/storage-service-encryption.md) transparente do lado do servidor em repouso. Os clusters seguros do HDInsight funcionarão perfeitamente com esse recurso de criptografia de dados do lado do servidor em repouso.

## <a name="next-steps"></a>Próximas etapas

* [Planejar clusters do HDInsight com ESP](apache-domain-joined-architecture.md)
* [Configurar clusters do HDInsight com ESP](apache-domain-joined-configure.md)
* [Gerenciar clusters do HDInsight com ESP](apache-domain-joined-manage.md)
* [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md)
* [Use o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

