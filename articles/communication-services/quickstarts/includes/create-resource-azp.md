---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: b602cbfde22cc87b42a32b007c19b626814d1660
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554486"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Criar o recurso de comunicação do Azure

Para criar um recurso dos Serviços de Comunicação do Azure, primeiro, entre no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da página, selecione **+ Criar um recurso**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Captura de tela com realce no botão criar um recurso no portal do Azure.":::

Insira **Comunicação** na entrada **Pesquisar o Marketplace** ou na barra de pesquisa na parte superior do portal.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Captura de tela que mostra uma pesquisa dos serviços de comunicação na barra de pesquisa.":::

Selecione **Serviços de Comunicação** nos resultados e, em seguida, selecione **Criar**.

:::image type="content" source="../media/create-a-communication-resource/create-communication-portal.png" alt-text="Captura de tela mostrando o painel Serviços de Comunicação, destacando o botão Criar.":::

Agora você pode configurar o seu recurso dos Serviços de Comunicação. Na primeira página do processo de criação, você será solicitado a especificar:

* A assinatura
* O grupo de recursos (você pode criar um ou escolher um grupo de recursos existente)
* O nome do recurso dos Serviços de Comunicação
* A geografia à qual o recurso será associado

Na próxima etapa, você pode atribuir marcas ao recurso. As marcas podem ser usadas para organizar os seus recursos do Azure. Confira a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre marcas.

Por fim, você pode examinar a sua configuração e **Criar** o recurso. Observe que a implantação levará alguns minutos para ser concluída.

## <a name="manage-your-communication-services-resource"></a>Gerenciar o seu recurso dos Serviços de Comunicação

Para gerenciar o recurso dos Serviços de Comunicação, acesse o [portal do Azure](https://portal.azure.com) e pesquise e selecione os **Serviços de Comunicação do Azure**.

Na página **Serviços de Comunicação**, selecione o nome do seu recurso.

A página **Visão Geral** do recurso contém opções para gerenciamento básico, como procurar, parar, iniciar, reiniciar e excluir. Você pode encontrar mais opções de configuração no menu à esquerda da página de recursos.
