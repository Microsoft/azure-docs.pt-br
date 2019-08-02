---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172339"
---
>[!IMPORTANT]
>Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Serviço do Azure Machine Learning.


### <a name="delete-everything"></a>Excluir tudo

Se você não pretende usar os recursos criados, exclua todo o grupo de recursos para não gerar encargos:

1. No portal do Azure, selecione **Grupos de recursos** no lado esquerdo da janela.
 
   ![Excluir um grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. No lado direito da janela, selecione o botão de reticências ( **...** ).

1. Selecione **Excluir grupo de recursos**.

A exclusão de um grupo de recursos também exclui todos os recursos criados na interface visual.  

### <a name="delete-only-the-compute-target"></a>Excluir apenas o destino de computação

O destino de computação que você criou aqui é *dimensionado automaticamente* para zero nós quando não estiver sendo usado. Isso serve para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o espaço de trabalho.

    ![Excluir o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. Na seção **Computação** do seu espaço de trabalho, selecione o recurso.

1. Selecione **Excluir**.

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Na interface visual em que você criou seu experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **Excluir**.

![Excluir experimentos](./media/aml-ui-cleanup/delete-experiment.png)
