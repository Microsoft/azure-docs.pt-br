---
title: Tutorial – Excluir uma nuvem privada da Solução VMware no Azure
description: Saiba como excluir uma nuvem privada da Solução VMware no Azure que não é mais necessária.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462092"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial: Excluir uma nuvem privada da Solução VMware no Azure

Se tiver uma nuvem privada da Solução VMware no Azure que não é mais necessária, você poderá excluí-la. A nuvem privada inclui um domínio de rede isolado, um ou mais clusters do vSphere provisionados em hosts de servidor dedicados e várias VMs (máquinas virtuais). Quando uma nuvem privada é excluída, todas as VMs, os dados delas e os clusters são excluídos. Os hosts dedicados da Solução VMware no Azure são apagados com segurança e retornados ao pool gratuito. O espaço de endereço de rede provisionado também é excluído.  

> [!CAUTION]
> A exclusão da nuvem privada é uma operação irreversível. Após a nuvem privada ser excluída, os dados não poderão ser recuperados, pois todos os componentes e as cargas de trabalho em execução serão terminados e todos os dados e configurações da nuvem privada serão destruídos, incluindo os endereços IP públicos.

## <a name="prerequisites"></a>Pré-requisitos

Se você precisar das VMs e dos dados delas posteriormente, lembre-se de fazer backup dos dados antes de excluir a nuvem privada.  Não há como recuperar as VMs e os respectivos dados.


## <a name="delete-the-private-cloud"></a>Excluir a nuvem privada

1. Acesse o console de Soluções VMware no Azure no [portal do Azure](https://portal.azure.com).

2. Selecione a nuvem privada a ser excluída.
 
3. Insira o nome da nuvem privada e selecione **Sim**. 

>[!NOTE]
>O processo de exclusão leva algumas horas para ser concluído.  
