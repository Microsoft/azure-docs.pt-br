---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807800"
---
Para recuperar a URL do SAS para os dados de treinamento do modelo personalizado, acesse o recurso de armazenamento no portal do Azure e selecione a guia **Gerenciador de Armazenamento**. Navegue até o contêiner, clique com o botão direito do mouse e selecione **Obter assinatura de acesso compartilhado**. É importante obter o SAS para o contêiner, não para a conta de armazenamento em si. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor da seção **URL** para um local temporário. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.