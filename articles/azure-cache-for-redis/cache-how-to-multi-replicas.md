---
title: Adicionar réplicas ao Cache do Azure para Redis (versão prévia)
description: Saiba mais sobre como adicionar mais réplicas nas suas instâncias para camada Premium de Cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566781"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Adicionar réplicas ao Cache do Azure para Redis (versão prévia)
Neste artigo, você aprenderá como configurar uma instância do Cache do Azure com réplicas adicionais usando o portal do Microsoft Azure.

As camadas Standard e Premium do Cache do Azure para Redis oferecem redundância ao hospedar cada cache em duas VMs (máquinas virtuais) dedicadas. Essas VMs são configuradas como primária e réplica. Quando a VM primária fica indisponível, a réplica detecta isso e automaticamente assume como a nova primária. Agora você pode aumentar o número de réplicas em um cache Premium para até três, dando a você um total de quatro VMs fazendo backup de um cache. Ter várias réplicas resulta em resiliência superior à que uma réplica única pode fornecer.

> [!IMPORTANT]
> Essa versão prévia é fornecida sem um Contrato de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para saber mais, confira os [Termos de uso complementares das versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Este recurso está em versão prévia - [Entre em contato conosco](mailto:azurecache@microsoft.com) se estiver interessado.
>

## <a name="create-a-cache"></a>Criar um cache
Para criar um cache, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.
  
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **Básicos**, defina as configurações para o novo cache.
   
    | Configuração      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Assinatura** | Selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
    | **Grupo de recursos** | Selecione um grupo de recursos ou selecione **Criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
    | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
    | **Localidade** | Selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
    | **Tipo de cache** | Selecione um cache de [Camada Premium](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |
   
1. Na página **Avançado**, escolha **Contagem de réplicas**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Contagem de Réplicas.":::

1. Deixe as outras opções nas configurações padrão. 

    > [!NOTE]
    > O suporte a várias réplicas funciona apenas com caches não clusterizados no momento.
    >

1. Clique em **Criar**.
   
    A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.

    > [!NOTE]
    > O número de réplicas em um cache não pode ser alterado depois da sua criação.
    >

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os recursos do Cache do Azure para Redis.

> [!div class="nextstepaction"]
> [Camadas de serviço Premium do Cache do Azure para Redis](cache-overview.md#service-tiers)
