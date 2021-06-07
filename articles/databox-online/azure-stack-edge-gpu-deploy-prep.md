---
title: Tutorial para preparar o portal do Azure e o ambiente de datacenter para implantar a GPU do Azure Stack Edge Pro | Microsoft Docs
description: O primeiro tutorial sobre como implantar a GPU do Azure Stack Edge Pro envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: e58473f5c3bc4bc6314fb0dc5c532e24daa225d6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065694"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Tutorial: Preparar a implantação do Azure Stack Edge Pro com GPU 

Este é o primeiro da série de tutoriais de implantação necessários para implantar completamente o Azure Stack Edge Pro com GPU. Este tutorial descreve como preparar o portal do Azure para implantar um recurso do Azure Stack Edge.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para a implantação do Azure Stack Edge Pro, você precisa primeiro preparar o ambiente. Quando o ambiente estiver pronto, siga as etapas exigidas e, se necessário, as etapas e os procedimentos opcionais para implantar totalmente o dispositivo. As instruções passo a passo de implantação indicam quando você deve executar cada uma das etapas necessárias e opcionais.

| Etapa | Descrição |
| --- | --- |
| **Preparação** |Essas etapas precisam ser concluídas na preparação para a próxima implantação. |
| **[Lista de verificação da configuração da implantação](#deployment-configuration-checklist)** |Use essa lista de verificação para coletar e registrar informações antes e durante a implantação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Esses pré-requisitos validam o ambiente como pronto para implantação. |
|  | |
|**Tutoriais de implantação** |Esses tutoriais são necessários para implantar seu dispositivo do Azure Stack Edge Pro em produção. |
|**[1. Preparar o portal do Azure para o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)** |Crie e configure seu recurso do Azure Stack Edge antes de instalar um dispositivo físico do Azure Stack Edge. |
|**[2. Instalar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|Desempacotar, colocar em rack e conectar os cabos do dispositivo físico do Azure Stack Edge Pro.  |
|**[3. Conectar-se ao Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |Depois que o dispositivo for instalado, conecte-se à IU da Web local do dispositivo.  |
|**[4. Definir configurações de rede para o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo as configurações de rede de computação e do proxy Web para seu dispositivo.   |
|**[5. Definir configurações do dispositivo para o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Atribua um nome de dispositivo e domínio DNS, configure o servidor de atualização e a hora do dispositivo. |
|**[6. Definir configurações de segurança para o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configure certificados para seu dispositivo. Use certificados gerados pelo dispositivo ou traga seus certificados.   |
|**[7. Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |Use a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar compartilhamentos NFS ou SMB ou conectar por meio de REST. |
|**[8. Configurar a computação](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure a função de computação em seu dispositivo. Um cluster do Kubernetes também será criado. |
|**[9A. Transferir dados com compartilhamentos do Edge](./azure-stack-edge-gpu-deploy-add-shares.md)** |Adicione compartilhamentos e conecte-se a ele por meio de SMB ou NFS. |
|**[9B. Transferir dados com contas de armazenamento do Edge](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)** |Adicione contas de armazenamento e conecte-se ao Armazenamento de Blobs por meio de APIs REST. |


Agora você pode começar a reunir informações sobre a configuração do software para seu dispositivo do Azure Stack Edge Pro.

## <a name="deployment-configuration-checklist"></a>Lista de verificação da configuração da implantação

Antes de implantar o dispositivo, você precisará reunir as informações para configurar o software em seu dispositivo do Azure Stack Edge Pro. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo no seu ambiente. Use a [lista de verificação de configuração de implantação do Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-checklist.md) para anotar os detalhes de configuração enquanto você implanta o dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

A seguir estão os pré-requisitos de configuração para o recurso do Azure Stack Edge, o dispositivo Azure Stack Edge Pro e a rede do datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

- Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Não há suporte para as assinaturas pagas conforme o uso. Para identificar o tipo de assinatura do Azure que você tem, confira [O que é uma oferta do Azure?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer).
- Você tem acesso de proprietário ou colaborador no nível do grupo de recursos aos recursos do Azure Stack Edge Pro/Data Box Gateway, do Hub IoT e do Armazenamento do Azure.

    - Para criar qualquer recurso do Azure Stack Edge/Data Box Gateway, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. 
    - Você também precisará verificar se os provedores de recursos `Microsoft.DataBoxEdge` e `MicrosoftKeyVault` estão registrados. Para criar um recurso do Hub IoT, o provedor `Microsoft.Devices` precisa ser registrado. 
        - Para registrar um provedor de recursos, no portal do Azure, acesse **Página Inicial > Assinaturas > Sua assinatura > Provedores de recursos**. 
        - Pesquise pelo provedor de recursos específico, por exemplo, `Microsoft.DataBoxEdge`, e registre-o. 
    - Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de administrador ou usuário à API do Graph do Azure Active Directory para gerar uma chave de ativação ou operações de credencial, como a criação de um compartilhamento que usa uma conta de armazenamento. Para obter mais informações, confira [API do Azure Active Directory Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Para o dispositivo Azure Stack Edge Pro

Antes de implantar um dispositivo físico, verifique se:

- Você examinou as informações de segurança que foram incluídas no pacote de remessa.
- Você tem um slot de 1 U disponível em um rack padrão de 19" no seu datacenter para montar o dispositivo em rack.
- Você tem acesso a uma superfície de trabalho plana, estável e nivelada na qual o dispositivo pode apoiar-se com segurança.
- O local em que você pretende instalar o dispositivo tem alimentação CA padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) no rack com um no-break.
- Você tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

- A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Azure Stack Edge Pro. Para obter mais informações, confira os requisitos do sistema do [Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

- Para as condições normais de operação do Azure Stack Edge Pro, você tem:

    - Uma largura de banda de download de, no mínimo, 10 Mbps para garantir que o dispositivo permaneça atualizado.
    - Uma largura de banda dedicada de download e upload de, no mínimo, 20 Mbps para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se você tiver um recurso do Azure Stack Edge existente para gerenciar seu dispositivo físico, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar um recurso do Azure Stack Edge, execute as etapas a seguir no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar no portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).

2. No painel esquerdo, selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge/Data Box Gateway**. Selecione **Criar**. 

3. Escolha a assinatura que deseja usar para o dispositivo Azure Stack Edge Pro. Selecione o país para onde deseja enviar este dispositivo físico. Selecione **Mostrar dispositivos**.

    ![Criar um recurso 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selecione o tipo de dispositivo. Em **Azure Stack Edge Pro**, escolha **Azure Stack Edge Pro com GPU** e **Selecionar**. Se você encontrar problemas ou não conseguir selecionar o tipo de dispositivo, vá para [Solucionar problemas de pedido](azure-stack-edge-troubleshoot-ordering.md).

    ![Criar um recurso 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Com base em suas necessidades de negócios, você pode selecionar o Azure Stack Edge Pro com uma ou duas GPUs (unidades de processamento gráfico) da NVIDIA. 

    ![Criar um recurso 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Na guia **Informações Básicas**, insira ou selecione os **Detalhes do projeto** a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Subscription    |A assinatura será preenchida de modo automático com base na seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
    |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).     |

7. Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome deve ter de 2 a 50 caracteres, contendo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|

    ![Criar um recurso 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. Selecione **Avançar: Endereço para entrega**.

    - Caso já tenha um dispositivo, marque a caixa de combinação **Já tenho um dispositivo**.

        ![Criar um recurso 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Se este for o novo dispositivo solicitado, insira o nome de contato, a empresa, o endereço para entrega do dispositivo e informações de contato.

        ![Criar um recurso 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Selecione **Avançar: Marcas**. Opcionalmente, forneça marcas para categorizar recursos e consolidar a cobrança. Selecione **Avançar: Revisar + criar**.

10. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Marque a caixa de combinação **Li os termos de privacidade**.

    ![Criar um recurso 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    Você também é notificado de que durante a criação do recurso, uma MSI (Identidade de Serviço Gerenciada) é habilitada e permite que você se autentique nos serviços de nuvem. Essa identidade existirá enquanto o recurso existir.

11. Selecione **Criar**.

    A criação do recurso leva alguns minutos. Uma MSI também é criada para permitir que o dispositivo do Azure Stack Edge se comunique com o provedor de recursos no Azure.

    Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

    ![Acesse o recurso do Azure Stack Edge Pro](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Depois que o pedido for feito, a Microsoft o examinará e contatará você (por email) com os detalhes da entrega.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> Se você quiser criar vários pedidos ao mesmo tempo ou clonar um pedido existente, poderá usar os [scripts em exemplos do Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Para obter mais informações, confira o arquivo LEIAME.

Se você tiver problemas durante o processo de pedido, confira [Solucionar problemas de pedidos](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se necessário, prepare seu ambiente para a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para criar um recurso do Azure Stack Edge, execute os comandos a seguir na CLI do Azure.

1. Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az_group_create) ou use um grupo de recursos existente:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Para criar um dispositivo, use o comando [az databoxedge device create](/cli/azure/databoxedge/device#az_databoxedge_device_create):

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo. A região armazena apenas os metadados para o gerenciamento de dispositivos. Os dados reais podem ser armazenados em qualquer conta de armazenamento.

   Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).

1. Para criar um pedido, execute o comando [az databoxedge order create](/cli/azure/databoxedge/order#az_databoxedge_order_create):

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

A criação do recurso leva alguns minutos. Execute o comando [az databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) para ver o pedido:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Depois que você fizer um pedido, a Microsoft examinará o pedido e contatará você por email com os detalhes de envio.

---

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Azure Stack Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Azure Stack Edge Pro ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Selecione o recurso criado e clique em **Visão geral**.

2. No painel direito, forneça o nome do Azure Key Vault ou aceite o nome padrão. O nome do cofre de chaves deve ter entre 3 e 24 caracteres.

   Um cofre de chaves é criado para cada recurso do Azure Stack Edge ativado com seu dispositivo. O cofre de chaves permite que você armazene e acesse segredos, por exemplo, a CIK (chave de integridade do canal) do serviço é armazenada no cofre de chaves. 

   Depois de especificar um nome para o cofre de chaves, selecione **Gerar chave** para criar uma chave de ativação. 

   ![Obter a chave de ativação](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   Aguarde alguns minutos enquanto o cofre de chaves e a chave de ativação estão sendo criados. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - A chave de ativação expira três dias depois de ser gerada.
> - Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu tópicos do Azure Stack Edge Pro, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Prossiga para o próximo tutorial para saber como instalar o Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Instalar o Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)