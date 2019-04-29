---
title: Códigos de erro dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico oferece uma visão geral dos códigos de erro dos Serviços de Mídia do Azure.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f3c362730e7908e88b363659b7fa580b6f2cddf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217138"
---
# <a name="azure-media-services-error-codes"></a>Códigos de erro dos Serviços de Mídia do Azure
Ao usar os Serviços de Mídia do Microsoft Azure, você pode receber códigos de erro HTTP do serviço dependendo dos problemas, como tokens de autenticação expirando a ações que não são compatíveis nos Serviços de Mídia. Veja a seguir uma lista de **códigos de erros HTTP** que podem ser retornados pelos Serviços de Mídia e suas possíveis causas.  

## <a name="400-bad-request"></a>400 Solicitação Inválida
A solicitação contém informações inválidas e foi rejeitada devido a um dos seguintes motivos:

* Uma versão de API incompatível foi especificada. Para obter a versão mais recente, confira [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).
* A versão da API dos Serviços de Mídia não foi especificada. Para saber mais sobre como especificar a versão da API, confira [Referência de API REST do Media Services Operations](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se você estiver usando os SDKs do .NET ou Java para se conectar aos Serviços de Mídia, a versão da API será especificada sempre que você tentar e executar alguma ação nos Serviços de Mídia.
  > 
  > 
* Uma propriedade indefinida foi especificada. O nome da propriedade está na mensagem de erro. Somente as propriedades que são membros de uma determinada entidade podem ser especificadas. Confira [Azure Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) (Referência da API REST dos Serviços de Mídia do Azure) para obter uma lista de entidades e suas propriedades.
* Um valor de propriedade inválido foi especificado. O nome da propriedade está na mensagem de erro. Confira o link anterior para ver os tipos de propriedade válidos e seus valores.
* Um valor de propriedade está ausente e é obrigatório.
* Parte da URL especificada contém um valor inválido.
* Foi feita uma tentativa de atualizar uma propriedade WriteOnce.
* Foi feita uma tentativa de criar um Job que tem uma entrada Asset com um AssetFile primário que não foi especificado ou não pôde ser determinado.
* Foi feita uma tentativa de atualizar um Localizador SAS. Localizadores SAS só podem ser criados ou excluídos. Os localizadores de streaming podem ser atualizados. Para saber mais, confira [Locators](https://docs.microsoft.com/rest/api/media/operations/locator) (Localizadores).
* Uma operação ou consulta sem suporte foi enviada.

## <a name="401-unauthorized"></a>401 Não Autorizado
A solicitação não pôde ser autenticada (antes de poder ser autorizada) devido a um dos seguintes motivos:

* Cabeçalho de autenticação ausente.
* Valor do cabeçalho de autenticação inválido.
  * O token expirou. 
  * O token contém uma assinatura inválida.

## <a name="403-forbidden"></a>403 Proibido
A solicitação não foi permitida devido a um dos seguintes motivos:

* A conta dos Serviços de Mídia não foi encontrada ou foi excluída.
* A conta dos Serviços de Mídia está desabilitada e o tipo de solicitação não é HTTP GET. As operações de serviço também retornarão uma resposta 403.
* O token de autenticação não contém informações de credenciais do usuário: AccountName e/ou SubscriptionId. Você pode encontrar essas informações na extensão da interface de usuário dos Serviços de Mídia para sua conta dos Serviços de Mídia no Portal de Gerenciamento do Azure.
* O recurso não pode ser acessado.
  
  * Foi feita uma tentativa de usar um MediaProcessor que não está disponível para sua conta dos Serviços de Mídia.
  * Foi feita uma tentativa de atualizar um JobTemplate definido pelos Serviços de Mídia.
  * Foi feita uma tentativa de substituir algum outro Localizador da conta dos Serviços de Mídia.
  * Foi feita uma tentativa de substituir alguma outra ContentKey da conta dos Serviços de Mídia.
* O recurso não pôde ser criado devido a uma cota de serviço que foi atingida para a conta dos Serviços de Mídia. Para saber mais sobre cotas de serviço, confira [Cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Não Encontrado
A solicitação não foi permitida em um recurso devido a um dos seguintes motivos:

* Foi feita uma tentativa de atualizar uma entidade que não existe.
* Foi feita uma tentativa de excluir uma entidade que não existe.
* Foi feita uma tentativa de criar uma entidade que se vincula a uma entidade que não existe.
* Foi feita uma tentativa de obter uma entidade que não existe.
* Foi feita uma tentativa de especificar uma conta de armazenamento que não está associada à conta dos Serviços de Mídia.  

## <a name="409-conflict"></a>409 Conflito
A solicitação não foi permitida devido a um dos seguintes motivos:

* Mais de um AssetFile tem o nome especificado no Asset.
* Foi feita uma tentativa de criar um segundo AssetFile primário no Asset.
* Foi feita uma tentativa de criar uma ContentKey com a Id especificada já utilizada.
* Foi feita uma tentativa para criar um Localizador com a Id especificada já utilizada.
* Mais de um IngestManifestFile possui o nome especificado no IngestManifest.
* Foi feita uma tentativa de vincular uma segunda ContentKey de criptografia de armazenamento ao Asset criptografado para armazenamento.
* Foi feita uma tentativa de vincular a mesma ContentKey ao Asset.
* Foi feita uma tentativa de criar um localizador para um Asset cujo contêiner de armazenamento está ausente ou não está mais associado ao Asset.
* Foi feita uma tentativa de criar um localizador para um Asset que já possui 5 localizadores em uso. (O Armazenamento do Azure impõe o limite de cinco políticas de acesso compartilhado em um contêiner de armazenamento).
* Vincular a conta de armazenamento de um Asset a um IngestManifestAsset não é igual à conta de armazenamento usada pelo IngestManifest pai.  

## <a name="500-internal-server-error"></a>500 Erro Interno do Servidor
Durante o processamento da solicitação, os Serviços de Mídia encontram algum erro que impede o processamento de continuar. Isso pode ser devido a um dos seguintes motivos:

* Falha na criação de um Asset ou Job porque as informações da cota de serviço da conta dos Serviços de Mídia estão temporariamente indisponíveis.
* Falha na criação de um contêiner de armazenamento de blobs do Asset ou IngestManifest porque as informações da conta de armazenamento da conta estão temporariamente indisponíveis.
* Outro erro inesperado.

## <a name="503-service-unavailable"></a>503 Serviço Indisponível
Atualmente, o servidor não pode receber solicitações. Esse erro pode ter sido causado por excesso de solicitações ao serviço. O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres do código de erro para obter informações mais detalhadas sobre o motivo pelo qual você obteve o erro 503. Esse erro nem sempre significa limitação.
> 
> 

As possíveis descrições de status são:

* "O servidor está ocupado. As execuções anteriores desse tipo de solicitação demoraram mais de {0} segundos."
* "O servidor está ocupado. Mais de {0} solicitações por segundo podem ser limitadas."
* "O servidor está ocupado. Mais de {0} solicitações por {1} segundos podem ser limitadas."

Para resolver esse erro, é recomendável usar a lógica de repetição de retirada exponencial. Isso significa usar esperas mais longas progressivamente entre as repetições de respostas de erro consecutivas.  Para saber mais, confira [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx) (Bloco de aplicativos para manipulação de falha transitória).

> [!NOTE]
> Se estiver usando o [SDK dos Serviços de Mídia do Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 foi implementada pelo SDK.  
> 
> 

## <a name="see-also"></a>Veja também
[Códigos de erro do gerenciamento dos Serviços de Mídia](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

