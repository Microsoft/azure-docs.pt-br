---
title: Integração de Azure Key Vault com Azure Stack recurso de borda e ativação de dispositivo
description: Descreve como o Azure Key Vault está associado ao gerenciamento de segredos durante a ativação do dispositivo Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439860"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integração do Azure Key Vault com o Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

O Azure Key Vault é integrado ao recurso Azure Stack Edge para gerenciamento secreto. Este artigo fornece detalhes sobre como um Azure Key Vault é criado para Azure Stack recurso do Edge durante a ativação do dispositivo e, em seguida, é usado para o gerenciamento de segredos. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Sobre o Key Vault e o Azure Stack Edge

Azure Key Vault serviço de nuvem é usado para armazenar e controlar com segurança o acesso a tokens, senhas, certificados, chaves de API e outros segredos. Key Vault também facilita a criação e o controle das chaves de criptografia usadas para criptografar seus dados. Para obter mais informações sobre transações permitidas e encargos correspondentes, consulte [preços para Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

Para Azure Stack serviço do Edge, um dos segredos usados é CIK (chave de integridade do canal). Essa chave permite que você criptografe seus segredos. Com a integração do Key Vault, o CIK é armazenado com segurança no cofre de chaves. Para obter mais informações, consulte [armazenar segredos e chaves com segurança](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Criação do cofre de chaves

Um cofre de chaves é criado para Azure Stack recurso de borda durante o processo de geração de chave de ativação. 

- Ao criar Azure Stack recurso de borda, você precisa registrar o provedor de recursos *Microsoft. keyvault* . O provedor de recursos será registrado automaticamente se você tiver acesso de proprietário ou colaborador à assinatura. O cofre de chaves é criado na mesma assinatura e no grupo de recursos que o Azure Stack recurso de borda. 

- Quando você cria um recurso de borda Azure Stack, um Identidade de Serviço Gerenciada (MSI) também é criado e persiste durante o tempo de vida do recurso e se comunica com o provedor de recursos na nuvem. 

    Quando o MSI é habilitado, o Azure cria uma identidade confiável para o recurso de borda do Azure Stack.

- Depois de criar o Azure Stack recurso de borda e gerar uma chave de ativação do portal do Azure, um cofre de chaves será criado. Esse cofre de chaves é usado para gerenciamento de segredos e persiste enquanto o recurso de borda Azure Stack existir. 

    ![Key Vault criado durante a geração da chave de ativação](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Você pode optar por aceitar o nome da chave padrão ou especificar um nome personalizado para o cofre de chaves. O nome do cofre de chaves deve ter de 3 a 24 caracteres. Você não pode usar um cofre de chaves que já esteja em uso. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![MSI criado durante a criação do recurso de Azure Stack Edge](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Para navegar até o cofre de chaves do Azure, vá para as **Propriedades** em seu Azure Stack recurso de borda e selecione o nome do cofre de chaves. 

- Para evitar a exclusão acidental, um bloqueio de recurso é habilitado no cofre de chaves. Uma exclusão reversível também é habilitada no cofre de chaves que permite que o cofre de chaves seja restaurado dentro de 90 dias se houver uma exclusão acidental. Para obter mais informações, consulte [visão geral da exclusão de Azure Key Vault reversível](../key-vault/general/soft-delete-overview.md)

    Se o cofre de chaves for excluído acidentalmente e a duração da proteção de limpeza de 90 dias não tiver passado, siga estas etapas para [recuperar o cofre de chaves](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). 

- Se você tiver um recurso de Azure Stack do Edge existente antes que o Azure Key Vault fosse integrado ao recurso do Azure Stack Edge, você não será afetado. Você pode continuar a usar o recurso de Azure Stack Edge existente. 

- Quando o recurso de borda do Azure Stack é excluído, o Azure Key Vault também é excluído com o recurso. Você recebe uma solicitação para fornecer sua confirmação. Se você não pretende excluir esse cofre de chaves, você pode optar por não fornecer consentimento. Somente o recurso de borda Azure Stack é excluído deixando o cofre de chaves intacto. 

- Se esse cofre de chaves foi usado para armazenar outras chaves, você ainda poderá restaurá-la dentro de 90 dias de exclusão. Durante esse período de proteção de limpeza, o nome do cofre de chaves não pode ser usado para criar um novo cofre de chaves.

Se você tiver problemas relacionados ao key Vault e à ativação do dispositivo, consulte [solucionar problemas de ativação do dispositivo](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [gerar a chave de ativação](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).