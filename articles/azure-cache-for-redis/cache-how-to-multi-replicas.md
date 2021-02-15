---
title: Adicionar réplicas ao cache do Azure para Redis (versão prévia)
description: Saiba como adicionar mais réplicas ao seu cache do Azure da camada Premium para instâncias de Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566781"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Adicionar réplicas ao cache do Azure para Redis (versão prévia)
Neste artigo, você aprenderá a configurar uma instância de cache do Azure com réplicas adicionais usando o portal do Azure.

O cache do Azure para as camadas Standard e Premium do Redis oferece redundância ao hospedar cada cache em duas VMs (máquinas virtuais) dedicadas. Essas VMs são configuradas como primária e réplica. Quando a VM primária torna-se indisponível, a réplica detecta que e assume como a nova primária automaticamente. Agora você pode aumentar o número de réplicas em um cache Premium até três, dando a você um total de quatro VMs fazendo backup de um cache. Ter várias réplicas resulta em maior resiliência do que a que uma única réplica pode fornecer.

> [!IMPORTANT]
> Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Este recurso está atualmente em visualização- [Fale conosco](mailto:azurecache@microsoft.com) se você estiver interessado.
>

## <a name="create-a-cache"></a>Criar um cache
Para criar um cache, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.
  
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **noções básicas** , defina as configurações para o novo cache.
   
    | Configuração      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Assinatura** | Selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
    | **Grupo de recursos** | Selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
    | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
    | **Localidade** | Selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
    | **Tipo de cache** | Selecione um cache de [camada Premium](https://azure.microsoft.com/pricing/details/cache/) . |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |
   
1. Na página **avançado** , escolha **contagem de réplicas**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Selecionar o Cache do Azure para Redis.":::

1. Deixe outras opções em suas configurações padrão. 

    > [!NOTE]
    > O suporte a várias réplicas funciona apenas com caches não clusterizados no momento.
    >

1. Clique em **Criar**.
   
    A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.

    > [!NOTE]
    > O número de réplicas em um cache não pode ser alterado depois de ser criado.
    >

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

> [!div class="nextstepaction"]
> [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)
