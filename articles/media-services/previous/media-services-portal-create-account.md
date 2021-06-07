---
title: Criar uma conta dos Serviços de Mídia do Azure com o Portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de criação de uma conta do Serviços de Mídia do Azure com o portal do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011911"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Criar uma conta de Serviços de Mídia usando o portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

O portal do Azure fornece uma maneira de criar rapidamente uma conta de Serviços de Mídia do Azure (AMS). Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure. Quando você cria uma conta de Serviços de Mídia, você também cria uma conta de armazenamento associada (ou usa uma existente). Se você excluir uma conta de Serviços de Mídia, os blobs em sua conta de armazenamento relacionada não serão excluídos.

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento na mesma localização da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.

Este artigo mostra como criar uma conta dos Serviços de Mídia usando o portal do Azure.

> [!NOTE]
> Para obter informações sobre a disponibilidade dos recursos dos serviços de mídia do Azure em regiões diferentes, consulte [recursos do AMS entre regiões](availability-regions-v-2.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>Criar uma conta do AMS

As etapas nesta seção mostram como criar uma conta do AMS.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+Criar um recurso** > **Mídia** > **Serviços de Mídia**.
3. Em **CRIAR CONTA DOS SERVIÇOS DE MÍDIA** , insira os valores necessários.

   1. Em **Nome da Conta**, insira o nome da nova conta AMS. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.
   2. Em Assinatura, selecione uma das diferentes assinaturas do Azure às quais você tem acesso.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).
   4. Em **local**, selecione a região geográfica que será usada para armazenar os registros de mídia e de metadados para sua conta de serviços de mídia. Essa região será usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. 
   5. Em **Conta de Armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blobs do conteúdo de mídia de sua conta de Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.

       Saiba mais sobre o armazenamento [aqui](../../storage/common/storage-introduction.md).
   6. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
4. Clique em **Criar** na parte inferior do formulário.

    Quando a conta é criada com êxito, a página de visão geral é carregada. Na tabela de ponto de extremidade de streaming, a conta terá um ponto de extremidade de streaming padrão em estado **Parado**. 

    >[!NOTE]
    >Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 
   
## <a name="to-manage-your-ams-account"></a>Para gerenciar sua conta do AMS

Para gerenciar sua conta do AMS (por exemplo, conectar-se à API do AMS programaticamente, carregar vídeos, codificar ativos, configurar a proteção de conteúdo, monitorar o andamento do trabalho), selecione **Configurações** no lado esquerdo do portal. De **Configurações**, navegue até uma das folhas disponíveis (por exemplo: **Acesso à API**, **Ativos**, **Trabalhos**, **Proteção de conteúdo**).

## <a name="next-steps"></a>Próximas etapas

Agora você pode carregar arquivos em sua conta do AMS. Para saber mais, veja [Carregar arquivos](media-services-portal-upload-files.md).

Se você planeja acessar a API do AMS programaticamente, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
