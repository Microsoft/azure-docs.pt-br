---
title: Tutorial para preparar o portal do Azure e o ambiente de datacenter para implantar o Azure Stack Edge Pro | Microsoft Docs
description: O primeiro tutorial sobre como implantar o Azure Stack Edge Pro envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 07b526d443b5f1b41bc6f811b7cccc0fbc6165ee
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761718"
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

* Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Não há suporte para as assinaturas pagas conforme o uso.

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

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se você tiver um recurso do Azure Stack Edge existente para gerenciar seu dispositivo físico, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso do Azure Stack Edge, execute as etapas a seguir no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar 

    - No portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).
    - Ou no portal do Azure Governamental nesta URL: [https://portal.azure.us](https://portal.azure.us). Para obter mais detalhes, acesse [Conectar-se ao Azure Governamental usando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).

2. No painel esquerdo, selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge/Data Box Gateway**. Selecione **Criar**.
3. Escolha a assinatura que deseja usar para o dispositivo Azure Stack Edge Pro. Selecione a região em que você deseja implantar o recurso do Azure Stack Edge. Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo. A região armazena apenas os metadados para o gerenciamento de dispositivos. Os dados reais podem ser armazenados em qualquer conta de armazenamento.
    
    Na opção **Azure Stack Edge Pro**, selecione **Criar**.

    ![Pesquisar serviço do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Na guia **Informações Básicas**, insira ou selecione os **Detalhes do projeto** a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Subscription    |Isso é preenchido automaticamente de acordo com a seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
    |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).     |

4. Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome deve ter de 2 a 50 caracteres, incluindo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|

    ![Detalhes do projeto e da instância](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Selecione **Avançar: Endereço para entrega**.

    - Caso já tenha um dispositivo, marque a caixa de combinação **Tenho um dispositivo Azure Stack Edge**.
    - Se esse for o novo dispositivo solicitado, insira o nome de contato, a empresa, o endereço para entrega do dispositivo e informações de contato.

    ![Endereço para entrega do novo dispositivo](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecione **Avançar: Revisar + criar**.

7. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Marque a caixa de combinação **Li os termos de privacidade**.

    ![Examinar os detalhes e os termos de privacidade do recurso do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecione **Criar**.

   A criação do recurso leva alguns minutos. Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

   ![Acesse o recurso do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Depois que o pedido for feito, a Microsoft analisará o pedido e o contatará (por email) com os detalhes da entrega.

![Notificação para revisão do pedido do Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)


> [!NOTE]
> Se você quiser criar vários pedidos ao mesmo tempo ou clonar um pedido existente, poderá usar os [scripts em exemplos do Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Para obter mais informações, confira o arquivo LEIAME.

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Azure Stack Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Azure Stack Edge Pro ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Vá até o recurso criado e selecione **Visão Geral**. Você verá uma notificação informando que pedido está sendo processado.

    ![Selecionar Visão geral](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

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