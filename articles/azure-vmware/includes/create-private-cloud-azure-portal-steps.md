---
title: Criar uma nuvem privada da Solução VMware no Azure
description: Etapas para criar uma nuvem privada da Solução VMware no Azure usando o portal do Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: da79881e609f982960468a8f26c98178f972ad43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725383"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, digite `Azure VMware Solution` e selecione **Solução VMware no Azure** na lista. Na janela **Solução VMware no Azure**, selecione **Criar**.

1. Na guia **Básico**, insira valores para os campos. A tabela a seguir lista as propriedades dos campos.

   | Campo   | Valor  |
   | ---| --- |
   | **Assinatura** | A assinatura que você planeja usar para a implantação.|
   | **Grupo de recursos** | O grupo de recursos com os recursos de sua nuvem privada. |
   | **Localidade** | Selecione uma localização, como **Leste dos EUA**.|
   | **Nome do recurso** | O nome da sua nuvem privada da Solução VMware no Azure. |
   | **SKU** | Selecione o seguinte valor do SKU: AV36 |
   | **Hosts** | O número de hosts a serem adicionados ao cluster da nuvem privada. O valor padrão é 3, que pode ser aumentado ou reduzido após a implantação.  |
   | **Bloco de endereços** | Insira um bloco de endereços IP para a rede CIDR da nuvem privada, por exemplo, 10.175.0.0/22. |
   | **Rede Virtual** | Selecione uma rede virtual ou crie uma para a nuvem privada da Solução VMware no Azure.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Na guia Básico, insira valores para os campos." border="true":::

1. Quando terminar, selecione **Examinar + Criar**. Na tela a seguir, verifique as informações inseridas. Se todas estiverem corretas, selecione **Criar**.

   > [!NOTE]
   > Essa etapa leva aproximadamente duas horas. 

1. Verifique se a implantação foi bem-sucedida. Navegue até o grupo de recursos que você criou e selecione sua nuvem privada.  Você verá o status **Com êxito** quando a implantação for concluída. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verifique se a implantação foi bem-sucedida." border="true":::