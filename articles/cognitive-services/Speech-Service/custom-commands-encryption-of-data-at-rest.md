---
title: Criptografia serviço de Comandos Personalizados de dados inativos
titleSuffix: Azure Cognitive Services
description: Criptografia de Comandos Personalizados de dados inativos.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716577"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Criptografia de comandos personalizados de dados em repouso

Os Comandos Personalizados criptografam automaticamente os dados quando eles são persistidos na nuvem. A criptografia do serviço dos Comandos Personalizados protege seus dados e ajuda a atender aos compromissos de conformidade e segurança de sua organização.

> [!NOTE]
> O serviço dos Comandos Personalizados não habilita automaticamente a criptografia para os recursos do LUIS associados ao seu aplicativo. Se necessário, você deve habilitar a criptografia para o recurso LUIS [aqui](../luis/encrypt-data-at-rest.md).

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia dos Serviços Cognitivos
Os dados são criptografados e descriptografados usando a criptografia [AES de 256 bits](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o padrão [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2). A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Como os dados são protegidos por padrão, você não precisa modificar seu código ou seus aplicativos para aproveitar a criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Quando você usa os Comandos Personalizados, o serviço de fala armazena os seguintes dados na nuvem:
* Configuração JSON por trás do aplicativo de Comandos Personalizados
* Chave de criação e previsão do LUIS

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. No entanto, você também pode gerenciar sua assinatura com suas próprias chaves de criptografia. As Chaves Gerenciadas pelo Cliente (CMK), também conhecidas como Bring Your Own Key (BYOK), oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.


> [!IMPORTANT]
> As chaves gerenciadas pelo cliente são recursos disponíveis se criadas depois de 27 de junho de 2020. Para usar a CMK com os Serviços de Fala, será necessário criar um recurso de Fala. Depois que o recurso for criado, você poderá usar o Azure Key Vault para configurar sua identidade gerenciada.

Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o formulário de solicitação de chaves gerenciadas pelo cliente. Levará aproximadamente de 3 a 5 dias úteis para você receber um retorno referente ao status da sua solicitação. Dependendo da demanda, você poderá ser colocado em uma fila e ser aprovado quando abrir espaço. Depois de aprovado para usar a CMK com os Serviços de Fala, você precisará criar um recurso de Fala no portal do Azure.
   > [!NOTE]
   > **As CMKs (chaves gerenciadas pelo cliente) têm suporte apenas para Comandos Personalizados.**
   >
   >  **A Fala Personalizada e Voz Personalizada ainda dão suporte apenas à política BYOS (traga o próprio armazenamento).**  [Saiba mais](speech-encryption-of-data-at-rest.md)
   >
   > Se você estiver usando o recurso de fala específico para acessar esse serviço, as necessidades de conformidade deverão ser atendidas configurando explicitamente a BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Você precisa usar o Azure Key Vault para armazenar chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de Fala e o cofre de chaves precisam estar na mesma região e no mesmo locatário do Azure AD (Azure Active Directory), mas podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, confira [O que é o Azure Key Vault?](../../key-vault/general/overview.md).

Quando um novo recurso de Fala é criado e usado para provisionar Comandos Personalizados, os dados do aplicativo são sempre criptografados usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre de chaves deve ser provisionado com políticas de acesso que concedam permissões de chave para a identidade gerenciada associada ao recurso dos Serviços Cognitivos. A identidade gerenciada fica disponível somente depois que o recurso é criado usando o tipo de preço exigido para a CMK.

Habilitar chaves gerenciadas pelo cliente também habilitará uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) atribuída ao sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída ao sistema estiver habilitada, esse recurso será registrado no Azure Active Directory. Depois de ser registrada, a identidade gerenciada receberá acesso ao cofre de chaves selecionado durante a configuração de chave gerenciada pelo cliente. 

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas ao sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, depois, você migrar a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário. Portanto, as chaves gerenciadas pelo cliente podem deixar de funcionar. Para obter mais informações, confira **Transferir uma assinatura entre diretórios do Azure AD** em [Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente exige que duas propriedades sejam definidas no cofre de chaves: **Exclusão Reversível** e **Não Limpar**. Essas propriedades não estão habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou a CLI do Azure em um cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se você não tiver a as propriedades **Exclusão Reversível** e **Não Limpar** habilitadas e excluir sua chave, não será possível recuperar os dados no recurso do Serviço Cognitivo.

Para saber como habilitar essas propriedades em um cofre de chaves existente, confira as seções intituladas **Como habilitar a exclusão temporária** e **Como habilitar a proteção contra a limpeza** em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Como usar a exclusão reversível com a CLI](../../key-vault/general/key-vault-recovery.md).

Somente as chaves RSA de tamanho 2048 são compatíveis com a criptografia do Armazenamento do Azure. Para obter mais informações sobre chaves, confira **Chaves do Key Vault** em [Sobre chaves, segredos e certificados do Azure Key Vault](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Habilitar chaves gerenciadas pelo cliente para seu recurso de Fala

Para habilitar chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o recurso de Fala.
1. Na folha **Configurações** do recurso de Fala, clique em **Criptografia**. Selecione a opção **Chaves Gerenciadas Pelo Cliente**, conforme mostrado na figura a seguir.

 ![Captura de tela mostrando como selecionar Chaves Gerenciadas Pelo Cliente](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave para associar ao recurso dos Serviços Cognitivos.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI da chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **Chaves**. Selecione a chave desejada e clique nela para ver as versões. Selecione uma versão da chave para ver as configurações dessa versão.
1. Copie o valor do campo **Identificador da Chave**, que fornece o URI.

    ![Captura de tela mostrando o URI da chave do cofre de chaves](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas configurações de **Criptografia** do serviço de Fala, escolha a opção **Inserir URI de chave**.
1. Cole o URI que você copiou no campo **URI da Chave**.

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](media/custom-commands/configure-cmk-fromKV.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Ao criar uma versão de uma chave, atualize o recurso de Fala para usar a nova versão. Siga estas etapas:

1. Navegue até o recurso de Fala e exiba as configurações de **Criptografia**.
1. Insira o URI da nova versão da chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para criptografia, siga estas etapas:

1. Navegue até o recurso de Fala e exiba as configurações de **Criptografia**.
1. Insira o URI da nova chave. Como alternativa, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="rotate-customer-managed-keys"></a>Fazer a rotação de chaves gerenciadas pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando ocorre a rotação da chave, você precisa atualizar o recurso do serviço de Fala para usar o novo URI da chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal do Azure, confira [Atualizar a versão da chave](#update-the-key-version).

A rotação da chave não dispara uma nova criptografia dos dados no recurso. Nenhuma ação adicional necessária pelo usuário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou a CLI do Azure. Para obter mais informações, confira [PowerShell do Azure Key Vault](/powershell/module/az.keyvault//) ou [CLI do Azure Key Vault](/cli/azure/keyvault). A revogação do acesso bloqueia o acesso a todos os dados no recurso dos Serviços Cognitivos, pois a chave de criptografia fica inacessível pelos Serviços Cognitivos.

## <a name="disable-customer-managed-keys"></a>Desabilitar as chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, seu recurso de Fala é criptografado com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até o recurso de Fala e exiba as configurações de **Criptografia**.
1. Desmarque a caixa de seleção ao lado da configuração **Usar sua chave**.

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de Chave gerenciada pelo cliente do serviço de Fala](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)
* [O que são identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md)