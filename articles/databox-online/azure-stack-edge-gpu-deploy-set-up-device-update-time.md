---
title: Tutorial para conectar, configurar e ativar um dispositivo do Azure Stack Edge Pro com GPU no portal do Azure | Microsoft Docs
description: O tutorial para implantar a GPU do Azure Stack Edge Pro instrui como se conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 4b00ac57e04ca52f662e097799dcbc831f82d927
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062923"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-with-gpu"></a>Tutorial: Definir as configurações do dispositivo para o Azure Stack Edge Pro com GPU

Este tutorial descreve como definir configurações relacionadas ao dispositivo para seu dispositivo do Azure Stack Edge Pro com uma GPU integrada. Você pode configurar o nome do dispositivo, o servidor de atualização e o servidor de horário por meio da IU da Web local.

As configurações do dispositivo podem levar cerca de 5 a 7 minutos para serem concluídas.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Definir configurações de dispositivo
> * Configurar atualização 
> * Configurar hora

## <a name="prerequisites"></a>Pré-requisitos

Antes de definir as configurações relacionadas ao dispositivo em seu dispositivo do Azure Stack Edge Pro com GPU, verifique se:

* Para o dispositivo físico:

    - Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
    - Você configurou a rede e habilitou e configurou a rede de computação em seu dispositivo, conforme detalhado no [Tutorial: configure a rede para o Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Definir configurações de dispositivo

Siga estas etapas para definir as configurações relacionadas ao dispositivo:

1. Na página **Dispositivo**, execute as seguintes etapas:

    1. Insira um nome amigável para seu dispositivo. O nome amigável deve conter de 1 a 13 caracteres e pode ter letras, números e hifens.

    2. Forneça um **domínio DNS** para seu dispositivo. Esse domínio é usado para configurar o dispositivo como um servidor de arquivos.

    3. Para validar e aplicar as configurações do dispositivo, selecione **Aplicar**.

        ![Página "Dispositivo" da IU da Web local 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Se você tiver alterado o nome do dispositivo e o domínio DNS, os certificados autoassinados gerados automaticamente no dispositivo não funcionarão. Você precisa escolher uma das seguintes opções ao configurar certificados: 
        
        - Gere e baixe os certificados do dispositivo. 
        - Traga seus certificados para o dispositivo, incluindo a cadeia de assinatura.
    

        ![Página "Dispositivo" da IU da Web local 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Quando o nome do dispositivo e o domínio DNS são alterados, o ponto de extremidade SMB é criado.  

    5. Depois que as configurações forem aplicadas, selecione **Avançar: atualizar servidor**.

        ![Página "Dispositivo" da IU da Web local 3](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Configurar atualização

1. Na página **Atualizar**, você pode configurar a localização da qual baixar as atualizações para seu dispositivo.  

    - Você pode obter as atualizações diretamente do **servidor do Microsoft Update**.

        ![Página "Servidor de Atualização" da IU da Web local](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        Você também pode optar por implantar atualizações do **WSUS** (Windows Server Update Services). Forneça o caminho para o servidor do WSUS.
        
        ![Página "Servidor de Atualização" da IU da Web local 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Se um servidor do Windows Update separado for configurado e se optar por se conectar via *HTTPS* (em vez de *HTTP*), você precisará dos certificados de cadeia de assinatura requeridos para se conectar ao servidor de atualização. Para obter informações sobre como criar e carregar certificados, acesse [Gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md). 

2. Selecione **Aplicar**.
3. Depois que o servidor de atualização estiver configurado, selecione **Avançar: Hora**.
    

## <a name="configure-time"></a>Configurar hora

Siga estas etapas para definir as configurações de hora em seu dispositivo. 

> [!IMPORTANT]
> Embora as configurações de hora sejam opcionais, é altamente recomendável que você configure um servidor NTP primário e um servidor NTP secundário na rede local para seu dispositivo. Se o servidor local não estiver disponível, os servidores NTP públicos poderão ser configurados.

Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.

1. Na página **Hora**, você pode selecionar o fuso horário e os servidores NTP primário e secundário para seu dispositivo.  
    
    1. Na lista suspensa **Fuso horário**, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está sendo implantado.
        O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    2. Na caixa **Servidor NTP primário**, digite o servidor primário para seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    3. Opcionalmente, na caixa **Servidor NTP secundário**, digite um servidor secundário para seu dispositivo.

    4. Para validar e aplicar as configurações de hora configuradas, selecione **Aplicar**.

        ![Página "Hora" da IU da Web local](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. Depois que as configurações forem aplicadas, selecione **Avançar: Certificados**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Definir configurações de dispositivo
> * Configurar atualização 
> * Configurar hora

Para saber como configurar certificados para seu dispositivo do Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Configurar certificados](./azure-stack-edge-gpu-deploy-configure-certificates.md)
