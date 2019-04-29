---
title: Códigos de erro da API REST – Azure Machine Learning Studio | Microsoft Docs
description: Esses códigos de erro podem ser retornados por uma operação em um serviço Web do Machine Learning do Azure.
keywords: ''
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 8e91d0cd68997dee9bb00cceeaa6b697f6644ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736557"
---
# <a name="azure-machine-learning-studio-rest-api-error-codes"></a>Códigos de erro da API REST do Azure Machine Learning Studio
 
Os seguintes códigos de erro podem ser retornados por uma operação em um serviço da web do Azure Machine Learning Studio.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de status HTTP 400)
 
Argumento inválido fornecido.
 
Essa classe de erros significa que um argumento fornecido em algum lugar foi inválido. Isso pode ser uma credencial ou um local de armazenamento do Azure para algo passado para o serviço Web. Examine o campo do "código" de erro na seção "detalhes" para diagnosticar qual argumento específico foi inválido.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| BadParameterValue | O valor do parâmetro fornecido não atende à regra de parâmetro no parâmetro |
| BadSubscriptionId | A Id da assinatura usada para a pontuação não está presente no recurso |
| BadVersionCall | Um parâmetro da versão inválido foi passado durante a chamada da API: {0}. Verifique a página de ajuda da API para passar a versão correta e tente novamente. |
| BatchJobInputsNotSpecified | As seguintes entradas obrigatórias não foram especificadas com a solicitação: {0}. Verifique se todos os dados de entrada estão especificados e tente novamente. |
| BatchJobInputsTooManySpecified | A solicitação especificou mais entradas do que definido no serviço. Lista de entradas aceitas: {0}. Verifique se todos os dados de entrada estão especificados corretamente e tente de novo. |
| BlobNameTooLong | O caminho do armazenamento de blobs do Azure fornecido para a saída de diagnóstico é muito longo: {0}. Reduza o caminho e tente novamente. |
| BlobNotFound | Não é possível acessar o blob do Azure fornecido – {0}.  Mensagem de erro do Azure: {1}. |
| ContainerIsEmpty | Nenhum nome de contêiner de armazenamento do Azure foi fornecido. Forneça um nome de contêiner válido e tente novamente. |
| ContainerSegmentInvalid | Nome do contêiner inválido. Forneça um nome de contêiner válido e tente novamente. |
| ContainerValidationFailed | A validação do contêiner de blobs falhou com este erro: {0}. |
| DataTypeNotSupported | Tipo de dados sem suporte fornecido. Forneça o(s) tipo(s) de dados válido(s) e tente novamente. |
| DuplicateInputInBatchCall | A solicitação de lote é inválida. Não é possível especificar uma e várias entradas ao mesmo tempo. Remova um desses itens da solicitação e tente novamente. |
| ExpiryTimeInThePast | A hora da expiração fornecida está no passado: {0}. Forneça uma hora de expiração futura em UTC e tente novamente. Para nunca expirar, defina a hora de expiração para NULL. |
| IncompleteSettings | As configurações de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInvalid | Nenhum nome do blob de armazenamento do Azure fornecido. Forneça um nome de blob válido e tente novamente. |
| InvalidBlob | Especificação de blob inválida para o blob: {0}. Verifique se a cadeia de conexão/caminho relativo ou especificação de token de SAS está correta e tente novamente. |
| InvalidBlobConnectionString | A cadeia de conexão especificada para um dos blobs de entrada/saída é inválida: {0}. Corrija isso e tente novamente. |
| InvalidBlobExtension | A referência do blob: {0} tem uma extensão de arquivo inválida ou ausente. O suporte para as extensões de arquivo desse tipo de saída são: "{1}". |
| InvalidInputNames | Nomes de entrada de serviço inválidos especificados na solicitação: {0}. Mapeie os dados de entrada para as entradas de serviço corretas e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição da saída inválido: {0}. O serviço não tem um nó de saída com esse nome. Passe um nome do nó de saída correto para substituir (aplica-se a diferenciação de letras maiúscula e minúscula). |
| InvalidQueryParameter | Parâmetro de consulta inválido '{0}'. {1} |
| MissingInputBlobInformation | Informações ausentes do blob de armazenamento do Azure. Forneça uma cadeia de conexão válida e o caminho relativo ou URI e tente novamente. |
| MissingJobId | Nenhuma Id de trabalho fornecida. Uma Id de trabalho é retornada quando um trabalho foi enviado pela primeira vez. Verifique se a Id de trabalho está correta e tente novamente. |
| MissingKeys | Nenhuma Chave fornecida ou uma Chave Primária ou Secundária não é fornecida. |
| MissingModelPackage | Nenhuma Id do pacote de modelo ou pacote de modelo fornecido. Forneça uma Id do pacote de modelo válida ou pacote de modelo e tente novamente. |
| MissingOutputOverrideSpecification | A solicitação não tem a especificação de blob para a substituição da saída {0}. Especifique um local de blob válido com a solicitação ou remova a especificação da saída se nenhuma substituição do local for desejada. |
| MissingRequestInput | O serviço Web espera uma entrada, mas nenhuma entrada foi fornecida. Verifique se entradas válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| MissingRequiredGlobalParameters | Nem todos os parâmetros de serviço Web necessários fornecidos. Verifique se o(s) parâmetro(s) esperado(s) para o(s) módulo(s) está(ão) correto(s) e tente novamente. |
| MissingRequiredOutputOverrides | Ao chamar um ponto de extremidade do serviço criptografado é obrigatório passar as substituições da saída para todas as saídas do serviço. Faltam substituições no momento para essas saídas: {0} |
| MissingWebServiceGroupId | Nenhuma Id do grupo de serviços Web fornecida. Forneça uma Id do grupo de serviços Web válida e tente novamente. |
| MissingWebServiceId | Nenhuma Id do serviço Web fornecida. Forneça uma Id do serviço Web válida e tente novamente. |
| MissingWebServicePackage | Nenhum pacote de Serviço Web fornecido. Forneça um pacote de serviço Web válido e tente novamente. |
| MissingWorkspaceId | Nenhuma Id do workspace fornecida. Forneça uma Id do workspace válida e tente novamente. |
| ModelConfigurationInvalid | Configuração do modelo inválida no pacote do modelo. Verifique se a configuração do modelo contém a definição do(s) ponto(s) de extremidade de saída, ponto de extremidade de erro padrão, ponto de extremidade de saída padrão e tente novamente. |
| ModelPackageIdInvalid | Id do pacote do modelo inválida. Verifique se a Id do pacote do modelo está correta e tente novamente. |
| RequestBodyInvalid | Não há corpo de solicitação fornecido ou erro ao desserializar o corpo da solicitação. |
| RequestIsEmpty | Nenhuma solicitação fornecida. Forneça uma solicitação válida e tente novamente. |
| UnexpectedParameter | Parâmetros inesperados fornecidos. Verifique se todos os nomes de parâmetro estão escritos corretamente, somente os parâmetros esperados são passados e tente novamente. |
| UnknownError | Erro desconhecido. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Não é possível alterar os requisitos de solicitações simultâneas para o serviço Web {0}. |
| WebServiceIdInvalid | Id do serviço Web inválida fornecida. A Id do serviço Web deve ser uma guid válida. |
| WebServiceTooManyConcurrentRequestRequirement | Não é possível definir o requisito de solicitações simultâneas para mais de {0}. |
| WebServiceTypeInvalid | Tipo de serviço Web inválido fornecido. Verifique se o tipo de serviço Web válido está correto e tente novamente. Tipos de serviço Web válidos: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de status HTTP 400)
 
