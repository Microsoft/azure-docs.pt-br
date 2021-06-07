---
title: Tutorial sobre como preparar o portal do Azure para implantar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implantar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101706088"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Preparar para implantar o Azure Data Box Gateway

Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente o seu Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implantar o recurso Data Box Gateway.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Baixar a imagem do dispositivo virtual
> * Obter a chave de ativação

## <a name="get-started"></a>Introdução

Consulte os tutoriais a seguir na sequência prescrita para implantar o Data Box Gateway.

| **#** | **Nesta etapa** | **Use estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Gateway](data-box-gateway-deploy-prep.md)** |Crie e configure o recurso de Gateway da caixa de dados antes do provisionamento de um dispositivo virtual do Data Box Gateway. |
| 2. |**[Provisione o Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Provisione o Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md)**|Para o Hyper-V, forneça e conecte-se a um dispositivo virtual do Data Box Gateway em um sistema host que execute o Hyper-V no Windows Server 2016 ou no Windows Server 2012 R2. <br><br><br> Para o VMware, provisione e conecte-se a um dispositivo virtual de Data Box Gateway em um sistema de host executando o VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Conectar-se, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Conectar-se a interface do usuário da Web local, conclua a configuração do dispositivo e ativar o dispositivo. Em seguida, é possível provisionar compartilhamentos SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicionar compartilhamentos, conecte-se aos compartilhamentos via SMB ou NFS. |

Agora, você pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui você encontra os pré-requisitos de configuração para o recurso Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso de Data Box Gateway

Antes de começar, verifique se:

* Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Você tem acesso de proprietário ou colaborador no nível do grupo de recursos aos recursos do Azure Stack Edge/Data Box Gateway, do Hub IoT e do Armazenamento do Azure.
    - Para criar qualquer recurso do Azure Stack Edge/Data Box Gateway, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisará verificar se o provedor `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de administrador ou de usuário para a API do Microsoft Graph. Para obter mais informações, confira [Referência de permissões do Microsoft Graph](/graph/permissions-reference).
- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo de Data Box Gateway

Antes de implantar um dispositivo virtual, verifique se:

- Você tem acesso a um sistema host que executa o Hyper-V no Windows Server 2012 R2 ou posterior ou o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser usado para provisionar um dispositivo.
- O sistema host é capaz de dedicar os seguintes recursos para provisionar seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelo menos 8 GB de RAM. É altamente recomendável ter pelo menos 16 GB de RAM.
  - Uma interface de rede.
  - Um disco de SO de 250 GB.
  - Um disco virtual de 2 TB de dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

- A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Data Box Gateway. Para obter mais informações, consulte os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).

- Para as condições normais de operação do Data Box Gateway, você deve ter:

    - Uma largura de banda de download de, no mínimo, 10 Mbps para garantir que o dispositivo permaneça atualizado.
    - Uma largura de banda dedicada de download e upload de, no mínimo, 20 Mbps para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se você tiver um recurso do Data Box Gateway existente para gerenciar seus dispositivos virtuais, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso do Data Box Gateway, execute as seguintes etapas no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar em qualquer um destes portais:

    - No portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - O portal do Azure Government nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter detalhes, acesse [Conectar-se ao Azure Government usando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).
    
2. Selecione **+ Criar um recurso**.

    ![Botão Criar um Recurso do Azure Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. Digite **Data Box Gateway** na caixa de pesquisa e pressione Enter.

    ![Pesquise o serviço Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Em seguida, selecione **Azure Data Box Gateway**.

    ![Selecione o SKU do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Selecione **Criar**.

    ![Clique em Criar para criar o recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. Na guia **Informações Básicas**:

    Insira ou escolha os **Detalhes do projeto** a seguir.
    
    |Setting  |Valor  |
    |---------|---------|
    |Assinatura    |Escolha a assinatura que deseja usar para o dispositivo do Data Box Gateway. A assinatura está vinculada à sua conta de cobrança.|
    |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).|

   Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome   |Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 caracteres contendo letras, números e hifens. <br> O nome deve começar e terminar com uma letra ou um número. |
    |Região  |Selecione a região em que você deseja implantar o recurso. Escolha uma localização perto da região geográfica em que você deseja implantar seu dispositivo. <br> Para obter uma lista de todas as regiões em que os recursos Data Base Gateway/Azure Stack Edge estão disponíveis, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). <br> Para o Azure Governamental, todas as regiões do governo listadas em [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) estão disponíveis.|

   Selecione **Examinar + criar** para examinar seu pedido.

   ![Entrada de detalhes do projeto e da instância para um pedido do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Selecione **Criar**.

    ![Detalhes do recurso do Data Box Gateway exibidos para análise](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

A criação do recurso leva alguns minutos. Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

![Um pedido do Data Box Gateway concluído](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>Baixar a imagem do dispositivo virtual

Depois que o recurso Data Box Gateway for criado, faça o download da imagem do dispositivo virtual apropriado para provisionar um dispositivo virtual em seu sistema host. As imagens do dispositivo virtual são específicas para um sistema operacional.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.

Siga estas etapas no [portal do Azure](https://portal.azure.com/) para baixar uma imagem de dispositivo virtual.

1. No recurso que você criou, selecione **Visão Geral**. Se você tiver um recurso existente do Azure Data Box Gateway, selecione o recurso e vá para **Visão Geral**. Selecione **Configuração do dispositivo**.

    ![Novo recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. No bloco **Baixar imagem**, selecione a imagem do dispositivo virtual correspondente ao sistema operacional no servidor de host usado para provisionar a VM. Os arquivos de imagem têm aproximadamente 5,6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e posterior](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMware ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

    ![Baixar imagem de dispositivo virtual do Azure Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Baixe e descompacte o arquivo em uma unidade local, observando onde o arquivo descompactado está localizado.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Data Box Gateway estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Data Box Gateway ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Selecione o recurso que você criou e, em seguida, selecione **Visão Geral**. Na **Configuração do dispositivo**, acesse o bloco **Configurar e ativar**.

    ![Bloco Configurar e ativar](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecione **Gerar chave** para criar uma chave de ativação. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias depois de ser gerada.
> - Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Baixar a imagem do dispositivo virtual
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a provisionar uma máquina virtual para o Data Box Gateway. Dependendo do sistema operacional host, consulte as instruções detalhadas em:

> [!div class="nextstepaction"]
> [Provisionar um Data Box Gateway no Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OU

> [!div class="nextstepaction"]
> [Provisionar um Data Box Gateway no VMware](./data-box-gateway-deploy-provision-vmware.md)