---
title: Tutorial para preparar o portal do Azure e o ambiente de datacenter para implantar o dispositivo Azure Stack Edge Mini R | Microsoft Docs
description: O primeiro tutorial sobre como implantar o dispositivo Azure Stack Edge Mini R envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 7042d3ede872c5dc854675334b964430671d1306
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065626"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Tutorial: Preparar a implantação do Azure Stack Edge Mini R

Este é o primeiro tutorial de uma série de tutoriais de implantação necessários para implantar um dispositivo do Azure Stack Edge Mini R de modo completo. Este tutorial descreve como preparar o portal do Azure para implantar um recurso do Azure Stack Edge.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para implantar o Azure Stack Edge Mini R, veja os tutoriais a seguir na sequência indicada.

| Etapa | Descrição |
| --- | --- |
| **Preparação** |Essas etapas precisam ser concluídas na preparação para a próxima implantação. |
| **[Lista de verificação da configuração da implantação](#deployment-configuration-checklist)** |Use essa lista de verificação para coletar e registrar informações antes e durante a implantação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Esses pré-requisitos validam o ambiente como pronto para implantação. |
|  | |
|**Tutoriais de implantação** |Esses tutoriais são necessários para implantar seu dispositivo Azure Stack Edge Mini R em produção. |
|**[1. Preparar o portal do Azure para o dispositivo](azure-stack-edge-mini-r-deploy-prep.md)** |Crie e configure seu recurso do Azure Stack Edge antes de instalar o dispositivo físico. |
|**[2. Instalar o dispositivo](azure-stack-edge-mini-r-deploy-install.md)**|Inspecione e conecte seu dispositivo físico.  |
|**[3. Conectar-se ao dispositivo](azure-stack-edge-mini-r-deploy-connect.md)** |Depois que o dispositivo for instalado, conecte-se à IU da Web local do dispositivo.  |
|**[4. Definir as configurações de rede](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo as configurações de rede de computação e do proxy Web para seu dispositivo.   |
|**[5. Definir configurações do dispositivo](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Atribua um nome de dispositivo e domínio DNS, configure o servidor de atualização e a hora do dispositivo. |
|**[6. Definir configurações de segurança](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Configure certificados usando seus certificados, configure a VPN e configure a criptografia em repouso para o dispositivo.   |
|**[7. Ativar o dispositivo](azure-stack-edge-mini-r-deploy-activate.md)** |Use a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar compartilhamentos NFS ou SMB ou conectar por meio de REST. |
|**[8. Configurar a computação](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure a função de computação em seu dispositivo. Um cluster do Kubernetes também será criado. |

Agora, você pode começar a configurar o portal do Azure.

## <a name="deployment-configuration-checklist"></a>Lista de verificação da configuração da implantação

Antes de implantar o dispositivo, você precisará reunir as informações para configurar o software em seu dispositivo Azure Stack Edge Mini R. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo no seu ambiente. Use a [lista de verificação de configuração de implantação do Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md) para anotar os detalhes de configuração enquanto você implanta o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

A seguir estão os pré-requisitos de configuração para o recurso do Azure Stack Edge, o dispositivo Azure Stack Edge e a rede do datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Para o dispositivo Azure Stack Edge

Antes de implantar um dispositivo físico, verifique se:

- Você analisou as informações de segurança do dispositivo em [Diretrizes de segurança para seu dispositivo do Azure Stack Edge](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

- A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Azure Stack Edge. Para obter mais informações, confira [Requisitos do sistema do Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Para as condições normais de operação do Azure Stack Edge, você tem:

    - Uma largura de banda de download de, no mínimo, 10 Mbps para garantir que o dispositivo permaneça atualizado.
    - Uma largura de banda dedicada de download e upload de, no mínimo, 20 Mbps para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se você tiver um recurso do Azure Stack Edge existente para gerenciar seu dispositivo físico, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar um recurso do Azure Stack Edge, execute as etapas a seguir no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar no portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).


2. No painel esquerdo, selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge/Data Box Gateway**. Selecione **Criar**. 

3. Escolha a assinatura que deseja usar para o dispositivo Azure Stack Edge Pro. Selecione o país para onde deseja enviar este dispositivo físico. Selecione **Mostrar dispositivos**.

    ![Criar um recurso 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Selecione o tipo de dispositivo. Em **Azure Stack Edge**, escolha **Azure Stack Edge Mini R** e **Selecionar**. Se você encontrar problemas ou não conseguir selecionar o tipo de dispositivo, vá para [Solucionar problemas de pedido](azure-stack-edge-troubleshoot-ordering.md).

    [![Criar um recurso 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. Na guia **Informações Básicas**, insira ou selecione os **Detalhes do projeto** a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Subscription    |A assinatura será preenchida de modo automático com base na seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
    |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).     |


6. Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome deve ter de 2 a 50 caracteres, incluindo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|

    ![Criar um recurso 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. Selecione **Avançar: Endereço para entrega**.

   - Caso já tenha um dispositivo, marque a caixa de combinação **Já tenho um dispositivo**.

     ![Criar um recurso 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Caso esse seja o novo dispositivo que você está solicitando, insira o nome de contato, a empresa, o endereço para a entrega do dispositivo e informações de contato.

     ![Criar um recurso 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. Selecione **Avançar: Marcas**. Opcionalmente, forneça marcas para categorizar recursos e consolidar a cobrança. Selecione **Avançar: Revisar + criar**.

9. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Marque a caixa de combinação **Li os termos de privacidade**.

    ![Criar um recurso 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Você também será notificado de que durante a criação do recurso um MSI (Identidade do Serviço Gerenciado) será habilitado. Ele permitirá obter uma autenticação nos serviços de nuvem. Essa identidade existirá enquanto o recurso existir.

10. Selecione **Criar**.

    A criação do recurso leva alguns minutos. Uma MSI também é criada para permitir que o dispositivo do Azure Stack Edge se comunique com o provedor de recursos no Azure.
    
    Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.
    
    ![Acesse o recurso do Azure Stack Edge Pro](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
Depois que o pedido for feito, a Microsoft analisará o pedido e o contatará (por email) com os detalhes da entrega.

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
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
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

Depois que o recurso do Azure Stack Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Azure Stack Edge Mini R ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Selecione o recurso criado e, em seguida, **Visão Geral**.

   ![Selecionar Configuração do dispositivo](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. No bloco **Ativar**, forneça o nome para o Azure Key Vault ou aceite o nome padrão. O nome do cofre de chaves deve ter entre 3 e 24 caracteres. 

    Um cofre de chaves é criado para cada recurso do Azure Stack Edge ativado com seu dispositivo. O cofre de chaves permite armazenar e acessar segredos. Por exemplo, o CIK (Chave de Integridade do Canal) do serviço será armazenado no cofre de chaves.

    Depois de especificar o nome do cofre de chaves, selecione **Gerar chave de ativação** para criar uma chave de ativação.

    [![Obter a chave de ativação](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Aguarde alguns minutos enquanto o cofre de chaves e a chave de ativação estão sendo criados. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.

> [!IMPORTANT]
> - A chave de ativação expira três dias depois de ser gerada.
> - Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Stack Edge, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Prossiga para o próximo tutorial para saber como instalar o Azure Stack Edge.

> [!div class="nextstepaction"]
> [Instalar o Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)
