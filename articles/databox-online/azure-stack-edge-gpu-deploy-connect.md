---
title: Tutorial para se conectar, configurar e ativar um dispositivo do Azure Stack Edge Pro com GPU no portal do Azure | Microsoft Docs
description: Saiba como conectar o dispositivo do Azure Stack Edge com uma GPU integrada usando a IU da Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 0046bc6376b0a03f75aff5461b99e3f450dc0a28
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062940"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Tutorial: Conectar-se ao Azure Stack Edge Pro com GPU

Este tutorial descreve como você faz para conectar o dispositivo do Azure Stack Edge Pro com uma GPU integrada usando a IU da Web local.

O processo de conexão pode levar cerca de cinco minutos para ser concluído.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se a um dispositivo físico


## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar o dispositivo do Azure Stack Edge Pro com uma GPU, verifique se você fez o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local

1. Configure o adaptador Ethernet no computador usado para conectar-se ao dispositivo Azure Stack Edge Pro com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Conecte o computador à porta 1 do seu dispositivo. Se você conectar o computador diretamente ao seu dispositivo (sem um comutador), use um cabo cruzado ou um adaptador USB Ethernet. Use a ilustração a seguir para identificar a PORTA 1 em seu dispositivo.

    ![Backplane de um dispositivo cabeado](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    O backplane do dispositivo pode ficar um pouco diferente, dependendo do modelo exato que você recebeu. Para obter mais informações, confira [Caber seu dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


3. Abrir uma janela do navegador e acessar o local da web da interface do usuário do dispositivo em `https://192.168.100.10`.  
    Essa ação pode levar alguns minutos depois que você tenha ativado o dispositivo.

    Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro de certificado de segurança do site](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Selecione **Continuar para essa página da Web**.  
    Essas etapas podem variar dependendo do navegador que você está usando.

5. Entrar para a web da interface do usuário do seu dispositivo. A senha padrão é *Senha1*. 
   
    ![Página de entrada do dispositivo Azure Stack Edge Pro](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. No prompt, altere a senha de administrador do dispositivo.  
    A nova senha deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.

Agora você está na página de **Visão geral** do seu dispositivo. A próxima etapa é definir as configurações de rede do seu dispositivo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se a um dispositivo físico


Para saber como definir as configurações de rede em seu dispositivo do Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Configurar a rede](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
