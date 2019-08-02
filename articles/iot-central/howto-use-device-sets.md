---
title: Usar conjuntos de dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como usar conjuntos de dispositivos no aplicativo Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503200"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Usar conjuntos de dispositivos no aplicativo Azure IoT Central

Este artigo descreve como, como operador, usar conjuntos de dispositivos no seu Microsoft IoT Central.

Um conjunto de dispositivos é uma lista de dispositivos que são agrupados porque eles correspondam a critérios especificados. Os conjuntos de dispositivos ajudam a gerenciar, visualizar e analisar dispositivos em grande escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um dispositivo definido para listar todos os dispositivos de ar-condicionado em Seattle para habilitar um técnico encontrar os dispositivos para os quais eles são responsáveis. Este artigo mostra como criar e configurar conjuntos de dispositivos.

## <a name="create-a-device-set"></a>Criar um conjunto de dispositivos

Para criar um conjunto de dispositivos:

1. Escolha **Conjuntos de Dispositivos** no menu de navegação esquerdo.

1. Selecione **+ novo**.

    ![Novo conjunto de dispositivos](media/howto-use-device-sets/image1.png)

1. Forneça ao dispositivo um nome que seja exclusivo em todo o aplicativo. Também é possível adicionar uma descrição. Um conjunto de dispositivos somente pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo a ser usado para esse conjunto.

1. Crie a consulta para identificar os dispositivos para o conjunto de dispositivos, selecionando uma propriedade, um operador de comparação e um valor. É possível adicionar várias consultas e dispositivos que atendam a **todos** os critérios colocados no conjunto de dispositivos. O conjunto de dispositivos criado por você é acessível a qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa visualizar, modificar ou excluir o conjunto de dispositivos.

    ![Consulta do conjunto de dispositivos](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > O conjunto de dispositivos é uma consulta dinâmica. Cada vez que você visualizar a lista de dispositivos, poderá haver diferentes dispositivos na lista. A lista depende de quais dispositivos atualmente atendem aos critérios da consulta.

1. Escolha **Salvar**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurar o painel para seu conjunto de dispositivo

Após criar o conjunto de dispositivos, você poderá configurar o **Painel**. O **Dashboard** é a home page de onde você coloca imagens e links. Além disso, é possível adicionar grades que listam os dispositivos no conjunto de dispositivos.

1. Escolha **Conjuntos de Dispositivos** no menu de navegação esquerdo.

1. Escolher o conjunto do dispositivo.

1. Selecione a guia **Painel** .

1. Selecione **Editar**.

    ![Modo de design ativado](media/howto-use-device-sets/image3.png)

1. Para obter informações sobre como adicionar uma imagem, consulte [Preparar e carregar imagens no aplicativo Azure IoT Central](howto-prepare-images.md).

1. Adicione um bloco de link:
    1. Escolha**Link** no painel direito.
    1. Forneça um **Título** ao link.
    1. Escolha uma URL a ser aberto quando o link é selecionado.
    1. Forneça ao link uma descrição que exiba abaixo do **Título**.
    1. Escolha **Salvar**.

        ![Link salvo](media/howto-use-device-sets/image7.png)

    1. É possível mover e redimensionar o bloco de link no **Painel**.

1. Adicione uma grade. Uma grade é uma tabela de dispositivos no conjunto de dispositivos com as colunas escolhidas.
    1. Escolha **Grade** no painel direito.
    1. Forneça um **Título** à grade.
    1. Selecione as colunas a serem mostrados, escolhendo **Adicionar/remover**. No painel que é exibido, escolha a coluna que você deseja mostrar e escolha a seta direita para selecioná-la.
    1. Escolha **OK**.
    1. Escolha **Salvar**.

        ![Salvar grade](media/howto-use-device-sets/image9.png)

    1. Arraste e solte a grade para colocá-la no **Painel**.

        > [!NOTE]
        > É possível adicionar várias imagens, links e grades.
  
    1. Selecione **Concluído**.

Para saber mais sobre como usar blocos no Azure IoT Central, consulte [usar blocos de dashboard](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Configurar um mapa do local no painel de conjuntos do dispositivo

Você pode adicionar um mapa para visualizar o local dos dispositivos em seu conjunto de dispositivos.

Para adicionar que um mapa ao seu dispositivo define o painel, você deve ter configurado uma medida de local ou a propriedade de local em seu modelo do dispositivo. Para obter mais informações, consulte [criar uma medida de localização](howto-set-up-template.md) ou [criar uma propriedade local](howto-set-up-template.md).

1. Em seu dispositivo definido **Dashboard**, selecione **mapa** da biblioteca.
2. Adicionar um título e escolher a medida de local ou a propriedade que foi configurado anteriormente.
3. Selecione **salvar** e a peça de mapa exibe os último locais conhecidos dos dispositivos em seu conjunto de dispositivos.
4. Quando um operador exibe o painel de conjuntos de dispositivos, o operador vê todos os blocos que você configurou, incluindo o mapa de localização.

Você pode redimensionar o bloco de mapa no painel. Selecionar um pin no mapa exibe as informações do dispositivo, nome e local. Selecione o pop-up para ir para a página de propriedades do dispositivo.

## <a name="configure-the-list-for-your-device-set"></a>Configurar a lista para o conjunto de dispositivos

Após criar o conjunto de dispositivos, será possível configurar uma **Lista**. A **Lista** mostra todos os dispositivos no conjunto de dispositivos em uma tabela com as colunas escolhidas.

1. Escolha **Conjuntos de Dispositivos** no menu de navegação esquerdo.

1. Escolha a guia **Lista**.

1. Escolha **Opções de Coluna**.

    ![Opções de Coluna](media/howto-use-device-sets/image11.png)

1. Escolha as colunas a serem mostradas, selecionando a coluna que você deseja mostrar e escolhendo a seta direita para selecioná-la.

    ![Escolher coluna](media/howto-use-device-sets/image12.png)

1. Escolha **OK**.

## <a name="analytics"></a>Análise

A análise em conjuntos de dispositivos é igual à guia de análise principal no menu de navegação esquerdo. É possível saber mais sobre análise, consultando o artigo sobre [como criar análises](howto-use-device-sets.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar conjuntos de dispositivos no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)
