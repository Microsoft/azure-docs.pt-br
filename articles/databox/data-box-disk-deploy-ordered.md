---
title: Tutorial para solicitar o Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para aprender a se inscrever e solicitar um Azure Data Box Disk para importar dados no Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 04ae2589e8abfab4826f2286d5f00869c24442f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057959"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Tutorial: Solicitar um Azure Data Box Disk

O Azure Data Box Disk é uma solução de nuvem híbrida que permite importar dados do local no Azure de maneira rápida, fácil e confiável. Transfira os dados para SSDs (unidades de estado sólido) fornecidas pela Microsoft e envie os discos novamente. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um Azure Data Box Disk. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Solicitar um Data Box Disk
> * Acompanhar o pedido
> * Cancelar o pedido

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar, preencha os seguintes pré-requisitos de configuração para o serviço do Data Box e o Data Box Disk.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:

* Você tem um computador cliente disponível do qual você pode copiar os dados. Computador cliente deve:
  * Executar um [Sistema operacional com suporte](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
  * Ter outro [software obrigatório](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente do Windows.  

## <a name="order-data-box-disk"></a>Solicitar Data Box Disk

Entrar em:

* O portal do Azure nesta URL: https://portal.azure.com a ordem do Data Box Disk.
* Ou no portal do Azure Governamental nesta URL: https://portal.azure.us. Para obter mais detalhes, acesse [Conectar-se ao Azure Governamental usando o portal](../azure-government/documentation-government-get-started-connect-with-portal.md).

Faça o seguinte para solicitar o Data Box Disk.

1. No canto superior esquerdo do portal, clique em **+ Criar um recurso** e procure *Azure Data Box*. Clique em **Azure Data Box**.

   ![Pesquisar Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Clique em **Criar**.

3. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as seguintes informações e clique em **Aplicar**.

    ![Selecionar a opção Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Configuração|Valor|
    |---|---|
    |Subscription|Selecione a assinatura para a qual o serviço Data Box está habilitado.<br> A assinatura está vinculada à sua conta de cobrança. |
    |Tipo de transferência| Importar para o Azure|
    |País/região de origem | Selecione o país/região em que os dados residem no momento.|
    |Região do Azure de destino|Selecione a região do Azure para onde você deseja transferir dados.|
  
4. Selecione **Data Box Disk**. A capacidade máxima da solução para um único pedido de 5 discos é de 35 TB. É possível criar vários pedidos de tamanhos maiores de dados.

     ![Selecionar a opção de Data Box Disk 2](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. No **Pedido**, especifique os **Detalhes do pedido**. Insira ou selecione as seguintes informações.

    |Configuração|Valor|
    |---|---|
    |Nome|Forneça um nome amigável para acompanhar o pedido.<br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número. |
    |Resource group| Use um grupo existente ou crie um novo. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. |
    |Região do Azure de destino| Selecione uma região para sua conta de armazenamento.<br> Atualmente, há suporte para contas de armazenamento em todas as regiões nos EUA, no Norte da Europa e na Europa Ocidental, no Canadá e na Austrália. |
    |Tamanho estimado de dados em TB| Insira uma estimativa em TB. <br>Com base no tamanho dos dados, a Microsoft envia um número apropriado de SSDs de 8 TB (7 TB de capacidade utilizável). <br>A capacidade máxima de uso de 5 discos é de 35 TB. |
    |Chave de acesso do disco| Forneça a chave de acesso do disco se você marcar **Usar chave personalizada em vez da chave de acesso gerada pelo Azure**. <br> Forneça uma chave de 12 a 32 caracteres alfanuméricos que tenha pelo menos um caractere numérico e um caractere especial. Os caracteres especiais permitidos são `@?_+`. <br> Você pode optar por ignorar essa opção e usar a chave de acesso gerada pelo Azure para desbloquear os discos.|
    |Destino de armazenamento     | Escolha entre conta de armazenamento, discos gerenciados ou ambos. <br> Com base na região especificada do Azure, selecione uma conta de armazenamento na lista filtrada de uma conta de armazenamento existente. O Data Box Disk pode ser vinculado a apenas 1 conta de armazenamento. <br> Você também pode criar uma nova conta de **Uso geral v1**, **Uso geral v2** ou de **Armazenamento de Blobs**. <br>Contas de armazenamento com redes virtuais são compatíveis. Para permitir que o serviço do Data Box trabalhe com contas de armazenamento protegido, habilite os serviços confiáveis em definições de firewall de rede da conta de armazenamento. Para obter mais informações, confira como [Adicionar o Azure Data Box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).|

    Se usar a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Ordem do Data Box Disk para a conta de armazenamento](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Se usar o Data Box Disk para criar discos gerenciados dos VHDs locais, você também precisará fornecer as seguintes informações:

    |Configuração  |Valor  |
    |---------|---------|
    |Resource group     | Se você pretende criar discos gerenciados de VHDs locais, crie um novo grupo de recursos. Use um grupo de recursos existente apenas se ele tiver sido criado para ordem do Data Box Disk para disco gerenciado pelo serviço Data Box. <br> Há suporte para apenas um grupo de recursos.|

    ![Ordem do Data Box Disk para o disco gerenciado](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço do Data Box faz upload dos VHDs na conta de armazenamento de preparo e, em seguida, os converte em discos gerenciados e os move para o grupo de recursos. Para obter mais informações, confira [Verificar o upload de dados para o Azure](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

6. Clique em **Próximo**.

    ![Fornecer detalhes do pedido](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. Na guia **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

   Após o processamento do pedido, você receberá uma notificação por email. Se você escolheu a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-disk-portal-customer-managed-shipping.md).

    ![Fornecer endereço de remessa](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. Nos **Detalhes da notificação**, especifique endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

9. Examine o **Resumo** de informações relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

10. Clique em **Pedido**. A criação do pedido demora alguns minutos.

## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Vá até seu pedido e depois até **Visão geral** para exibir o status. O portal mostra o trabalho sob o estado **Pedido**.

![Data Box Disk com o status de pedido](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

Se os discos não estiverem disponíveis, você receberá uma notificação. Se os discos estiverem disponíveis, a Microsoft identificará os discos para remessa e preparará o pacote de disco. Durante a preparação do disco, ocorrerão as seguintes ações:

* Os discos são criptografados usando a criptografia BitLocker AES-128.  
* Os discos são bloqueados para impedir o acesso não autorizado a eles.
* A chave de acesso que desbloqueia os discos é gerada durante esse processo.

Quando a preparação de disco for concluída, o portal mostra o pedido no estado **Processado**.

A Microsoft preparará e enviará seus discos por meio de uma operadora regional. Depois que os discos forem enviados, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.

Só é possível fazer o cancelamento quando os discos foram solicitados e o pedido está sendo processado para remessa. Depois que o pedido for processado, não é mais possível cancelá-lo.

![Cancelar o pedido](media/data-box-disk-deploy-ordered/cancel-order1.png)

Para excluir um pedido cancelado, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Solicitar Data Box Disk
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como proteger seu Data Box Disk.

> [!div class="nextstepaction"]
> [Configure seu Azure Data Box Disk](./data-box-disk-deploy-set-up.md)