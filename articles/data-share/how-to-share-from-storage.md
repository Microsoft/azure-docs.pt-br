---
title: Compartilhar e receber dados do Armazenamento de Blobs do Azure e do Azure Data Lake Storage
description: Saiba como compartilhar e receber dados do armazenamento de BLOBs do Azure e Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: c94d6f7d4d06d7dbaa6a2ad54f94575a6cc85d33
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644666"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Compartilhar e receber dados do Armazenamento de Blobs do Azure e do Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

O compartilhamento de dados do Azure dá suporte ao compartilhamento baseado em instantâneo de uma conta de armazenamento. Este artigo explica como compartilhar e receber dados do armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2.

O compartilhamento de dados do Azure dá suporte ao compartilhamento de arquivos, pastas e sistemas de arquivos do Azure Data Lake Gen1 e Azure Data Lake Gen2. Ele também dá suporte ao compartilhamento de BLOBs, pastas e contêineres do armazenamento de BLOBs do Azure. No momento, somente há suporte para BLOBs de blocos. Os dados compartilhados dessas fontes podem ser recebidos pelo Azure Data Lake Gen2 ou pelo armazenamento de BLOBs do Azure.

Quando os sistemas de arquivos, os contêineres ou as pastas são compartilhados no compartilhamento baseado em instantâneo, os consumidores de dados podem optar por fazer uma cópia completa dos dados de compartilhamento. Ou podem usar o recurso de instantâneo incremental para copiar somente arquivos novos ou atualizados. O recurso de instantâneo incremental se baseia na hora da última modificação dos arquivos. 

Os arquivos existentes com o mesmo nome são substituídos durante um instantâneo. Um arquivo que é excluído da origem não é excluído no destino. Subpastas vazias na origem não são copiadas para o destino. 

## <a name="share-data"></a>Compartilhar dados

