---
title: Tutorial – Renderizar uma cena do Blender usando o Lote do Azure e o Batch Explorer
description: Tutorial – Como processar vários quadros de uma cena do Blender usando o Lote do Azure e o aplicativo cliente Batch Explorer
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490833"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Tutorial: Renderizar uma cena do Blender usando o Batch Explorer

Este tutorial mostra como renderizar vários quadros de uma cena de demonstração do Blender. O Blender é usado no tutorial por estar disponível gratuitamente para o cliente e para as VMs de renderização, mas o processo é bem semelhante ao de outros aplicativos que poderiam ser usados, como o Maya ou o 3ds Max.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Carregar uma cena do Blender no armazenamento do Azure
> * Criar um pool do Lote com vários nós para executar a renderização
> * Renderizar vários quadros
> * Exibir e baixar os arquivos de quadro renderizados

## <a name="prerequisites"></a>Prerequisites

Você precisa de uma assinatura pré-paga ou de outra opção de compra do Azure para usar aplicativos de renderização no Lote e pagar por uso. O licenciamento de pagamento por uso não terá suporte se você usar uma oferta gratuita do Azure que forneça crédito monetário.

Você precisa de uma conta do Lote do Azure com uma conta de armazenamento associada.  Consulte um dos artigos de Início Rápido do Lote, como o [artigo sobre a CLI](./quick-create-cli.md), para criar uma conta do Lote.

Uma cota de núcleos de baixa prioridade de pelo menos 50 núcleos é necessária para o tamanho da VM e o número de VMs especificados neste tutorial. A cota padrão pode ser usada, mas um tamanho menor de VM terá que ser usado, ou seja, as imagens levarão mais tempo para renderizar. O processo para solicitar uma cota de núcleo maior está detalhado [neste artigo](./batch-quota-limit.md).

