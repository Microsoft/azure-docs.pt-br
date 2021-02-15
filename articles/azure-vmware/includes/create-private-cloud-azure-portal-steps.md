---
title: Implantar a Solução VMware no Azure
description: Etapas para implantar a Solução VMware no Azure usando o portal do Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578301"
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
   | **Senha de administrador do vCenter** | Insira uma senha de administrador da nuvem. |
   | **Senha do NSX-T Manager** | Insira uma senha de administrador do NSX-T. |
   | **Bloco de endereços** | Insira um bloco de endereços IP para a rede CIDR da nuvem privada, por exemplo, 10.175.0.0/22. |
   | **Rede Virtual** | Selecione uma rede virtual ou crie uma para a nuvem privada da Solução VMware no Azure.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Na guia Básico, insira valores para os campos." border="true":::

1. Quando terminar, selecione **Examinar + Criar**. Na tela a seguir, verifique as informações inseridas. Se todas estiverem corretas, selecione **Criar**.

   > [!NOTE]
   > Essa etapa leva aproximadamente duas horas. 

1. Verifique se a implantação foi bem-sucedida. Navegue até o grupo de recursos que você criou e selecione sua nuvem privada.  Você verá o status **Com êxito** quando a implantação for concluída. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Na guia Básico, insira valores para os campos." border="true":::