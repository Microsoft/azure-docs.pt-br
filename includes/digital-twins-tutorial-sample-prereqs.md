---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – pré-requisitos para o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660514"
---
## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

Além disso, antes de começar, **instale o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versão 16.5 (ou posterior)** em seu computador de desenvolvimento. Se já tiver uma versão mais antiga instalada, você poderá abrir o aplicativo do *Instalador do Visual Studio* no seu computador e seguir os prompts para atualizar a instalação.

O tutorial é orientado por um projeto de exemplo escrito em C#. O exemplo está localizado aqui: [Exemplos de ponta a ponta dos Gêmeos Digitais do Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Obtenha um projeto de exemplo** em seu computador acessando o link de exemplo e clicando no botão *Procurar códigos* abaixo do título. Isso levará você a um repositório GitHub para obter exemplos que poderão ser baixados como um arquivo *.zip* clicando no botão *Código*, depois em *Baixar arquivo .zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Uma exibição do repositório de gêmeos digitais de exemplo no GitHub. Após clicar no botão Código, uma pequena caixa de diálogo será exibida, na qual o botão Baixar arquivo .zip estará realçado." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Isso baixará uma pasta *.zip* chamada **digital-twins-samples-master.zip** em seu computador. Descompacte a pasta e extraia os arquivos.

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparar uma instância dos Gêmeos Digitais do Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