Argumento inválido do usuário fornecido.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| InputMismatchError | Os dados de entrada não coincidem com o esquema da porta de entrada. |
| InputParseError | Falha da análise do vetor de entrada.  Verifique se o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0}. |
| MissingRequiredGlobalParameters | Parâmetro(s) esperado(s) pelo serviço Web ausente(s). Verifique se todos os parâmetros necessários esperados pelo serviço Web estão corretos e tente novamente. |
| UnexpectedParameter | Verifique apenas se os parâmetros necessários esperados pelo serviço Web estão corretos e tente novamente. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (código de status HTTP 400)
 
A solicitação é inválida no contexto atual.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| CannotStartJob | O trabalho não pode ser iniciado porque está no estado {0}. |
| IncompatibleModel | O modelo é incompatível com a versão de solicitação. A versão de solicitação somente oferece suporte a modelos de saída datatable simples. |
| MultipleInputsNotAllowed | O modelo não permite várias entradas. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de status HTTP 400)
 
A execução do módulo encontrou um erro interno de biblioteca.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (código de status HTTP 400)
 
A execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de status HTTP 400)
 
Pacote de serviço Web inválido. Verifique se o pacote do serviço Web fornecido está correto e tente novamente.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| FormatError | O pacote do serviço Web está malformado. Detalhes: {0} |
| RuntimesError | O grafo do pacote do serviço Web é inválido. Detalhes: {0} |
| ValidationError | O grafo do pacote do serviço Web é inválido. Detalhes: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de status HTTP 401)
 
