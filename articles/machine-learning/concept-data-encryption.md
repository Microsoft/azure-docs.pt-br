---
title: Criptografia de dados com o Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning computações e armazenamentos de dados fornece criptografia de dados em repouso e em trânsito.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 211ef9571b5a126686b4583330dc0f80863fd47e
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992039"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Criptografia de dados com Azure Machine Learning

O Azure Machine Learning usa uma variedade de serviços de armazenamento de dados do Azure e recursos de computação ao treinar modelos e executar a inferência. Cada um deles tem sua própria história sobre como eles fornecem criptografia para dados em repouso e em trânsito. Neste artigo, saiba mais sobre cada um e o que é melhor para seu cenário.

> [!IMPORTANT]
> Para a criptografia de nível de produção durante o __treinamento__, a Microsoft recomenda usar Azure Machine Learning cluster de computação. Para a criptografia de nível de produção durante a __inferência__, a Microsoft recomenda usar o serviço kubernetes do Azure.
>
> Azure Machine Learning instância de computação é um ambiente de desenvolvimento/teste. Ao usá-lo, recomendamos que você armazene seus arquivos, como blocos de anotações e scripts, em um compartilhamento de arquivos. Seus dados devem ser armazenados em um datastore.

## <a name="encryption-at-rest"></a>Criptografia em repouso

> [!IMPORTANT]
> Caso seu workspace contenha dados confidenciais, recomendamos definir o [sinalizador hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) ao criar seu workspace. O `hbi_workspace` sinalizador só pode ser definido quando um espaço de trabalho é criado. Ele não pode ser alterado para um espaço de trabalho existente.

