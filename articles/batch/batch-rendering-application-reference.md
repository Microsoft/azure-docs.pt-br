---
title: Usar aplicativos de renderização - Lote do Microsoft Azure
description: Como usar aplicativos de renderização com o Lote do Microsoft Azure
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775382"
---
# <a name="rendering-applications"></a>Aplicativos de renderização

Os aplicativos de renderização são usados ao criar os trabalhos e tarefas em lote. A propriedade da linha de comando da tarefa especifica os parâmetros e a linha de comando apropriada.  A maneira mais fácil de criar as tarefas de trabalho é usar os modelos do Azure Batch Explorer conforme especificado [neste artigo](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Os modelos podem ser versões visualizadas e modificadas, se necessário.

Este artigo fornece uma breve descrição de como executar cada aplicativo de renderização.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderização com Autodesk 3ds Max

### <a name="renderer-support"></a>Suporte do renderizador

Além dos renderizadores incorporados no 3ds Max, os seguintes renderizadores estão disponíveis nas imagens VM de renderização e podem ser referenciados pelo arquivo de cena 3ds Max cena:

* Autodesk Arnold
* V-Ray do Chaos Group

### <a name="task-command-line"></a>Linha de comando

Invocar o `3dsmaxcmdio.exe` aplicativo para executar a renderização de linha de comando em um nó de pool.  Este aplicativo está no caminho de quando a tarefa é executada. O `3dsmaxcmdio.exe` aplicativo tem os mesmos parâmetros disponíveis que o `3dsmaxcmd.exe` aplicativo, que está documentado na [3ds Max ajuda documentação](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (renderização | Seção de renderização de linha de comando).

Por exemplo: 

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Observações:

* Tome muito cuidado para garantir que os arquivos de ativo são encontrados.  Verifique se os caminhos estão corretos e relativos usando a janela **Acompanhamento de Ativos**, ou use o `-bitmapPath` parâmetro na linha de comando.
* Verificar se há problemas com a renderização, como incapacidade de encontrar os ativos, verificando o `stdout.txt` arquivo escrito por 3ds Max quando a tarefa é executada.

### <a name="batch-explorer-templates"></a>Modelos do Azure Batch Explorer

Modelos de pool e de trabalho podem ser acessados da **galeria** no Azure Batch Explorer.  Os arquivos de origem de modelo estão disponíveis no [Repositório de dados do Azure Batch Explorer no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Renderização com Autodesk Maya

### <a name="renderer-support"></a>Suporte do renderizador

Além dos renderizadores incorporados no Maya, os seguintes renderizadores estão disponíveis nas imagens VM de renderização e podem ser referenciados pelo arquivo de cena 3ds Max cena:

* Autodesk Arnold
* V-Ray do Chaos Group

### <a name="task-command-line"></a>Linha de comando

O `renderer.exe` renderizador de linha de comando é usado na linha de comando de tarefa. O renderizador de linha de comando é documentado em [Maya ajuda](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

No exemplo a seguir, uma tarefa de preparação de trabalho é usada para copiar os arquivos de cena e ativos para o diretório de trabalho de preparação de trabalho, uma pasta de saída é usada para armazenar a imagem de renderização e 10 do quadro é renderizado.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização de V-Ray, o arquivo de cena Maya normalmente especificaria V-Ray como o renderizador.  Ele também pode ser especificado na linha de comando:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Para a renderização de Arnold, o arquivo de cena Maya normalmente especificaria Arnold como o renderizador.  Ele também pode ser especificado na linha de comando:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Modelos do Azure Batch Explorer

Modelos de pool e de trabalho podem ser acessados da **galeria** no Azure Batch Explorer.  Os arquivos de origem de modelo estão disponíveis no [Repositório de dados do Azure Batch Explorer no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Próximas etapas

Use os modelos de pool e o trabalho a partir de [repositório de dados no GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) usando o Azure Batch Explorer.  Quando for necessário, crie novos modelos ou modifique um dos modelos fornecidos.