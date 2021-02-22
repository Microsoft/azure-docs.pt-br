---
title: Início Rápido – Criar um ponto de extremidade e um perfil de CDN do Azure
description: Este início rápido mostra como habilitar a CDN do Azure criando um novo ponto de extremidade de CDN e perfil de CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 45edf57ed41f8cf7be839edade5038a0787424a4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367275"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Início Rápido: Criar um perfil e um ponto de extremidade da CDN do Azure

Neste guia de início rápido, você habilita a CDN (Rede de Distribuição de Conteúdo) do Azure criando um perfil da CDN, que é uma coleção de um ou mais pontos de extremidade CDN. Depois de criar um perfil e um ponto de extremidade, você pode iniciar a distribuição de conteúdo para seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure denominada *cdnstorageacct123*, que você usa para o nome de host de origem. Para concluir esse requisito, confira [Integrar uma conta de Armazenamento do Azure à CDN do Azure](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto de extremidade CDN

Depois de criar um perfil CDN, você o usa para criar um ponto de extremidade.

1. No portal do Azure, selecione o perfil CDN que você criou em seu painel. Se você não conseguir encontrá-lo, poderá abrir o grupo de recursos no qual você o criou ou usar a barra de pesquisa na parte superior do portal, inserir o nome do perfil e selecionar o perfil nos resultados.
   
1. Na página do perfil da CDN, selecione **+ Ponto de Extremidade**.
   
    ![Perfil CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    O painel **Adicionar um ponto de extremidade** é exibido.

3. Insira os seguintes valores de configuração:

    | Configuração | Valor |
    | ------- | ----- |
    | **Nome** | Digite *cdn-endpoint-123* para seu nome de host do ponto de extremidade. Esse nome deve ser globalmente exclusivo em todo o Azure. Caso ele já esteja em uso, insira um nome diferente. Esse nome será usado para acessar os recursos armazenados em cache no domínio _&lt;nome do ponto de extremidade&gt;_ .azureedge.net.|
    | **Tipo de origem** | Selecione **Armazenamento**. | 
    | **Nome do host de origem** | Selecione o nome de host da conta de armazenamento do Azure que você está usando na lista suspensa, como *cdnstorageacct123.blob.core.windows.net*. |
    | **Caminho de origem** | Deixe em branco. |
    | **Cabeçalho de host de origem** | Deixe o valor padrão (que é o nome do host para a conta de armazenamento). |  
    | **Protocolo** | Deixe as opções padrão **HTTP** e **HTTPS** selecionadas. |
    | **Porta de origem** | Deixe os valores de porta padrão. | 
    | **Otimizado para** | Deixe a seleção padrão: **Distribuição na Web geral**. |

    ![Painel Adicionar ponto de extremidade](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Selecione **Adicionar** para criar um novo ponto de extremidade. Depois que o ponto de extremidade é criado, ele aparece na lista de pontos de extremidade do perfil.
    
   ![Ponto de extremidade CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   O tempo necessário para que o ponto de extremidade se propague depende do tipo de preço selecionado quando você criou o perfil. O **Standard Akamai** geralmente é concluído em um minuto; o **Standard Microsoft**, em 10 minutos; e o **Standard Verizon** e o **Premium Verizon**, em até 30 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou um perfil CDN e um ponto de extremidade em um grupo de recursos. Salve esses recursos se você deseja ir para as [Próximas etapas](#next-steps) e saiba como adicionar um domínio personalizado em seu ponto de extremidade. No entanto, se você não espera usar esses recursos no futuro, poderá excluí-los ao excluir o grupo de recursos, evitando assim encargos adicionais:

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione **CDNQuickstart-rg**.

2. Na página **Grupo de recursos**, selecione **Excluir grupo de recursos**, digite *CDNQuickstart-rg* na caixa de texto e, em seguida, selecione **Excluir**. Essa ação exclui o grupo de recursos, o perfil e o ponto de extremidade que você criou neste início rápido.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: usar a CDN para o conteúdo estático do servidor de um aplicativo Web](cdn-add-to-web-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md)
