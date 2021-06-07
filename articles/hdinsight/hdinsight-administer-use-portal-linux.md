---
title: Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure
description: Saiba como criar e gerenciar clusters do Azure HDInsight usando o portal do Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 33171527f03b0cc386f683f55f6cf9a70913abdb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871207"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Usando o [portal do Azure](https://portal.azure.com), você pode gerenciar [Apache Hadoop](https://hadoop.apache.org/) clusters no Azure HDInsight. Use o seletor de guias acima para saber mais sobre como criar clusters Hadoop no HDInsight usando outras ferramentas.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster existente do Apache Hadoop no HDInsight.  Consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução

Entre em [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Listar e mostrar clusters

A página **Clusters do HDInsight** lista os clusters existentes.  Do portal:
1. Selecione **Todos os serviços** no menu esquerdo.
2. Selecione **Clusters do HDInsight** em **ANÁLISES**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Página inicial do cluster

Selecione o nome do cluster na página [**clusters HDInsight**](#showClusters) .  Isso abrirá a exibição **Visão geral**, que é semelhante à seguinte imagem:

:::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png" alt-text="Fundamentos do cluster HDInsight do portal do Azure":::

**Menu principal:**  

| Item| Descrição |
|---|---|
|Mover|Move o cluster para outro grupo de recursos ou outra assinatura.|
|Excluir|Exclui o cluster. |
|Atualizar|Atualiza a exibição.|

**Menu à esquerda:**  

  - **Menu do canto superior esquerdo**

    | Item| Descrição |
    |---|---|
    |Visão geral|Fornece informações gerais para o cluster.|
    |Log de atividades|Mostra e consulta logs de atividades.|
    |Controle de acesso (IAM)|Utiliza as atribuições de função.  Consulte [atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).|
    |Marcas|Permite estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto** e usar um valor comum para todos os serviços associados a um projeto específico.|
    |Diagnosticar e resolver problemas|Exibe informações sobre solução de problemas.|
    |Guia de Início Rápido|Exibe informações que ajudam você a começar a usar o HDInsight.|
    |Ferramentas|Informações de ajuda para ferramentas relacionadas ao HDInsight.|

  - **Menu configurações**  

    | Item| Descrição |
    |---|---|
    |Tamanho do cluster|Verifique, aumente e diminua o número de nós do trabalhador do cluster. Ver [Dimensionar clusters](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limites de cota|Exibe os núcleos usados e disponíveis para sua assinatura.|
    |SSH + logon no cluster|Mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH). Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Configura o acesso ao Data Lake Storage Gen1.  Consulte [Início rápido: Configurar clusters no HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Contas de armazenamento|Exibe as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.|
    |Aplicativos|Adicionar/remove aplicativos do HDInsight.  Consulte [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Ações de script|Execute scripts Bash no cluster. Confira [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).|
    |Metastores externos|Exiba os metastores [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). Os metastores só podem ser configurados durante o processo de criação do cluster.|
    |Parceiro HDInsight|Adiciona/remove o parceiro do HDInsight atual.|
    |Propriedades|Exibe as [propriedades do cluster](#properties).|
    |Locks|Adiciona um bloqueio para impedir que o cluster seja modificado ou excluído.|
    |Exportar modelo|Exibe e exporta o modelo do Azure Resource Manager para o cluster. No momento, você pode exportar apenas a conta de armazenamento do Azure dependente. Consulte [Criar clusters do Apache Hadoop baseados em Linux no HDInsight usando os modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu Monitoramento**

    | Item| Descrição |
    |---|---|
    |Alertas|Gerencia os alertas e ações.|
    |Métricas|Monitore as métricas de cluster em logs de Azure Monitor.|
    |Configurações de diagnóstico|Configurações sobre onde armazenar as métricas de diagnóstico.|
    |Azure Monitor|Monitore seu cluster no Azure Monitor.|

  - **Menu Suporte + solução de problemas**

    | Item| Descrição |
    |---|---|
    |Integridade de recursos|Confira [Visão geral do Azure Resource Health](../service-health/resource-health-overview.md).|
    |Nova solicitação de suporte|Permite criar um tíquete de suporte com o suporte da Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a> Propriedades do cluster

Na [página inicial do cluster](#homePage), em **Configurações**, selecione **Propriedades**.

|Item | Descrição |
|---|---|
|NOME do host|Nome do cluster.|
|URL DO CLUSTER|A URL para a interface da Web do Ambari.|
|Ponto de extremidade privado|O ponto de extremidade privado para o cluster.|
|SSH (Secure Shell)|O nome de usuário e o nome do host a ser usado para acessar o cluster por meio do SSH.|
|STATUS|Um dos: abortado, aceito, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacional, em execução, erro, exclusão, excluído, TimedOut, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.|
|REGION|Localização do Azure. Para ter acesso à lista de locais do Azure com suporte, veja a caixa de listagem suspensa **Região** em [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA DE CRIAÇÃO|A data em que o cluster foi implantado.|
|SISTEMA OPERACIONAL|**Windows** ou **Linux**.|
|TYPE|Hadoop, HBase, Storm, Spark.|
|Versão|Consulte [HDInsight versions](hdinsight-component-versioning.md) (Versões do HDInsight).|
|Versão mínima do TLS|A versão do TLS.|
|ASSINATURA|Nome da assinatura.|
|FONTE DE DADOS PADRÃO|O sistema de arquivos padrão do cluster.|
|Tamanhos dos nós de trabalho|O tamanho selecionado da VM dos nós de trabalho.|
|Tamanho de nó de cabeçalho|O tamanho selecionado da VM dos nós de cabeçalho.|
|Rede virtual|O nome da rede virtual, em que o cluster é implantado, se um tiver sido selecionado no momento da implantação.|

## <a name="move-clusters"></a>Mover clusters

Você pode mover um cluster do HDInsight para outro grupo de recursos do Azure ou outra assinatura.

Na [home page do cluster](#homePage):

1. Selecione **Mover** no menu superior.
2. Selecione **Mover para outro grupo de recursos** ou **Mover para outra assinatura**.
3. Siga as instruções da página nova.

## <a name="delete-clusters"></a>Excluir clusters

A exclusão de um cluster não exclui a conta de armazenamento padrão nem as contas de armazenamento vinculadas. Você pode recriar o cluster usando as mesmas contas de armazenamento e as mesmas metastores. Recomendamos usar um novo contêiner de Blobs padrão ao recriar o cluster.

Na [home page do cluster](#homePage):

1. Selecione **Excluir** no menu superior.
2. Siga as instruções da página nova.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Depois de criar um cluster, você pode adicionar outras contas do Armazenamento do Azure e contas do Azure Data Lake Storage. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Dimensionar clusters

O recurso de colocação em escala de clusters permite que você altere o número de nós de trabalhador usados por um cluster Azure HDInsight sem precisar recriar o cluster.

Consulte [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md) para obter informações completas.

## <a name="pauseshut-down-clusters"></a>Pausar/desligar clusters

A maioria dos trabalhos do Hadoop é composta por trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso.
Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Há várias maneiras de programar o processo:

- Use o Azure Data Factory. Consulte [Criar clusters Apache Hadoop baseados em Linux sob demanda no HDInsight usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços vinculados HDInsight sob demanda.
- Use o Azure PowerShell.  Consulte [Analisar dados de atraso de voo](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Use a CLI do Azure. Consulte [gerenciar clusters do Azure HDInsight usando o CLI do Azure](hdinsight-administer-use-command-line.md).
- Use o SDK .NET do HDInsight. Ver [trabalhos de envio do Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para saber mais sobre preços, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do Portal, veja [Excluir clusters](#delete-clusters)

## <a name="upgrade-clusters"></a>Atualizar clusters

Consulte [Atualizar o cluster do HDInsight para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a interface do usuário do Apache Ambari web

O Ambari fornece uma interface do usuário da Web de gerenciamento do Hadoop intuitiva e fácil de usar com suporte de suas APIs RESTful. O Ambari permite aos administradores do sistema gerenciar e monitorar clusters Hadoop.

Na [home page do cluster](#homePage):

1. Selecione **Painéis do cluster**.

    :::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png" alt-text="Menu de cluster do HDInsight Apache Hadoop":::

1. Selecione **Página inicial do Ambari** na página nova.
1. Insira o nome de usuário do cluster e a senha.  O nome de usuário do cluster padrão é _admin_.

Para obter mais informações, consulte [Gerenciar clusters do HDInsight usando a interface da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar senhas

Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight (conta de usuário HTTP) e a conta de usuário SSH são criadas durante o processo de criação. Você pode usar a interface do usuário do portal para alterar a senha da conta de usuário do cluster e ações de script para alterar a conta de usuário do SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a senha de usuário do cluster

> [!NOTE]  
> Alterar a senha de usuário (admin) do cluster poderá fazer as ações de script executadas em relação a esse cluster falharem. Se você tiver ações de script persistente direcionadas para nós de trabalho, esses scripts poderão falhar quando você adicionar nós ao cluster por meio de operações de redimensionamento. Para saber mais sobre as Ações de Script, confira [Personalizar clusters HDInsight usando ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

Na [home page do cluster](#homePage):
1. Selecione **SSH + login do cluster** em **Configurações**.
2. Selecione **Redefinir credencial**.
3. Insira e confirme a nova senha nas caixas de texto.
4. Selecione **OK**.

A senha é alterada em todos os nós no cluster.

### <a name="change-the-ssh-user-password-or-public-key"></a>Alterar a senha do usuário SSH ou a chave pública

1. Usando um editor de texto, salve o texto a seguir como um arquivo chamado **changecredentials.sh**.

    > [!IMPORTANT]  
    > Você deve usar um editor que usa LF como o fim da linha. Se o editor usar CRLF, então ele não funcionará.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Carregue o arquivo para um local de armazenamento que possa ser acessado no HDInsight usando um endereço HTTP ou HTTPS. Por exemplo, um repositório de arquivos público como o OneDrive ou o Armazenamento de Blobs do Azure. Salve o URI (endereço HTTP ou HTTPS) no arquivo, pois esse URI será necessário na próxima etapa.
3. No [Home Page de cluster](#homePage), selecione **ações de script** em **configurações**.
4. Na página **ações de script** , selecione **Enviar novo**.
5. Na página **Enviar ação de script** , insira as seguintes informações:

> [!NOTE]
> As senhas SSH não podem conter os seguintes caracteres:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Campo | Valor |
   | --- | --- |
   | Tipo de script | Selecione **- Personalizar** na lista suspensa.|
   | Nome |"Alterar credenciais SSH" |
   | URI do script Bash |O URI para o arquivo changecredentials.sh |
   | Tipo (s) de nó: (cabeçalho, trabalho, Nimbus, supervisor ou Zookeeper.) |✓ para todos os tipos de nó listados |
   | Parâmetros |Insira o nome de usuário SSH e a nova senha. Deve haver um espaço entre o nome de usuário e a senha. |
   | Persistir esta ação de script... |Deixe este campo desmarcado. |

6. Selecione **Criar** para aplicar o script. Quando o script for concluído, você poderá se conectar ao cluster usando SSH com as novas credenciais.

## <a name="find-the-subscription-id"></a>Localizar a ID da assinatura

Cada cluster é vinculado a uma assinatura do Azure.  A ID de assinatura do Azure está visível na [página inicial do cluster](#homePage).

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos

No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo do Azure Resource Manager. O grupo Resource Manager está visível na [página inicial do cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Localizar contas de armazenamento

Os clusters do HDInsight usam uma conta de Armazenamento do Azure ou um Azure Data Lake Storage para armazenar dados. Cada cluster do HDInsight pode ter uma conta de armazenamento padrão e um número de contas de armazenamento vinculadas. Para listar as contas de armazenamento, na [página inicial do cluster](#homePage), em **Configurações**, selecione **Contas de armazenamento**.

## <a name="monitor-jobs"></a>Monitorar trabalhos

Consulte [Gerenciar clusters do HDInsight usando a interface da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Tamanho do cluster

O bloco **tamanho do cluster** do [Home Page de cluster](#homePage) exibe o número de núcleos alocados para esse cluster e como eles são alocados para os nós dentro deste cluster.

> [!IMPORTANT]  
> Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para obter mais informações sobre como usar o Ambari, consulte [gerenciar clusters HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Conectar a um cluster

- [Usar o Apache Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Usar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu algumas funções administrativas básicas. Confira os seguintes artigos para saber mais:

- [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
- [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
- [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Detalhes sobre como usar a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Usar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
- [Usar o Apache Sqoop no HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Usar funções definidas pelo usuário (UDF) do Python com o Apache Hive e o Apache Pig no HDInsight](hadoop/python-udf-hdinsight.md)
- [Qual versão do Apache Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)