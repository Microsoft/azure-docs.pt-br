---
title: Definir a versão do Redis para o cache do Azure para Redis (versão prévia)
description: Saiba como configurar a versão do Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349130"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Definir a versão do Redis para o cache do Azure para Redis (versão prévia)
Neste artigo, você aprenderá a configurar a versão do software Redis para ser usada com sua instância de cache. O cache do Azure para Redis oferece a versão principal mais recente do Redis e pelo menos uma versão anterior. Ele atualizará essas versões regularmente, pois o software Redis mais recente será lançado. Você pode escolher entre as duas versões disponíveis. Tenha em mente que seu cache será atualizado para a próxima versão automaticamente se a versão que está usando atualmente não for mais suportada.

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Criar um cache
Para criar um cache, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.
  
1. Na página **Novo** , selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **noções básicas** , defina as configurações para o novo cache.
   
    | Configuração      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Assinatura** | Selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
    | **Grupo de recursos** | Selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
    | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
    | **Localidade** | Selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
    | **Tipo de cache** | Selecione uma [camada de cache e um tamanho](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |
   
1. Na página **avançado** , escolha uma versão do Redis a ser usada.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Versão do Redis.":::

1. Clique em **Criar**. 
   
    A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução** , o cache estará pronto para uso.

    > [!NOTE]
    > Neste momento, a versão do Redis não pode ser alterada depois que um cache é criado.
    >

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

> [!div class="nextstepaction"]
> [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)