Use as informações nas seções a seguir para compartilhar dados usando o compartilhamento de dados do Azure. 
### <a name="prerequisites-to-share-data"></a>Pré-requisitos para compartilhar dados

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Localize o endereço de email de entrada do Azure do destinatário. O alias de email do destinatário não funcionará para suas finalidades.
* Se o armazenamento de dados do Azure de origem estiver em uma assinatura do Azure diferente daquela em que você criará o recurso de compartilhamento de dados, registre o [provedor de recursos Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura em que o repositório de dados do Azure está localizado. 

### <a name="prerequisites-for-the-source-storage-account"></a>Pré-requisitos para a conta de armazenamento de origem

* Uma conta de armazenamento do Azure. Se você ainda não tiver uma conta, [crie uma](../storage/common/storage-account-create.md).
* Permissão para gravar na conta de armazenamento. A permissão de gravação está em *Microsoft. Storage/storageAccounts/Write*. Faz parte da função colaborador.
* Permissão para adicionar a atribuição de função à conta de armazenamento. Essa permissão está no *Microsoft. Authorization/atribuições/gravação de função*. Faz parte da função de proprietário. 

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Criar uma conta de compartilhamento de dados

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. No canto superior esquerdo do portal, abra o menu e, em seguida, selecione **criar um recurso** (+).

1. Pesquise *Data Share*.

1. Selecione **compartilhamento de dados** e **criar**.

1. Forneça os detalhes básicos do seu recurso de compartilhamento de dados do Azure: 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione uma assinatura do Azure para sua conta de compartilhamento de dados.|
    | Grupo de recursos | *test-resource-group* | Use um grupo de recursos existente ou crie um grupo de recursos. |
    | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | Nome | *datashareaccount* | Nomeie sua conta de compartilhamento de dados. |
    | | |

1. Selecione **examinar + criar**  >  **criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos. 

1. Quando a implantação for concluída, selecione **Ir para o recurso**.

### <a name="create-a-share"></a>Criar um compartilhamento

1. Acesse sua página de **visão geral** do compartilhamento de dados.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Captura de tela mostrando a visão geral do compartilhamento de dados.":::

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Forneça os detalhes para seu compartilhamento. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![Captura de tela mostrando detalhes do compartilhamento de dados.](./media/enter-share-details.png "Insira os detalhes do compartilhamento de dados.") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de itens ao seu compartilhamento, selecione **Adicionar conjuntos** de os. 

    ![Captura de tela mostrando como adicionar conjuntos de itens ao seu compartilhamento.](./media/datasets.png "Conjuntos de dados.")

1. Selecione um tipo de conjunto de tipos para adicionar. A lista de tipos de conjunto de conjuntos depende de se você selecionou o compartilhamento baseado em instantâneo ou o compartilhamento in-loco na etapa anterior. 

    ![Captura de tela mostrando onde selecionar um tipo de conjunto de texto.](./media/add-datasets.png "Adicionar conjuntos de os.")    

1. Vá para o objeto que você deseja compartilhar. Em seguida, selecione **Adicionar conjuntos** de os. 

    ![Captura de tela mostrando como selecionar um objeto a ser compartilhado.](./media/select-datasets.png "Selecione conjuntos de os.")    

1. Na guia **destinatários** , adicione o endereço de email do consumidor de dados selecionando **Adicionar destinatário**. 

    ![Captura de tela mostrando como adicionar endereços de email do destinatário.](./media/add-recipient.png "Adicionar destinatários.") 

1. Selecione **Continuar**.

1. Se você selecionou um tipo de compartilhamento de instantâneo, poderá configurar o agendamento de instantâneo para atualizar seus dados para o consumidor de dados. 

    ![Captura de tela mostrando as configurações de agendamento do instantâneo.](./media/enable-snapshots.png "Habilite instantâneos.") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**.

1. Na guia **revisar + criar** , examine o conteúdo do pacote, as configurações, os destinatários e as configurações de sincronização. Em seguida, selecione **Criar**.

Agora você criou o compartilhamento de dados do Azure. O destinatário do seu compartilhamento de dados pode aceitar seu convite. 

## <a name="receive-data"></a>Receber dados

As seções a seguir descrevem como receber dados compartilhados.
### <a name="prerequisites-to-receive-data"></a>Pré-requisitos para receber dados
Antes de aceitar um convite de compartilhamento de dados, verifique se você tem os seguintes pré-requisitos: 

* Uma assinatura do Azure. Se você não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Um convite do Azure. O assunto do email deve ser "convite de compartilhamento de dados do Azure de *\<yourdataprovider\@domain.com>* ".
* Um [provedor de recursos Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) registrado em:
    * A assinatura do Azure em que você criará um recurso de compartilhamento de dados.
    * A assinatura do Azure onde os armazenamentos de dados do Azure de destino estão localizados.

### <a name="prerequisites-for-a-target-storage-account"></a>Pré-requisitos para uma conta de armazenamento de destino

* Uma conta de armazenamento do Azure. Se você ainda não tiver uma, [crie uma conta](../storage/common/storage-account-create.md). 
* Permissão para gravar na conta de armazenamento. Essa permissão está em *Microsoft. Storage/storageAccounts/Write*. Faz parte da função colaborador. 
* Permissão para adicionar a atribuição de função à conta de armazenamento. Esta atribuição está em *Microsoft. Authorization/atribuições/gravação de função*. Faz parte da função de proprietário.  

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Abrir um convite

Você pode abrir um convite de email ou diretamente do portal do Azure.

1. Para abrir um convite de email, verifique sua caixa de entrada para obter um convite de seu provedor de dados. O convite de Microsoft Azure é intitulado "convite de compartilhamento de dados do Azure de *\<yourdataprovider\@domain.com>* ". Selecione **Exibir convite** para ver seu convite no Azure. 

   Para abrir um convite da portal do Azure, pesquise por *convites de compartilhamento de dados*. Você verá uma lista de convites de compartilhamento de dados.

   ![Captura de tela mostrando a lista de convites na portal do Azure.](./media/invitations.png "Lista de convites.") 

1. Selecione o compartilhamento que você deseja exibir. 

### <a name="accept-an-invitation"></a>Aceitar um convite
1. Examine todos os campos, incluindo o **termos de uso**. Se você concordar com os termos, marque a caixa de seleção. 

   ![Captura de tela mostrando a área de Termos de uso.](./media/terms-of-use.png "Termos de uso.") 

1. Em **conta de compartilhamento de dados de destino**, selecione a assinatura e o grupo de recursos em que você implantará o compartilhamento de dados. Em seguida, preencha os seguintes campos:

   * No campo **conta de compartilhamento de dados** , selecione **criar novo** se você não tiver uma conta de compartilhamento de dados. Caso contrário, selecione uma conta de compartilhamento de dados existente que aceitará o compartilhamento de dados. 

   * No campo **nome do compartilhamento recebido** , deixe o padrão que o provedor de dados especificou ou especifique um novo nome para o compartilhamento recebido. 

1. Selecione **aceitar e configurar**. Uma assinatura de compartilhamento é criada. 

   ![Captura de tela mostrando onde aceitar as opções de configuração.](./media/accept-options.png "Aceitar opções") 

    O compartilhamento recebido aparece em sua conta de compartilhamento de dados. 

    Se você não quiser aceitar o convite, selecione **rejeitar**. 

### <a name="configure-a-received-share"></a>Configurar um compartilhamento recebido
Siga as etapas nesta seção para configurar um local para receber dados.

1. Na guia **conjuntos de valores** , marque a caixa de seleção ao lado do conjunto de um no qual você deseja atribuir um destino. Selecione **mapear para destino** para escolher um armazenamento de dados de destino. 

   ![Captura de tela mostrando como mapear para um destino.](./media/dataset-map-target.png "Mapear para o destino.") 

1. Selecione um armazenamento de dados de destino para os dados. Os arquivos no armazenamento de dados de destino que têm o mesmo caminho e nome que os arquivos nos dados recebidos serão substituídos. 

   ![Captura de tela mostrando onde selecionar uma conta de armazenamento de destino.](./media/map-target.png "Armazenamento de destino.") 

1. Para o compartilhamento baseado em instantâneo, se o provedor de dados usar um agendamento de instantâneo para atualizar regularmente os dados, você poderá habilitar a agenda na guia **agendamento de instantâneo** . Selecione a caixa ao lado do agendamento do instantâneo. Em seguida, selecione **Habilitar**. Observe que o primeiro instantâneo agendado começará dentro de um minuto após o tempo de agendamento e os instantâneos subsequentes serão iniciados em segundos da hora agendada.

   ![Captura de tela mostrando como habilitar uma agenda de instantâneo.](./media/enable-snapshot-schedule.png "Habilitar agendamento de instantâneo.")

### <a name="trigger-a-snapshot"></a>Disparar um instantâneo
As etapas nesta seção se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo da guia **detalhes** . Na guia, selecione **disparar instantâneo**. Você pode optar por disparar um instantâneo completo ou um instantâneo incremental de seus dados. Se você estiver recebendo dados do seu provedor de dados pela primeira vez, selecione **cópia completa**. Quando um instantâneo estiver em execução, os instantâneos subsequentes não serão iniciados até que o anterior seja concluído.

   ![Captura de tela mostrando a seleção do instantâneo de gatilho.](./media/trigger-snapshot.png "Disparar instantâneo.") 

1. Quando o status da última execução for *bem-sucedido*, vá para o armazenamento de dados de destino para exibir os dados recebidos. Selecione **conjuntos de valores** e, em seguida, selecione o link caminho de destino. 

   ![Captura de tela mostrando um mapeamento de conjunto de banco de um consumidor.](./media/consumer-datasets.png "Mapeamento de conjunto de clientes do consumidor.") 

### <a name="view-history"></a>Exibir histórico
Você pode exibir o histórico de seus instantâneos somente no compartilhamento baseado em instantâneo. Para exibir o histórico, abra a guia **histórico** . Aqui você vê o histórico de todos os instantâneos que foram gerados nos últimos 30 dias. 

## <a name="storage-snapshot-performance"></a>Desempenho do instantâneo de armazenamento
O desempenho do instantâneo de armazenamento é afetado por vários fatores, além do número de arquivos e do tamanho dos dados compartilhados. É sempre recomendável realizar seus próprios testes de desempenho. Veja abaixo alguns fatores de exemplo que afetam o desempenho.

* Acesso simultâneo aos armazenamentos de dados de origem e de destino.  
* Local dos armazenamentos de dados de origem e de destino. 
* Para o instantâneo incremental, o número de arquivos no conjunto de pesquisa compartilhado pode afetar o tempo necessário para localizar a lista de arquivos com a hora da última modificação após o último instantâneo bem-sucedido. 


## <a name="next-steps"></a>Próximas etapas
Você aprendeu como compartilhar e receber dados de uma conta de armazenamento usando o serviço de compartilhamento de dados do Azure. Para saber mais sobre o compartilhamento de outras fontes de dados, consulte [armazenamentos de dados com suporte](supported-data-stores.md).