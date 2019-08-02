---
title: Configurar um mapeamento de conjuntos de dados
description: Configurar um mapeamento de conjuntos de dados
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789235"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Como configurar um mapeamento de conjunto de dados para um compartilhamento recebido na visualização de compartilhamento de dados do Azure

Este artigo explica como configurar um mapeamento de conjunto de dados para um compartilhamento recebidas usando visualização de compartilhamento de dados do Azure. Você vai querer fazer isso, se você aceita um convite de compartilhamento de dados, mas optou por "aceitar e configurar mais tarde". Caso contrário, simplesmente talvez queira alterar a conta de armazenamento de destino para os dados recebidos. 

## <a name="navigate-to-a-received-data-share"></a>Navegue até um compartilhamento de dados recebidos

No serviço de compartilhamento de dados do Azure, navegue até seu compartilhamento recebido e selecione o **detalhes** guia. 

![Mapeamento de conjunto de dados](./media/dataset-mapping.png "mapeamento de conjunto de dados") 

Marque a caixa ao lado do conjunto de dados que você deseja atribuir um destino para e, em seguida, clique em **+ são mapeados para o destino**. Você talvez precise cancelar o mapeamento primeiro se você já tiver configurado uma conta de armazenamento de destino e você deseja alterar o mapeamento para uma conta de armazenamento diferente. 

![Mapa de destino](./media/dataset-map-target.png "são mapeados para o destino") 

## <a name="select-a-new-storage-account"></a>Selecione uma nova conta de armazenamento 

Selecione uma conta de armazenamento que você gostaria que os dados sejam colocados no. Observe que todos os dados que já existe em qualquer mapeou anteriormente a contas de armazenamento não será movido automaticamente para a nova conta de armazenamento.

![Conta de armazenamento de destino](./media/map-target.png "armazenamento de destino") 

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o [compartilhar seus dados](share-your-data.md) tutorial.



