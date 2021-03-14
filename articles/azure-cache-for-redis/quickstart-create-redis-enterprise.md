---
title: 'Início rápido: Criar um cache Redis Enterprise'
description: Neste início rápido, você aprenderá a criar uma instância do Cache do Azure para Redis em camadas Enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 50535ab9ac2a3541ade7b9abd8f355bbeaf6d4b3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364997"
---
# <a name="quickstart-create-a-redis-enterprise-cache-preview"></a>Início rápido: Criar um cache Redis Enterprise (versão prévia)

As camadas Enterprise do Cache do Azure para Redis fornecem [Redis Enterprise](https://redislabs.com/redis-enterprise/) totalmente integrado e gerenciado no Azure. No momento, elas estão disponíveis como uma versão prévia. Há duas novas camadas nessa versão prévia:
* Enterprise, que usa DRAM (memória volátil) em uma máquina virtual para armazenar dados
* O Enterprise Flash, que usa NVMe (memória não volátil) e memória volátil ou SSD para armazenar dados.

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter uma assinatura do Azure antes de começar. Caso não tenha uma, crie uma [conta](https://azure.microsoft.com/). Para obter mais informações, confira [Requisitos da camada Enterprise](cache-overview.md#enterprise-and-enterprise-flash-tier-requirements).

## <a name="create-a-cache"></a>Criar um cache
1. Para criar um cache, entre no portal do Azure usando o link em seu convite de versão prévia e selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecionar Cache do Azure para Redis":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* da instância de cache será *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net*. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Os níveis Enterprise estão disponíveis em regiões do Azure limitadas durante a versão prévia. |
   | **Tipo de cache** | Selecione no menu suspenso um nível *Enterprise* ou *Enterprise Flash* e um tamanho. |  A camada determina o tamanho, o desempenho e os recursos disponíveis para o cache. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Noções básicas da camada Enterprise":::

   > [!NOTE] 
   > Lembre-se de marcar a caixa em "Termos" antes de continuar.
   >

1. Selecione **Avançar: Rede** e ignore.

   > [!NOTE] 
   > A opção de link privado está sendo distribuída e pode não estar disponível imediatamente em sua região.
   >

1. Selecione **Avançar: Avançado** e defina **Política de cluster** como **Enterprise**.
   
   Você pode manter as configurações padrão ou alterá-las conforme apropriado. Ao ativar **Permitir acesso somente via TLS**, você deve usar o TLS para acessar o novo cache do aplicativo.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Noções avançadas da camada Enterprise":::

   > [!NOTE] 
   > Os Módulos Redis ainda não são compatíveis com a camada Enterprise Flash. Se você planeja usar um Módulo Redis, não se esqueça de escolher um cache de camada Enterprise.
   >
   
1. Selecione **Avançar: Marcas** e ignore.

1. Selecione **Avançar: Revisar + criar**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Resumo da camada Enterprise":::

1. Examine as configurações e clique em **Criar**.
   
   A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar uma instância da camada Enterprise do Cache do Azure para Redis.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)