Por fim, o [Batch Explorer](https://azure.github.io/BatchExplorer/) deve ser instalado; ele está disponível para Windows, OSX e Linux. Ele é opcional, mas se o [Blender](https://www.blender.org/download/) estiver instalado, o arquivo de modelo de exemplo poderá ser exibido.

## <a name="download-the-demo-scene"></a>Baixar a cena de demonstração

Baixe o arquivo ZIP de demonstração "Sala de aula" do Blender na [página da Web Arquivos de Demonstração do Blender](https://www.blender.org/download/demo-files/) e descompacte-o em uma unidade local.

## <a name="upload-a-scene-to-azure-storage"></a>Carregar uma cena no armazenamento do Azure

Crie um contêiner de conta de armazenamento para os arquivos de cena de demonstração:

* Iniciar o Batch Explorer
* Selecione o item de menu 'Dados' no menu principal à esquerda.
* Selecione ‘grupos de arquivos’ no menu suspenso.
* Selecione o botão '+' e crie um novo 'grupo de arquivos vazio' chamado 'blender-classroom'
  * Um grupo de arquivos é basicamente um contêiner de blobs do Armazenamento do Azure com um prefixo 'fgrp-'; isso é uma convenção usada para filtrar outros contêineres na conta de armazenamento

![Grupo de arquivos para arquivos de cena](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Carregue os arquivos de cena:

* Selecione o novo contêiner e arraste e solte o conteúdo da pasta 'sala de aula' para o contêiner no Batch Explorer.

![Arquivos de cena carregados](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Criar contêiner de armazenamento do Azure para imagens de saída

Crie um contêiner de conta de armazenamento para os arquivos de cena de demonstração de saída:

* Selecione o item de menu 'Dados' no menu principal à esquerda.
* Selecione o botão '+' e crie um novo 'grupo de arquivos vazio' chamado 'render-output'

![Grupo de arquivos para arquivos de saída](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Criar um pool de VMs para renderização

Criar um pool do Lote usando a imagem de VM do Azure Marketplace de renderização que contém o aplicativo Blender:

* Selecione o item de menu 'Galeria' no menu principal à esquerda.
* Selecione o item 'Blender' para obter a lista de itens do aplicativo.
* Selecione os itens para renderizar quadros no Windows Server
* O ícone de link no lado direito do item pode, opcionalmente, ser selecionado para exibir os arquivos de modelo que serão usados para criar um pool e um trabalho.

![Itens da galeria do Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Selecione o botão 'Criar pool para uso posterior'
  * Deixe o nome do Pool como "blender-windows"
  * Defina a 'contagem de VMs dedicadas' como "0"
  * Defina a 'contagem de VMs de baixa prioridade' como "3"
  * Defina o 'tamanho do nó' como "Standard_F16"; outro tamanho de VM pode ser selecionado, mas o tempo para renderizar um quadro dependerá principalmente do número de núcleos.
* Selecione o botão verde para criar o pool
  * O pool será criado quase imediatamente, mas levará alguns minutos para que as VMs sejam alocadas e iniciadas.

![Modelo de pool para o Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Observe que, quando há VMs em um pool, o custo dessas VMs é cobrado em sua assinatura do Azure. O pool ou as VMs precisam ser excluídos para parar as cobranças. Exclua o pool no final deste tutorial para evitar a continuação dos encargos.

O status do pool e das VMs pode ser monitorado no modo de exibição 'Pools'. O exemplo a seguir mostra que as três VMs foram alocadas, duas foram iniciadas e estão ociosas e uma ainda está iniciando: ![mapa de calor do pool](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Criar um trabalho de renderização

Crie um trabalho de renderização para renderizar alguns quadros usando o pool que foi criado:

* Selecione o item de menu 'Galeria' no menu principal à esquerda.
* Selecione o item 'Blender' para obter a lista de itens do aplicativo.
* Selecione os itens para renderizar quadros no Windows Server.
* Selecione o botão 'Executar trabalho com o pool existente'
* Selecione o pool 'blender-windows'
* Defina o 'nome do trabalho' como 'blender-render-tutorial1'
* Selecione 'fgrp-blender-classroom' como 'Dados de entrada'
* Selecione o ícone do 'arquivo do Blend' e selecione 'classroom.blend'
* Deixe o 'Início de quadro' como '1' e defina 'Quadro final' como '5'
* Defina 'Saídas' como 'fgrp-render-output'
* Selecione o botão verde para criar o trabalho; um trabalho será criado com cinco tarefas, uma para cada quadro

![Modelo de trabalho para o Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Depois que o trabalho e todas as tarefas tiverem sido criados, o trabalho será exibido junto com as tarefas: ![lista de tarefas do trabalho](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Quando uma tarefa começa a ser executada pela primeira vez em uma VM no pool, uma tarefa de preparação de trabalho do Lote é executada para fazer a cópia dos arquivos de cena do grupo de arquivos do armazenamento na VM a fim de permitir o acesso pelo Blender.
O status de renderização pode ser determinado exibindo o arquivo de log stdout.txt produzido pelo Blender.  Selecione uma tarefa; por padrão, são exibidas as 'Saídas da tarefa' e o arquivo 'stdout.txt' pode ser selecionado e exibido.
![arquivo stdout](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Se o pool 'blender-windows' for selecionado, as VMs no pool serão vistas em estado de execução: ![mapa de calor do pool com nós em execução](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

As imagens renderizadas levarão vários minutos para serem produzidas, dependendo do tamanho de VM selecionado.  Usando a VM F16 especificada anteriormente, os quadros levaram aproximadamente 16 minutos para serem renderizados.

## <a name="view-the-rendering-output"></a>Exibir a saída de renderização

Quando os quadros forem renderizados, essas tarefas serão mostradas como concluídas: ![Conclusão de tarefas](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

A imagem renderizada é gravada na VM primeiro e pode ser exibida pela seleção da pasta 'wd': ![imagem renderizada no nó do pool](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

O modelo de trabalho também especifica que os arquivos de log e o quadro de saída têm write-back feito no grupo de arquivo da conta do Armazenamento do Azure especificado quando o trabalho foi criado.  A interface do usuário 'Dados' pode ser usada para exibir os arquivos de saída e os logs; ela pode ser usada também para baixar os arquivos: ![imagem renderizada no grupo de arquivos do armazenamento](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Quando todas as tarefas forem concluídas, o trabalho será marcado como concluído: ![trabalho e todas as tarefas concluídas](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

> [!WARNING]
> O pool deve ser excluído (ela também pode ser redimensionado para zero nós) para interromper os encargos das VMs gerados para a assinatura do Azure.

* Selecione 'Pools'
* Selecione o pool 'blender-windows'
* Clique com botão direito do mouse e clique em 'Excluir' ou selecione o ícone de Lixeira acima do pool

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Carregar uma cena do Blender no armazenamento do Azure
> * Criar um pool do Lote com vários nós para executar a renderização
> * Renderizar vários quadros
> * Exibir e baixar os arquivos de quadro renderizados

Continue explorando os aplicativos de renderização disponíveis por meio do Batch Explorer na seção **Galeria**. Para cada aplicativo, existem vários modelos disponíveis, e essa lista aumentará com o tempo. Por exemplo, para o Blender existem modelos que dividem uma única imagem em blocos, ou seja, partes de uma imagem podem ser processadas em paralelo.

Para saber mais sobre a renderização em escala de nuvem, consulte as opções do Serviço de Renderização do Lote.

> [!div class="nextstepaction"]
> [Serviço de Renderização em Lotes](batch-rendering-service.md)
