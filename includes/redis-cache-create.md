---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 0f1decae5fb3ec4a07f01c5bff7475f3d73a3cbb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171728"
---
1. Para criar um cache, primeiro entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Criar um recurso** > **Bancos de dados** > **Cache do Azure para Redis**.

    ![Novo cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Em **Novo Cache do Azure para Redis**, defina as configurações para o novo cache.

    | Configuração      | Valor sugerido  | DESCRIÇÃO |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Nome globalmente exclusivo | O nome do cache. Ele deve ser uma cadeia de caracteres de 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome de cache não pode começar nem terminar com o caractere `-` e os caracteres `-` consecutivos não são válidos.  | 
    | **Assinatura** | Sua assinatura | A assinatura na qual essa nova instância do Cache do Azure para Redis foi criada. | 
    | **Grupo de recursos** |  *TestResources* | Nome do novo grupo de recursos no qual criar o seu cache. Ao colocar todos os recursos para um aplicativo em um grupo, você pode gerenciá-los juntos. Por exemplo, excluir o grupo de recursos exclui todos os recursos associados ao aplicativo. | 
    | **Localidade** | Leste dos EUA | Escolha uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
    | **[Tipo de preço](https://azure.microsoft.com/pricing/details/cache/)** |  C0 Básico (cache de 250 MB) |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](../articles/azure-cache-for-redis/cache-overview.md). |
    | **Fixar no painel** |  Selecionado | Fixe o novo cache ao seu painel para facilitar sua localização. |

    ![Criar o cache](media/redis-cache-create/redis-cache-cache-create.png) 

3. Depois que as novas configurações de cache estiverem configuradas, selecione **Criar**. 

    Pode levar alguns minutos para que o cache seja criado. Para verificar o status, você pode monitorar o progresso no painel. Depois de o cache ter sido criado, ele mostra o status **Executando** e está pronto para uso.

    ![Cache criado](media/redis-cache-create/redis-cache-cache-created.png)

