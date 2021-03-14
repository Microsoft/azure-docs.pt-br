---
title: Habilitar o acesso privado com o link privado (visualização)-Portal
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o acesso privado para soluções de gêmeos digitais do Azure com o link privado, usando o portal do Azure.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417008"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Habilitar o acesso privado com o link privado (visualização): portal do Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Este artigo descreve as diferentes maneiras de [habilitar o link privado com um ponto de extremidade privado para uma instância do gêmeos digital do Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (atualmente em visualização). Configurar um ponto de extremidade privado para sua instância do gêmeos digital do Azure permite proteger sua instância do gêmeos digital do Azure e eliminar a exposição pública, bem como evitar dados vazamento de sua [rede virtual do Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Este artigo percorre o processo usando o [**portal do Azure**](https://portal.azure.com).

Aqui estão as etapas abordadas neste artigo: 
1. Ative o link privado e configure um ponto de extremidade privado para uma instância de gêmeos digital do Azure.
1. Desabilitar ou habilitar sinalizadores de acesso à rede pública para restringir o acesso à API somente para conexões de vínculo privado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um ponto de extremidade privado, você precisará de uma [**rede virtual do Azure (VNet)**](../virtual-network/virtual-networks-overview.md) em que o ponto de extremidade pode ser implantado. Se você ainda não tiver uma VNet, poderá seguir um dos [guias de início rápido](../virtual-network/quick-create-portal.md) da rede virtual do Azure para configurá-la.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Adicionar um ponto de extremidade privado para uma instância do gêmeos digital do Azure 

