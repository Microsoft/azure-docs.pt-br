---
title: Solucionar problemas do Azure Data Factory | Microsoft Docs
description: Saiba como solucionar problemas de atividades de controle externo no Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382796"
---
# <a name="troubleshoot-azure-data-factory"></a>Solucionar problemas do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para atividades de controle externo no Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Conector e atividade de cópia

Para problemas de conector, como encontrar um erro ao usar a atividade de cópia, veja [Solucionar problemas de conectores do Azure Data Factory](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Código de erro: 3200

- **Mensagem**: Erro 403.

- **Causa**: `The Databricks access token has expired.`

- **Recomendação**: Por padrão, o token de acesso do Azure Databricks é válido por 90 dias. Crie um token e atualize o serviço vinculado.

### <a name="error-code-3201"></a>Código de erro: 3201

- **Mensagem**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa**: `Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: Especifique o caminho do notebook na atividade do Databricks.

<br/> 

- **Mensagem**: `Cluster... does not exist.`

- **Causa**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se o cluster do Databricks existe.

<br/> 

- **Mensagem**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Especifique caminhos absolutos para esquemas de endereçamento de workspace ou `dbfs:/folder/subfolder/foo.py` para arquivos armazenados no DFS (Sistema de Arquivos do Databricks).

<br/> 

- **Mensagem**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Verifique a [definição de serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Mensagem**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Verifique a [definição de serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Mensagem**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Veja a mensagem de erro.

<br/>

### <a name="error-code-3202"></a>Código de erro: 3202

- **Mensagem**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Causa**: `Too many Databricks runs in an hour.`

- **Recomendação**: Verifique todos os pipelines que usam este workspace do Databricks para saber a taxa de criação de trabalho. Se os pipelines inicializaram muitas execuções do Databricks em agregação, migre alguns pipelines para um novo workspace.

<br/> 

- **Mensagem**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa**: `Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o JSON do pipeline e verifique se todos os parâmetros no notebook baseParameters especificam um valor não vazio.

<br/> 

- **Mensagem**: `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **Causa**: O usuário que gerou o token de acesso não tem permissão para acessar o cluster do Databricks especificado no serviço vinculado.

- **Recomendação**: Verifique se o usuário tem as permissões necessárias no workspace.

### <a name="error-code-3203"></a>Código de erro: 3203

- **Mensagem**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa**: O cluster foi encerrado. Para clusters interativos, esse problema pode ser uma condição de corrida.

- **Recomendação**: Para evitar esse erro, use clusters de trabalho.

### <a name="error-code-3204"></a>Código de erro: 3204

- **Mensagem**: `Job execution failed.`

- **Causa**: Mensagens de erro indicam vários problemas, como um estado de cluster inesperado ou uma atividade específica. Geralmente, nenhuma mensagem de erro é exibida.

- **Recomendação**: N/D

### <a name="error-code-3208"></a>Código de erro: 3208

- **Mensagem**: `An error occurred while sending the request.`

- **Causa**: a conexão de rede com o serviço databricks foi interrompida.

- **Recomendação**: se você estiver usando um tempo de execução de integração auto-hospedado, verifique se a conexão de rede é confiável dos nós do tempo de execução de integração. Se você estiver usando o tempo de execução de integração do Azure, a nova tentativa normalmente funcionará.
 
## <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake

A tabela a seguir se aplica ao U-SQL.
 
### <a name="error-code-2709"></a>Código de erro: 2709

- **Mensagem**: `The access token is from the wrong tenant.`

- **Causa**: Locatário do Azure AD (Azure Active Directory) incorreto.

- **Recomendação**: Locatário do Azure AD (Azure Active Directory) incorreto.

<br/>

- **Mensagem**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Causa**: Esse erro é causado pela limitação no Data Lake Analytics.

- **Recomendação**: Reduza o número de trabalhos enviados ao Data Lake Analytics. Altere os gatilhos do Data Factory e as configurações de simultaneidade nas atividades ou aumente os limites no Data Lake Analytics.

<br/> 

- **Mensagem**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: Esse erro é causado pela limitação no Data Lake Analytics.

- **Recomendação**: Reduza o número de trabalhos enviados ao Data Lake Analytics. Altere os gatilhos do Data Factory e as configurações de simultaneidade nas atividades ou aumente os limites no Data Lake Analytics.

### <a name="error-code-2705"></a>Código de erro: 2705

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário forneceu para trabalhos do Data Lake Analytics tem acesso à conta do Data Lake Analytics e à instância do Data Lake Storage padrão na pasta raiz.

### <a name="error-code-2711"></a>Código de erro: 2711

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário forneceu para trabalhos do Data Lake Analytics tem acesso à conta do Data Lake Analytics e à instância do Data Lake Storage padrão na pasta raiz.

<br/> 

- **Mensagem**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**: O caminho para o arquivo U-SQL está errado ou as credenciais do serviço vinculado não têm acesso.

- **Recomendação**: Verifique o caminho e as credenciais fornecidas no serviço vinculado.

### <a name="error-code-2704"></a>Código de erro: 2704

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário forneceu para trabalhos do Data Lake Analytics tem acesso à conta do Data Lake Analytics e à instância do Data Lake Storage padrão na pasta raiz.

### <a name="error-code-2707"></a>Código de erro: 2707

- **Mensagem**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Causa**: A conta do Data Lake Analytics no serviço vinculado está incorreta.

- **Recomendação**: Verifique se a conta certa foi fornecida.

### <a name="error-code-2703"></a>Código de erro: 2703

- **Mensagem**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Causa**: O erro é do Data Lake Analytics.

- **Recomendação**: O trabalho que foi enviado para o Data Lake Analytics falhou bem como o script no Data Lake Analytics. Investigue no Data Lake Analytics. No portal, acesse a conta do Data Lake Analytics e procure o trabalho usando a ID da execução de atividade do Data Factory (não use a ID de execução do pipeline). O trabalho fornece mais informações sobre o erro e o ajudará a solucionar problemas.

   Se a resolução não estiver clara, entre em contato com a equipe de suporte do Data Lake Analytics e forneça a URL (localizador de recursos universal) do trabalho, que inclui o nome da conta e a ID do trabalho.
 
## <a name="azure-functions"></a>Azure Functions

### <a name="error-code-3602"></a>Código de erro: 3602

- **Mensagem**: `Invalid HttpMethod: '%method;'.`

- **Causa**: O Httpmethod especificado no conteúdo da atividade não compatível com a atividade de função do Azure.

- **Recomendação**: Os Httpmethods compatíveis são: PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.

### <a name="error-code-3603"></a>Código de erro: 3603

- **Mensagem**: `Response Content is not a valid JObject.`

- **Causa**: A função do Azure que foi chamada não retornou um conteúdo JSON na resposta. A atividade da função do Azure do ADF (Azure Data Factory) só é compatível com conteúdo de resposta JSON.

- **Recomendação**: Atualize a função do Azure para retornar um conteúdo JSON válido, assim como uma função de C# pode retornar `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Código de erro: 3606

- **Mensagem**: Chave de função ausente na atividade de função do Azure.

- **Causa**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade de entrada da função do Azure tem uma propriedade chamada `functionKey`.

### <a name="error-code-3607"></a>Código de erro: 3607

- **Mensagem**: `Azure function activity missing function name.`

- **Causa**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade de entrada da função do Azure tem uma propriedade chamada `functionName`.

### <a name="error-code-3608"></a>Código de erro: 3608

- **Mensagem**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa**: Os detalhes da função do Azure na definição da atividade podem estar incorretos.

- **Recomendação**: Corrija os detalhes da função do Azure e tente novamente.

### <a name="error-code-3609"></a>Código de erro: 3609

- **Mensagem**: `Azure function activity missing functionAppUrl.`

- **Causa**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade de entrada da Função do Azure tem uma propriedade chamada `functionAppUrl`.

### <a name="error-code-3610"></a>Código de erro: 3610

- **Mensagem**: `There was an error while calling endpoint.`

- **Causa**: A URL da função pode estar incorreta.

- **Recomendação**: Verifique se o valor de `functionAppUrl` na atividade JSON está correto e tente novamente.

### <a name="error-code-3611"></a>Código de erro: 3611

- **Mensagem**: `Azure function activity missing Method in JSON.`

- **Causa**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade de entrada da função do Azure tem uma propriedade chamada `method`.

### <a name="error-code-3612"></a>Código de erro: 3612

- **Mensagem**: `Azure function activity missing LinkedService definition in JSON.`

- **Causa**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade de entrada da função do Azure tem detalhes do serviço vinculado.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Código de erro: 4101

- **Mensagem**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa**: Formato inadequado ou definição da propriedade `%propertyName;` ausente.

- **Recomendação**: Verifique se a atividade `%activityName;` tem a propriedade `%propertyName;` definida com os dados corretos.

### <a name="error-code-4110"></a>Código de erro: 4110

- **Mensagem**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa**: A definição da atividade AzureMLExecutePipeline não está completa.

- **Recomendação**: Verifique se a definição JSON da atividade AzureMLExecutePipeline de entrada tem detalhes do serviço vinculados corretamente.

### <a name="error-code-4111"></a>Código de erro: 4111

- **Mensagem**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: Definição de atividade incorreta.

- **Recomendação**: Verifique se a definição JSON da atividade AzureMLExecutePipeline de entrada tem detalhes do serviço vinculados corretamente.

### <a name="error-code-4112"></a>Código de erro: 4112

- **Mensagem**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa**: Formato inadequado ou definição da propriedade '%propertyName;' ausente.

- **Recomendação**: Verifique se o serviço vinculado tem a propriedade `%propertyName;` definida com os dados corretos.

### <a name="error-code-4121"></a>Código de erro: 4121

- **Mensagem**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A Credencial usada para acessar o Azure Machine Learning expirou.

- **Recomendação**: Verifique se a credencial está válida e tente novamente.

### <a name="error-code-4122"></a>Código de erro: 4122

- **Mensagem**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial fornecida no serviço vinculado do Azure Machine Learning está inválida ou não tem permissão para a operação.

- **Recomendação**: Verifique se a credencial no serviço vinculado está válida e tem permissão para acessar o Azure Machine Learning.

### <a name="error-code-4123"></a>Código de erro: 4123

- **Mensagem**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: As propriedades da atividade, como `pipelineParameters`, são inválidas para o pipeline do Azure ML (Machine Learning).

- **Recomendação**: Verifique se o valor das propriedades da atividade corresponde ao conteúdo esperado do pipeline do Azure ML publicado especificado no serviço vinculado.

### <a name="error-code-4124"></a>Código de erro: 4124

- **Mensagem**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: O ponto de extremidade do pipeline do Azure ML publicado não existe.

- **Recomendação**: Verifique se o ponto de extremidade do pipeline de Azure Machine Learning publicado especificado no serviço vinculado existe no Azure Machine Learning.

### <a name="error-code-4125"></a>Código de erro: 4125

- **Mensagem**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: Há um erro de servidor no Azure Machine Learning.

- **Recomendação**: Tente novamente mais tarde. Contate a equipe do Azure Machine Learning para obter ajuda se o problema continuar.

### <a name="error-code-4126"></a>Código de erro: 4126

- **Mensagem**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa**: Falha na execução do pipeline do Azure ML.

- **Recomendação**: Verifique se há mais logs de erros no Azure Machine Learning e, em seguida, corrija o pipeline de ML.

## <a name="common"></a>Comum

### <a name="error-code-2103"></a>Código de erro: 2103

- **Mensagem**: `Please provide value for the required property '%propertyName;'.`

- **Causa**: O valor necessário para a propriedade não foi fornecido.

- **Recomendação**: Forneça o valor da mensagem e tente novamente.

### <a name="error-code-2104"></a>Código de erro: 2104

- **Mensagem**: `The type of the property '%propertyName;' is incorrect.`

- **Causa**: O tipo de propriedade fornecido não está correto.

- **Recomendação**: Corrija o tipo da propriedade e tente novamente.

### <a name="error-code-2105"></a>Código de erro: 2105

- **Mensagem**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa**: O valor da propriedade é inválido ou não está no formato esperado.

- **Recomendação**: Veja a documentação da propriedade e verifique se o valor fornecido inclui o formato e o tipo corretos.

### <a name="error-code-2106"></a>Código de erro: 2106

- **Mensagem**: `The storage connection string is invalid. %errorMessage;`

- **Causa**: A cadeia de conexão para o armazenamento está inválida ou tem formato incorreto.

- **Recomendação**: Acesse o portal do Azure e localize seu armazenamento, em seguida, copie e cole a cadeia de conexão no serviço vinculado e tente novamente.

### <a name="error-code-2108"></a>Código de erro: 2108

- **Mensagem**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: A solicitação falhou devido a um problema subjacente, como conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Use Fiddler/Postman para validar a solicitação.

### <a name="error-code-2110"></a>Código de erro: 2110

- **Mensagem**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa**: O serviço vinculado especificado na atividade está incorreto.

- **Recomendação**: Verifique se o tipo de serviço vinculado é um dos tipos compatíveis para a atividade. Por exemplo, o tipo de serviço vinculado para atividades HDI pode ser HDInsight ou HDInsightOnDemand.

### <a name="error-code-2111"></a>Código de erro: 2111

- **Mensagem**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa**: O tipo da propriedade fornecida não está correto.

- **Recomendação**: Corrija o tipo de propriedade e tente novamente.

### <a name="error-code-2112"></a>Código de erro: 2112

- **Mensagem**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa**: O tipo de nuvem não é compatível ou não pôde ser determinado para armazenamento proveniente do EndpointSuffix.

- **Recomendação**: Use o armazenamento em outra nuvem e tente novamente.

### <a name="error-code-2128"></a>Código de erro: 2128

- **Mensagem**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Valide se o ponto de extremidade que você está tentando visitar está respondendo às solicitações. Você pode usar ferramentas como Fiddler/Postman.

## <a name="custom"></a>Personalizado

A tabela a seguir se aplica ao Lote do Azure.
 
### <a name="error-code-2500"></a>Código de erro: 2500

- **Mensagem**: `Hit unexpected exception and execution failed.`

- **Causa**: `Can't launch command, or the program returned an error code.`

- **Recomendação**: Verifique se o arquivo executável existe. Se o programa tiver sido iniciado, verifique se *stdout.txt* e *stderr.txt* foram carregados na conta de armazenamento. É uma boa prática incluir logs em seu código para depuração.

### <a name="error-code-2501"></a>Código de erro: 2501

- **Mensagem**: `Cannot access user batch account; please check batch account settings.`

- **Causa**: Chave de acesso do Lote ou nome do pool incorreto.

- **Recomendação**: Verifique o nome do pool e a chave de acesso do Lote no serviço vinculado.

### <a name="error-code-2502"></a>Código de erro: 2502

- **Mensagem**: `Cannot access user storage account; please check storage account settings.`

- **Causa**: Nome da conta de armazenamento ou chave de acesso incorreto.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço vinculado.

### <a name="error-code-2504"></a>Código de erro: 2504

- **Mensagem**: `Operation returned an invalid status code 'BadRequest'.`

- **Causa**: Muitos arquivos no `folderPath` da atividade personalizada. O tamanho total de `resourceFiles` não pode ter mais de 32.768 caracteres.

- **Recomendação**: Remova os arquivos desnecessários ou compacte-os e adicione um comando unzip para extraí-los.
   
   Por exemplo, use `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Código de erro: 2505

- **Mensagem**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa**: As atividades personalizadas são compatíveis apenas com contas de armazenamento que usam uma chave de acesso.

- **Recomendação**: Veja a descrição do erro.

### <a name="error-code-2507"></a>Código de erro: 2507

- **Mensagem**: `The folder path does not exist or is empty: ...`

- **Causa**: Não há arquivos na conta de armazenamento no caminho especificado.

- **Recomendação**: O caminho da pasta deve conter os arquivos executáveis que você deseja executar.

### <a name="error-code-2508"></a>Código de erro: 2508

- **Mensagem**: `There are duplicate files in the resource folder.`

- **Causa**: vários arquivos com o mesmo nome estão em subpastas diferentes de FolderPath.

- **Recomendação**: Estrutura de pastas nivelam atividades personalizadas em folderPath. Se você precisar preservar a estrutura de pastas, compacte os arquivos e extraia-os no Lote do Azure usando um comando unzip.
   
   Por exemplo, use `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Código de erro: 2509

- **Mensagem**: `Batch url ... is invalid; it must be in Uri format.`

- **Causa**: As URLs do lote devem ser semelhantes a `https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: Veja a descrição do erro.

### <a name="error-code-2510"></a>Código de erro: 2510

- **Mensagem**: `An error occurred while sending the request.`

- **Causa**: A URL do lote é inválida.

- **Recomendação**: Verifique a URL do lote.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Código de erro: 206

- **Mensagem**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Causa**: Houve um problema interno com o serviço que causou esse erro.

- **Recomendação**: Esse problema pode ser transitório. Tente novamente o trabalho depois de algum tempo.

### <a name="error-code-207"></a>Código de erro: 207

- **Mensagem**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Causa**: Ocorreu um erro interno ao tentar determinar a região da conta de armazenamento primária.

- **Recomendação**: Tente outro armazenamento. 

### <a name="error-code-208"></a>Código de erro: 208

- **Mensagem**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Causa**: Ocorreu um erro interno ao tentar ler a entidade de serviço ou instanciar a autenticação MSI.

- **Recomendação**: Considere fornecer uma entidade de serviço que tenha permissões para criar um cluster HDInsight na assinatura fornecida e tente novamente. Verifique se [Gerenciar Identidades está configurado corretamente](../hdinsight/hdinsight-managed-identities.md).

### <a name="error-code-2300"></a>Código de erro: 2300

- **Mensagem**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Causa**: A mensagem de erro contém uma mensagem semelhante a `The remote name could not be resolved.`. O URI do cluster fornecido pode ser inválido.

- **Recomendação**: Verifique se o cluster não foi excluído e se o URI fornecido está correto. Ao abrir o URI em um navegador, você deverá ver a interface do usuário do Ambari. Se o cluster estiver em uma rede virtual, o URI deverá ser o URI privado. Para abri-lo, use uma VM (Máquina Virtual) que faça parte da mesma rede virtual.

   Para obter mais informações, confira [Conectar-se diretamente aos serviços do Apache Hadoop](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Causa**: Se a mensagem de erro contiver uma mensagem semelhante a `A task was canceled.`, o envio do trabalho atingiu o tempo limite.

- **Recomendação**: O problema pode ser a conectividade geral do HDInsight ou a conectividade da rede. Primeiro, confirme se a interface do usuário do Ambari no HDInsight está disponível em qualquer navegador. Em seguida, verifique se suas credenciais ainda estão válidas.
   
   Se você estiver usando um IR (integration runtime) auto-hospedado, execute esta etapa na VM ou no computador em que o IR auto-hospedado está instalado. Em seguida, tente enviar o trabalho no Data Factory novamente.

   Para obter mais informações, leia [Interface do usuário da Web do Ambari](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a `User admin is locked out in Ambari` ou `Unauthorized: Ambari user name or password is incorrect`, as credenciais do HDInsight estão incorretas ou expiraram.

- **Recomendação**: Corrija as credenciais e reimplante o serviço vinculado. Primeiro, verifique se as credenciais funcionam no HDInsight abrindo o URI do cluster em qualquer navegador e tentando entrar. Se as credenciais não funcionarem, você poderá redefini-las no portal do Azure.

   Para o cluster ESP, redefina a senha por meio da [redefinição de senha por autoatendimento](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a `502 - Web server received an invalid response while acting as a gateway or proxy server`, esse erro é retornado pelo serviço do HDInsight.

- **Recomendação**: Um erro 502 geralmente ocorre quando o processo do Servidor do Ambari foi desligado. Você pode reiniciar os serviços do Ambari reiniciando o nó principal.

    1. Conecte-se a um de seus nós no HDInsight usando SSH.
    1. Identifique o host do nó de cabeçalho ativo executando `ping headnodehost`.
    1. Conecte-se ao nó de cabeçalho ativo, pois o Servidor do Ambari fica no nó de cabeçalho ativo usando o SSH. 
    1. Reinicialize o nó de cabeçalho ativo.

       Para obter mais informações, confira a documentação de solução de problemas do Azure HDInsight. Por exemplo:

       * [Erro 502 da interface de usuário do Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException para servidor Thrift do Apache Spark](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Solução de problemas de erros de gateway incorreto no Gateway de Aplicativo](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a `Unable to service the submit job request as templeton service is busy with too many submit job requests` ou `Queue root.joblauncher already has 500 applications, cannot accept submission of application`, há muitos trabalhos sendo enviados ao HDInsight ao mesmo tempo.

- **Recomendação**: Limite o número de trabalhos simultâneos enviados ao HDInsight. Veja a simultaneidade de atividade do Data Factory se os trabalhos estiverem sendo enviados pela mesma atividade. Altere os gatilhos para que as execuções de pipeline simultâneas sejam distribuídas com o passar do tempo.

   Veja a [Documentação do HDInsight](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) para ajustar `templeton.parallellism.job.submit` conforme o erro sugere.

### <a name="error-code-2301"></a>Código de erro: 2301

- **Mensagem**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa**: O cluster ou serviço HDInsight tem problemas.

- **Recomendação**: Esse erro ocorre quando o ADF não recebe uma resposta do cluster HDInsight ao tentar solicitar o status do trabalho em execução. Esse problema pode estar no próprio cluster ou o serviço HDInsight pode ter uma interrupção.

   Veja a documentação de solução de problemas do HDInsight em https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide ou entre em contato com o suporte para obter assistência adicional.

### <a name="error-code-2302"></a>Código de erro: 2302

- **Mensagem**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa**: O trabalho foi enviado para o cluster HDI e falhou.

- **Recomendação**: 

 1. Verifique a interface do usuário do Ambari:
    1. Verifique se todos os serviços ainda estão em execução.
    1. Na interface do usuário do Ambari, verifique a seção de alertas em seu painel.
       1. Para obter mais informações sobre alertas e resoluções de alertas, confira [Gerenciar e monitorar um cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Examine a memória do YARN. Se a memória do YARN estiver alta, o processamento dos trabalhos poderá ser atrasado. Se você não tiver recursos suficientes para acomodar seu aplicativo/trabalho do Spark, escale verticalmente o cluster para garantir que o cluster tenha memória e núcleos suficientes. 
 1. Execute um trabalho de teste de exemplo.
    1. Se você executar o mesmo trabalho no back-end do HDInsight, verifique se ele foi bem-sucedido. Para obter exemplos de execuções de amostra, veja [Executar os exemplos do MapReduce incluídos no HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se o trabalho ainda estiver falhando no HDInsight, verifique os logs e as informações do aplicativo para fornecer ao suporte:
    1. Verifique se o trabalho foi enviado para o YARN. Se o trabalho não foi enviado para o YARN, use `--master yarn`.
    1. Se o aplicativo concluiu a execução, colete a hora de início e de término do aplicativo YARN. Se o aplicativo não concluiu a execução, colete a Hora de início/Hora de inicialização.
    1. Verifique e colete o log do aplicativo com `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Verifique e colete os logs do Resource Manager do YARN no diretório `/var/log/hadoop-yarn/yarn`.
    1. Se essas etapas não forem suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte e forneça os logs e carimbos de data e hora acima.

### <a name="error-code-2303"></a>Código de erro: 2303

- **Mensagem**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa**: O trabalho foi enviado para o cluster HDI e falhou.

- **Recomendação**: 

 1. Verifique a interface do usuário do Ambari:
    1. Verifique se todos os serviços ainda estão em execução.
    1. Na interface do usuário do Ambari, verifique a seção de alertas em seu painel.
       1. Para obter mais informações sobre alertas e resoluções de alertas, confira [Gerenciar e monitorar um cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Examine a memória do YARN. Se a memória do YARN estiver alta, o processamento dos trabalhos poderá ser atrasado. Se você não tiver recursos suficientes para acomodar seu aplicativo/trabalho do Spark, escale verticalmente o cluster para garantir que o cluster tenha memória e núcleos suficientes. 
 1. Execute um trabalho de teste de exemplo.
    1. Se você executar o mesmo trabalho no back-end do HDInsight, verifique se ele foi bem-sucedido. Para obter exemplos de execuções de amostra, veja [Executar os exemplos do MapReduce incluídos no HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se o trabalho ainda estiver falhando no HDInsight, verifique os logs e as informações do aplicativo para fornecer ao suporte:
    1. Verifique se o trabalho foi enviado para o YARN. Se o trabalho não foi enviado para o YARN, use `--master yarn`.
    1. Se o aplicativo concluiu a execução, colete a hora de início e de término do aplicativo YARN. Se o aplicativo não concluiu a execução, colete a Hora de início/Hora de inicialização.
    1. Verifique e colete o log do aplicativo com `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Verifique e colete os logs do Resource Manager do YARN no diretório `/var/log/hadoop-yarn/yarn`.
    1. Se essas etapas não forem suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte e forneça os logs e carimbos de data e hora acima.

### <a name="error-code-2304"></a>Código de erro: 2304

- **Mensagem**: `MSI authentication is not supported on storages for HDI activities.`

- **Causa**: Os serviços vinculados de armazenamento usados no serviço vinculado do HDI (HDInsight) ou na atividade do HDI são configurados com uma autenticação da MSI que não é compatível.

- **Recomendação**: Forneça cadeias de conexão completas para as contas de armazenamento usadas no serviço vinculado do HDI ou na atividade do HDI.

### <a name="error-code-2305"></a>Código de erro: 2305

- **Mensagem**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: As informações de conexão do cluster HDI estão incorretas, o usuário fornecido não tem permissões para executar a ação necessária ou o serviço HDInsight tem problemas de resposta às solicitações do ADF.

- **Recomendação**: Verifique se as informações do usuário estão corretas e se a interface do usuário do Ambari para o cluster HDI pode ser aberta em um navegador de VM em que o IR está instalado (para um IR auto-hospedado) ou pode ser aberta em qualquer computador (para o Azure IR).

### <a name="error-code-2306"></a>Código de erro: 2306

- **Mensagem**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: O JSON fornecido para a ação de script é inválido.

- **Recomendação**: A mensagem de erro deve ajudar a identificar o problema. Corrija a configuração JSON e tente novamente.

   Verifique o [Serviço vinculado do Azure HDInsight sob demanda](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter mais informações.

### <a name="error-code-2310"></a>Código de erro: 2310

- **Mensagem**: `Failed to submit Spark job. Error: '%message;'`

- **Causa**: O ADF tentou criar um lote em um cluster do Spark usando a API Livy (livy/batch), mas recebeu um erro.

- **Recomendação**: Siga a mensagem de erro para corrigir o problema. Se não houver informações suficientes para resolver isso, entre em contato com a equipe do HDI e forneça a ID do lote e a ID do trabalho, que podem ser encontradas na saída da execução da atividade da página de monitoramento do ADF. Para solucionar os problemas, colete o log completo do trabalho em lotes.

   Para obter mais informações sobre como coletar o log completo, confira [Obter o log completo de um trabalho em lotes](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Código de erro: 2312

- **Mensagem**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa**: O trabalho falhou no cluster do HDInsight Spark.

- **Recomendação**: Siga os links da saída da execução da atividade na página de monitoramento do ADF para solucionar problemas de execução no cluster do HDInsight Spark. Contate a equipe de suporte do HDInsight para obter mais assistência.

   Para obter mais informações sobre como coletar o log completo, confira [Obter o log completo de um trabalho em lotes](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Código de erro: 2313

- **Mensagem**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa**: O lote foi excluído no cluster do HDInsight Spark.

- **Recomendação**: Solucionar problemas de lotes no cluster do HDInsight Spark. Contate o suporte do HDInsight para obter mais assistência. 

   Para obter mais informações sobre como coletar o log completo, confira [Obter o log completo de um trabalho em lotes](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job) e compartilhe o log completo com o suporte do HDInsight para obter mais assistência.

### <a name="error-code-2328"></a>Código de erro: 2328

- **Mensagem**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: A mensagem de erro deve ajudar a solucionar o problema.

### <a name="error-code-2329"></a>Código de erro: 2329

- **Mensagem**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: A mensagem de erro deve ajudar a solucionar o problema.

### <a name="error-code-2331"></a>Código de erro: 2331

- **Mensagem**: `The file path should not be null or empty.`

- **Causa**: O caminho de arquivo fornecido está vazio.

- **Recomendação**: Forneça um caminho para um arquivo existente.

### <a name="error-code-2340"></a>Código de erro: 2340

- **Mensagem**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa**: O serviço vinculado HDInsightOnDemand não é compatível com a execução por meio de IR auto-hospedado.

- **Recomendação**: Selecione um Azure IR e tente novamente.

### <a name="error-code-2341"></a>Código de erro: 2341

- **Mensagem**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa**: A URL fornecida não está no formato correto.

- **Recomendação**: Corrija a URL do cluster e tente novamente.

### <a name="error-code-2342"></a>Código de erro: 2342

- **Mensagem**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: As credenciais fornecidas estão incorretas para o cluster ou houve uma configuração de rede ou um problema de conexão ou o IR está tendo problemas para se conectar com o cluster.

- **Recomendação**: 
    1. Verifique se as credenciais estão corretas abrindo a interface do usuário do Ambari do cluster HDInsight em um navegador.
    1. Se o cluster estiver em uma VNet (Rede Virtual) e um IR auto-hospedado estiver sendo usado, a URL do HDI deverá ser a URL privada em VNets e deverá ter "-int" listado após o nome do cluster.
    
       Por exemplo, altere `https://mycluster.azurehdinsight.net/` para `https://mycluster-int.azurehdinsight.net/`. Observe o `-int` após `mycluster`, mas antes de `.azurehdinsight.net`
    1. Se o cluster estiver na VNet, o IR auto-hospedado estiver sendo usado e a URL privada tiver sido usada e, ainda assim, a conexão falhar, a VM em que o IR está instalado teve problemas ao se conectar com o HDI. 
    
       Conecte-se à VM em que o IR está instalado e abra a interface do usuário do Ambari em um navegador. Use a URL privada para o cluster. Essa conexão deve funcionar no navegador. Caso contrário, contate a equipe de suporte do HDInsight para obter mais assistência.
    1. Se o IR auto-hospedado não estiver sendo usado, o cluster HDI deverá ser acessível publicamente. Abra a interface do usuário do Ambari em um navegador e verifique se ela é aberta. Se houver algum problema com o cluster ou com os serviços, entre em contato com a equipe de suporte do HDInsight para obter assistência.

       A URL do cluster do HDI usada no serviço vinculado do ADF deve ser acessível para o IR do ADF (auto-hospedado ou Azure) para que a conexão de teste passe e para que as execuções funcionem. Esse estado pode ser verificado abrindo a URL em um navegador em uma VM ou em qualquer computador público.

### <a name="error-code-2343"></a>Código de erro: 2343

- **Mensagem**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa**: o nome de usuário ou a senha está vazio.

- **Recomendação**: Forneça as credenciais corretas para se conectar ao HDI e tente novamente.

### <a name="error-code-2345"></a>Código de erro: 2345

- **Mensagem**: `Failed to read the content of the hive script. Error: '%message;'`

- **Causa**: O arquivo de script não existe ou o ADF não pôde se conectar à localização do script.

- **Recomendação**: Verifique se o script existe e se o serviço vinculado associado tem as credenciais apropriadas para uma conexão.

### <a name="error-code-2346"></a>Código de erro: 2346

- **Mensagem**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa**: O ADF tentou estabelecer uma conexão ODBC com o cluster HDI e falhou com um erro.

- **Recomendação**: 

   1. Confirme se você configurou corretamente sua conexão ODBC/JDBC (Java Database Connectivity).
      1. Para JDBC, se você estiver usando a mesma rede virtual, poderá obter essa conexão de:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Para garantir que você tenha a configuração JDBC correta, veja [Consultar Apache Hive por meio do driver JDBC no HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Para ODB (Open Database), confira o [Tutorial: Consultar Apache Hive com ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) para ter a configuração correta. 
   1. Verifique se Hiveserver2, Metastore do Hive e Hiveserver2 Interactive estão ativos e funcionando. 
   1. Verifique a interface do usuário do Ambari:
      1. Verifique se todos os serviços ainda estão em execução.
      1. Na interface do usuário do Ambari, verifique a seção de alertas em seu painel.
         1. Para obter mais informações sobre alertas e resoluções de alertas, confira [Gerenciar e monitorar um cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se essas etapas não forem suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight.

### <a name="error-code-2347"></a>Código de erro: 2347

- **Mensagem**: `Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: O ADF enviou o script do hive para execução no cluster HDI por meio da conexão ODBC e o script falhou no HDI.

- **Recomendação**: 

   1. Confirme se você configurou corretamente sua conexão ODBC/JDBC (Java Database Connectivity).
      1. Para JDBC, se você estiver usando a mesma rede virtual, poderá obter essa conexão de:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Para garantir que você tenha a configuração JDBC correta, veja [Consultar Apache Hive por meio do driver JDBC no HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Para ODB (Open Database), confira o [Tutorial: Consultar Apache Hive com ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) para ter a configuração correta. 
   1. Verifique se Hiveserver2, Metastore do Hive e Hiveserver2 Interactive estão ativos e funcionando. 
   1. Verifique a interface do usuário do Ambari:
      1. Verifique se todos os serviços ainda estão em execução.
      1. Na interface do usuário do Ambari, verifique a seção de alertas em seu painel.
         1. Para obter mais informações sobre alertas e resoluções de alertas, confira [Gerenciar e monitorar um cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se essas etapas não forem suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight.

### <a name="error-code-2348"></a>Código de erro: 2348

- **Mensagem**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa**: As propriedades do serviço vinculado de armazenamento não estão definidas corretamente.

- **Recomendação**: Somente as cadeias de conexão completas são compatíveis com o serviço vinculado do armazenamento principal para atividades do HDI. Verifique se você não está usando autorizações ou aplicativos do MSI.

### <a name="error-code-2350"></a>Código de erro: 2350

- **Mensagem**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: As credenciais fornecidas para se conectar ao armazenamento em que os arquivos devem estar localizados estão incorretas ou os arquivos não existem.

- **Recomendação**: Esse erro ocorre quando o ADF se prepara para atividades do HDI e tenta copiar arquivos para o armazenamento principal antes de enviar o trabalho para o HDI. Verifique se os arquivos existem na localização fornecida e se a conexão do armazenamento está correta. Como as atividades do ADF do HDI não são compatíveis com a autenticação MSI em contas de armazenamento relacionadas a atividades do HDI, verifique se esses serviços vinculados têm chaves completas ou estão usando o Azure Key Vault.

### <a name="error-code-2351"></a>Código de erro: 2351

- **Mensagem**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa**: O arquivo não existe no caminho especificado.

- **Recomendação**: Verifique se o arquivo realmente existe e se o serviço vinculado com informações de conexão que apontam para esse arquivo tem as credenciais corretas.

### <a name="error-code-2352"></a>Código de erro: 2352

- **Mensagem**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa**: As propriedades do serviço vinculado de armazenamento de arquivos não estão definidas corretamente.

- **Recomendação**: Verifique se as propriedades do serviço vinculado de armazenamento de arquivos estão configuradas corretamente.

### <a name="error-code-2353"></a>Código de erro: 2353

- **Mensagem**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa**: As propriedades do serviço vinculado de armazenamento do script não estão definidas corretamente.

- **Recomendação**: Verifique se as propriedades do serviço vinculado de armazenamento do script estão configuradas corretamente.

### <a name="error-code-2354"></a>Código de erro: 2354

- **Mensagem**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa**: O tipo de serviço vinculado de armazenamento não é compatível com a atividade.

- **Recomendação**: Verifique se o serviço vinculado selecionado tem um dos tipos compatíveis com a atividade. As atividades do HDI são compatíveis com os serviços vinculados AzureBlobStorage e AzureBlobFSStorage.

   Para obter mais informações, leia [Comparar opções de armazenamento para uso com clusters do Azure HDInsight](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>Código de erro: 2355

- **Mensagem**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: O valor fornecido para `commandEnvironment` está incorreto.

- **Recomendação**: Verifique se o valor fornecido é semelhante a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verifique também se cada variável aparece na lista apenas uma vez.

### <a name="error-code-2356"></a>Código de erro: 2356

- **Mensagem**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: O valor fornecido para `commandEnvironment` está incorreto.

- **Recomendação**: Verifique se o valor fornecido é semelhante a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verifique também se cada variável aparece na lista apenas uma vez.

### <a name="error-code-2357"></a>Código de erro: 2357

- **Mensagem**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa**: As credenciais fornecidas estão incorretas.

- **Recomendação**: Verifique se as informações de conexão no ADLS Gen 1 estão vinculadas ao serviço e verifique se a conexão de teste foi realizada com sucesso.

### <a name="error-code-2358"></a>Código de erro: 2358

- **Mensagem**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: O valor fornecido para a propriedade necessária `TimeToLive` tem um formato inválido. 

- **Recomendação**: Atualize o valor para o intervalo sugerido e tente novamente.

### <a name="error-code-2359"></a>Código de erro: 2359

- **Mensagem**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: O valor fornecido para a propriedade `roles` é inválido.

- **Recomendação**: Atualize o valor para uma das sugestões e tente novamente.

### <a name="error-code-2360"></a>Código de erro: 2360

- **Mensagem**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa**: A cadeia de conexão fornecida para o `HCatalogLinkedService` é inválida.

- **Recomendação**: Atualize o valor para uma cadeia de conexão correta do Azure SQL e tente novamente.

### <a name="error-code-2361"></a>Código de erro: 2361

- **Mensagem**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa**: A criação do cluster falhou e o ADF não recebeu um erro do serviço do HDInsight.

- **Recomendação**: Abra o portal do Azure e tente localizar o recurso do HDI com o nome fornecido e, em seguida, verifique o status de provisionamento. Contate a equipe de suporte do HDInsight para obter mais assistência.

### <a name="error-code-2362"></a>Código de erro: 2362

- **Mensagem**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa**: O armazenamento adicional fornecido não era o Armazenamento de Blobs do Azure.

- **Recomendação**: Forneça uma conta de Armazenamento de Blobs do Azure como um armazenamento adicional para o serviço vinculado sob demanda do HDInsight.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>Erro de SSL quando o serviço vinculado do ADF está usando o cluster do HDInsight ESP

- **Mensagem**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Causa**: o problema provavelmente está relacionado ao repositório de confiança do sistema.

- **Resolução**: você pode navegar até o caminho **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** e abrir DriverConfiguration64.exe para alterar a configuração.

    ![Desmarque usar repositório de confiança do sistema](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Atividade da Web

### <a name="error-code-2128"></a>Código de erro: 2128

- **Mensagem**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Esse problema é devido à conectividade de rede, uma falha de DNS, uma validação de certificado do servidor ou um tempo limite.

- **Recomendação**: Valide se o ponto de extremidade que você está tentando visitar está respondendo às solicitações. Você pode usar ferramentas como **Fiddler/Postman**.

### <a name="error-code-2108"></a>Código de erro: 2108

- **Mensagem**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: A solicitação falhou devido a um problema subjacente, como conectividade de rede, uma falha de DNS, uma validação de certificado do servidor ou um tempo limite.

- **Recomendação**: Use Fiddler/Postman para validar a solicitação.

#### <a name="more-details"></a>Mais detalhes
Para usar **Fiddler** para criar uma sessão HTTP do aplicativo Web monitorado:

1. Baixe, instale e abra o [Fiddler](https://www.telerik.com/download/fiddler).

1. Se o aplicativo Web usar HTTPS, acesse **Ferramentas** > **Opções do Fiddler** > **HTTPS**.

   1. Na guia HTTPS, selecione **Capturar conexões HTTPS** e **Descriptografar tráfego HTTPS**.

      ![Opções do Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se o aplicativo usar certificados TLS/SSL, adicione o certificado Fiddler ao seu dispositivo.

   Acesse: **Ferramentas** > **Opções do Fiddler** > **HTTPS** > **Ações** > **Exportar Certificado Raiz para a Área de Trabalho**.

1. Desligue a captura acessando **Arquivo** > **Capturar Tráfego**. Ou pressione **F12**.

1. Limpe o cache do navegador para que todos os itens em cache sejam removidos e tenham que ser baixados novamente.

1. Crie uma solicitação:

1. Selecione a guia **Composer**.

   1. Defina o método HTTP e a URL.
 
   1. Se necessário, adicione cabeçalhos e corpo da solicitação.

   1. Selecione **Executar**.

1. Ative a captura de tráfego novamente e conclua a transação com problema em sua página.

1. Acesse: **Arquivo** > **Salvar** > **Todas as Sessões**.

Para saber mais, confira [Introdução ao Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="general"></a>Geral

### <a name="activity-stuck-issue"></a>Problema de atividade paralisada

Quando você observa que a atividade está sendo executada muito mais do que as suas execuções normais sem mal progresso, pode acontecer de estar presa. Você pode tentar cancelá-lo e tentar novamente para ver se ele ajuda. Se for uma atividade de cópia, você pode aprender sobre o monitoramento de desempenho e solução de problemas de [desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md). Se for um fluxo de dados, aprenda com o guia de desempenho e ajuste do [mapeamento de fluxos de dados](concepts-data-flow-performance.md) .

### <a name="payload-is-too-large"></a>A carga é muito grande

**Mensagem de erro:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Causa:** A carga para cada execução de atividade incluirá a configuração da atividade, os conjuntos de (s) associados e as configurações de serviço vinculado (s), se houver, e uma pequena parte das propriedades do sistema geradas por tipo de atividade. O limite desse tamanho de carga é 896 KB, conforme mencionado na seção [Data Factory limites](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

**Recomendação:** Você atingiu esse limite provavelmente porque você passa um ou mais valores de parâmetro grandes de saída de atividade upstream ou externa, especialmente se você passar dados reais entre atividades no fluxo de controle. Verifique se você pode reduzir o tamanho dos valores de parâmetro grandes ou ajuste a lógica do pipeline para evitar passar esses valores entre as atividades e tratá-lo dentro da atividade.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

* [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/)
* [Página de P e R da Microsoft](/answers/topics/azure-data-factory.html)
