---
title: Usar recursos de renderização em lote
description: Como usar os recursos de renderização de Lote do Azure. Experimente usar o aplicativo Batch Explorer, seja diretamente ou invocado de um plug-in de aplicativo cliente.
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: a5bf4d0ed2173d048d968f60dea72a1a0c25ab05
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429778"
---
# <a name="using-azure-batch-rendering"></a>Usar a renderização de Lote do Azure

Há várias maneiras de usar a renderização de Lote do Azure:

* APIs:
  * Grave código usando qualquer uma das APIs de Lote.  Desenvolvedores podem integrar os recursos do Lote do Azure aos fluxos de trabalho ou aplicativos existentes, sejam baseados em nuvem ou locais.
* Ferramentas de linha de comando:
  * A [linha de comando do Azure](/cli/azure/) ou o [PowerShell](/powershell/azure/) pode ser usado para script de uso do Lote.
  * Em particular, o [suporte de modelo da CLI do Lote](./batch-cli-templates.md) torna muito mais fácil criar pools e enviar trabalhos.
* Interface do usuário do Batch Explorer:
  * O [Batch Explorer](https://github.com/Azure/BatchLabs) é uma ferramenta do cliente de plataforma cruzada que também permite que as contas do Batch sejam gerenciadas e monitoradas.
  * Para cada um dos aplicativos de renderização, são fornecidos vários modelos de trabalho e pool que podem ser usados para facilmente criar pools e enviar trabalhos.  Um conjunto de modelos é listado na interface do usuário do aplicativo, com os arquivos de modelo sendo acessados no GitHub.
  * Modelos personalizados podem ser criados a partir do zero ou os modelos fornecidos pelo GitHub podem ser copiados e modificados.
* Plug-ins de aplicativo cliente:
  * Estão disponíveis plug-ins que permitem que a renderização de Lote seja usada diretamente nos aplicativos de modelagem e design de cliente.  Os plug-ins invocam principalmente o aplicativo Batch Explorer com informações contextuais sobre o modelo 3D atual e incluem recursos para ajudar a gerenciar ativos.

A melhor forma de experimentar o Lote do Azure e a maneira mais simples para os usuários finais, que não são desenvolvedores e nem especialistas do Azure, é usar o aplicativo Batch Explorer diretamente ou invocando a partir de um plug-in de aplicativo cliente.

## <a name="using-batch-explorer"></a>Usar o Batch Explorer

Para um tutorial passo a passo para usar o Batch Explorer para executar a renderização, consulte o [tutorial do Blender](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>Baixar e instalar

Os [downloads do Batch Explorer estão disponíveis](https://azure.github.io/BatchExplorer/) para Windows, OSX e Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Usar modelos para criar pools e executar trabalhos

Um conjunto abrangente de modelos está disponível para uso com o Batch Explorer, que facilita a criação de pools e o envio de trabalhos para os vários aplicativos de renderização, sem necessidade de especificar todas as propriedades necessárias para criar pools, trabalhos e tarefas diretamente com o Batch.  Os modelos disponíveis no Batch Explorer são armazenados e visíveis em [um repositório GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galeria do Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

São fornecidos modelos que atendem a todos os aplicativos presentes no Marketplace nas imagens de VM de renderização do Marketplace.  Para cada aplicativo há vários modelos, incluindo modelos de pool para atender a pools de CPU e GPU, pools do Windows e do Linux, e os modelos de trabalho incluem renderização de Blender em bloco ou quadro completo e renderização distribuída V-Ray. O conjunto de modelos fornecidos será expandido ao longo do tempo para atender a outros recursos do Lote como o dimensionamento automático do pool.

Também é possível que modelos personalizados sejam produzidos, a partir do zero ou modificando os modelos fornecidos. Os modelos personalizados podem ser usados, selecionando o item "Modelos locais" na seção "Galeria" do Batch Explorer.

### <a name="file-system-and-data-movement"></a>Sistema de arquivos e movimentação de dados

A seção "Dados" no Batch Explorer permite que os arquivos sejam copiados entre um sistema de arquivos local e as contas do Armazenamento do Microsoft Azure.

## <a name="next-steps"></a>Próximas etapas

Para exemplos de renderização de Lote, experimente os dois tutoriais:

* [Renderizar usando a CLI do Azure](./tutorial-rendering-cli.md)
* [Renderizar usando o Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)
