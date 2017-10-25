---
title: Criar uma imagem do Docker para Gerenciamento de Modelos no Azure Machine Learning | Microsoft Docs
description: "Este documento descreve as etapas para criar uma imagem do Docker para o serviço Web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 4c4391cecaf10428b5d4cacf3b39e6a08d417053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referência da API da conta do Gerenciamento de Modelos do Azure Machine Learning

Para obter informações de configuração do ambiente de implantação, consulte [Configuração da conta do Gerenciamento de Modelos](model-management-configuration.md).

A API do Gerenciamento de Modelos do Azure Machine Learning implementa as seguintes operações:

- Registro de modelo
- Criação do manifesto
- Criação de imagens do Docker
- Criação de serviço Web

Você pode usar essa imagem para criar um serviço Web localmente, em um cluster do ACS remoto ou em outro ambiente do Docker com suporte de sua escolha.

## <a name="prerequisites"></a>Pré-requisitos
Verifique se você executou as etapas de instalação no documento [Guia de início rápido para instalação e criação](quickstart-installation.md).

Os seguintes itens são necessários antes de continuar:
1. Provisionamento da conta do Gerenciamento de Modelos
2. Criação do ambiente para implantar e gerenciar de modelos
3. Um modelo do Machine Learning

### <a name="aad-token"></a>Token do AAD
Ao usar a CLI, faça logon usando `az login`. A CLI usa o token do AAD do arquivo .azure. Se você quiser usar as APIs, haverá as seguintes opções:

##### <a name="acquiring-the-aad-token-manually"></a>Adquirir o token do AAD manualmente
Você pode fazer `az login` e obter o token mais recente do arquivo .azure no diretório base.

##### <a name="acquiring-the-aad-token-programmatically"></a>Adquirir o token do AAD programaticamente
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Depois de criar a entidade de serviço, salve a saída. Agora você pode usá-la para obter o token do AAD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
O token é colocado no cabeçalho de autorização para chamadas à API. Consulte abaixo para obter mais detalhes.


## <a name="register-model"></a>Registrar modelo

A etapa de registro de modelo registra o modelo do Machine Learning na conta do Gerenciamento de Modelos do Azure que você criou. Esse registro permite acompanhar os modelos e suas versões que são atribuídos no momento do registro. O usuário fornece o nome do modelo. Os próximos registros de modelos com o mesmo nome gera uma nova versão e uma nova id.

### <a name="request"></a>Solicitação

| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrição
Registrar um Modelo

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| model | body | O payload usado para registrar um modelo | Sim | [Modelo](#model) |


### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | OK. O registro do modelo foi bem-sucedido | [Modelo](#model) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Consular a lista de modelos em uma conta
### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrição
Consulte a lista de modelos em uma conta. A lista de resultados pode ser filtrada usando a marca e o nome. Se nenhum filtro for passado, a consulta listará todos os modelos na conta especificada. A lista retornada é paginada e a contagem de itens em cada página é um parâmetro opcional

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| name | query | O nome do objeto | Não | string |
| marca | query | A marca de modelo | Não | string |
| count | query | O número de itens a serem recuperados em uma página | Não | string |
| $skipToken | O token de continuação para recuperar a próxima página | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso | [PaginatedModelList](#paginatedmodellist) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obter detalhes do modelo
### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Descrição
Obter o modelo por id

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do objeto | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso | [Modelo](#model) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrar um manifesto com o modelo registrado e todas as dependências

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrição
Registrar um manifesto com o modelo registrado e todas as suas dependências

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| manifestRequest | body | O payload usado para registrar um manifesto | Sim | [Manifesto](#manifest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | O registro do manifesto foi bem-sucedido | [Manifesto](#manifest) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Consultar a lista de manifestos em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrição
Consulte a lista de manifestos em uma conta. A lista de resultados pode ser filtrada usando a ID do modelo e o nome do manifesto. Se nenhum filtro for passado, a consulta listará todos os manifestos na conta especificada. A lista retornada é paginada e a contagem de itens em cada página é um parâmetro opcional

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| modelId | query | A ID do modelo | Não | string |
| manifestName | query | O nome do manifesto | Não | string |
| count | query | O número de itens a serem recuperados em uma página | Não | string |
| $skipToken | query | O token de continuação para recuperar a próxima página | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [PaginatedManifestList](#paginatedmanifestlist) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obter detalhes do manifesto

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Descrição
Obtém o manifesto por id

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do objeto | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [Manifesto](#manifest) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Criar uma imagem

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrição
Criar uma imagem como uma imagem do Docker no ACR

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| imageRequest | body | O payload usado para criar uma imagem | Sim | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | A URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa de criação de imagem. | Operation-Location |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Consultar a lista de imagens em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrição
Consulte a lista de imagens em uma conta. A lista de resultados pode ser filtrada usando o nome e a ID do manifesto. Se nenhum filtro for passado, a consulta listará todas as imagens na conta especificada. A lista retornada é paginada e a contagem de item em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| manifestId | query | A ID do manifesto | Não | string |
| manifestName | query | O nome do manifesto | Não | string |
| count | query | O número de itens a serem recuperados em uma página | Não | string |
| $skipToken | query | O token de continuação para recuperar a próxima página | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [PaginatedImageList](#paginatedimagelist) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obter os detalhes da imagem

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Descrição
Obtém a imagem por ID

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID da imagem | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [Imagem](#image) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Criar um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrição
Criar um serviço usando uma imagem

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| serviceRequest | body | O payload usado para criar um serviço | Sim | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | A URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa de criação de serviço. | Operation-Location |
| 409 | O serviço com o nome especificado já existe |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Consulte a lista de serviços em uma conta.

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrição
Consulte a lista de serviços em uma conta. A lista de resultados pode ser filtrada usando nome/ID do modelo, nome/ID do manifesto, ID da imagem, nome do serviço ou ID do recurso de computação do Machine Learning. Se nenhum filtro for passado, a consulta listará todos os serviços na conta. A lista retornada é paginada e a contagem de item em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| serviceName | query | O nome do serviço | Não | string |
| modelId | query | O Nome do Modelo | Não | string |
| modelName | query | A ID do modelo | Não | string |
| manifestId | query | A ID do manifesto | Não | string |
| manifestName | query | O Nome do Manifesto | Não | string |
| imageId | query | A ID da Imagem | Não | string |
| computeResourceId | query | A ID do recurso de computação do Machine Learning | Não | string |
| count | query | O número de itens a serem recuperados em uma página | Não | string |
| $skipToken | query | O token de continuação para recuperar a próxima página | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [PaginatedServiceList](#paginatedservicelist) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obter detalhes do serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Obtém o serviço por ID

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do objeto | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [ServiceResponse](#serviceresponse) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Atualizar um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Atualizar um serviço existente

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do objeto | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| serviceUpdateRequest | body | O payload usado para atualizar um serviço existente | Sim |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | A URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa do serviço de atualização. | Operation-Location |
| 404 | O serviço com a ID especificada não existe |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Excluir um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| EXCLUIR |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Exclui um serviço

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do objeto | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso |  |
| 204 | O serviço com a ID especificada não existe |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obter chaves de serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrição
Obtém as chaves de serviço

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do serviço | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [AuthKeys](#authkeys)
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Gerar novamente as chaves de serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrição
Gera novamente as chaves de serviço e as retorna

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID do serviço | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| regenerateKeyRequest | body | O payload usado para atualizar um serviço existente | Sim | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [AuthKeys](#authkeys)
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Consulte a lista de implantações em uma conta.

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Descrição
Consulte a lista de implantações em uma conta. A lista de resultados pode ser filtrada usando a ID de serviço, que retornará somente as implantações criadas para o serviço específico. Se nenhum filtro for passado, a consulta listará todas as implantações na conta especificada.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |
| serviceId | query | A ID do Serviço | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [DeploymentList](#deploymentlist) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obter detalhes de implantação

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Descrição
Obtém a implantação por ID

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID de Implantação | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [Implantação](#deployment) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obter detalhes da operação

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Descrição
Obter o status da operação assíncrona por ID da operação

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | A ID de assinatura do Azure | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | O nome da conta do Gerenciamento de Modelos | Sim | string |
| ID | path | A ID da operação | Sim | string |
| api-version | query | A versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | O Token de Autorização que deve ser algo como 'Portador XXXXXX' | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | sucesso | [OperationStatus](#asyncoperationstatus) |
| padrão | resposta de erro que descreve por que a operação falhou | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definições

<a name="asset"></a>
### <a name="asset"></a>Ativo
Objeto de ativo que será necessário durante a criação da imagem do Docker


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID do ativo|string|
|**mimeType**  <br>*opcional*|O tipo MIME do conteúdo do modelo. Para obter mais detalhes sobre o tipo MIME, abra https://www.iana.org/assignments/media-types/media-types.xhtml|string|
|**unpack**  <br>*opcional*|Indica onde precisamos desempacotar o conteúdo durante a criação da imagem do Docker.|Booliano|
|**url**  <br>*opcional*|A URL de localização do ativo|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
O estado da operação assíncrona

*Tipo*: enum (NotStarted, Em execução, Cancelado, Com êxito, Com falha)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
O status da operação


|Nome|Descrição|Esquema|
|---|---|---|
|**createdTime**  <br>*opcional*  <br>*read-only*|A hora de criação de operação assíncrona (UTC)|cadeia de caracteres (data e hora)|
|**endTime**  <br>*opcional*  <br>*read-only*|A hora de término da operação assíncrona (UTC)|cadeia de caracteres (data e hora)|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|A ID da operação assíncrona|string|
|**operationType**  <br>*opcional*|O tipo de operação assíncrona|Enum (imagem, serviço)|
|**resourceLocation**  <br>*opcional*|O recurso criado/atualizado pela operação assíncrona|string|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
As chaves de autenticação de um serviço


|Nome|Descrição|Esquema|
|---|---|---|
|**primaryKey**  <br>*opcional*|A chave primária|string|
|**secondaryKey**  <br>*opcional*|A chave secundária|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Configurações para escala automática


|Nome|Descrição|Esquema|
|---|---|---|
|**autoscaleEnabled**  <br>*opcional*|Habilitar ou desabilitar a escala automática|Booliano|
|**maxReplicas**  <br>*opcional*|O número máximo para aumentar as réplicas de pod.  <br>**Valor mínimo**: `1`|inteiro|
|**minReplicas**  <br>*opcional*|O número mínimo para reduzir as réplicas de pod verticalmente.  <br>**Valor mínimo**: `0`|inteiro|
|**refreshPeriodInSeconds**  <br>*opcional*|Tempo de atualização para o gatilho de dimensionamento automático.  <br>**Valor mínimo**: `1`|inteiro|
|**targetUtilization**  <br>*opcional*|Percentual de utilização em que o dimensionamento automático será disparado.  <br>**Valor mínimo**: `0`  <br>**Valor máximo**: `100`|inteiro|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Recursos de computação do Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID do recurso|string|
|**tipo**  <br>*opcional*|Tipo de recurso|enum (cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuração para reservar recursos para o contêiner no cluster


|Nome|Descrição|Esquema|
|---|---|---|
|**cpu**  <br>*opcional*|Especifica a reserva de CPU. Formato para o Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu|string|
|**memory**  <br>*opcional*|Especifica a reserva de memória. Formato para o Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory|string|


<a name="deployment"></a>
### <a name="deployment"></a>Implantação
Instância de uma implantação do Azure Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*read-only*|A hora de criação de implantação (UTC)|cadeia de caracteres (data e hora)|
|**expiredAt**  <br>*opcional*  <br>*read-only*|A hora de expiração da implantação (UTC)|cadeia de caracteres (data e hora)|
|**ID**  <br>*opcional*|A ID de implantação|string|
|**imageId**  <br>*opcional*|A ID da imagem associada a essa implantação|string|
|**serviceName**  <br>*opcional*|O nome do serviço|string|
|**status**  <br>*opcional*|Status de implantação atual|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Uma matriz de objetos de implantação.

*Tipo*: matriz de <[implantação](#deployment)>


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Detalhe do erro do serviço de Gerenciamento de Modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**code**  <br>*obrigatório*|código de erro|string|
|**message**  <br>*obrigatório*|mensagem de erro|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Objeto de erro do serviço de Gerenciamento de Modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**code**  <br>*obrigatório*|código de erro|string|
|**details**  <br>*opcional*|Uma matriz de objetos de detalhe do erro.|matriz de < [ErrorDetail](#errordetail) >|
|**message**  <br>*obrigatório*|Mensagem de erro|string|
|**statusCode**  <br>*opcional*|Código de status HTTP|inteiro|


<a name="image"></a>
### <a name="image"></a>Imagem
A imagem do Azure Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*opcional*|A ID do ambiente criado nos Serviços de Computação do Machine Learning|string|
|**createdTime**  <br>*opcional*|A hora de criação da imagem (UTC)|cadeia de caracteres (data e hora)|
|**creationState**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*opcional*|O texto de descrição da imagem|string|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|A ID da Imagem|string|
|**imageBuildLogUri**  <br>*opcional*|URI dos logs carregados do build da imagem|string|
|**imageLocation**  <br>*opcional*|Cadeia de caracteres do local do Registro de Contêiner do Azure da imagem criada|string|
|**imageType**  <br>*opcional*||[ImageType](#imagetype)|
|**manifest**  <br>*opcional*||[Manifesto](#manifest)|
|**name**  <br>*opcional*|O nome da imagem|string|
|**version**  <br>*opcional*|A versão da imagem definida pelo serviço de Gerenciamento de Modelos|inteiro|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Solicitação para criar uma imagem do Azure Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*obrigatório*|A ID do ambiente criado nos Serviços de Computação do Machine Learning|string|
|**description**  <br>*opcional*|O texto de descrição da imagem|string|
|**imageType**  <br>*obrigatório*||[ImageType](#imagetype)|
|**manifestId**  <br>*obrigatório*|A ID do manifesto do qual a imagem será criada|string|
|**name**  <br>*obrigatório*|O nome da imagem|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Especifica o tipo da imagem

*Tipo*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifesto
O manifesto do Azure Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**assets**  <br>*obrigatório*|A lista de ativos|matriz de < [ativos](#asset) >|
|**createdTime**  <br>*opcional*  <br>*read-only*|A hora de criação do manifesto (UTC)|cadeia de caracteres (data e hora)|
|**description**  <br>*opcional*|O texto de descrição do manifesto|string|
|**driverProgram**  <br>*obrigatório*|O programa de driver do manifesto.|string|
|**ID**  <br>*opcional*|A ID do manifesto|string|
|**modelIds**  <br>*opcional*|Lista de IDs dos modelos registrados. A solicitação falhará se um dos modelos incluídos não estiver registrado|matriz de < cadeia de caracteres >|
|**modelType**  <br>*opcional*|Especifica que os modelos já estão registrados com o serviço de Gerenciamento de Modelos|Enum (registrado)|
|**name**  <br>*obrigatório*|O nome do manifesto|string|
|**targetRuntime**  <br>*obrigatório*||[TargetRuntime](#targetruntime)|
|**version**  <br>*opcional*  <br>*read-only*|A versão do manifesto atribuída pelo serviço de Gerenciamento de Modelos|inteiro|
|**webserviceType**  <br>*opcional*|Especifica o tipo desejado de serviço Web que será criado usando o manifesto|Enum (em tempo real)|


<a name="model"></a>
### <a name="model"></a>Modelo
Instância de uma modelo do Azure Machine Learning


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*read-only*|A hora de criação do modelo (UTC)|cadeia de caracteres (data e hora)|
|**description**  <br>*opcional*|O texto de descrição do modelo|string|
|**ID**  <br>*opcional*  <br>*read-only*|A ID do modelo|string|
|**mimeType**  <br>*obrigatório*|O tipo MIME do conteúdo do modelo. Para obter mais detalhes sobre o tipo MIME, abra https://www.iana.org/assignments/media-types/media-types.xhtml|string|
|**name**  <br>*obrigatório*|O nome do modelo|string|
|**marcas**  <br>*opcional*|Lista de marcas do modelo|matriz de < cadeia de caracteres >|
|**unpack**  <br>*opcional*|Indica se é necessário descompactar o modelo durante a criação da imagem do Docker.|Booliano|
|**url**  <br>*obrigatório*|A URL do modelo. Geralmente, colocamos uma URL de SAS aqui.|string|
|**version**  <br>*opcional*  <br>*read-only*|A versão do modelo atribuída pelo serviço de Gerenciamento de Modelos|inteiro|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
As informações de coleta de dados do modelo


|Nome|Descrição|Esquema|
|---|---|---|
|**eventHubEnabled**  <br>*opcional*|Habilitar o hub de eventos para um serviço|Booliano|
|**storageEnabled**  <br>*opcional*|Habilitar o armazenamento para um serviço|Booliano|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Lista paginada de imagens


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Um link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Uma matriz de objetos de modelo|matriz de < [imagens](#image) >|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Lista paginada de manifestos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Um link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Uma matriz de objetos de manifesto.|matriz de < [manifestos](#manifest) >|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Lista paginada de modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Um link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Uma matriz de objetos de modelo.|matriz de < [modelos](#model) >|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Lista paginada de serviços.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Um link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Uma matriz de objetos de serviço.|matriz de < [ServiceResponse](#serviceresponse) >|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Solicitação para criar um serviço


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obrigatório*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obrigatório*|A imagem para criar o serviço|string|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas  <br>**Valor mínimo**: `1`|inteiro|
|**name**  <br>*obrigatório*|O nome do serviço|string|
|**numReplicas**  <br>*opcional*|O número de réplicas de pod em execução em um determinado momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Solicitação para gerar novamente a chave de um serviço


|Nome|Descrição|Esquema|
|---|---|---|
|**keyType**  <br>*opcional*|Especifica qual chave deve ser gerada novamente|enum (primária, secundária)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Status detalhado do serviço


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*|A hora de criação do serviço (UTC)|cadeia de caracteres (data e hora)|
|**ID**  <br>*opcional*|A ID do Serviço|string|
|**image**  <br>*opcional*||[Imagem](#image)|
|**manifest**  <br>*opcional*||[Manifesto](#manifest)|
|**models**  <br>*opcional*|Lista de modelos|matriz de < [modelos](#model) >|
|**name**  <br>*opcional*|O nome do serviço|string|
|**scoringUri**  <br>*opcional*|O URI para pontuação do serviço|string|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*opcional*|A hora da última atualização (UTC)|cadeia de caracteres (data e hora)|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obrigatório*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas  <br>**Valor mínimo**: `1`|inteiro|
|**numReplicas**  <br>*opcional*|O número de réplicas de pod em execução em um determinado momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Solicitação para atualizar um serviço


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*opcional*|A imagem para criar o serviço|string|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas  <br>**Valor mínimo**: `1`|inteiro|
|**numReplicas**  <br>*opcional*|O número de réplicas de pod em execução em um determinado momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Tipo do tempo de execução de destino.


|Nome|Descrição|Esquema|
|---|---|---|
|**properties**  <br>*obrigatório*||< cadeia de caracteres, cadeia de caracteres > mapa|
|**runtimeType**  <br>*obrigatório*|Especifica o tempo de execução|enum (SparkPython, Python)|

