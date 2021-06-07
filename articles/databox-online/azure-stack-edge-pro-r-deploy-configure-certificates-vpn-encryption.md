---
title: Tutorial para configurar certificados para um dispositivo Azure Stack Edge Pro R no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge Pro R instrui como se conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059965"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Tutorial: Configurar certificados para o Azure Stack Edge Pro R

Este tutorial descreve como é possível configurar certificados para o dispositivo Azure Stack Edge Pro R usando a IU da Web local.

O tempo que essa etapa levará varia dependendo da opção específica que você escolher e de como o fluxo de certificado é estabelecido em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico
> * Configurar a VPN
> * Configurar a criptografia em repouso

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar seu dispositivo Azure Stack Edge Pro R, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).
* Se você planeja trazer seus certificados:
    - Você deve manter seus certificados preparados no formato apropriado, incluindo o certificado de cadeia de assinatura. Para obter detalhes sobre o certificado, acesse [Gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Configurar certificados para o dispositivo

1. Na página **Certificados**, você vai configurar seus certificados. Dependendo de você ter ou não alterado o nome do dispositivo ou o domínio DNS na página **Dispositivo**, você poderá escolher uma das opções a seguir para seus certificados.

    - Se você não alterou o nome do dispositivo nem o domínio DNS na etapa anterior, pode ignorar esta etapa e prosseguir para a próxima. Para começar, o dispositivo gerou automaticamente certificados autoassinados. 

    - Se você alterou o nome do dispositivo ou o domínio DNS, verá que o status dos certificados será exibido como **Não válido**. 

        ![Página 2 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Selecione um certificado para exibir os detalhes do status.

        ![Página 3 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Isso ocorre porque os certificados não refletem o nome e o domínio DNS do dispositivo atualizado (que são usados no nome da entidade e na alternativa da entidade). Para ativar com êxito seu dispositivo, você pode colocar seus certificados de ponto de extremidade assinados e as cadeias de assinatura correspondentes. Primeiro, você adiciona a cadeia de assinatura e carrega os certificados de ponto de extremidade. Para obter mais informações, acesse [Traga seus certificados em seu dispositivo Azure Stack Edge Pro R](#bring-your-own-certificates).

    - Se você tiver alterado o nome do dispositivo ou o domínio DNS e não trouxer seus certificados, a **ativação será bloqueada**.

    
#### <a name="bring-your-own-certificates"></a>Trazer seus certificados

Siga estas etapas para adicionar seus certificados, incluindo a cadeia de assinatura.

1. Para carregar o certificado, na página **Certificado**, selecione **+ Adicionar certificado**.

    ![Página 4 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Carregue a cadeia de assinatura primeiro e selecione **Validar e adicionar**.

    ![Página 5 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Agora, é possível carregar outros certificados. Por exemplo, é possível carregar os certificados de ponto de extremidade do Armazenamento de Blobs e do Azure Resource Manager.

    ![Página 6 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Também é possível carregar o certificado da IU da Web local. Depois de carregar esse certificado, será necessário iniciar o navegador e limpar o cache. Em seguida, será necessário conectar-se à IU da Web local do dispositivo.  

    ![Página 7 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Também é possível carregar o certificado de nó.

    ![Página 8 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Por fim, você pode carregar o certificado VPN.

    ![Página 9 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    A qualquer momento, você pode selecionar um certificado e exibir os detalhes para garantir que correspondam ao certificado que você carregou.

    A página de certificados será atualizada para refletir os certificados recém-adicionados.

    ![Página 10 de "Certificados" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Exceto para a nuvem pública do Azure, os certificados de cadeia de assinatura precisam ser trazidos antes da ativação para todas as configurações de nuvem (Azure Government ou Azure Stack).

1. Selecione **< Voltar para começar**.

## <a name="configure-vpn"></a>Configurar a VPN

1. No bloco **Segurança**, selecione **Configurar** para VPN.

    ![Página "VPN" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Para configurar a VPN, primeiro você precisará verificar se tem toda a configuração necessária feita no Azure. Para obter detalhes, confira [Configurar pré-requisitos](azure-stack-edge-placeholder.md) e [Configurar recursos do Azure para VPN](azure-stack-edge-placeholder.md). Após a conclusão, você poderá fazer a configuração na IU local.
    
    1. Na página VPN, selecione **Configurar**.
    2. Na folha **Configurar VPN**:

    - Habilite **Configurações de VPN**.
    - Forneça o **Segredo compartilhado de VPN**. Essa é a chave compartilhada que você forneceu quando criou o objeto de conexão VPN do Azure.
    - Forneça o endereço **IP do gateway de VPN**. Este é o endereço IP do gateway de rede local do Azure.
    - Para o **Grupo PFS**, selecione **Nenhum**. 
    - Para o **Grupo DH**, selecione **Grupo2**.
    - Para **Método de integridade IPsec**, selecione **SHA256**.
    - Para **Constantes de transformação IPseccipher**, selecione **GCMAES256**.
    - Para **Constantes de transformação de autenticação IPsec**, selecione **GCMAES256**.
    - Para **Método de criptografia IKE**, selecione **AES256**.
    - Selecione **Aplicar**.

        ![Configurar IU local 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Para carregar o arquivo de configuração de rota VPN, selecione **Carregar**. 
    
        ![Configurar IU local 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Navegue até o arquivo *json* da configuração de VPN que você baixou em seu sistema local na etapa anterior.
        - Selecione a região como a região do Azure associada ao dispositivo, à rede virtual e aos gateways.
        - Selecione **Aplicar**.
    
            ![Configurar IU local 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Para adicionar rotas específicas do cliente, configure os intervalos de endereços IP a serem acessados usando somente a VPN. 
    
        - Em **Intervalos de endereços IP a serem acessados usando somente a VPN**, selecione **Configurar**.
        - Forneça um intervalo IPv4 válido e selecione **Adicionar**. Repita as etapas para adicionar outros intervalos.
        - Selecione **Aplicar**.
    
            ![Configurar IU local 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Selecione **< Voltar para começar**.

## <a name="configure-encryption-at-rest"></a>Configurar a criptografia em repouso

1. No bloco **Segurança**, selecione **Configurar** para criptografia em repouso. Essa é uma configuração necessária e até que isso seja configurado com êxito, você não poderá ativar o dispositivo. 

    No alocador, após a imagem dos dispositivos, a criptografia BitLocker no nível de volume está habilitada. Depois de receber o dispositivo, você precisa configurar a criptografia em repouso. O pool de armazenamento e os volumes são recriados e você pode fornecer chaves do BitLocker para habilitar a criptografia em repouso e, portanto, criar uma segunda camada de criptografia para seus dados em repouso.

1. No painel **Criptografia em repouso**, forneça uma chave codificada em Base-64 com 32 caracteres. Essa é uma configuração única e essa chave é usada para proteger a chave de criptografia real. Você pode optar por gerar essa chave automaticamente ou inserir uma.

    ![Painel "Criptografia em repouso" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Essa chave é salva em um arquivo de chave na página **Detalhes da nuvem** após o dispositivo ser ativado.

1. Selecione **Aplicar**. Essa operação leva vários minutos e o status dela é exibido no bloco **Segurança**.

    ![Página "Criptografia em repouso" da IU da Web local](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Depois que o status aparecer como **Concluído**, selecione **< Voltar para a Introdução**.

Agora, seu dispositivo está pronto para ser ativado. 


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico
> * Configurar a VPN
> * Configurar a criptografia em repouso

Para saber como ativar seu dispositivo Azure Stack Edge Pro R, confira:

> [!div class="nextstepaction"]
> [Ativar dispositivo Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-activate.md)