Ao usar o [portal do Azure](https://portal.azure.com), você pode optar por ativar o link privado com um ponto de extremidade privado para uma instância do gêmeos digital do Azure como parte da configuração inicial da instância ou habilitá-la posteriormente em uma instância que já existe. 

Qualquer um desses métodos de criação fornecerá as mesmas opções de configuração e o mesmo resultado final para sua instância. Esta seção descreve como fazer cada uma delas.

>[!TIP]
> Você também pode configurar um ponto de extremidade de link privado por meio do serviço de link privado, em vez de por meio de sua instância de gêmeos digital do Azure. Isso também fornece as mesmas opções de configuração e o mesmo resultado final.
>
> Para obter mais detalhes sobre como configurar recursos de link privado, consulte a documentação de link privado para o [portal do Azure](../private-link/create-private-endpoint-portal.md), [CLI do Azure](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Adicionar um ponto de extremidade privado durante a criação da instância

Nesta seção, você habilitará o link privado com um ponto de extremidade privado em uma instância do gêmeos digital do Azure que está sendo criada no momento. Esta seção se concentra na etapa de rede do processo de criação; para obter uma explicação completa da criação de uma nova instância de gêmeos digital do Azure, consulte [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md).

As opções de vínculo privado estão localizadas na guia **rede** da configuração da instância.

Nessa guia, você pode habilitar os pontos de extremidade privados selecionando a opção de **Endpoint particular** para o **método de conectividade**.

Isso adicionará uma seção chamada **conexões de ponto de extremidade privado** , em que você pode configurar os detalhes do seu ponto de extremidade privado. Selecione o botão **+ Adicionar** para continuar.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Captura de tela da portal do Azure mostrando a guia rede da caixa de diálogo Criar recurso para o gêmeos digital do Azure. Há um realce em volta do nome da guia, a opção de ponto de extremidade particular para o método de conectividade e o botão + Adicionar para criar uma nova conexão de ponto de extremidade privado." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Isso abrirá uma página para inserir os detalhes de um novo ponto de extremidade privado.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Captura de tela da portal do Azure mostrando a página criar ponto de extremidade privado. Ele contém os campos descritos abaixo.":::

1. Preencha as seleções para sua **assinatura** e **grupo de recursos**. Defina o **local** para o mesmo local que a VNet que você usará. Escolha um **nome** para o ponto de extremidade e para **sub-recursos de destino** selecione *API*.

1. Em seguida, selecione a **rede virtual** e a **sub-rede** que você gostaria de usar para implantar o ponto de extremidade.

1. Por fim, selecione se deseja **integrar com a zona DNS privada**. Você pode usar o padrão **Sim** ou, para obter ajuda com essa opção, você pode seguir o link no portal para [saber mais sobre a integração do DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).

Depois de preencher as opções de configuração, clique em **OK** para concluir.

Isso retornará à guia **rede** da configuração da instância do gêmeos digital do Azure, em que o novo ponto de extremidade deve estar visível em * * conexões de ponto de extremidade privadas.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Captura de tela da portal do Azure mostrando a guia rede da caixa de diálogo Criar recurso para o gêmeos digital do Azure. Há um realce em volta da nova conexão de ponto de extremidade privado e os botões de navegação (revisão + criar, anterior, próximo: avançado)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Você pode usar os botões de navegação inferiores para continuar com o restante da configuração da instância.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adicionar um ponto de extremidade privado a uma instância existente

Nesta seção, você habilitará o link privado com um ponto de extremidade privado para uma instância do gêmeos digital do Azure que já existe.

1. Primeiro, navegue até o [portal do Azure](https://portal.azure.com) em um navegador. Traga sua instância de gêmeos digital do Azure pesquisando seu nome na barra de pesquisa do Portal.

1. Selecione **rede (visualização)** no menu à esquerda.

1. Alterne para a guia **conexões de ponto de extremidade privado** .

1. Selecione **+ ponto de extremidade privado** para abrir a configuração **criar um ponto de extremidade privado** .

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Captura de tela da portal do Azure mostrando a página rede (visualização) para uma instância do gêmeos digital do Azure. A guia conexões de ponto de extremidade privado é realçada e o botão de ponto de extremidade + privado também é realçado." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Na guia  **noções básicas**   , insira ou selecione a **assinatura** e o **grupo de recursos** do seu projeto e um **nome** e uma **região** para o ponto de extremidade. A região precisa ser a mesma que a região para a VNet que você está usando.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Captura de tela do portal do Azure mostrando a primeira guia (noções básicas) da caixa de diálogo criar um ponto de extremidade privado. Ele contém os campos descritos acima.":::

    Quando tiver terminado, selecione o botão **Avançar: recurso >** para ir para a próxima guia.

1. Na guia **recurso** , insira ou selecione estas informações: 
    * **Método de conexão**: selecione **conectar a um recurso do Azure em meu diretório** para pesquisar sua instância de gêmeos digital do Azure.
    * **Assinatura**: Insira sua assinatura.
    * **Tipo de recurso**: selecione **Microsoft. DigitalTwins/digitalTwinsInstances**
    * **Recurso**: selecione o nome da instância de gêmeos digital do Azure.
    * **Sub-recurso de destino**: selecione a **API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Captura de tela da portal do Azure mostrando a segunda guia (recurso) da caixa de diálogo criar um ponto de extremidade privado. Ele contém os campos descritos acima.":::

    Quando tiver terminado, selecione o botão **Avançar: configuração >** para ir para a próxima guia.    

1. Na guia **configuração** , insira ou selecione estas informações:
    * **Rede virtual**: selecione sua rede virtual.
    * **Sub-rede**: escolha uma sub-rede da sua rede virtual.
    * **Integrar com a zona DNS privada**: selecione se deseja **integrar com a zona DNS privada**. Você pode usar o padrão **Sim** ou, para obter ajuda com essa opção, você pode seguir o link no portal para [saber mais sobre a integração do DNS privado](../private-link/private-endpoint-overview.md#dns-configuration).
    Se você selecionar **Sim**, poderá deixar as informações de configuração padrão.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Captura de tela do portal do Azure mostrando a terceira guia (configuração) da caixa de diálogo criar um ponto de extremidade privado. Ele contém os campos descritos acima.":::

    Quando tiver terminado, você poderá selecionar o botão **revisar + criar** para concluir a instalação. 

1. Na guia **revisar + criar** , examine suas seleções e selecione o botão  **criar** . 

Quando o ponto de extremidade terminar de implantar, ele deverá aparecer nas conexões de ponto de extremidade privado para sua instância de gêmeos digital do Azure.

>[!TIP]
> O ponto de extremidade também pode ser exibido no centro de links privado no portal do Azure.

## <a name="disable--enable-public-network-access-flags"></a>Desabilitar/habilitar sinalizadores de acesso à rede pública

Você pode configurar sua instância de gêmeos digital do Azure para negar todas as conexões públicas e permitir somente conexões por meio de pontos de extremidade privados para aprimorar a segurança de rede. Essa ação é feita com um **sinalizador de acesso à rede pública**. 

Essa política permite restringir o acesso à API somente para conexões de link privado. Quando o sinalizador de acesso à rede pública é definido como *desabilitado*, todas as chamadas à API REST para o plano de dados da instância do gêmeos digital do Azure da nuvem pública retornarão `403, Unauthorized` . Como alternativa, quando a política está definida como *desabilitada* e uma solicitação é feita por meio de um ponto de extremidade privado, a chamada à API terá sucesso.

Este artigo mostra como atualizar o valor do sinalizador de rede usando o [portal do Azure](https://portal.azure.com). Para obter instruções sobre como fazer isso com a ferramenta de comando CLI do Azure ou ARMClient, consulte a [versão da CLI](how-to-enable-private-link-cli.md) deste artigo.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Para desabilitar ou habilitar o acesso à rede pública no [portal do Azure](https://portal.azure.com), abra o portal e navegue até sua instância de gêmeos digital do Azure.

1. Selecione **rede (visualização)** no menu à esquerda.

1. Na guia **acesso público** , defina **permitir acesso à rede pública para** **desabilitado** ou **todas as redes**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Captura de tela da portal do Azure mostrando a página de rede (visualização) para uma instância do gêmeos digital do Azure. A guia acesso público é realçada e a opção para escolher se deseja permitir o acesso à rede pública também é realçada." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o link privado para o Azure: 
* [*O que é o serviço de vínculo privado do Azure?*](../private-link/private-link-service-overview.md)