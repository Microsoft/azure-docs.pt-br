---
title: Tutorial para preparar o portal do Azure e o ambiente de datacenter para implantar o Azure Stack Edge Pro | Microsoft Docs
description: O primeiro tutorial sobre como implantar o Azure Stack Edge Pro envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: alkohli
ms.openlocfilehash: 0e0d7808e449a6e4f3e341cd40acdc220e65c0ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068210"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Tutorial: Preparar a implantação do Azure Stack Edge Pro  

Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente seu Azure Stack Edge Pro. Este tutorial descreve como preparar o portal do Azure para implantar um recurso do Azure Stack Edge. 

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Obter a chave de ativação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="get-started"></a>Introdução

Veja os tutoriais a seguir na sequência indicada para implantar o Azure Stack Edge Pro.

| **#** | **Nesta etapa** | **Use estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Crie e configure seu recurso do Azure Stack Edge antes de instalar um dispositivo físico do Azure Stack Edge. |
| 2. |**[Instalar o Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|Desempacotar, colocar em rack e conectar os cabos do dispositivo físico do Azure Stack Edge Pro.  |
| 3. |**[Conectar, configurar e ativar o Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Conectar-se a interface do usuário da Web local, conclua a configuração do dispositivo e ativar o dispositivo. O dispositivo está pronto para configurar compartilhamentos NFS ou SMB.  |
| 4. |**[Transferir dados com o Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Adicione compartilhamentos e conecte-se a ele por meio de SMB ou NFS. |
| 5. |**[Transformar dados com o Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Configure módulos de computação no dispositivo para transformar os dados à medida que eles são migrados para o Azure. |

Agora, você pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A seguir estão os pré-requisitos de configuração para o recurso do Azure Stack Edge, o dispositivo Azure Stack Edge Pro e a rede do datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

* Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Não há suporte para assinaturas pagas conforme o uso.

* Você tem acesso de proprietário ou colaborador no nível do grupo de recursos aos recursos do Azure Stack Edge/Data Box Gateway, do Hub IoT e do Armazenamento do Azure.

  * Para conceder acesso de colaborador, você precisa ser um **Proprietário** no nível da assinatura. Para conceder acesso de colaborador a outra pessoa, no portal do Azure, acesse **Todos os Serviços** > **Assinaturas** > **Controle de acesso (IAM)**  >  **+Adicionar** > **Adicionar atribuição de função**. Para saber mais, confira [Tutorial: Conceder acesso aos recursos do Azure a um usuário usando o portal do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Para criar qualquer recurso do Azure Stack Edge/Data Box Gateway, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisará verificar se o provedor de recursos `Microsoft.DataBoxEdge` está registrado. Para saber como registrar um provedor de recursos, confira [Registrar provedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar qualquer recurso do Hub IoT, verifique se o provedor Microsoft.Devices está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
* Você tem acesso de usuário ou administrador à API do Azure Active Directory Graph. Para obter mais informações, confira [API do Azure Active Directory Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
* Você não está bloqueado por nenhuma política do Azure configurada pelo administrador do sistema. Para saber mais sobre políticas, confira o [Início Rápido: Criar uma atribuição de política para identificar recursos fora de conformidade](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Para o dispositivo Azure Stack Edge Pro

Antes de implantar um dispositivo físico, verifique se:

* Você examinou as informações de segurança que foram incluídas no pacote de remessa.
* Você tem um slot de 1 U disponível em um rack padrão de 19" no seu datacenter para montar o dispositivo em rack.
* Você tem acesso a uma superfície de trabalho plana, estável e nivelada na qual o dispositivo pode apoiar-se com segurança.
* O local em que você pretende instalar o dispositivo tem alimentação CA padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) no rack com um no-break.
* Você tem acesso a um dispositivo físico.

### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

* A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Azure Stack Edge Pro. Para obter mais informações, confira os requisitos do sistema do [Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

* Para as condições normais de operação do Azure Stack Edge Pro, você tem:

  * Uma largura de banda de download de, no mínimo, 10 Mbps para garantir que o dispositivo permaneça atualizado.
  * Uma largura de banda dedicada de download e upload de, no mínimo, 20 Mbps para transferir arquivos.

## <a name="create-new-resource-for-existing-device"></a>Criar um recurso para um dispositivo existente

Se você já é cliente do Azure Stack Edge Pro, use o procedimento a seguir para criar um recurso, caso precise substituir ou redefinir o dispositivo existente.

Se você é um novo cliente, recomendamos que você explore o uso de dispositivos Azure Stack Edge Pro – GPU para suas cargas de trabalho. Para obter mais informações, acesse [O que é o Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-overview.md). Para obter informações sobre como solicitar um dispositivo Azure Stack Edge Pro com GPU, acesse [Criar um recurso para o Azure Stack Edge Pro – GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Para criar um recurso do Azure Stack Edge Pro para um dispositivo existente, execute as etapas a seguir no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar em:

    - No portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou no portal do Azure Governamental nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter mais detalhes, acesse [Conectar-se ao Azure Governamental usando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).

1. Selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge**. Em seguida, selecione **Criar**.

1. Selecione a assinatura do dispositivo Azure Stack Edge Pro e o país para envio do dispositivo em **Enviar para**.

   ![Seleção da assinatura e do país para entrega do dispositivo](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Na lista de tipos de dispositivos exibida, selecione **Azure Stack Edge Pro – FPGA**. Em seguida, escolha **Selecionar**. 

   O tipo de dispositivo **Azure Stack Edge Pro – FPGA** só será exibido se você já tiver um dispositivo. Caso precise solicitar um novo dispositivo, acesse [Criar um recurso do Azure Stack Edge Pro – GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Pesquisar serviço do Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. Na guia **Informações Básicas**:

   1. Insira ou escolha os **Detalhes do projeto** a seguir.
    
       |Setting  |Valor  |
       |---------|---------|
       |Subscription    |Esse valor é preenchido automaticamente de acordo com a seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
       |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).     |

   1. Insira ou selecione os **Detalhes da instância** a seguir.

       |Configuração  |Valor  |
       |---------|---------|
       |Nome   | Um nome amigável para identificar o recurso.<br>O nome deve ter de 2 a 50 caracteres, incluindo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
       |Região     |Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|

   1. Selecione **Examinar + criar**.

    ![Detalhes do projeto e da instância](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. Na guia **Examinar + criar**, examine os **Termos de uso**, os **Detalhes de preço** e os detalhes do recurso. Em seguida, selecione **Criar**.

    ![Examinar os detalhes e os termos de privacidade do recurso do Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. A criação do recurso leva alguns minutos. Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

   ![Acesse o recurso do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Depois que o pedido for feito, a Microsoft o examinará e contatará você (por email) com os detalhes da entrega.

![Notificação para revisão do pedido do Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Azure Stack Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Azure Stack Edge Pro ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Vá até o recurso criado e selecione **Visão Geral**. Você verá uma notificação informando que pedido está sendo processado.

    ![Selecionar Visão geral](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Depois que o pedido for processado e o dispositivo estiver a caminho, a **Visão geral** será atualizada. Aceite o nome do **Azure Key Vault** padrão ou insira um novo. Selecione **Gerar chave de ativação**. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * A chave de ativação expira três dias depois de ser gerada.
> * Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu tópicos do Azure Stack Edge Pro, como:

> [!div class="checklist"]
>
> * Criar um novo recurso
> * Obter a chave de ativação

Prossiga para o próximo tutorial para saber como instalar o Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Instalar o Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)