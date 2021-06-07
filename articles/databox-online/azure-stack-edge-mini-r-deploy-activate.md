---
title: Tutorial para criptografar e ativar um dispositivo Azure Stack Edge Mini R no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge Mini R ensina como criptografar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
ms.openlocfilehash: 92dfff13d964418ea5519d30c7a1b91621d5e98e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061631"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Tutorial: Ativar o Azure Stack Edge Mini R

Este tutorial descreve como você pode ativar o dispositivo Azure Stack Edge Mini R usando a IU da Web local.

O processo de ativação pode levar cerca de 15 minutos para ser concluído.

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Mini R, verifique o seguinte:

* Para o dispositivo físico: 
    
    - Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).
    - Você definiu as configurações de rede e de rede de computação, conforme detalhado em [Configurar rede, rede de computação e proxy Web](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
    - Você carregou seus certificados no dispositivo caso tenha alterado o nome do dispositivo ou o domínio DNS por meio da página **Dispositivo**. Essas etapas são detalhadas em [Configurar certificados, VPN e criptografia em repouso](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md). Se você ainda não realizou essa etapa, a ativação será bloqueada.
    - Você configurou a criptografia em repouso para seu dispositivo. Se você ainda não realizou essa etapa, verá um erro durante a ativação do dispositivo e a ativação será bloqueada. Para obter mais informações, confira [Configurar certificados, VPN e criptografia em repouso](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md).
    
* Você tem a chave de ativação do serviço Azure Stack Edge que você criou para gerenciar o dispositivo Azure Stack Edge Mini R. Para obter mais informações, vá para [Preparar para implantar o Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Ativar o dispositivo

1. Na IU da Web local do dispositivo, acesse a página **Introdução**.
2. No bloco **Ativação**, selecione **Ativar**. 

    ![Página "Detalhes de nuvem" da IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. No painel **Ativar**:
    1. Insira a **Chave de ativação** que você recebeu em [Obter chave de ativação para o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Você pode habilitar a coleta de logs proativa para permitir que a Microsoft colete logs com base no status de integridade do dispositivo. Os logs coletados dessa maneira são carregados em uma conta de armazenamento do Azure.
    
    1. Selecione **Aplicar**.

    ![Página "Detalhes de nuvem" da IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Primeiro, o dispositivo é ativado. Em seguida, você será solicitado a baixar o arquivo de chave.
    
    ![Página "Detalhes de nuvem" da IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
    Selecione **Baixar e continuar** e salve o arquivo *device-serial-no.json* em um local seguro fora do dispositivo. **Esse arquivo de chave contém as chaves de recuperação para o disco do sistema operacional e discos de dados em seu dispositivo**. Essas chaves podem ser necessárias para facilitar uma futura recuperação do sistema.

    Aqui estão os conteúdos do arquivo *keys.json*:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    A seguinte tabela explica as várias chaves:
    
    |Campo  |Descrição  |
    |---------|---------|
    |`Id`    | Esta é a identificação do dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Essas são as chaves do BitLocker para os discos de dados e são usadas para recuperar os dados locais em seu dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Essa é a chave do BitLocker para o volume do sistema. Essa chave ajuda na recuperação da configuração do sistema e dos dados do sistema para seu dispositivo. |
    |`SEDEncryptionExternalKey`| Essa chave fornecida pelo usuário ou gerada pelo sistema é usada para proteger as unidades de dados com criptografia automática que têm criptografia interna. |
    |`ServiceEncryptionKey`| Essa chave protege os dados que fluem por meio do serviço do Azure. Essa chave garante que um comprometimento do serviço do Azure não resulte em um comprometimento das informações armazenadas. |

6. Vá para a página **Visão geral**. O estado do dispositivo agora deve ser mostrado como **Ativado**.

    ![Página "Detalhes de nuvem" da IU da Web local 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
A ativação do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.

Se você encontrar problemas durante a ativação, acesse [Solucionar problemas de ativação e erros do Azure Key Vault](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

Para saber como transferir dados com o seu dispositivo Azure Stack Edge Mini R, confira:

> [!div class="nextstepaction"]
> [Transferir dados com o Azure Stack Edge Mini R](./azure-stack-edge-gpu-deploy-add-shares.md)