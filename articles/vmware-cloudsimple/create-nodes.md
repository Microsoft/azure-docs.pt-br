---
title: Provisionar nós para a solução VMware por CloudSimple - Azure
description: Saiba como adicionar nós ao seu VMWare com implantação CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165247"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Provisionar nós para a solução VMware por CloudSimple - Azure

Provisionar nós no portal do Azure. Em seguida, você pode definir pagamento medida que acesse a capacidade para o seu ambiente de nuvem privada CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Adicionar um nó provisionado para sua nuvem privada de CloudSimple

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple nós**.

   ![Pesquisa CloudSimple nós](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple nós**.
4. Clique em **adicionar** para criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura onde você deseja provisionar nós CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local para os recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o tipo de nó. Você pode escolher o [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade máxima de memória e computação.
11. Selecione o número de nós para provisionar.
12. Selecione **Examinar + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

* [Criar a nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
