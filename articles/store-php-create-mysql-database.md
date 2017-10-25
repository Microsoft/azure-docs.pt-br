---
title: Criar e conectar-se a um banco de dados MySQL no Azure
description: Saiba como usar o Portal do Azure para criar um banco de dados MySQL e, em seguida, conectar-se a ele de um aplicativo Web PHP no Azure.
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Criar e conectar-se a um banco de dados MySQL no Azure
Este tutorial mostrará como criar um banco de dados MySQL no [Portal do Azure](https://portal.azure.com) (o provedor é[ClearDB](http://www.cleardb.com/)) e como conectar a ele a partir de um aplicativo Web PHP em execução no [Serviço de Aplicativo do Azure](app-service/app-service-web-overview.md).

> [!NOTE]
> Você também pode criar um banco de dados MySQL como parte de um <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">modelo de aplicativo do Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Criar um banco de dados MySQL no Portal do Azure
Para criar um banco de dados MySQL no Portal do Azure, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Novo** > **Dados + Armazenamento** > **Banco de dados MySQL**.

    ![Criar um banco de dados MySQL no Azure - iniciar](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Na [folha](azure-portal-overview.md)Novo Banco de Dados MySQL, configure seu novo banco de dados MySQL da seguinte maneira (*folha*: uma página do portal abre horizontalmente):

   * **Nome do Banco de Dados**: digite um nome que possa ser identificado de forma exclusiva
   * **Assinatura**: escolha a assinatura a ser usada
   * **Tipo de Banco de Dados**: escolha **Compartilhado** para os tipos de baixo custo ou gratuito, ou **Dedicado** para obter recursos dedicados.
   * **Grupo de recursos**: adicione o banco de dados MySQL a um [grupo de recursos](azure-resource-manager/resource-group-overview.md) existente ou coloque-o em um novo. Os recursos no mesmo grupo podem ser gerenciados juntos com facilidade.
   * **Local**: escolha um local perto de você. Ao adicionar a um grupo de recursos existente, você fica bloqueado no local do grupo de recursos.
   * **Tipo de Preço**: clique em **Tipo de Preço** e escolha uma opção de preço (o tipo **Mercury** é gratuito) e clique em **Selecionar**.
   * **Termos Legais**: clique em **Termos Legais**, examine os detalhes da compra e clique em **Comprar**.
   * **Fixar no painel**: selecione se você quiser acessá-lo diretamente do painel. Isso é especialmente útil se você ainda não estiver familiarizado com a navegação do portal.

     A captura de tela a seguir é apenas um exemplo de como você pode configurar o banco de dados MySQL.  
     ![Criar um banco de dados MySQL no Azure - configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Quando tiver concluído a configuração, clique em **Criar**.

    ![Criar um banco de dados MySQL no Azure - criar](./media/store-php-create-mysql-database/create-db-3-create.png)

    Você verá uma notificação pop-up informando que a implantação foi iniciada.

    ![Criar um banco de dados MySQL no Azure - em andamento](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Você receberá outro pop-up depois que a implantação for bem-sucedida. O portal também abrirá automaticamente a folha do banco de dados MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Conectar-se ao seu banco de dados MySQL
Para ver as informações de conexão do novo banco de dados MySQL, basta clicar em **Propriedades** na folha de seu aplicativo Web.

![Criar um banco de dados MySQL no Azure - folha Banco de Dados MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Agora você pode usar essas informações de conexão em qualquer aplicativo Web. Um exemplo que mostra como usar as informações de conexão de um aplicativo PHP simples está disponível [aqui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).
