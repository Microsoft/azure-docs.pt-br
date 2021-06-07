---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – pré-requisitos para o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622287"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste tutorial, primeiro conclua os pré-requisitos a seguir. 

Se você não tiver uma assinatura do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

### <a name="get-required-resources"></a>Obter os recursos necessários

Para concluir este tutorial, **instale o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versão 16.5 (ou posterior)** em seu computador de desenvolvimento. Se já tiver uma versão mais antiga instalada, você poderá abrir o aplicativo do *Instalador do Visual Studio* no seu computador e seguir os prompts para atualizar a instalação.

>[!NOTE]
> Verifique se a instalação do Visual Studio 2019 inclui a **[carga de trabalho de desenvolvimento do Azure](/dotnet/azure/configure-visual-studio)** . Essa carga de trabalho permite que um aplicativo publique funções do Azure e execute outras tarefas de desenvolvimento do Azure.

O tutorial é orientado por um projeto de exemplo escrito em C#. O exemplo está localizado aqui: [Exemplos de ponta a ponta dos Gêmeos Digitais do Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Obtenha um projeto de exemplo** em seu computador acessando o link de exemplo e clicando no botão *Procurar códigos* abaixo do título. Isso levará você a um repositório GitHub para obter exemplos que poderão ser baixados como um arquivo *.zip* clicando no botão *Código*, depois em *Baixar arquivo .zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Uma exibição do repositório de gêmeos digitais de exemplo no GitHub. Após clicar no botão Código, uma pequena caixa de diálogo será exibida, na qual o botão Baixar arquivo .zip estará realçado." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Isso baixará uma pasta *.zip* chamada **digital-twins-samples-master.zip** em seu computador. Descompacte a pasta e extraia os arquivos.

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparar uma instância dos Gêmeos Digitais do Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
