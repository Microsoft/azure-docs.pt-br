---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 05331c710817e575deb7729189c9b2d8ccbafd7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753804"
---
1. Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure que você usará. 

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard.png)

1. No canto superior esquerdo do Portal, selecione **Criar um recurso**.

   ![Criar um recurso no Portal do Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

1. Na barra de pesquisa, digite **Machine Learning**. Selecione o resultado da pesquisa do **Espaço de trabalho de serviço de Machine Learning**.

   ![Pesquisa de workspace](./media/aml-create-in-portal/allservices-search.PNG)

1. No painel **Workspace do Machine Learning**, role até a parte inferior e selecione **Criar** para começar.

   ![Criar](./media/aml-create-in-portal/portal-create-button.png)

1. No painel **Espaço de trabalho de serviço ML**, configure seu espaço de trabalho.

   Campo|DESCRIÇÃO
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de espaços de trabalho criados por outras pessoas.  
   Assinatura |Selecione a assinatura do Azure que você deseja usar.
   Grupo de recursos | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. 
   Local padrão | Selecione a localização mais próxima a seus usuários e recursos de dados. Este local é onde o local de trabalho é criado.

   ![Criar workspace](./media/aml-create-in-portal/workspace-create.png)

1. Para iniciar o processo de criação, selecione **Criar**. Pode demorar um pouco para o workspace ser criado.

1. Para verificar o status da implantação, selecione o ícone de notificações **sino** na barra de ferramentas.

1. Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. Ele também está presente na seção notificações. Para exibir o novo espaço de trabalho, selecione **Ir para o recurso**.

   ![Status de criação do espaço de trabalho](./media/aml-create-in-portal/notifications.png)
