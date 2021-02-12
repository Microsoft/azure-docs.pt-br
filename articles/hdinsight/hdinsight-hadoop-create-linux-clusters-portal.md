---
title: Criar clusters Apache Hadoop usando o navegador da Web, Azure HDInsight
description: Aprenda a criar clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no HDInsight. Use o navegador da Web e o portal do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: c68d342cf21d69fa97ba3d5171ba596662fd845f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945810"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá como criar clusters Azure HDInsight baseados no Linux usando o portal. Detalhes adicionais estão disponíveis em [Criar clusters HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, consulte [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-clusters"></a>Criar clusters

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu superior, selecione **+ Criar um recurso**.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Criando um novo cluster no portal do Azure")

1. Selecione **Análise** > **Azure HDInsight** para acessar a página **Criar cluster HDInsight**.

## <a name="basics"></a>Noções básicas

![Conceitos básicos de criação de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Criando um novo cluster no portal do Azure")

Na guia **Informações Básicas**, forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Subscription|Na lista suspensa, selecione a assinatura do Azure usada para o cluster.|
|Resource group|Na lista suspensa, selecione o grupo de recursos existente ou selecione **Criar**.|
|Nome do cluster|Insira um nome global exclusivo.|
|Região|Na lista suspensa, selecione uma região em que o cluster foi criado.|
|Tipo de cluster|Clique em **Selecionar tipo de cluster** para abrir uma lista. Na lista, selecione o tipo de cluster desejado. Os clusters HDInsight são fornecidos em tipos diferentes. Eles correspondem à carga de trabalho ou tecnologia para a qual o cluster está sintonizado. Não há nenhum método com suporte para criar um cluster que combine vários tipos.|
|Versão|Na lista suspensa, selecione uma **versão**. Use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).|
|Nome de usuário de logon do cluster|Forneça o nome de usuário, o padrão é **admin**.|
|Senha de logon do cluster|Forneça a senha.|
|Confirmar senha de logon do cluster|Insira a senha novamente|
|Nome de usuário do Secure Shell (SSH)|Forneça o nome de usuário, o padrão é **sshuser**|
|Usar senha de logon do cluster para SSH|Se você quiser a mesma senha SSH que a senha de administrador especificada anteriormente, marque a caixa de seleção **usar senha de logon do cluster para SSH** . Caso contrário, forneça uma **SENHA** ou **CHAVE PÚBLICA** para autenticar o usuário SSH. Uma chave pública é a abordagem recomendada. Escolha **Selecionar** na parte inferior para salvar a configuração das credenciais.  Para saber mais, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selecione **Avançar: >>de armazenamento** para avançar para a próxima guia.

## <a name="storage"></a>Armazenamento

> [!WARNING] 
> A partir de 15 de junho, 2020 clientes não poderão criar uma nova entidade de serviço usando o HDInsight. Consulte [criar entidade de serviço e certificados](../active-directory/develop/howto-create-service-principal-portal.md) usando Azure Active Directory.