O `hbi_workspace` sinalizador controla a quantidade de [dados que a Microsoft coleta para fins de diagnóstico](#microsoft-collected-data) e habilita a [criptografia adicional em ambientes gerenciados pela Microsoft](../security/fundamentals/encryption-atrest.md). Além disso, ele habilita as seguintes ações:

* Inicia a criptografia do disco de rascunho local em seu Azure Machine Learning cluster de computação, desde que você não tenha criado nenhum cluster anterior nessa assinatura. De outro modo, você precisará gerar um tíquete de suporte para habilitar a criptografia do disco de rascunho dos seus clusters de computação 
* Limpa o disco de rascunho local entre as execuções
* Passa com segurança credenciais para sua conta de armazenamento, registro de contêiner e conta SSH da camada de execução para seus clusters de computação usando o cofre de chaves
* Habilita a filtragem de IP para garantir que os pools de lote subjacentes não possam ser chamados por nenhum serviço externo que não seja o AzureMachineLearningService
* Observe que não há suporte para instâncias de computação no espaço de trabalho Ain

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Azure Machine Learning armazena instantâneos, a saída e logs na conta de armazenamento de blobs do Azure que está vinculada ao workspace do Azure Machine Learning e à sua assinatura. Todos os dados armazenados no armazenamento de blobs do Azure são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para obter informações sobre como usar suas próprias chaves para dados armazenados no armazenamento de blobs do Azure, consulte [Criptografia do Armazenamento do Microsoft Azure com chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Geralmente, os dados de treinamento também são armazenados no armazenamento de blobs do Azure para que sejam acessíveis ao treinamento de destinos de computação. Esse armazenamento não é gerenciado pelo Azure Machine Learning, ele é montado em destinos de computação como um sistema de arquivos remoto.

Caso precise __girar ou revogar__ sua chave, você poderá fazer isso a qualquer momento. Ao girar uma chave, a conta de armazenamento será iniciada por meio de uma nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desabilitar) uma chave, a conta de armazenamento cuida das solicitações com falha. Geralmente, leva uma hora para que a rotação ou a revogação entrem em vigor.

Para obter informações sobre como regenerar as chaves de acesso, consulte [Regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning armazena metadados em uma instância de Azure Cosmos DB. Essa instância é associada a uma assinatura da Microsoft gerenciada pelo Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar a instância do Azure Cosmos DB, você pode criar uma instância do Cosmos DB dedicada para usar no seu workspace. Recomendamos essa abordagem caso você queira armazenar seus dados, como informações de histórico de execução, fora da instância de Cosmos DB multilocatária hospedada em nossa assinatura da Microsoft. 

Para habilitar o provisionamento de uma instância do Cosmos DB em sua assinatura com chaves gerenciadas pelo cliente, execute as seguintes ações:

* Registre os provedores de recursos Microsoft. MachineLearning e Microsoft.DocumentDB em sua assinatura, se ainda não tiver feito isso.

* Use os parâmetros a seguir ao criar o workspace do Azure Machine Learning. Ambos os parâmetros são obrigatórios e têm suporte para SDK, CLI, APIs REST e modelos do Resource Manager.

    * `resource_cmk_uri`: Esse parâmetro é o URI do recurso completo da chave gerenciada pelo cliente no cofre de chaves, incluindo as [informações de versão para a chave](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Esse parâmetro é a ID do recurso do cofre de chaves em sua assinatura. O cofre de chaves precisa estar na mesma região e assinatura que você usará para o workspace do Azure Machine Learning. 
    
        > [!NOTE]
        > Essa instância do cofre de chaves pode ser diferente do cofre de chaves criado pelo Azure Machine Learning quando você provisionar o workspace. Caso queira usar a mesma instância do cofre de chaves para o workspace, passe o mesmo cofre de chaves ao provisionar o workspace usando o [parâmetro key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Caso precise __girar ou revogar__ sua chave, você poderá fazer isso a qualquer momento. Ao girar uma chave, o Cosmos DB será iniciado por meio de uma nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desabilitar) uma chave, o Cosmos DB cuida das solicitações com falha. Geralmente, leva uma hora para que a rotação ou a revogação entrem em vigor.

Para obter mais informações sobre chaves gerenciadas pelo cliente com o Cosmos DB, consulte [Configurar chaves gerenciadas pelo cliente para sua conta do Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Todas as imagens de contêiner no seu registro (Registro de Contêiner do Azure) são criptografadas em repouso. O Azure criptografa uma imagem automaticamente antes de armazená-la e a descriptografa quando o Azure Machine Learning efetua o pull da imagem.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar o Registro de Contêiner do Azure, você precisa criar seu próprio ACR e anexá-lo enquanto estiver provisionando o workspace ou criptografar a instância padrão que é criada no momento do provisionamento do workspace.

> [!IMPORTANT]
> Azure Machine Learning requer que a conta de administrador seja habilitada no registro de contêiner do Azure. Por padrão, essa configuração é desabilitada quando você cria um registro de contêiner. Para obter informações sobre como habilitar a conta de administrador, consulte [conta de administrador](../container-registry/container-registry-authentication.md#admin-account).
>
> Após a criação de um Registro de Contêiner do Azure para um workspace, não o exclua. Fazer isso interromperá o workspace do Azure Machine Learning.

Para obter um exemplo de como criar um workspace usando um Registro de Contêiner do Azure existente, consulte os seguintes artigos:

* [Criar um workspace para o Azure Machine Learning com a CLI do Azure](how-to-manage-workspace-cli.md).
* [Crie um espaço de trabalho com o SDK do Python](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Usar um modelo do Azure Resource Manager para criar um workspace para o Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instance

Você pode criptografar um recurso da ACI (Instância de Contêiner do Azure) implantada usando chaves gerenciadas pelo cliente. A chave gerenciada pelo cliente usada para a ACI pode ser armazenada no Azure Key Vault para seu workspace. Para obter informações sobre como gerar uma chave, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para usar a chave ao implantar um modelo na ACI, crie uma nova configuração de implantação usando `AciWebservice.deploy_configuration()`. Forneça as informações da chave usando os seguintes parâmetros:

* `cmk_vault_base_url`: a URL do cofre de chaves que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: a versão da chave.

Para obter mais informações sobre como criar e usar uma configuração de implantação, consulte os seguintes artigos:

* Referência [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Onde e como implantar](how-to-deploy-and-where.md)
* [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre como usar uma chave gerenciada pelo cliente com a ACI, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Você pode criptografar um recurso do Serviço de Kubernetes do Azure implantada usando chaves gerenciadas pelo cliente. Para obter mais informações, consulte [Traga suas próprias chaves com o Serviço de Kubernetes do Azure](../aks/azure-disk-customer-managed-keys.md). 

Esse processo permite que você criptografe os dados e o disco do SO das máquinas virtuais implantadas no cluster do Kubernetes.

> [!IMPORTANT]
> Esse processo funciona apenas com o AKS K8s versão 1.17 ou superior. O Azure Machine Learning agora tem suporte para o AKS 1.17 em 13 de janeiro de 2020.

### <a name="machine-learning-compute"></a>Computação do Machine Learning

O disco do SO para cada nó de computação armazenado no Armazenamento do Microsoft Azure é criptografado com chaves gerenciadas pela Microsoft em contas de armazenamento do Azure Machine Learning. Esse destino de computação é efêmero e, normalmente, os clusters são reduzidos quando não há execuções na fila. A máquina virtual subjacente é desprovisionada e o disco do SO é excluído. Não há suporte para o Azure Disk Encryption do disco do SO.

Cada máquina virtual também tem um disco temporário local para operações do SO. Caso deseje, você pode usar o disco para preparar os dados de treinamento. O disco é criptografado por padrão para workspaces com o parâmetro `hbi_workspace` definido como `TRUE`. Esse ambiente tem uma curta duração e apenas durante a execução, e o suporte à criptografia é limitado apenas a chaves gerenciadas pelo sistema.

### <a name="azure-databricks"></a>Azure Databricks

O Azure Databricks pode ser usado em pipelines de Machine Learning. Por padrão, o DBFS (Sistema de Arquivos do Databricks) usado pelo Azure Databricks é criptografado usando uma chave gerenciada pela Microsoft. Para configurar o Azure Databricks para usar chaves gerenciadas pelo cliente, consulte [Configurar chaves gerenciadas pelo cliente no DBFS padrão (raiz)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao usar serviços como o Machine Learning Automatizado, a Microsoft pode gerar dados temporários e pré-processados para treinar vários modelos. Esses dados são armazenados em um datastore em seu workspace, o que permite que você aplique controles de acesso e criptografia adequadamente.

Talvez também seja interessante criptografar [informações de diagnóstico registradas do seu ponto de extremidade implantado](how-to-enable-app-insights.md) em sua instância do Azure Application Insights.

## <a name="encryption-in-transit"></a>Criptografia em trânsito

O Azure Machine Learning usa o TLS para proteger a comunicação interna entre vários microsserviços do Azure Machine Learning. Todo o acesso ao Armazenamento do Microsoft Azure também ocorre em um canal seguro.

Para proteger chamadas externas feitas ao ponto de extremidade de pontuação, Azure Machine Learning usa TLS. Para obter mais informações, consulte [Usar o TLS para proteger um serviço Web por meio do Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Coleta e tratamento de dados

### <a name="microsoft-collected-data"></a>Dados coletados da Microsoft

A Microsoft pode coletar informações de identificação que não são de usuário, como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome do experimento de machine learning) ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos esses dados são armazenados usando chaves gerenciadas pela Microsoft no armazenamento hospedado em assinaturas de propriedade da Microsoft e seguem [padrões de tratamento de dados e políticas de privacidade padrão da Microsoft](https://privacy.microsoft.com/privacystatement).

A Microsoft também recomenda não armazenar informações confidenciais (como segredos de chave de conta) em variáveis de ambiente. As variáveis de ambiente são registradas, criptografadas e armazenadas pela Microsoft. De modo semelhante, ao nomear a [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py), evite incluir informações confidenciais, como nomes de usuário ou nomes de projetos secretos. Essas informações podem ser exibidas nos logs de telemetria acessíveis a engenheiros de Suporte da Microsoft.

Para recusar os dados de diagnóstico sendo coletados, defina o parâmetro `hbi_workspace` como `TRUE` ao provisionar o workspace. Essa funcionalidade tem suporte ao usar o SDK do Python do AzureML, a CLI, as APIs REST ou os modelos do Azure Resource Manager.

## <a name="using-azure-key-vault"></a>Como usar o Azure Key Vault

O Azure Machine Learning usa a instância do Azure Key Vault associada ao workspace para armazenar credenciais de vários tipos:

* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do Repositório do Contêiner do Azure
* Cadeias de conexão para armazenamento de dados

As senhas e chaves SSH para computar destinos como o Azure HDInsight e VMs são armazenadas em um cofre de chaves separado associado à assinatura da Microsoft. O Azure Machine Learning não armazena nenhuma senha ou chave fornecida por usuários. Em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para conectar às VMs e ao HDInsight para executar os experimentos.

Cada workspace tem uma identidade gerenciada associada atribuída pelo sistema que tem o mesmo nome do workspace. Essa identidade gerenciada tem acesso a todas as chaves, segredos e certificados presentes no cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se ao armazenamento do Azure](how-to-access-data.md)
* [Obter dados de um armazenamento de dados](how-to-create-register-datasets.md)
* [Conectar aos dados](how-to-connect-data-ui.md)
* [Treinar com conjuntos de dados](how-to-train-with-datasets.md)