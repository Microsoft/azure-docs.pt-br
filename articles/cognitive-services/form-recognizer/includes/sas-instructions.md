---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467180"
---
Para recuperar a URL do SAS para os dados de treinamento do modelo personalizado, acesse o recurso de armazenamento no portal do Azure e selecione a guia **Gerenciador de Armazenamento**. Navegue até o contêiner, clique com o botão direito do mouse e selecione **Obter assinatura de acesso compartilhado**. É importante obter o SAS para o contêiner, não para a conta de armazenamento em si. Verifique se as permissões de **Leitura**, **Gravação**, **Exclusão** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor da seção **URL** para um local temporário. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
