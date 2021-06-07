---
title: Tutorial para configurar certificados para um dispositivo Azure Stack Edge Mini R no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge Mini R instrui como se conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062991"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Tutorial: Configurar certificados, VPN e criptografia para o Azure Stack Edge Mini R

Este tutorial descreve como configurar certificados, VPN e criptografia em repouso para o dispositivo Azure Stack Edge Mini R usando a IU da Web local.

O tempo que essa etapa levará varia dependendo da opção específica que você escolher e de como o fluxo de certificado é estabelecido em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico
> * Configurar a VPN
> * Configurar a criptografia em repouso

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Mini R, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).

* Se você planeja trazer seus certificados:
    - Você deve manter seus certificados preparados no formato apropriado, incluindo o certificado de cadeia de assinatura. Para obter detalhes sobre o certificado, acesse [Gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md)

    - Se o dispositivo estiver implantado na nuvem Azure Government, Azure Government Secret ou Azure Government Top Secret e não estiver implantado na nuvem pública do Azure, um certificado de cadeia de assinatura será necessário para ativar o dispositivo. 
    Para obter detalhes sobre o certificado, acesse [Gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Configurar certificados para o dispositivo

1. Na página **Certificados**, você vai configurar seus certificados. Dependendo de você ter ou não alterado o nome do dispositivo ou o domínio DNS na página **Dispositivo**, você poderá escolher uma das opções a seguir para seus certificados.

    - Se você não alterou o nome do dispositivo padrão nem o domínio DNS padrão na etapa anterior e não deseja trazer seus certificados, pode ignorar esta etapa e prosseguir para a próxima. Para começar, o dispositivo gerou automaticamente certificados autoassinados. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Se você alterou o nome do dispositivo ou o domínio DNS, verá que o status dos certificados será exibido como **Não válido**. 

        ![Página 2 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Selecione um certificado para exibir os detalhes do status.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        O status do certificado é **Não válido** porque os certificados não refletem o nome e o domínio DNS do dispositivo atualizado (que são usados no nome da entidade e na alternativa da entidade). Para ativar com êxito seu dispositivo, você pode colocar seus certificados de ponto de extremidade assinados e as cadeias de assinatura correspondentes. Primeiro, você adiciona a cadeia de assinatura e carrega os certificados de ponto de extremidade. Para obter mais informações, acesse [Traga seus certificados em seu dispositivo Azure Stack Edge Mini R](#bring-your-own-certificates).


    - Se você tiver alterado o nome do dispositivo ou o domínio DNS e não trouxer seus certificados, a **ativação será bloqueada**.


#### <a name="bring-your-own-certificates"></a>Trazer seus certificados

Você já adicionou a cadeia de assinatura em uma etapa anterior no dispositivo. Agora, você pode carregar os certificados de ponto de extremidade, o certificado de nó, o certificado de IU local e o certificado de VPN. Siga estas etapas para adicionar seus certificados.

1. Para carregar o certificado, na página **Certificado**, selecione **+ Adicionar certificado**.

    [![Página 4 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. É possível carregar outros certificados. Por exemplo, é possível carregar os certificados de ponto de extremidade do Armazenamento de Blobs e do Azure Resource Manager.

    ![Página 6 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Também é possível carregar o certificado da IU da Web local. Depois de carregar esse certificado, será necessário iniciar o navegador e limpar o cache. Em seguida, será necessário conectar-se à IU da Web local do dispositivo.  

    ![Página 7 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Também é possível carregar o certificado de nó.


    ![Página 8 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Por fim, você pode carregar o certificado VPN.
        
    ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. A qualquer momento, você pode selecionar um certificado e exibir os detalhes para garantir que correspondam ao certificado que você carregou.

    [![Página 9 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    A página de certificados será atualizada para refletir os certificados recém-adicionados.

    [![Página 10 de "Certificados" da IU da Web local](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Exceto pela nuvem pública do Azure, os certificados de cadeia de assinatura precisam ser trazidos antes da ativação para todas as configurações de nuvem (Azure Government ou Azure Stack Hub).


## <a name="configure-vpn"></a>Configurar a VPN

1. No bloco **Segurança**, selecione **Configurar** para VPN. 

    Para configurar a VPN, primeiro você precisará verificar se tem toda a configuração necessária feita no Azure. Para ver os detalhes, confira [Configurar a VPN por meio do PowerShell para o dispositivo Azure Stack Edge Mini R](azure-stack-edge-placeholder.md). Após a conclusão, você poderá fazer a configuração na IU local.
    
    1. Na página VPN, selecione **Configurar**.
        ![Configurar VPN na IU local 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. Na folha **Configurar VPN**:

        1. Forneça a agenda de telefones como entrada.
        2. Forneça o arquivo JSON de intervalo de IP do Data Center do Azure como entrada. Baixe este arquivo de: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519).
        3. Selecione **eastus** como região.
        4. Selecione **Aplicar**.

        ![Configurar VPN na IU local 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Configure os intervalos de endereços IP a serem acessados usando somente a VPN. 
    
        - Em **Intervalos de endereços IP a serem acessados usando somente a VPN**, selecione **Configurar**.
        - Insira o intervalo de IPv4 da VNET que você escolheu para sua Rede Virtual do Azure.
        - Selecione **Aplicar**.
    
        ![Configurar VPN na IU local 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Agora, o dispositivo está pronto para ser criptografado. Configurar a criptografia em repouso.


## <a name="enable-encryption"></a>Habilitar criptografia

1. No bloco **Segurança**, selecione **Configurar** para criptografia em repouso. Essa é uma configuração necessária e até que isso seja configurado com êxito, você não poderá ativar o dispositivo. 

    ![Página "Criptografia em repouso" da IU da Web local 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    No alocador, após a imagem dos dispositivos, a criptografia BitLocker no nível de volume está habilitada. Depois de receber o dispositivo, você precisa configurar a criptografia em repouso. O pool de armazenamento e os volumes são recriados e você pode fornecer chaves do BitLocker para habilitar a criptografia em repouso e, portanto, criar uma segunda camada de criptografia para seus dados em repouso.

1. No painel **Criptografia em repouso**, insira uma chave codificada em Base 64 (AES de 256 bits) com 32 caracteres. Essa é uma configuração única e essa chave é usada para proteger a chave de criptografia real. 

    ![Página "Criptografia em repouso" da IU da Web local 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Você também pode optar por gerar essa chave automaticamente.

    ![Página "Criptografia em repouso" da IU da Web local 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Selecione **Aplicar**. Essa operação leva vários minutos e o status dela é exibido no bloco **Segurança**.

    ![Página "Criptografia em repouso" da IU da Web local 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Depois que o status aparecer como **Concluído**, volte para **Introdução**.

Agora, seu dispositivo está pronto para ser ativado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico
> * Configurar a VPN
> * Configurar a criptografia em repouso

Para saber como ativar seu dispositivo Azure Stack Edge Mini R, confira:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge Mini R](./azure-stack-edge-mini-r-deploy-activate.md)
