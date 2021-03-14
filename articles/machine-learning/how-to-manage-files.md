---
title: Criar e gerenciar arquivos em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como criar e gerenciar arquivos em seu espaço de trabalho no Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101159"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Como criar e gerenciar arquivos em seu espaço de trabalho

Saiba como criar e gerenciar os arquivos em seu espaço de trabalho do Azure Machine Learning.  Esses arquivos são armazenados no armazenamento de espaço de trabalho padrão. Arquivos e pastas podem ser compartilhados com qualquer outra pessoa com acesso de leitura ao espaço de trabalho e podem ser usados de qualquer instância de computação no espaço de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Criar arquivos

Para criar um novo arquivo na pasta padrão ( `Users > yourname` ):

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**.
1. Selecione a **+** imagem.
1. Selecione  **criar nova** imagem de arquivo.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar arquivo":::

1. Dê um nome ao arquivo.
1. Selecione um tipo de arquivo.
1. Selecione **Criar**.

Os notebooks e a maioria dos tipos de arquivo de texto são exibidos na seção de versão prévia.  A maioria dos outros tipos de arquivo não tem uma visualização.

Para criar um novo arquivo em uma pasta diferente:
1. Selecione o "..." no final da pasta
1. Selecione **Criar arquivo**.

> [!IMPORTANT]
> O conteúdo em blocos de anotações e scripts pode potencialmente ler dados de suas sessões e acessar dados sem sua organização no Azure.  Carregar somente arquivos de fontes confiáveis. Para obter mais informações, consulte [proteger práticas recomendadas de código](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Gerenciar arquivos com o Git

[Use um terminal de instância de computação](how-to-access-terminal.md#git) para clonar e gerenciar repositórios git.

## <a name="clone-samples"></a>Clonar amostras

Seu espaço de trabalho contém uma pasta de **blocos de anotações de exemplo** com blocos de anotações criados para ajudá-lo a explorar o SDK e servir como exemplos para seus próprios projetos de Machine Learning.   solitário esses blocos de anotações em sua própria pasta para executá-los e editá-los.  

Para um exemplo, veja [Tutorial: Criar seu primeiro experimento de ML](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Compartilhar arquivos

Copie e cole a URL para compartilhar um arquivo.  Somente outros usuários do espaço de trabalho podem acessar essa URL.  Saiba mais sobre [conceder acesso ao seu workspace](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Excluir um arquivo

Você *não pode* excluir os arquivos de **blocos de anotações de exemplo** .  Esses arquivos fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

Você *pode* excluir arquivos encontrados na seção **arquivos** de qualquer uma das seguintes maneiras:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Use um navegador compatível (Microsoft Edge, Chrome ou Firefox).
* [Use um terminal](how-to-access-terminal.md) de qualquer instância de computação em seu espaço de trabalho. A pasta **~/CloudFiles** é mapeada para armazenamento na sua conta de armazenamento de espaço de trabalho.
* No Jupyter ou no JupyterLab com suas ferramentas.
