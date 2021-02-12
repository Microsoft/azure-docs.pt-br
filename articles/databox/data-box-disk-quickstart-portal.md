---
title: Início Rápido do Microsoft Azure Data Box Disk | Microsoft Docs
description: Use este guia de início rápido para implantar rapidamente o Azure Data Box Disk no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 23615daf4a07e02b01bbd5a9cdf57ec9a81a2b76
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347380"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Início Rápido: Implantar o Azure Data Box Disk usando o portal do Azure

Este início rápido descreve como implantar o Azure Data Box Disk usando o portal do Azure. As etapas incluem como, rapidamente, criar um pedido, receber discos, descompactar, conectar e copiar dados para discos de modo que eles sejam carregados no Azure.

Para obter a implantação detalhada passo a passo e instruções de acompanhamento, acesse [Tutorial: Solicitar o Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

Este guia descreve as etapas necessárias para usar o Azure Data Box Disk no portal do Azure. Este guia ajuda a responder às perguntas a seguir.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Verifique se sua assinatura é habilitada para o serviço Azure Data Box. Para ativar sua assinatura para esse serviço, [Inscreva-se no serviço](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Examinar os pré-requisitos** : verifique o número de discos e cabos, o sistema operacional e outros softwares.
> - **Conectar e desbloquear** : conecte o dispositivo e desbloqueie o disco para copiar os dados.
> - **Copiar dados para o disco e validá-los** : copie dados para os discos nas pastas pré-criadas.
> - **Devolver os discos** : devolva os discos para o datacenter do Azure em que os dados foram carregados em sua conta de armazenamento.
> - **Verificar os dados no Azure** : verifique se os dados foram carregados em sua conta de armazenamento antes de excluí-los do servidor de dados de origem.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Order

### <a name="portal"></a>[Portal](#tab/azure-portal)

Esta etapa leva aproximadamente 5 minutos.

1. Criar um novo recurso do Azure Data Box no portal do Azure. 
2. Selecione uma assinatura habilitada para esse serviço e escolha o tipo de transferência como **Importação**. Forneça o **País de origem** onde os dados residem e a **região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Disk**. A capacidade máxima da solução é de 35 TB, e você pode criar vários pedidos de disco para tamanhos maiores de dados.  
4. Insira os detalhes do pedido e as informações de envio. Se o serviço estiver disponível em sua região, forneça endereços de email de notificação, examine o resumo e, em seguida, crie o pedido.

Depois que o pedido é criado, os discos estão preparados para remessa.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use esses comandos da CLI do Azure para criar um trabalho do Data Box Disk.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou usar um grupo de recursos existente:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Use o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento ou usar uma existente:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Execute o comando [az databox job create](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_create) para criar um trabalho do Data Box com o SKU DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Execute o comando [az databox job update](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_update) para atualizar um trabalho, como neste exemplo, em que você altera o nome e o email de contato:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Execute o comando [az databox job show](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_show) para obter informações sobre o trabalho:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Use o comando [az databox job list]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list) para ver todos os trabalhos do Data Box para um grupo de recursos:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Execute o comando [az databox job cancel](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_cancel) para cancelar um trabalho:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Execute o comando [az databox job delete](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_delete) para excluir um trabalho:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Use o comando [az databox job list-credentials]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list_credentials) para listar as credenciais de um trabalho do Data Box:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Depois que o pedido for criado, o dispositivo estará preparado para remessa.

---

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

1. A unidade contém as pastas *PageBlob* , *BlockBlob* , *AzureFile* , *ManagedDisk* e *DataBoxDiskImport*. Arraste e solte para copiar os dados que precisam ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, arraste e solte os dados como VHD/VHDX para a pasta *PageBlob* e os dados apropriados para *AzureFile*. Copiar os VHDs que você deseja carregar como discos gerenciados para uma pasta sob *ManagedDisk*.

    Um contêiner é criado na conta de armazenamento do Azure para cada subpasta em *BlockBlob* e a pasta *PageBlob*. Um compartilhamento de arquivos é criado para uma subpasta sob *AzureFile*.

    Todos os arquivos nas pastas *BlockBlob* e *PageBlob* são copiados para um contêiner padrão `$root` sob a conta de Armazenamento do Azure. Copie os arquivos para uma pasta na pasta no *AzureFile*. Os arquivos copiados diretamente para a pasta *AzureFile* falham e são carregados como blobs de blocos.

    > [!NOTE]
    > - Todos os contêineres, blobs e arquivos devem estar em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se essas regras não forem seguidas, o carregamento de dados do Azure falhará.
    > - Verifique se os arquivos não excedem ~4,75 TiB para blobs de blocos, ~8 TiB para blobs de páginas e ~1 TiB para Arquivos do Azure.

2. **(Opcional mas recomendado)** Depois que a cópia for concluída, é altamente recomendável que pelo menos você execute o `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* e selecione a opção 1 para validar os arquivos. Também é recomendável permitir esse tempo, use a opção 2 para também gerar somas de verificação para validação (pode demorar dependendo do tamanho de dados). Essas etapas minimizam as chances de qualquer falha ao carregar os dados para o Azure.
3. Remova a unidade com segurança.

## <a name="ship-to-azure"></a>Enviar para o Azure

Essa etapa demora cerca de 5 a 7 minutos para ser concluída.

1. Coloque todos os discos em conjunto no pacote original. Use a etiqueta de remessa inclusa. Se a etiqueta for danificada ou perdida, baixe-a partir do portal. Vá para **Visão geral** e clique em **Baixar etiqueta de remessa** na barra de comandos.
2. Entregue o pacote lacrado no local de remessa.  

O serviço do Data Box Disk envia uma notificação por email e atualiza o status do trabalho no portal do Azure.

## <a name="verify-your-data"></a>Verificar seus dados

O tempo para concluir essa operação depende do tamanho dos dados.

1. Quando o Data Box Disk estiver conectado à rede de datacenter do Azure, o carregamento de dados do Azure é iniciado automaticamente.
2. O serviço do Azure Data Box notifica que a cópia de dados foi concluída por meio do portal do Azure.
    
    1. Verifique se há logs de erros para quaisquer falhas e tome as medidas apropriadas.
    2. Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte.

## <a name="clean-up-resources"></a>Limpar os recursos

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
