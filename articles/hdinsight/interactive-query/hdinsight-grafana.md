---
title: Usar Grafana no Microsoft Azure HDInsight
description: Saiba como acessar Grafana no Microsoft Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: be804ac1aea76daf5f17e62dd97b8b57b8fdf1fb
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458816"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Acessar Grafana no Microsoft Azure HDInsight


[Grafana](https://grafana.com/) é um popular construtor de gráficos e painéis de código aberto. O Grafana é rico; não apenas permite que os usuários criem painéis personalizáveis e compartilha´veis, mas também oferece painéis de amostra/com script, integração LDAP, fontes de dados múltiplas e muito mais.

Atualmente, no Azure HDInsight, Grafana é compatível com os tipos de cluster Hbase e Consulta Interativa.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster do Apache Hadoop

Nesta seção, você criará um cluster do Interactive Query no HDInsight usando o modelo do Azure Resource Manager. Não é necessário ter experiência com o modelo do Resource Manager para acompanhar este artigo. 

1. Clique no botão **Implantar no Azure** abaixo para entrar no Azure e abra o modelo do Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Insira ou selecione os valores, conforme sugerido na captura de tela a seguir:

    > [!NOTE]  
    > Os valores fornecidos devem ser exclusivos e seguir as diretrizes de nomenclatura. O modelo não executa verificações de validação. Se os valores que você fornecer já estiverem em uso ou não seguirem as diretrizes, você receberá um erro após o envio do modelo.       
    > 
    >
    
    ![HDInsight para Linux - Introdução ao modelo do Resource Manager no portal](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Implantar o cluster Hadoop no HDInsigut usando o Portal do Azure e um modelo do gerenciador de grupo de recursos")

    Digite ou selecione os valores a seguir:
    
    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Assinatura**     |  Selecione sua assinatura do Azure. |
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione um grupo de recursos existente.  Um grupo de recursos é um contêiner de componentes do Azure.  Nesse caso, o grupo de recursos contém o cluster HDInsight e a conta de Armazenamento do Azure dependente. |
    |**Localidade**     | Selecione um local do Azure no qual você deseja criar o cluster.  Escolha um local mais próximo a você para obter melhor desempenho. |
    |**Tipo de cluster**     | Selecione **hadoop**. |
    |**Nome do cluster**     | Insira um nome para o cluster do Apache Hadoop. Como todos os clusters no HDInsight compartilham o mesmo namespace DNS esse nome precisa ser exclusivo. O nome pode ter até 59 caracteres incluindo letras, números e hifens. O primeiro e último caracteres do nome não podem ser hifens. |
    |**Nome e senha de logon do cluster**     | O nome padrão de logon é **admin**. A senha deve ter no mínimo 10 caracteres e deve conter pelo menos um dígito, uma letra maiúscula, uma minúscula e um caractere não alfanumérico (exceto os caracteres ' " ` \). **Não forneça** senhas comuns, como "Pass@word1".|
    |**Nome de usuário e senha SSH**     | O nome de usuário padrão é **sshuser**.  Você pode renomear o nome de usuário SSH.  A senha de usuário SSH tem os mesmos requisitos que a senha de logon do cluster.|
       
    Algumas propriedades foram inseridas no código do modelo.  Você pode configurar esses valores do modelo. Para obter mais explicações sobre essas propriedades, consulte [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selecione **Concordo com os termos e condições declarados acima** e **Fixar no painel**, depois selecione **Comprar**. Você verá um novo bloco chamado **Enviando a implantação** no painel do portal. Demora cerca de 20 minutos para criar um cluster.

    ![Progresso da implantação de modelo](./media/hdinsight-grafana/deployment-progress-tile.png "Progresso da implantação de modelo do Azure")

4. Depois que o cluster for criado, a legenda do bloco será alterada para o nome do grupo de recursos especificado. O bloco também lista o cluster HDInsight criado dentro do grupo de recursos. 
   
    ![Grupo de recursos de introdução ao HDInsight para Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de cluster do Azure HDInsight")
    
5. O bloco também lista o armazenamento padrão associado ao cluster. Cada cluster tem uma dependência na [conta de Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Ela é conhecida como a conta de armazenamento padrão. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocados na mesma região do Azure. A exclusão dos clusters não exclui a conta de armazenamento.
    

> [!NOTE]  
> Para outros métodos de criação do cluster e Noções básicas sobre as propriedades usadas neste artigo, consulte [clusters do HDInsight criar](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Acessar o painel do Grafana

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Clusters HDInsight** e, em seguida, selecione o cluster que você criou na última seção.

3. Em **Links Rápidos**, clique em **Painel do Cluster**.

    ![Portal do painel do cluster HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Painel do cluster HDInsight no portal")

4. No painel, clique no bloco **Grafana**. Como alternativa, navegue até o caminho `/grafana/` da URL do cluster. Por exemplo: `https://<clustername>.azurehdinsight.net/grafana/`.

5. Insira as credenciais de usuário do cluster Hadoop.

6. A aparência do painel do Grafana é semelhante a este exemplo:

    ![Painel do Grafana HDInsight](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel do Grafana HDInsight")

   

## <a name="clean-up-resources"></a>Limpar recursos
Após a conclusão do artigo, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. 

> [!NOTE]  
> Se você for prosseguir *imediatamente* no próximo tutorial para saber como executar operações de ETL usando Hadoop no HDInsight, convém manter o cluster em execução. Isso porque, no tutorial, você precisa criar um cluster Hadoop novamente. No entanto, se você não for conferir o próximo tutorial imediatamente, exclua o cluster agora.

**Para excluir o cluster e/ou a conta de armazenamento padrão**

1. Volte para a guia do navegador onde você tem o portal do Azure. Você deve estar na página de visão geral do cluster. Se você quiser apenas excluir o cluster, mas manter a conta de armazenamento padrão, selecione **Excluir**.

    ![Excluir o cluster HDInsight](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Excluir o cluster HDInsight")

2. Se você quiser excluir o cluster, bem como a conta de armazenamento padrão, selecione o nome do grupo de recursos (realçado na captura de tela anterior) para abrir a página do grupo de recursos.

3. Selecione **Excluir grupo de recursos** para excluir o grupo de recursos, que contém o cluster e a conta de armazenamento padrão. Observe que a exclusão do grupo de recursos exclui a conta de armazenamento. Para manter a conta de armazenamento, exclua apenas o cluster.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight baseado em Linux usando um modelo do Resource Manager e como realizar consultas básicas do Apache Hive. No próximo artigo, saiba como executar uma operação de ETL (extração, transformação e carregamento) usando o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Se você estiver pronto para começar a trabalhar com seus próprios dados e precisa saber mais sobre como o HDInsight armazena dados ou obtém dados no HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight](../hdinsight-upload-data.md).

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como usar o Hive com o HDInsight, incluindo como executar consultas do Hive no Visual Studio, consulte [Usar o Apache Hive com o HDInsight](../hdinsight-use-hive.md).
* Para aprender sobre o Pig, uma linguagem usada para transformar dados, consulte [Use o Apache Pig com o HDInsight](../hdinsight-use-pig.md).
* Para saber mais sobre o MapReduce, uma maneira de gravar programas que processam dados no Hadoop, consulte [Usar o MapReduce com HDInsight](../hdinsight-use-mapreduce.md).
* Para saber mais sobre como usar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Introdução às ferramentas do Hadoop do Visual Studio para o HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Se você quiser saber mais sobre como criar ou gerenciar um cluster HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, consulte [Gerenciar clusters HDInsight usando o Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que você pode selecionar ao criar um cluster HDInsight, confira [Como criar o HDInsight no Linux usando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
