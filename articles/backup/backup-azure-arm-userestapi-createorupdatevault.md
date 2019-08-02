---
title: 'Serviço de Backup do Azure: Criar cofres de Serviços de recuperação usando a API REST'
description: gerenciar operações de backup e restauração do Backup da VM do Azure usando a API REST
author: pvrk
manager: shivamg
keywords: API REST; Backup de VM do Azure; Restauração de VM do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 0373098dd344df79be79871227f20c8a995958fa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466933"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Criar o Cofre de Serviços de Recuperação do Azure usando a API REST

As etapas para criar uma Área Segura dos Serviços de Recuperação do Azure usando a API REST são descritas na [documentação da API REST do Vault de criação](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Vamos usar este documento como referência para criar um cofre chamado "testVault" em "Oeste dos EUA".

Para criar ou atualizar um cofre dos serviços de recuperação do Azure, use a seguinte operação *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar uma solicitação

Para criar a solicitação *PUT*, o parâmetro `{subscription-id}` é obrigatório. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Defina `{resourceGroupName}` e `{vaultName}` para seus recursos, junto com o parâmetro `api-version`. Este artigo usa `api-version=2016-06-01`.

Os cabeçalhos a seguir são necessários:

| Cabeçalho da solicitação   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Obrigatória. Defina como `application/json`. |
| *Authorization:* | Obrigatória. Defina como um `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para saber mais sobre como criar a solicitação, confira [Componentes de uma solicitação/resposta de API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As definições comuns a seguir são usadas para criar um corpo de solicitação:

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   Cadeia      |  eTag Opcional       |
|localização     |  true       |Cadeia         |   Local do recurso      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|SKU     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador exclusivo do sistema para cada recurso do Azure     |
|marcações     |         | Objeto        |     Marcações de recursos    |

Observe que o nome do vault e o nome do grupo de recursos são fornecidos no PUT URI. O corpo da solicitação define o local.

## <a name="example-request-body"></a>Exemplo do corpo de solicitação

O corpo do exemplo a seguir é usado para criar um cofre no "Oeste dos EUA". Especifique o local. A SKU é sempre "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Responses

Há duas respostas bem-sucedidas para a operação para criar ou atualizar um cofre do Recovery Services:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Criado     | [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado em      |

Para saber mais sobre as respostas da API REST, veja [Processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exemplo de resposta

Uma resposta condensada *201 criada* do corpo da solicitação de exemplo anterior mostra um *id* foi atribuído e o *provisioningState* é *bem-sucedido*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

[Crie uma política de backup para fazer backup de uma VM do Azure neste Vault](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs REST do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
