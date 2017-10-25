---
title: "Segurança do Hadoop - clusters HDInsight ingressados no domínio - Azure | Microsoft Docs"
description: Saiba como...
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/31/2016
ms.author: saurinsh
ms.openlocfilehash: 303be1d303df8074283cb1d37c74923cca80ae59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters-preview"></a>Uma introdução à segurança do Hadoop com clusters HDInsight ingressados no domínio (visualização)

O Azure HDInsight até hoje dava suporte apenas a um administrador local de usuário único. Isso funcionava bem para equipes de aplicativos ou departamentos menores. À medida que cargas de trabalho com base em Hadoop ganharam mais popularidade no setor corporativo, a necessidade de recursos de nível empresarial, como controle de acesso baseado em função, suporte a vários usuários e autenticação baseada no Active Directory tornou-se cada vez mais importante. Usando clusters de HDInsight do domínio, você pode criar um cluster HDInsight associado a um domínio do Active Directory, configurar uma lista de funcionários da empresa que podem se autenticar por meio do Azure Active Directory para fazer logon no cluster HDInsight. Qualquer pessoa fora da empresa não pode fazer logon nem acessar o cluster HDInsight. O administrador corporativo pode configurar o controle de acesso baseado em função de segurança do Hive utilizando o [Apache Ranger](http://hortonworks.com/apache/ranger/), restringindo assim o acesso a dados apenas tanto quanto necessário. Por fim, o administrador pode auditar o acesso a dados por funcionários e as alterações feitas às políticas de controle de acesso, atingindo assim um alto grau de controle de seus recursos corporativos.

> [!NOTE]
> Os novos recursos descritos nesta visualização estão disponíveis somente em clusters HDInsight baseados em Linux para carga de trabalho do Hive. As outras cargas de trabalho, como HBase, Spark, Storm e Kafka, serão habilitadas em versões futuras.

> [!IMPORTANT]
> O Oozie não está habilitado no HDInsight ingressado no domínio.

## <a name="benefits"></a>Benefícios
I Enterprise Security contém quatro grandes pilares: segurança do perímetro, autenticação, autorização e criptografia.

![Pilares de benefícios de clusters HDInsight associados ao domínio](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Segurança de Perímetro
A segurança de perímetro no HDInsight é obtida usando redes virtuais e serviço de Gateway. Hoje, um administrador de empresa pode criar um cluster HDInsight dentro de uma rede virtual e usar grupos de segurança de rede (regras de firewall de entrada ou saída) para restringir o acesso à rede virtual. Somente os endereços IP definidos nas regras de firewall de entrada serão capazes de se comunicar com o cluster HDInsight, fornecendo assim segurança de perímetro. Outra camada de segurança de perímetro é obtida com o serviço de Gateway. O Gateway é o serviço que atua como a primeira linha de defesa para qualquer solicitação de entrada para o cluster HDInsight. Ele aceita a solicitação, a valida e só então permite que a solicitação passe para os outros nós no cluster, fornecendo assim segurança de perímetro para outros nós de dados e nome no cluster.

### <a name="authentication"></a>Autenticação
Com essa visualização pública, um administrador de empresa pode provisionar um cluster HDInsight associado ao domínio, em uma [rede virtual](https://azure.microsoft.com/services/virtual-network/). Os nós do cluster HDInsight serão adicionados ao domínio gerenciado pela empresa. Isso é feito com o uso de [Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md). Todos os nós no cluster são associados a um domínio que a empresa gerencia. Com essa configuração, os funcionários da empresa podem fazer logon nos nós de cluster usando suas credenciais de domínio. Eles também podem usar suas credenciais de domínio para se autenticar em outros pontos de extremidade aprovados, como Hue, Ambari Views, ODBC, JDBC, PowerShell e APIs REST para interagir com o cluster. O administrador tem controle total sobre a limitação do número de usuários que interagem com o cluster por meio desses pontos de extremidade.

### <a name="authorization"></a>Autorização
Uma prática recomendada, seguida pela maioria das empresas, é que nem todos os funcionário têm acesso a todos os recursos da empresa. Da mesma forma, com esta versão, o administrador pode definir políticas de controle de acesso baseado em função para os recursos de cluster. Por exemplo, o administrador pode configurar o [Apache Ranger](http://hortonworks.com/apache/ranger/) para definir políticas de controle de acesso para o Hive. Essa funcionalidade assegura que os funcionários possam acessar somente os dados de que precisam para serem bem-sucedidos em seus trabalhos. O acesso a SSH no cluster também é restrito somente ao administrador.

### <a name="auditing"></a>Auditoria
Além de proteger os recursos de cluster do HDInsight contra usuários não autorizados e proteger os dados, é necessário fazer a auditoria de todo o acesso a recursos de cluster e a dados para rastrear acesso não autorizado ou não intencional aos recursos. Com essa visualização, o administrador pode exibir e relatar todo o acesso aos recursos de cluster do HDInsight e dados. O administrador também pode exibir e relatar todas as alterações nas políticas de controle de acesso feitas nos pontos de extremidade do Apache Ranger com suporte. Um cluster HDInsight associado a um domínio usa a interface do usuário familiar do Apache Ranger para pesquisar logs de auditoria. No back-end, o Ranger usa o [Apache Solr](http://hortonworks.com/apache/solr/) para armazenar e pesquisar os logs.

### <a name="encryption"></a>Criptografia
A proteção dos dados é importante para atender aos requisitos de conformidade e segurança organizacional e, além de restringir o acesso a dados de funcionários não autorizados, eles também devem ser protegidos por criptografia. Os repositórios de dados para clusters HDInsight, Blob de Armazenamento do Azure e Azure Data Lake Storage dão suporte a [criptografia de dados](../storage/common/storage-service-encryption.md) transparente do lado do servidor em repouso. A proteção de clusters HDInsight funcionará perfeitamente com essa criptografia do lado do servidor de dados em capacidade em repouso.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight associado a um domínio, confira [Configurar clusters HDInsight associados a domínio](hdinsight-domain-joined-configure.md).
* Para gerenciar um cluster HDInsight associado a um domínio, confira [Gerenciar clusters HDInsight associados a domínio](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
