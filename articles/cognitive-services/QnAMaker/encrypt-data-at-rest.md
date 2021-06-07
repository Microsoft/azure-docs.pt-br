---
title: QnA Maker criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e também permite que você gerencie suas assinaturas de serviços cognitivas com suas próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados em repouso para QnA Maker e como habilitar e gerenciar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524380"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker criptografia de dados em repouso

QnA Maker criptografa automaticamente seus dados quando eles são persistidos na nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há a opção de gerenciar sua assinatura com suas próprias chaves chamadas CMK (chaves gerenciadas pelo cliente). O CMK oferece maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados. Se o CMK estiver configurado para sua assinatura, a criptografia dupla será fornecida, que oferece uma segunda camada de proteção, permitindo que você controle a chave de criptografia por meio de seu Azure Key Vault.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

QnA Maker usa o suporte do CMK do Azure Search. Configure o [CMK no Azure Search usando Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Esta instância do Azure deve ser associada ao serviço de QnA Maker para torná-lo CMK habilitado.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

QnA Maker usa o [suporte do CMK do Azure Search](../../search/search-security-manage-encryption-keys.md)e associa automaticamente o CMK fornecido para criptografar os dados armazenados no índice do Azure Search.

---

> [!IMPORTANT]
> O recurso de serviço de Azure Search deve ter sido criado após janeiro de 2019 e não pode estar na camada gratuita (compartilhada). Não há suporte para configurar chaves gerenciadas pelo cliente no portal do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

O serviço de QnA Maker usa o CMK do serviço de Azure Search. Siga estas etapas para habilitar o CMKs:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

1. Crie uma nova instância de Azure Search e habilite os pré-requisitos mencionados nos [pré-requisitos de chave gerenciada pelo cliente para o Azure pesquisa cognitiva](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Exibir configurações de criptografia 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando você cria um recurso de QnA Maker, ele é automaticamente associado a uma instância de Azure Search. Esta instância não pode ser usada com CMK. Para usar o CMK, você precisará associar sua instância recém-criada do Azure Search que foi criado na etapa 1. Especificamente, você precisará atualizar o `AzureSearchAdminKey` e o `AzureSearchName` em seu QnA Maker recurso.

   ![Exibir configurações de criptografia 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova configuração de aplicativo:
   * **Nome**: definido como `CustomerManagedEncryptionKeyUrl`
   * **Valor**: Use o valor que você obteve na etapa 1 ao criar sua instância de Azure Search.

   ![Exibir configurações de criptografia 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora seu serviço de QnA Maker está habilitado para CMK.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

1.  Vá para a guia **criptografia** do seu serviço QnA Maker gerenciado (versão prévia).
2.  Selecione a opção **chaves gerenciadas pelo cliente** . Forneça os detalhes das [chaves gerenciadas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) e clique em **salvar**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker configuração de CMK gerenciada (visualização)" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Em um salvamento bem-sucedido, o CMK será usado para criptografar os dados armazenados no índice de Azure Search.

> [!IMPORTANT]
> É recomendável definir seu CMK em um novo serviço de Pesquisa Cognitiva do Azure antes que qualquer base de dados de conhecimento seja criada. Se você definir CMK em um serviço de QnA Maker com bases de dados de conhecimento existentes, poderá perder o acesso a eles. Leia mais sobre como [trabalhar com conteúdo criptografado](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) na pesquisa cognitiva do Azure.

> [!NOTE]
> Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie os [Serviços cognitivas Customer-Managed formulário de solicitação de chave](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão disponíveis em todas as regiões de Azure Search.

## <a name="encryption-of-data-in-transit"></a>Criptografia de dados em trânsito

QnA Maker portal é executado no navegador do usuário. Cada ação dispara uma chamada direta para a respectiva API de serviço cognitiva. Portanto, QnA Maker é compatível com os dados em trânsito.
No entanto, como o serviço do portal QnA Maker está hospedado no oeste dos EUA, ele ainda não é ideal para clientes que não sejam dos EUA. 

## <a name="next-steps"></a>Próximas etapas

* [Criptografia em Azure Search usando CMKs no Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Criptografia de dados em repouso](../../security/fundamentals/encryption-atrest.md)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)