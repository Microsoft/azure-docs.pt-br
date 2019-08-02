---
title: Tutorial sobre como solicitar o Azure Data Box Heavy | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como solicitar um Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592404"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Solicitar o Azure Data Box Heavy


O Azure Data Box Heavy é uma solução híbrida que permite importar dados locais para o Azure de maneira rápida, fácil e confiável. Você transfere seus dados para um dispositivo de armazenamento de 770 TB (capacidade utilizável aproximada) fornecido pela Microsoft e, em seguida, devolve o dispositivo. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um Azure Data Box Heavy. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Pré-requisitos do Data Box Heavy
> * Solicitar um Data Box Heavy
> * Acompanhar o pedido
> * Cancelar o pedido

## <a name="prerequisites"></a>Pré-requisitos

Preencha os seguintes pré-requisitos de configuração para o serviço e dispositivo do Data Box antes de implantar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, verifique se:

- O dispositivo é ajustado por meio de entradas e portais padrão. No entanto, verifique se o dispositivo pode ser ajustado por meio de todas as suas entradas. As dimensões do dispositivo são: largura: 26” comprimento: 48” altura: 28”.
- Se ele for instalado em um andar que não seja o térreo, você precisará ter acesso ao dispositivo por meio de um elevador ou uma rampa. O dispositivo pesa aproximadamente 226,8 kg (500 lb).
- Verifique se você tem um site simples no datacenter com proximidade a uma conexão de rede disponível que possa acomodar um dispositivo com esse volume.


### <a name="for-service"></a>Para o serviço

Antes de começar, verifique se:
- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
- Verifique se a assinatura usada para o serviço Data Box é de um dos seguintes tipos:
    - Microsoft EA (Contrato Enterprise). Leia mais sobre [assinaturas de EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - CSP (Provedor de Soluções na Nuvem). Saiba mais sobre o [programa do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Verifique se você tem acesso de proprietário ou colaborador à assinatura para criar um pedido do Data Box Heavy.

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:
- O dispositivo foi desempacotado.
- Você deve ter um computador host conectado à rede do datacenter. O Data Box Heavy copiará os dados deste computador. O computador host precisa executar um sistema operacional compatível, conforme descrito em [Requisitos do sistema do Azure Data Box Heavy](data-box-system-requirements.md).
- Você precisa ter um laptop com um cabo RJ-45 para se conectar à IU local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
- Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE.
- Você precisa de um cabo de 10 ou 40 Gbps por nó do dispositivo. Escolha cabos que sejam compatíveis com o adaptador de rede [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - Para o cabo de 40 Gbps, a extremidade do dispositivo no cabo precisa ser QSFP+.
    - Para o cabo de 10 Gbps, você precisará de um cabo SFP+ que se conecta a um comutador de 10 G em uma extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que se conecta ao dispositivo.
    - Os cabos de alimentação são incluídos com o dispositivo.

## <a name="order-data-box-heavy"></a>Solicitar o Data Box Heavy

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecione **+ Criar um recurso** e pesquise *Azure Data Box*. Selecione **Azure Data Box**.
    
   [![Pesquisar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecione **Criar**.

4. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as informações a seguir e selecione **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Assinatura     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Tipo de transferência     | Selecione **Importar no Azure**.        |
    |País de origem     | Selecione o país/região em que os dados residem no momento.         |
    |Região do Azure de destino     | Selecione a região do Azure para onde você deseja transferir dados.        |

    [![Selecionar a disponibilidade da família Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecione **Data Box Heavy**. A capacidade máxima utilizável em um único pedido é de 770 TB.

    [![Selecionar Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. No **Pedido**, especifique os **Detalhes do pedido**. Insira ou selecione as informações a seguir e selecione **Avançar**.
    
    |Configuração  |Valor  |
    |---------|---------|
    |NOME     | Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.      |
    |Grupo de recursos     | Use um grupo existente ou crie um novo. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos.         |
    |Região do Azure de destino     | Selecione uma região para sua conta de armazenamento. <br> Para saber mais, acesse [disponibilidade por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Destino de armazenamento     | Escolha entre conta de armazenamento, discos gerenciados ou ambos. <br> Com base na região especificada do Azure, selecione uma ou mais contas de armazenamento existente na lista filtrada de uma conta de armazenamento existente. <br>O Data Box Heavy pode ser vinculado a até 10 contas de armazenamento. <br> Você também pode criar uma nova conta de **Uso geral v1**, **Uso geral v2** ou de **Armazenamento de Blobs**. <br> Não há suporte para contas do Azure Data Lake Storage Gen2. Confira as [contas de armazenamento compatíveis com seu dispositivo](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Contas de armazenamento com redes virtuais são compatíveis. Para permitir que o serviço do Data Box trabalhe com contas de armazenamento protegido, habilite os serviços confiáveis em definições de firewall de rede da conta de armazenamento. Para obter mais informações, confira como [Adicionar o serviço Azure Data Box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).|

    Se usar a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Pedido do Data Box Heavy para a conta de armazenamento](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Se, além da conta de armazenamento como o destino de armazenamento, você também estiver usando o Data Box Heavy para criar discos gerenciados com base nos VHDs locais, você precisará fornecer as seguintes informações:

    |Configuração  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Se você pretende criar discos gerenciados de VHDs locais, crie novos grupos de recursos. Use um grupo de recursos existente somente se o grupo de recursos tiver sido criado anteriormente durante a criação de um pedido do Data Box Heavy para o disco gerenciado pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. Há suporte para um limite máximo de dez grupos de recursos.|

    ![Pedido do Data Box Heavy para o disco gerenciado](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço do Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de preparo antes de convertê-los em discos gerenciados e movê-los para os grupos de recursos. Para obter mais informações, confira [Verificar o upload de dados para o Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Selecione **Validar endereço**. 

    O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito. Selecione **Avançar**.

8. Nos **Detalhes da notificação**, especifique endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

9. Examine o **Resumo** de informações relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

10. Selecione **Pedido**. A criação do pedido demora alguns minutos.


## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box Heavy e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrerão as seguintes ações:

- Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
- Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
- Também é gerada uma senha do dispositivo que ajuda a desbloqueá-lo.
- O Data Box Heavy é bloqueado para impedir o acesso não autorizado ao dispositivo a qualquer momento.

Após a conclusão da preparação do dispositivo, o portal mostra o pedido no estado **Processado**.

![Pedido do Data Box Heavy processado](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft preparará e enviará seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Pedido do Data Box Heavy despachado](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.

Depois de fazer um pedido, você poderá cancelá-lo a qualquer momento antes do status ser marcado como processado.
 
Para excluir um pedido cancelado, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Solicitar o Data Box Heavy
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como configurar o Data Box Heavy.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
