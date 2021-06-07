---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95971324"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **+Criar um recurso** > **Mídia** > **Serviços de Mídia**.
1. Na seção **Criar uma conta dos Serviços de Mídia**, insira os valores necessários.

    | Name | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos Serviços de Mídia. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.|
    |**Assinatura**|Caso tenha mais de uma assinatura, selecione uma na lista de assinaturas do Azure às quais você tem acesso.|
    |**Grupo de recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localidade**|Selecione a região geográfica que será usada para armazenar os registros de mídia e metadados da sua conta dos Serviços de Mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. |
    |**Conta de armazenamento**|Selecione uma conta de armazenamento para fornecer o armazenamento de blobs do conteúdo de mídia da sua conta dos Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.<br/><br/>Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Use o portal do Azure para adicionar contas de armazenamento secundárias. Para obter mais informações, confira [Contas do Armazenamento do Azure com as contas dos Serviços de Mídia do Azure](../storage-account-concept.md).<br/><br/>A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.|
    |**Configurações avançadas**| Crie uma conta usando uma identidade gerenciada pelo sistema selecionando o botão de opção **Gerenciada pelo sistema**.  Essa seleção permitirá que você use as chaves gerenciadas pelo cliente ou o BYOK (Bring Your Own Key) e os Serviços de Mídia para habilitar o armazenamento confiável.  Para obter mais informações sobre as chaves gerenciadas pelo cliente, confira [Bring Your Own Key (chaves gerenciadas pelo cliente) com os Serviços de Mídia](../concept-use-customer-managed-keys-byok.md). Além disso, as [identidades gerenciadas](../concept-managed-identities.md) também serão habilitadas.

1. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
1. Clique em **Criar** na parte inferior do formulário.
