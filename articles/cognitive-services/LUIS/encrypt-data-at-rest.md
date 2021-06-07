---
title: Criptografia do serviço de Reconhecimento vocal de dados inativos
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e permite que você gerencie suas assinaturas dos Serviços Cognitivos com as próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados inativos para o LUIS (Reconhecimento vocal) e explica como habilitar e gerenciar a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524386"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Criptografia do serviço de Reconhecimento vocal de dados inativos

O serviço de Reconhecimento vocal criptografa automaticamente os dados quando eles são persistidos na nuvem. A criptografia do serviço de Reconhecimento vocal protege seus dados e ajuda você a atender aos compromissos de conformidade e segurança de sua organização.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia dos Serviços Cognitivos

Os dados são criptografados e descriptografados usando a criptografia [AES de 256 bits](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [padrão FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2). A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Como os dados são protegidos por padrão, você não precisa modificar seu código ou seus aplicativos para aproveitar a criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há a opção de gerenciar sua assinatura com as próprias chaves chamada CMK (chaves gerenciadas pelo cliente). A CMK oferece maior flexibilidade para criar, desabilitar e revogar controles de acesso, assim como fazer a rotação deles. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Também há uma opção para gerenciar sua assinatura com as próprias chaves. A CMK (chaves gerenciadas pelo cliente), também conhecida como BYOK (Bring Your Own Key), oferecem maior flexibilidade para criar, desabilitar e revogar controles de acesso, assim como fazer a rotação deles. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Você precisa usar o Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso dos Serviços Cognitivos e o cofre de chaves precisam estar na mesma região e no mesmo locatário do Azure AD (Azure Active Directory), mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, confira [O que é o Azure Key Vault?](../../key-vault/general/overview.md).

### <a name="customer-managed-keys-for-language-understanding"></a>Chaves gerenciadas pelo cliente para Reconhecimento vocal

Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o  [formulário de solicitação de chaves gerenciadas pelo cliente do serviço LUIS](https://aka.ms/cogsvc-cmk). Levará aproximadamente de 3 a 5 dias úteis para você receber um retorno em referente ao status da sua solicitação. Dependendo da demanda, você poderá ser colocado em uma fila e ser aprovado quando abrir espaço. Depois de receber aprovação para usar a CMK com o LUIS, você precisará criar um recurso de Reconhecimento vocal do portal do Azure e selecionar E0 como o Tipo de Preço. O novo SKU funcionará da mesma forma que a SKU F0 que já está disponível, exceto para a CMK. Os usuários não poderão atualizar o SKU do F0 para o novo E0.

![Imagem da assinatura do LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>Limitações

Há algumas limitações no uso da camada E0 com aplicativos existentes/criados anteriormente:

* A migração para um recurso da E0 será bloqueada. Os usuários só poderão migrar os aplicativos deles para os recursos de F0. Depois de migrar um recurso existente para a F0, você pode criar um recurso na camada E0. Saiba mais sobre a [migração aqui](./luis-migration-authoring.md).  
* A migração de aplicativos entre um recurso ao E0 será bloqueada. Uma solução alternativa para essa limitação é exportar seu aplicativo existente e importá-lo como um recurso da E0.
* Não há suporte para o recurso de verificação ortográfica do Bing.
* O registro em log do tráfego do usuário final será desabilitado se o seu aplicativo for da E0.
* Não há suporte para a funcionalidade priming de fala do Serviço de Bot do Azure para aplicativos na camada E0. Esse recurso está disponível por meio do Serviço de Bot do Azure, que não dá suporte à CMK.
* A funcionalidade de priming de fala do portal requer o Armazenamento de Blobs do Azure. Para obter mais informações, confira [traga o próprio armazenamento](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Um novo recurso dos Serviços Cognitivos sempre é criptografado usando as chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada ao recurso de Serviços Cognitivos. A identidade gerenciada está disponível somente depois que o recurso é criado usando o tipo de preço para a CMK.

Para saber como usar chaves gerenciadas pelo cliente com o Azure Key Vault para a criptografia de Serviços Cognitivos, confira:

- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia dos Serviços Cognitivos do portal do Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Habilitar chaves gerenciadas pelo cliente também habilitará uma identidade gerenciada atribuída ao sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída ao sistema estiver habilitada, esse recurso será registrado no Azure Active Directory. Depois de ser registrada, a identidade gerenciada receberá acesso ao Key Vault selecionado durante a configuração de chave gerenciada pelo cliente. Saiba mais sobre [Identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas ao sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, depois, você migrar a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário. Portanto, as chaves gerenciadas pelo cliente podem deixar de funcionar. Para obter mais informações, confira **Transferir uma assinatura entre diretórios do Azure AD** em [Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente, você precisa usar um Azure Key Vault para armazenar suas chaves. Habilite as propriedades **Exclusão Temporária** e **Não Limpar** no cofre de chaves.

Somente chaves RSA de tamanho 2048 têm suporte com a criptografia dos Serviços Cognitivos. Para obter mais informações sobre chaves, confira **Chaves do Key Vault** em [Sobre chaves, segredos e certificados do Azure Key Vault](../../key-vault/general/about-keys-secrets-certificates.md).

### <a name="rotate-customer-managed-keys"></a>Fazer a rotação de chaves gerenciadas pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando ocorre a rotação da chave, você precisa atualizar o recurso dos Serviços Cognitivos para usar o novo URI da chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal do Azure, confira a seção intitulada **Atualizar a versão da chave** em [Configurar chaves gerenciadas pelo cliente para os Serviços Cognitivos usando o portal do Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

A rotação da chave não dispara uma nova criptografia dos dados no recurso. Nenhuma ação adicional necessária pelo usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou a CLI do Azure. Para obter mais informações, confira [PowerShell do Azure Key Vault](/powershell/module/az.keyvault//) ou [CLI do Azure Key Vault](/cli/azure/keyvault). Revogar o acesso bloqueia o acesso a todos os dados no recurso dos Serviços Cognitivos, pois a chave de criptografia não é acessível pelos Serviços Cognitivos.

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chaves gerenciadas pelo cliente do serviço LUIS](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)
