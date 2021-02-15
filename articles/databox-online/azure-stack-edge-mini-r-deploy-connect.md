---
title: Tutorial para conectar-se ao Azure Stack Edge Mini R no portal do Azure
description: Saiba como conectar-se ao dispositivo Azure Stack Edge Mini R usando a IU da Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463487"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Tutorial: Conectar-se ao Azure Stack Edge Mini R

Este tutorial descreve como você pode conectar-se ao dispositivo Azure Stack Edge Mini R usando a IU da Web local.

O processo de conexão pode levar cerca de cinco minutos para ser concluído.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se a um dispositivo físico



## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu dispositivo Azure Stack Edge, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local

1. Configure o adaptador Ethernet no computador usado para conectar-se ao dispositivo Azure Stack Edge Pro com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Conecte o computador à porta 1 do seu dispositivo. Se você conectar o computador diretamente ao seu dispositivo (sem um comutador), use um cabo cruzado ou um adaptador USB Ethernet. Use a ilustração a seguir para identificar a PORTA 1 em seu dispositivo.

    ![Cabeamento para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se a um dispositivo físico


Para saber como definir as configurações de rede em seu dispositivo, confira:

> [!div class="nextstepaction"]
> [Configurar a rede](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
