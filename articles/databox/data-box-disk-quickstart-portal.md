---
title: Início Rápido do Microsoft Azure Data Box Disk | Microsoft Docs
description: Use este guia de início rápido para implantar rapidamente o Azure Data Box Disk no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 02/26/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 65bf4e973ce33b2898abf585fe306a8bc85c64a0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477793"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Início Rápido: Implantar o Azure Data Box Disk usando o portal do Azure

::: zone-end

::: zone target="chromeless"

# <a name="get-started-with-azure-data-box-disk-using-azure-portal"></a>Introdução ao Azure Data Box Disk usando o portal do Azure

::: zone-end

::: zone target="docs"

Este início rápido descreve como implantar o Azure Data Box Disk usando o portal do Azure. As etapas incluem como, rapidamente, criar um pedido, receber discos, descompactar, conectar e copiar dados para discos de modo que eles sejam carregados no Azure.

Para obter a implantação detalhada passo a passo e instruções de acompanhamento, acesse [Tutorial: Solicitar o Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

Este início rápido descreve como implantar o Azure Data Box Disk usando o portal do Azure. As etapas incluem examinar os pré-requisitos, desbloquear os discos, conectar e copiar dados para discos para carregar no Azure.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Verifique se sua assinatura é habilitada para o serviço Azure Data Box. Para ativar sua assinatura para esse serviço, [Inscreva-se no serviço](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

## <a name="prerequisites"></a>Pré-requisitos

- Você fez o pedido para o Data Box Disk usando o [Tutorial: Solicitar o Azure Data Box Disk](data-box-disk-deploy-ordered.md).
- Você recebeu os discos e o status do trabalho o portal foi atualizado para **Entregue**.
- Você tem um computador cliente disponível do qual você pode copiar os dados. Computador cliente deve:

    - Executar um [sistema operacional com suporte](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ter outro [software obrigatório](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente do Windows.

::: zone-end

::: zone target="docs"

## <a name="order"></a>Classificar

Esta etapa leva aproximadamente 5 minutos.

1. Criar um novo recurso do Azure Data Box no portal do Azure. 
2. Selecione uma assinatura habilitada para esse serviço e escolha o tipo de transferência como **Importação**. Forneça o **País de origem** onde os dados residem e a **região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Disk**. A capacidade máxima da solução é de 35 TB, e você pode criar vários pedidos de disco para tamanhos maiores de dados.  
4. Insira os detalhes do pedido e as informações de envio. Se o serviço estiver disponível em sua região, forneça endereços de email de notificação, examine o resumo e, em seguida, crie o pedido.

Depois que o pedido é criado, os discos estão preparados para remessa.

## <a name="unpack"></a>Desempacotar

Esta etapa leva aproximadamente 5 minutos.

O Data Box Disk é enviado em uma caixa expressa da UPS. Abra a caixa e verifique se a ela contém:

- 1 a 5 discos de USB embalados em plástico-bolha.
- Um cabo de conexão por disco.
- Uma etiqueta de remessa para remessa de devolução.

## <a name="connect-and-unlock"></a>Conectar e desbloquear

Esta etapa leva aproximadamente 5 minutos.

1. Use o cabo incluído para conectar o disco em um computador Windows/Linux executando uma versão com suporte. Para obter mais informações sobre versões de sistema operacional com suporte, vá até [Requisitos de sistema do Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Para desbloquear o disco:

    1. No portal do Azure, acesse **Geral > Detalhes do Dispositivo** e obtenha a chave de acesso.
    2. Baixe e extraia a ferramenta de desbloqueio do Data Box Disk específico do sistema operacional no computador usado para copiar os dados para discos. 
    3. Execute a ferramenta de Desbloqueio do Data Box Disk e forneça a senha. Para qualquer nova inserção de disco, execute a ferramenta de desbloqueio novamente e forneça a chave de acesso. **Não use a caixa de diálogo BitLocker nem a chave do BitLocker para desbloquear o disco.** Para obter mais informações sobre como desbloquear discos, acesse [Desbloquear discos no cliente do Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos no cliente do Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. A letra da unidade atribuída ao disco é exibida pela ferramenta. Anote a letra da unidade de disco. Isso é usado nas etapas subsequentes.

## <a name="copy-data-and-validate"></a>Copiar dados e validar

O tempo para concluir essa operação depende do tamanho dos dados.

1. A unidade contém as pastas *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* e *DataBoxDiskImport*. Arraste e solte para copiar os dados que precisam ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, arraste e solte os dados como VHD/VHDX para a pasta *PageBlob* e os dados apropriados para *AzureFile*. Copiar os VHDs que você deseja carregar como discos gerenciados para uma pasta sob *ManagedDisk*.

    Um contêiner é criado na conta de armazenamento do Azure para cada subpasta em *BlockBlob* e a pasta *PageBlob*. Um compartilhamento de arquivos é criado para uma subpasta sob *AzureFile*.

    Todos os arquivos nas pastas *BlockBlob* e *PageBlob* são copiados para um contêiner padrão `$root` sob a conta de Armazenamento do Azure. Copie os arquivos para uma pasta na pasta no *AzureFile*. Os arquivos copiados diretamente para a pasta *AzureFile* falham e são carregados como blobs de blocos.

    > [!NOTE]
    > - Todos os contêineres, blobs e arquivos devem estar em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se essas regras não forem seguidas, o carregamento de dados do Azure falhará.
    > - Verifique se os arquivos não excedem ~4,75 TiB para blobs de blocos, ~8 TiB para blobs de páginas e ~1 TiB para Arquivos do Azure.

2. **(Opcional mas recomendado)**  Depois que a cópia for concluída, é altamente recomendável que pelo menos você execute o `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* e selecione a opção 1 para validar os arquivos. Também é recomendável permitir esse tempo, use a opção 2 para também gerar somas de verificação para validação (pode demorar dependendo do tamanho de dados). Essas etapas minimizam as chances de qualquer falha ao carregar os dados para o Azure.
3. Remova a unidade com segurança.

## <a name="ship-to-azure"></a>Enviar para o Azure

Essa etapa demora cerca de 5 a 7 minutos para ser concluída.

1. Coloque todos os discos em conjunto no pacote original. Use a etiqueta de remessa inclusa. Se a etiqueta for danificada ou perdida, baixe-a partir do portal. Vá para **Visão geral** e clique em **Baixar etiqueta de remessa** na barra de comandos.
2. Entregue o pacote lacrado no local de remessa.  

O serviço do Data Box Disk envia uma notificação por email e atualiza o status do trabalho no portal do Azure.

## <a name="verify-your-data"></a>Verifique seus dados

O tempo para concluir essa operação depende do tamanho dos dados.

1. Quando o Data Box Disk estiver conectado à rede de datacenter do Azure, o carregamento de dados do Azure é iniciado automaticamente.
2. O serviço do Azure Data Box notifica que a cópia de dados foi concluída por meio do portal do Azure.
    
    1. Verifique se há logs de erros para quaisquer falhas e tome as medidas apropriadas.
    2. Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte.

## <a name="clean-up-resources"></a>Limpar recursos

Esta etapa leva de 2 a 3 minutos para ser concluída.

Para limpar, você pode cancelar o pedido do Data Box e, em seguida, excluí-lo.

- Você pode cancelar o pedido do Data Box no portal do Azure antes de o pedido ser processado. Depois que o pedido tiver sido processado, ele não pode ser cancelado. O pedido progride até atingir o estágio concluído.

    Para cancelar o pedido, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.  

- Você pode excluir o pedido depois que o status é mostrado como **Completo** ou **Cancelado** no portal do Azure.

    Para excluir o pedido, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou o Azure Data Box Disk para ajudar a importar seus dados no Azure. Para saber mais sobre o gerenciamento de Azure Data Box Disk, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Data Box Disk](data-box-portal-ui-admin.md)

::: zone-end
