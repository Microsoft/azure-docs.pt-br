---
title: Tutorial sobre como preparar o portal do Azure para implantar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implantar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 428b42e62f44d182de109740359544135561e54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441486"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Preparar para implantar o Azure Data Box Gateway


Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente o seu Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implantar o recurso Data Box Gateway. 

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
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

- Sua assinatura do Microsoft Azure deve ser compatível com o recurso do Data Box Gateway. Há suporte para as assinaturas pagas conforme o uso.
- Você tem acesso de proprietário ou colaborador no nível do grupo de recursos aos recursos do Data Box Edge/Data Box Gateway, do Hub IoT e do Armazenamento do Azure.

    - Para criar qualquer recurso do Data Box Edge/Data Box Gateway, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisará verificar se o provedor `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de usuário ou administrador à API do Azure Active Directory Graph. Para obter mais informações, confira [API do Azure Active Directory Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo de Data Box Gateway

Antes de implantar um dispositivo virtual, verifique se:

- Você tem acesso a um sistema host que executa o Hyper-V no Windows Server 2012 R2 ou posterior ou o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser usado para provisionar um dispositivo.
- O sistema host é capaz de dedicar os seguintes recursos para provisionar seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelo menos 8 GB de RAM.
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

1. Use suas credenciais do Microsoft Azure para entrar em:

    - No portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou no portal do Azure Governamental nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter mais detalhes, acesse [Conectar-se ao Azure Governamental usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ Criar um recurso**. Pesquise **Data Box Edge / Data Box Gateway**. Selecione Data Box Edge / Data Box Gateway. Selecione **Criar**.
3. Escolha a assinatura que deseja usar para o dispositivo do Data Box Gateway. Selecione a região na qual você deseja implantar o recurso Data Box Gateway. Para esta versão, o Leste dos EUA, o Sudeste Asiático e o Oeste da Europa estão disponíveis. Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo. Na opção **Data Box Gateway**, selecione **Criar**.

    ![Serviço de Data Box Gateway de pesquisa](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na guia **Informações Básicas**, insira ou selecione os **Detalhes do projeto** a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Assinatura    |Isso é preenchido automaticamente de acordo com a seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |NOME   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 caracteres contendo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |   
    |Região     |Para esta versão, Leste dos EUA, Sudeste da Ásia e Oeste da Europa estão disponíveis para implantação do recurso. Para o Azure Governamental, todas as regiões do governo listadas em [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) estão disponíveis. <br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|
    
    ![Criar recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selecione **Examinar + criar**.
 
7. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Selecione **Criar**.

    ![Examinar os detalhes do recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

A criação do recurso leva alguns minutos. Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

![Examinar os detalhes do recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Baixar a imagem do dispositivo virtual

Depois que o recurso Data Box Gateway for criado, faça o download da imagem do dispositivo virtual apropriado para provisionar um dispositivo virtual em seu sistema host. As imagens do dispositivo virtual são específicas para um sistema operacional.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.

Siga estas etapas no [portal do Azure](https://portal.azure.com/) para baixar uma imagem de dispositivo virtual.

1. No recurso que você criou, selecione **Visão Geral**. Se você tiver um recurso existente do Azure Data Box Gateway, selecione o recurso e vá para **Visão Geral**. Selecione **Configuração do dispositivo**.

    ![Novo recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. No bloco **Baixar imagem**, selecione a imagem do dispositivo virtual correspondente ao sistema operacional no servidor de host usado para provisionar a VM. Os arquivos de imagem têm aproximadamente 5,6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e posterior](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMWare ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

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