A solicitação não está autorizada a acessar os recursos.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| AdminRequestUnauthorized | Não Autorizado |
| ManagementRequestUnauthorized | Não Autorizado |
| ScoreRequestUnauthorized | Credenciais inválidas fornecidas. |
 
## <a name="notfound-http-status-code-404"></a>Não Encontrado (código de status HTTP 404)
 
Recurso não encontrado.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| ModelPackageNotFound | Pacote do modelo não encontrado. Verifique se a Id do pacote do modelo está correta e tente novamente. |
| WebServiceIdNotFoundInWorkspace | Serviço Web nesse workspace não encontrado. Há uma incompatibilidade entre webServiceId e workspaceId. Verifique se o serviço Web fornecido faz parte do workspace e tente novamente. |
| WebServiceNotFound | Serviço Web não encontrado. Verifique se a Id do serviço Web está correta e tente novamente. |
| WorkspaceNotFound | Workspace não encontrado. Verifique se a Id do workspace está correta e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>Tempo Limite da Solicitação (código de status HTTP 408)
 
A operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| RequestCanceled | A solicitação foi cancelada pelo cliente. |
| ScoreRequestTimeout | Tempo limite da solicitação de execução. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de status HTTP 409)
 
O recurso já existe.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nome do parâmetro de saída inválido. Tente usar o módulo do editor de metadados para renomear as colunas e tente novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>Violação da Cota de Memória (código de status HTTP 413)
 
O modelo excedeu a cota de memória atribuída a ele.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| OutOfMemoryLimit | O modelo consumiu mais memória do que era apropriado para ele. O máximo de memória permitido para o modelo é {0} MB. Verifique seu modelo quanto a problemas. |
 
## <a name="internalerror-http-status-code-500"></a>Erro Interno (código de status HTTP 500)
 
A execução encontrou um erro interno.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | O processo do contêiner falhou com o erro do sistema |
| ContainerProcessTerminatedWithUnknownError | O processo do contêiner falhou com um erro desconhecido |
| ContainerValidationFailed | A validação do contêiner de blobs falhou com este erro: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nenhum argumento fornecido. Verifique se os argumentos válidos são passados e tente novamente. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | A id da porta={0} tem um tipo de dados sem suporte: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Falha na geração do Swagger, Detalhes: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Código de status do trabalho desconhecido {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Detalhes: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de status HTTP 500)
 
A execução encontrou um erro interno. Sistema com memória insuficiente. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de status HTTP 500)
 
Pacote do modelo inválido. Verifique se o pacote do modelo fornecido está correto e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de status HTTP 500)
 
Pacote de serviço Web inválido. Verifique se o pacote Web fornecido está correto e tente novamente.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| ModuleError | O grafo do pacote do serviço Web é inválido. Detalhes: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (código de status HTTP 503)
 
A solicitação não pode ser executada quando os contêineres estão sendo inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (código de status HTTP 503)
 
O serviço está temporariamente indisponível.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| NoMoreResources | Nenhum recurso disponível para a solicitação. |
| RequestThrottled | A solicitação foi restringida para o ponto de extremidade {0}. A simultaneidade máxima do ponto de extremidade é {1}. |
| TooManyConcurrentRequests | Muitas solicitações simultâneas enviadas. |
| TooManyHostsBeingInitialized | Muitos hosts sendo inicializados ao mesmo tempo. Considere limitar/tentar novamente. |
| TooManyHostsBeingInitializedPerModel | Muitos hosts sendo inicializados ao mesmo tempo. Considere limitar/tentar novamente. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>Tempo Limite do Gateway (código de status HTTP 504)
 
A operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível.
 
| Código do erro | Mensagem do usuário |
| ---------- |--------------|
| BackendInitializationTimeout | A inicialização do serviço Web não pôde ser concluída dentro do tempo permitido. |
| BackendScoreTimeout | A execução de solicitação do serviço Web não pôde ser concluída no tempo permitido. |
 
