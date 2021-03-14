---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751050"
---
Você pode optar por gerenciar a criptografia no nível de cada disco gerenciado com suas próprias chaves. A criptografia do lado do servidor para discos gerenciados com chaves gerenciadas pelo cliente oferece uma experiência integrada com o Azure Key Vault. É possível importar [as chaves RSA](../articles/key-vault/keys/hsm-protected-keys.md) para o Key Vault ou gerar novas chaves RSA no Azure Key Vault. 

Os discos gerenciados do Azure tratam a criptografia e a descriptografia de maneira totalmente transparente com o uso da [criptografia de envelope](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ela criptografa dados usando uma DEK (chave de criptografia de dados) baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 que, por sua vez, é protegida com suas chaves. O serviço de armazenamento gera chaves de criptografia de dados e as criptografa com chaves gerenciadas pelo cliente usando a criptografia RSA. A criptografia de envelope permite alternar (mudar) as chaves periodicamente de acordo com as políticas de conformidade, sem afetar as VMs. Quando você alterna as chaves, o serviço de armazenamento criptografa novamente as chaves de criptografia de dados com as novas chaves gerenciadas pelo cliente. 

#### <a name="full-control-of-your-keys"></a>Controle total de suas chaves

Você deve conceder acesso a discos gerenciados em seu Key Vault para usar suas chaves para criptografar e descriptografar o DEK. Isso permite o controle total de dados e chaves. É possível desabilitar as chaves ou revogar o acesso aos discos gerenciados a qualquer momento. Você também pode auditar o uso da chave de criptografia com monitoramento do Azure Key Vault para garantir que apenas os discos gerenciados ou outros serviços confiáveis do Azure acessem as chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Ao desabilitar ou excluir a chave, todas as VMs com discos que usam essa chave serão desligadas automaticamente. Depois disso, as VMs não serão utilizáveis, a menos que a chave seja habilitada novamente ou você atribua uma nova chave.    

Para ultra discos: quando você desabilita ou exclui uma chave, qualquer VM com ultra disks usando a chave não será desligada automaticamente. Depois de desalocar e reiniciar as VMs, os discos deixarão de usar a chave e as VMs não ficarão online novamente. Para colocar as VMs online novamente, você deve atribuir uma nova chave ou habilitar a chave existente.    

O diagrama a seguir mostra como os discos gerenciados usam o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Fluxo de trabalho de chaves gerenciadas pelo cliente e disco gerenciado. Um administrador cria um Azure Key Vault e, em seguida, cria um conjunto de criptografia de disco e configura o conjunto de criptografia de disco. O conjunto está associado a uma VM, o que permite que o disco use o Azure AD para autenticar":::

A lista a seguir explica o diagrama mais detalhadamente:

1. Um administrador do Azure Key Vault cria os recursos do Key Vault.
1. O administrador do Key Vault importa as chaves RSA para o Key Vault ou gera novas chaves RSA no Key Vault.
1. Esse administrador cria uma instância do recurso de conjunto de criptografia de disco, especificando uma ID do Azure Key Vault e uma URL da chave. O conjunto de criptografia de disco é um novo recurso que simplifica o gerenciamento de chaves dos discos gerenciados. 
1. Quando um conjunto de criptografia de disco é criado, uma [identidade gerenciada atribuída ao sistema](../articles/active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Active Directory (AD) e associada ao conjunto de criptografia de disco. 
1. O administrador do Azure Key Vault concede a permissão de identidade gerenciada para realizar operações no Key Vault.
1. Um usuário da VM cria discos associando-os ao conjunto de criptografia de disco. O usuário da VM também pode habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para recursos existentes, associando-as ao conjunto de criptografia de disco. 
1. Os discos gerenciados usam a identidade gerenciada para enviar solicitações ao Azure Key Vault.
1. Para leitura ou gravação de dados, os discos gerenciados enviam solicitações ao Azure Key Vault para criptografar (encapsular) e descriptografar (desencapsular) a chave de criptografia de dados, a fim de criptografar e descriptografar os dados. 

Para revogar o acesso às chaves gerenciadas pelo cliente, confira [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](/cli/azure/keyvault). Revogar o acesso bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia não é acessível pelo Armazenamento do Microsoft Azure.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Rotação de chave automática de chaves gerenciadas pelo cliente (versão prévia)

Você pode optar por habilitar a rotação de chaves automática para a versão de chave mais recente. Um disco faz referência a uma chave por meio de seu conjunto de criptografia de disco. Quando você habilita a rotação automática para um conjunto de criptografia de disco, o sistema atualiza automaticamente todos os discos gerenciados, instantâneos e imagens que fazem referência ao conjunto de criptografia de disco para usar a nova versão da chave em uma hora. No momento, o recurso está disponível em regiões limitadas na visualização. Para disponibilidade regional, consulte a seção [regiões com suporte](#supported-regions) .