---
title: Tutorial para configurar certificados para um dispositivo Azure Stack Edge Pro com uma GPU no portal do Azure | Microsoft Docs
description: O tutorial para implantar o Azure Stack Edge Pro com uma GPU instrui como se conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 8b1203693b67bb1a8b9699b84dd3a437027e4c3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055307"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Tutorial: Configurar certificados para o Azure Stack Edge Pro com uma GPU

Este tutorial descreve como é possível configurar certificados para o dispositivo Azure Stack Edge Pro com uma GPU integrada usando a IU da Web local.

O tempo que essa etapa levará varia dependendo da opção específica que você escolher e de como o fluxo de certificado é estabelecido em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar o dispositivo do Azure Stack Edge Pro com uma GPU, verifique se você fez o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
* Se você planeja trazer seus certificados:
    - Você deve manter seus certificados preparados no formato apropriado, incluindo o certificado de cadeia de assinatura. Para obter detalhes sobre o certificado, acesse [Gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Configurar certificados para o dispositivo

1. Na página **Certificados**, você vai configurar seus certificados. Dependendo de você ter ou não alterado o nome do dispositivo ou o domínio DNS na página **Dispositivo**, você poderá escolher uma das opções a seguir para seus certificados.

    - Se você não alterou o nome do dispositivo nem o domínio DNS na etapa anterior e não deseja trazer seus certificados, pode ignorar esta etapa e prosseguir para a próxima. Para começar, o dispositivo gerou automaticamente certificados autoassinados. 

        ![Página "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Se você alterou o nome do dispositivo ou o domínio DNS, verá que o status dos certificados será exibido como **Não válido**. 

        ![Página 2 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Selecione um certificado para exibir os detalhes do status.

        ![Página 3 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Isso ocorre porque os certificados não refletem o nome e o domínio DNS do dispositivo atualizado (que são usados no nome da entidade e na alternativa da entidade). Para ativar o dispositivo com êxito, escolha uma das seguintes opções: 
    
        - **Gere todos os certificados de dispositivo**. Esses certificados de dispositivo só devem ser usados para teste e não usados com cargas de trabalho de produção. Para obter mais informações, acesse [Gerar certificados de dispositivo em seu Azure Stack Edge Pro](#generate-device-certificates).

        - **Traga seus certificados**. Você pode colocar seus certificados de ponto de extremidade assinados e as cadeias de assinatura correspondentes. Primeiro, você adiciona a cadeia de assinatura e carrega os certificados de ponto de extremidade. **É recomendável que você sempre traga seus certificados para cargas de trabalho de produção.** Para obter mais informações, acesse [Traga seus certificados em seu dispositivo Azure Stack Edge Pro](#bring-your-own-certificates).
    
        - Você pode trazer alguns de seus certificados e gerar alguns certificados de dispositivo. A opção **Gerar certificados** vai regenerar os certificados de dispositivo.

    - Se você tiver alterado o nome do dispositivo ou o domínio DNS e não gerar certificados nem trouxer seus certificados, a **ativação será bloqueada**.


### <a name="generate-device-certificates"></a>Gerar certificados de dispositivo

Siga estas etapas para gerar certificados de dispositivo.

Use estas etapas para regenerar e baixar os certificados de dispositivo Azure Stack Edge Pro:

1. Na IU local do dispositivo, acesse **Configuração > Certificados**. Selecione **Gerar certificados**.

    ![Gerar e baixar o certificado 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Em **Gerar certificados de dispositivo**, selecione **Gerar**. 

    ![Gerar e baixar o certificado 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Os certificados de dispositivo agora são gerados e aplicados. São necessários alguns minutos para gerar e aplicar os certificados.
    
    > [!IMPORTANT]
    > Enquanto a operação de geração de certificado está em andamento, não traga seus certificados nem tente adicioná-los por meio da opção **+ Adicionar certificado**.

    Você será notificado quando a operação for concluída com êxito. **Para evitar possíveis problemas de cache, reinicie o navegador.**
    
    ![Gerar e baixar o certificado 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Depois que os certificados são gerados: 

    - O status de todos os certificados é mostrado como **Válido**. 

        ![Gerar e baixar o certificado 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Você pode selecionar um nome de certificado específico e exibir os detalhes do certificado. 

        ![Gerar e baixar o certificado 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - A coluna **Baixar** agora é preenchida. Essa coluna tem links para baixar os certificados regenerados. 

        ![Gerar e baixar o certificado 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Selecione o link de download para um certificado e, quando solicitado, salve o certificado em questão. 

    ![Gerar e baixar o certificado 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Repita esse processo para todos os certificados que você deseja baixar. 
    
    ![Gerar e baixar o certificado 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Os certificados gerados pelo dispositivo são salvos como certificados DER com o seguinte formato de nome: 

    `<Device name>_<Endpoint name>.cer`. Esses certificados contêm a chave pública para os certificados correspondentes instalados no dispositivo. 

Será necessário instalar esses certificados no sistema cliente que você está usando para acessar os pontos de extremidade no dispositivo ASE. Esses certificados estabelecem a confiança entre o cliente e o dispositivo.

Para importar e instalar esses certificados no cliente que você está usando para acessar o dispositivo, siga as etapas em [Importar certificados nos clientes que acessam o dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Se você estiver usando o Gerenciador de Armazenamento do Azure, precisará instalar certificados em seu cliente no formato PEM e converter os certificados gerados pelo dispositivo no formato PEM. 

> [!IMPORTANT]
> - O link de download só estará disponível para os certificados gerados pelo dispositivo, mas o mesmo não acontecerá se você trouxer seus certificados.
> - É possível optar por usar uma combinação, usando certificados gerados pelo dispositivo e trazendo seus certificados, desde que os outros requisitos de certificado sejam atendidos. Para obter mais informações, acesse [Requisitos de certificado](azure-stack-edge-gpu-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Trazer seus certificados

Siga estas etapas para adicionar seus certificados, incluindo a cadeia de assinatura.

1. Para carregar o certificado, na página **Certificado**, selecione **+ Adicionar certificado**.

    ![Página 4 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Carregue a cadeia de assinatura primeiro e selecione **Validar e adicionar**.

    ![Página 5 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Agora, é possível carregar outros certificados. Por exemplo, é possível carregar os certificados de ponto de extremidade do Armazenamento de Blobs e do Azure Resource Manager.

    ![Página 6 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Também é possível carregar o certificado da IU da Web local. Depois de carregar esse certificado, será necessário iniciar o navegador e limpar o cache. Em seguida, será necessário conectar-se à IU da Web local do dispositivo.  

    ![Página 7 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Também é possível carregar o certificado de nó.

    ![Página 8 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    A qualquer momento, você pode selecionar um certificado e exibir os detalhes para garantir que correspondam ao certificado que você carregou.

    ![Página 9 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    A página de certificados será atualizada para refletir os certificados recém-adicionados.

    ![Página 10 de "Certificados" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Exceto para a nuvem pública do Azure, os certificados de cadeia de assinatura precisam ser trazidos antes da ativação para todas as configurações de nuvem (Azure Government ou Azure Stack).


Agora, seu dispositivo está pronto para ser ativado. Selecione **< Voltar para começar**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Configurar certificados para o dispositivo físico

Para saber como ativar seu dispositivo Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-activate.md)
