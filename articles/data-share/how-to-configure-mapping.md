---
title: Configurar um mapeamento de conjunto de dados no Azure data share
description: Saiba como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88257842"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Como configurar um mapeamento de DataSet para um compartilhamento recebido no compartilhamento de dados do Azure

Este artigo explica como configurar um mapeamento de DataSet para um compartilhamento recebido usando o compartilhamento de dados do Azure. Talvez você queira configurar um mapeamento de conjunto de dados para especificar um armazenamento de data de destino no qual você deseja receber dados ou se precisar alterar o armazenamento de dados de destino.

## <a name="navigate-to-a-received-data-share"></a>Navegar até um compartilhamento de dados recebido

No serviço de compartilhamento de dados do Azure, navegue até o seu compartilhamento recebido e selecione a guia **DataSets** . 

![Mapeamento de DataSet](./media/dataset-mapping.png "Mapeamento de DataSet") 

Marque a caixa ao lado do conjunto de um para o qual você gostaria de atribuir um destino. Selecione **+ mapear para destino** para escolher um novo repositório de destino. Selecione **desmapear** primeiro se o conjunto de dados já estiver mapeado e você quiser alterar o armazenamento de dado de destino.

![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

## <a name="select-a-target-store"></a>Selecionar um repositório de destino

Selecione um tipo de armazenamento de dados de destino no qual deseja que os dados sejam inseridos. Para o compartilhamento baseado em instantâneo, todos os dados que já existem em todas as contas de armazenamento mapeadas anteriormente não serão automaticamente movidos para o novo repositório de destino. Para o compartilhamento in-loco, selecione um armazenamento de dados na Localização especificada. A Localização é o data center do Azure no qual o armazenamento de dados de origem do provedor de dados está localizado.

![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecionar um formato de arquivo (somente fontes SQL)

Se os dados de origem forem de uma fonte baseada em SQL e você quiser recebê-los como um arquivo, poderá escolher em qual formato ele será recebido. 

![Escolher formato](./media/sql-file-formats.png "Formatos de arquivo SQL")

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).



