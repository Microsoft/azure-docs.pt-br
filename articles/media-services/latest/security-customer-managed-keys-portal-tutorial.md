---
title: Usar chaves gerenciadas pelo cliente ou o portal BYOK
description: Neste tutorial, use o portal do Azure para habilitar as chaves gerenciadas pelo cliente ou o BYOK (Bring Your Own Key) com uma conta de armazenamento dos Serviços de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281059"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Tutorial: Usar o portal do Azure para usar as chaves gerenciadas pelo cliente ou o BYOK com os Serviços de Mídia

Com a API 2020-05-01, você pode usar uma chave RSA gerenciada pelo cliente com uma conta dos Serviços de Mídia do Azure que tenha uma identidade gerenciada pelo sistema. Este tutorial aborda as etapas usadas no portal do Azure.

Os serviços usados são:

- Armazenamento do Azure
- Cofre de Chave do Azure
- Serviços de Mídia do Azure

Neste tutorial, você aprenderá a usar o portal do Azure para:

> [!div class="checklist"]
> - Crie um grupos de recursos.
> - Criar uma conta de armazenamento com uma identidade gerenciada pelo sistema.
> - Criar uma conta dos Serviços de Mídia com uma identidade gerenciada pelo sistema.
> - Crie um cofre de chaves para armazenar uma chave RSA gerenciada pelo cliente.

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Chaves gerenciadas pelo sistema

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Para as etapas de criação da conta de armazenamento a seguir, você selecionará a opção de chave gerenciada pelo sistema em Configurações avançadas.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Para as etapas de criptografia de armazenamento a seguir, você selecionará a **opção de chave gerenciada pelo cliente**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Alterar a chave

Os Serviços de Mídia detectam automaticamente quando a chave é alterada. OPCIONAL: Para testar esse processo, crie outra versão de chave para a mesma chave. Os Serviços de Mídia devem detectar que a chave foi alterada.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender continuar usando os recursos que criou e *não quiser continuar sendo cobrado*, exclua-os.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo para saber como:
> [!div class="nextstepaction"]
> [Codificar um arquivo remoto baseado em URL e transmitir o vídeo com o REST](stream-files-tutorial-with-rest.md)
