---
title: Tutorial para se conectar, configurar e ativar um dispositivo Azure Stack Edge Pro R no portal do Azure | Microsoft Docs
description: Saiba como conectar o dispositivo Azure Stack Edge Pro R usando a IU da Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 1ded68783ec45c649ab4aa41996cb0113a7fa42d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059914"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Tutorial: Conectar-se ao Azure Stack Edge Pro R

Este tutorial descreve como você pode conectar-se ao dispositivo Azure Stack Edge Pro R usando a IU da Web local.

O processo de conexão pode levar cerca de cinco minutos para ser concluído.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se a um dispositivo físico


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Pro R, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local

1. Configure o adaptador Ethernet no computador usado para conectar-se ao dispositivo Azure Stack Edge Pro R com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Conecte o computador à porta 1 do seu dispositivo. Se você conectar o computador diretamente ao seu dispositivo (sem um comutador), use um cabo cruzado ou um adaptador USB Ethernet. Use a ilustração a seguir para identificar a PORTA 1 em seu dispositivo.

    ![Backplane de um dispositivo cabeado](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Abrir uma janela do navegador e acessar o local da web da interface do usuário do dispositivo em `https://192.168.100.10`.  
    Essa ação pode levar alguns minutos depois que você tenha ativado o dispositivo.

    Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro de certificado de segurança do site](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Selecione **Continuar para essa página da Web**.  
    Essas etapas podem variar dependendo do navegador que você está usando.

5. Entrar para a web da interface do usuário do seu dispositivo. A senha padrão é *Senha1*. 
   
    ![Página de entrada do dispositivo Azure Stack Edge](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. No prompt, altere a senha de administrador do dispositivo.  
    A nova senha deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.

Agora você está na página de **Visão geral** do seu dispositivo. A próxima etapa é definir as configurações de rede do seu dispositivo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se a um dispositivo físico


Para saber como definir as configurações de rede em seu dispositivo Azure Stack Edge Pro R, confira:

> [!div class="nextstepaction"]
> [Configurar a rede](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