![HDInsight criar armazenamento de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Criando um novo cluster no portal do Azure-Storage")

### <a name="primary-storage"></a>Armazenamento primário

Na lista suspensa para **tipo de armazenamento primário**, selecione o tipo de armazenamento padrão. Os campos posteriores a serem concluídos variarão com base na sua seleção. Para o **armazenamento do Azure**:

1. Para o **método de seleção**, escolha **selecionar na lista** ou **usar a tecla de acesso**.
    * Para **selecionar na lista**, selecione sua **conta de armazenamento principal** na lista suspensa ou selecione **criar nova**.
    * Para **usar chave de acesso**, insira o **nome da conta de armazenamento**. Em seguida, forneça a **chave de acesso**.

1. Para **contêiner**, aceite o valor padrão ou insira um novo.

### <a name="additional-azure-storage"></a>Armazenamento do Azure adicional

Opcional: selecione **Adicionar armazenamento do Azure** para armazenamento de cluster adicional. Não há suporte para o uso de uma conta de armazenamento adicional em uma região diferente do cluster HDInsight.

### <a name="metastore-settings"></a>Configurações de metastore

Opcional: especifique um banco de dados SQL existente para salvar os metadados Apache Hive, Apache Oozie e, ou Apache Ambari fora do cluster. O banco de dados SQL do Azure que é usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. Ao criar um metastore, não use traços nem hifens para nomear um banco de dados. Esses caracteres podem causar falha no processo de criação do cluster.

> [!IMPORTANT]
> Para formas de cluster que dão suporte a metastores, o metastore padrão fornece um banco de dados SQL do Azure com um **limite de DTU básico de camada 5 (não atualizável)**! Adequado para fins de teste básico. Para cargas de trabalho grandes ou de produção, é recomendável migrar para um metastore externo.

Selecione **Avançar: segurança + rede >>** para avançar para a próxima guia.

## <a name="security--networking"></a>Segurança + rede

![HDInsight criar rede de segurança de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight criar rede de segurança de cluster")

Na guia **segurança + rede** , forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Pacote de segurança empresarial|Opcional: marque a caixa de seleção para usar **Enterprise Security Package**. Para obter mais informações, consulte [configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcional: selecione uma versão de TLS na lista suspensa. Para obter mais informações, confira [Protocolo TLS](./transport-layer-security.md).|
|Rede virtual|Opcional: selecione uma rede virtual e uma sub-rede existentes na lista suspensa. Para obter informações, consulte [planejar uma implantação de rede virtual para clusters do Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos de configuração específicos para a rede virtual.|
|Configurações de criptografia de disco|Opcional: marque a caixa de seleção para usar criptografia. Para obter mais informações, confira [Criptografia de disco de chave gerenciada pelo cliente](./disk-encryption.md).|
|Proxy REST do Kafka|Essa configuração só está disponível para o tipo de cluster Kafka. Para obter mais informações, consulte [usando um proxy REST](./kafka/rest-proxy.md).|
|Identidade|Opcional: selecione uma identidade de serviço atribuída pelo usuário existente na lista suspensa. Para obter mais informações, consulte [identidades gerenciadas no Azure HDInsight](./hdinsight-managed-identities.md).|

Selecione **Avançar: configuração + preço >>** para avançar para a próxima guia.

## <a name="configuration--pricing"></a>Configuração + preços

![Criar configuração de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Guia configuração e preços")

Na guia **configuração + preços** , forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|+ Adicionar aplicativo|Opcional: selecione os aplicativos desejados. Você, a Microsoft ou os fornecedores independentes de software (ISVs)  podem desenvolver esses aplicativos. Para obter mais informações, consulte [Instalar aplicativos durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Tamanho do nó|Opcional: selecione um nó de tamanho diferente.|
|Número de nós|Opcional: Insira o número de nós para o tipo de nó especificado. Se você planeja mais de 32 nós de trabalho, selecione um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM. Planeje os nós na criação de cluster ou, dimensionando o cluster após a criação.|
|Habilitar dimensionamento automático|Opcional: marque a caixa de seleção para habilitar o recurso. Para obter mais informações, consulte [dimensionar automaticamente clusters do Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Adicionar ação de script|Opcional: essa opção funcionará se você quiser usar um script personalizado para personalizar um cluster, pois o cluster está sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).|

Selecione **examinar + criar >>** para validar a configuração do cluster e avançar para a guia final.

## <a name="review--create"></a>Examinar + criar

![Resumo de criação de cluster do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Especificar o número de nós de cluster")

Examine as configurações. Selecione **Criar** para criar o cluster.

Levará algum tempo para que o cluster seja criado, geralmente, cerca de 20 minutos. Monitore as **Notificações** para conferir o processo de provisionamento.

## <a name="post-creation"></a>Pós-criação

Após o término do processo de criação, selecione **Ir para Recurso** na notificação **Implantação com êxito**. A janela do cluster fornece as informações a seguir.

![Visão geral do cluster de portal do Azure de HDI](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")

Alguns dos ícones na janela são explicados da seguinte maneira:

|Propriedade | Descrição |
|---|---|
|Visão geral|Fornece todas as informações essenciais sobre o cluster. Exemplos são o nome, o grupo de recursos ao qual ele pertence, a localização, o sistema operacional e a URL do painel do cluster.|
|Painéis de cluster|Direciona você para o portal do Ambari associado ao cluster.|
|SSH + logon no cluster|Fornece as informações necessárias para acessar o cluster usando o SSH.|
|Excluir|exclui o cluster HDInsight.|

## <a name="delete-the-cluster"></a>Excluir o cluster

Consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)