---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 56545b02c35d68455497c93582073c73d6c75da5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171594"
---
Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas:

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Contas de armazenamento**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](./media/storage-create-account-portal-include/create-resource-group.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Deixe esses campos definidos como seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implantação     |Gerenciador de Recursos         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (uso geral v2)         |
   |Replicação     |Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)         |
   |Camada de acesso     |Dinâmica         |

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Para obter mais informações sobre tipos de contas de armazenamento e outras configurações da conta de armazenamento, confira [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
