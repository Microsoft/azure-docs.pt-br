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
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061597"
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
