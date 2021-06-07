---
title: Tutorial – Personalizar o painel do operador no Azure IoT Central
description: Este tutorial mostra como personalizar o painel do operador em um aplicativo IoT Central e gerenciar dispositivos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831699"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial:  Personalizar o painel do operador e gerenciar dispositivos no Azure IoT Central


Neste tutorial, como construtor, você aprenderá a personalizar o painel do operador no aplicativo de análise na loja do Azure IoT Central. Os operadores do aplicativo podem usar o painel personalizado para executar o aplicativo e gerenciar os dispositivos anexados.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Alterar o nome do painel
> * Personalizar blocos de imagem no painel
> * Organizar blocos para modificar o layout
> * Adicionar blocos de telemetria para exibir condições
> * Adicionar blocos de propriedade para exibir detalhes do dispositivo
> * Adicionar blocos de comando para executar comandos

## <a name="prerequisites"></a>Pré-requisitos

O construtor deve concluir o tutorial para criar o aplicativo de análise na loja do Azure IoT Central e adicionar dispositivos:

* [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (Obrigatório)

## <a name="change-the-dashboard-name"></a>Alterar o nome do painel
Para personalizar o painel do operador, é necessário editar o painel padrão no aplicativo. Além disso, você pode criar painéis adicionais. A primeira etapa para personalizar o painel no aplicativo é por meio da alteração do nome.

1. Navegue até o site do [Gerenciador de aplicativos do Azure IoT Central](https://aka.ms/iotcentral).

1. Abra o aplicativo de monitoramento de condições criado no tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

1. Escolha **Editar** na barra de ferramentas do painel. No modo de edição, você pode personalizar a aparência, o layout e o conteúdo do painel.

    ![Painel de edição – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. O painel esquerdo pode ser ocultado. A ocultação do painel esquerdo proporciona uma área de trabalho maior para a edição do painel.

1. Insira um nome amigável para o painel em **Nome do painel**. Este tutorial usa uma empresa fictícia chamada Contoso e o nome do painel de exemplo é *Painel da Contoso*. 

1. Clique em **Salvar**. As alterações são salvas no painel e o modo de edição é desabilitado.

    ![Alterar nome do painel – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Personalizar blocos de imagem no painel
Um painel de aplicativo do Azure IoT Central é formado por um ou mais blocos. Um bloco é um contêiner retangular para exibição de conteúdo em um painel. Você associa vários tipos de conteúdo a blocos e arrasta, solta e redimensiona blocos para personalizar um layout de painel. Há vários tipos de blocos para exibir conteúdo. Os blocos de imagem contêm imagens e você pode adicionar uma URL para permitir que os usuários cliquem na imagem. Os blocos de rótulo exibem texto sem formatação. Os blocos de markdown contêm conteúdo formatado e permitem definir uma imagem, uma URL, um título e um código de markdown que é renderizado como HTML. Os blocos de telemetria, propriedade ou comando exibem dados específicos do dispositivo. 

Nesta seção, você aprenderá como personalizar blocos de imagem no painel.

Para personalizar o bloco de imagem que exibe uma imagem de marca no painel:

1. Escolha **Editar** na barra de ferramentas do painel. 

1. Escolha **Configurar** no bloco de imagem que exibe a imagem da marca Northwind. 

    ![Editar imagem da marca – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Altere o **Título**. O título aparece quando um usuário passa o cursor do mouse sobre a imagem.

1. Escolha **Imagem**. Uma caixa de diálogo é aberta e permite que você carregue uma imagem personalizada. 

1. Opcionalmente, especifique uma URL para a imagem.

1. Escolha **Atualizar configuração**. O botão **Atualizar configuração** salva as alterações do painel e deixa o modo de edição habilitado.

    ![Salvar imagem da marca – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Como opção, escolha **Configurar** no bloco intitulado **Documentação** e especifique uma URL para o conteúdo de suporte. 

Para personalizar o bloco de imagem que exibe um mapa das zonas abrangidas pelo sensor na loja:

1. Escolha **Configurar** no bloco de imagem que exibe o mapa de zona padrão da loja. 

1. Escolha **Imagem** e use a caixa de diálogo para carregar uma imagem personalizada de um mapa de zona da loja. 

1. Escolha **Atualizar configuração**.

    ![Salvar mapa da loja – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    O mapa da loja Contoso de exemplo mostra quatro zonas: duas zonas de finalização de compras, uma zona de vestuário e cuidados pessoais e uma zona de mercearia e rotisseria. Neste tutorial, você associará sensores a essas zonas para fornecer a telemetria.

    ![Zonas da loja – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Clique em **Salvar**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Organizar blocos para modificar o layout
A reorganização dos blocos para a criação de uma exibição útil é uma etapa fundamental da personalização de um painel. Os operadores de aplicativos usam o painel para visualizar a telemetria do dispositivo, gerenciar dispositivos e monitorar condições em uma loja. O Azure IoT Central simplifica a tarefa do construtor de aplicativos de criação de um painel. O modo de edição do painel permite adicionar, mover, redimensionar e excluir blocos rapidamente. O modelo de aplicativo **Análise na loja – finalizar compra** também simplifica a tarefa de criação de um painel. Ele fornece um layout de painel funcional, com sensores conectados e blocos que exibem as contagens das linhas de finalização de compras e as condições do ambiente.

Nesta seção, você reorganizará o painel no modelo de aplicativo **Análise na loja – finalizar compra** para criar um layout personalizado.

Para remover blocos que você não planeja usar no aplicativo:

1. Escolha **Editar** na barra de ferramentas do painel. 

1. Escolha **X Excluir** para remover os seguintes blocos: **De volta a todas as zonas**, **Visitar o painel da loja**, **Tempo de espera** e todos os três blocos associados a **Finalizar compra 3**. O painel da loja da Contoso não usa esses blocos. 

    ![Excluir blocos – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Role para exibir os blocos restantes do painel.

1. Escolha **X Excluir** para remover os seguintes blocos: **Zona de finalização de compras – Aquecimento**, **Zona de finalização de compras – Resfriamento**, **Configurações do sensor de ocupação**, **Configurações do sensor do termostato** e **Condições do ambiente**. 

   ![Excluir blocos restantes – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Clique em **Salvar**. A remoção de blocos não utilizados libera espaço na página de edição e simplifica a exibição de painel para os operadores.

1. Exiba suas alterações do painel.

   ![Após a exclusão de blocos – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Depois de remover blocos não utilizados, reorganize os blocos restantes para criar um layout organizado. O novo layout inclui espaço para blocos que você adicionará em uma etapa posterior.

Para reorganizar os blocos restantes:

1. Selecione **Editar**.

1. Escolha o bloco **Firmware de ocupação** e arraste-o para a direita do bloco de bateria **Ocupação**.

1. Escolha o bloco **Firmware do termostato** e arraste-o para a direita do bloco de bateria **Termostato**.

1. Clique em **Salvar**.

1. Exiba as alterações do layout. 

    ![Blocos de bateria do firmware – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Adicionar blocos de telemetria para exibir condições
Depois de personalizar o layout do painel, você estará pronto para adicionar blocos para mostrar a telemetria. Para criar um bloco de telemetria, escolha um modelo e uma instância do dispositivo e escolha a telemetria específica do dispositivo para exibir no bloco. O modelo de aplicativo **Análise na loja – finalizar compra** inclui vários blocos de telemetria no painel. Os quatro blocos nas duas zonas de finalização de comprar exibem a telemetria do sensor de ocupação simulada. O bloco **Tráfego de pessoas** mostra contagens nas duas zonas de finalização de compras. 

Nesta seção, você adicionará mais dois blocos de telemetria para mostrar a telemetria do ambiente dos sensores RuuviTag adicionados no tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md). 

Para adicionar blocos para exibir dados sobre o ambiente a partir dos sensores RuuviTag:

1. Selecione **Editar**.

1. Escolha `RuuviTag` na lista **Modelo do dispositivo**. 

1. Escolha uma **Instância do dispositivo** de um dos dois sensores RuuviTag. Na loja da Contoso de exemplo, escolha `Zone 1 Ruuvi` para criar um bloco de telemetria para a Zona 1. 

1. Escolha `Relative humidity` e `temperature` na lista **Telemetria**. Estes são os itens de telemetria exibidos para cada zona no bloco.

1. Escolha **Combinar**. 

    ![Adicionar bloco 1 do RuuviTag – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Um novo bloco é mostrado para exibir a telemetria combinada da umidade e temperatura do sensor escolhido. 

1. Escolha **Configurar** no novo bloco para o sensor RuuviTag. 

1. Altere o **Título** para *Ambiente da Zona 1*. 

1. Escolha **Atualizar configuração**.

1. Repita as etapas anteriores para criar um bloco para a segunda instância do sensor. Defina o **Título** como *Ambiente da Zona 2* e, em seguida, escolha **Atualizar configuração.**

1. Arraste o bloco intitulado **Ambiente da Zona 2** abaixo do bloco **Firmware do termostato**. 

1. Arraste o bloco intitulado **Ambiente da Zona 1** abaixo do bloco **Tráfego de pessoas**. 

1. Clique em **Salvar**. O painel exibe a telemetria da zona nos dois novos blocos.

    ![Todos os blocos do RuuviTag – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Para editar o bloco **Tráfego de pessoas** para exibir a telemetria para apenas duas zonas de finalização de compras:

1. Selecione **Editar**. 

1. Escolha **Configurar** no bloco **Tráfego de pessoas**.

1. Em **Telemetria**, escolha **contagem 1**, **contagem 2** e **contagem 3**. 

1. Escolha **Atualizar configuração**. Isso limpará a configuração do bloco. 

1. Escolha novamente **Configurar** no bloco **Tráfego de pessoas**.

1. Em **Telemetria**, escolha **contagem 1** e **contagem 2**. 

1. Escolha **Atualizar configuração**. 

1. Clique em **Salvar**.  O painel atualizado exibe contagens apenas para as duas zonas de finalização de compras, que se baseiam no sensor de ocupação simulada.

    ![Duas pistas de tráfego de pessoas – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Adicionar blocos de propriedade para exibir detalhes do dispositivo
Os operadores de aplicativos usam o painel gerenciar dispositivos e monitorar o status. Adicione um bloco para cada RuuviTag para que os operadores possam exibir a versão do software. 

Para adicionar um bloco de propriedade para cada RuuviTag:

1. Selecione **Editar**.

1. Escolha `RuuviTag` na lista **Modelo do dispositivo**. 

1. Escolha uma **Instância do dispositivo** de um dos dois sensores RuuviTag. Na loja da Contoso de exemplo, escolha `Zone 1 Ruuvi` para criar um bloco de telemetria para a Zona 1. 

1. Escolha **Propriedades > Versão do software**.

1. Escolha **Combinar**. 

1. Escolha **Configurar** no bloco recém-criado intitulado **Versão do software**. 

1. Altere o **Título** para *Versão do software do Ruuvi 1*.

1. Escolha **Atualizar configuração**. 

1. Arraste o bloco intitulado **Versão do software do Ruuv 1** abaixo do bloco **Ambiente da Zona 1**.

1. Repita as etapas anteriores para criar um bloco de propriedade de versão do software para o segundo RuuviTag. 

1. Clique em **Salvar**.  

    ![Blocos de propriedade do RuuviTag – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Adicionar blocos de comando para executar comandos
Os operadores de aplicativos também usam o painel gerenciar dispositivos por meio da execução de comandos. Você pode adicionar blocos de comando ao painel e eles executarão comandos predefinidos em um dispositivo. Nesta seção, você adicionará um bloco de comando para permitir que os operadores reinicializem o gateway Rigado. 

Para adicionar um bloco de comando para reinicializar o gateway:

1. Selecione **Editar**. 

1. Escolha `C500` na lista **Modelo do dispositivo**. Este é o modelo para o gateway Rigado C500. 

1. Escolha a instância do gateway em **Instância do dispositivo**.

1. Escolha **Comando > Reinicializar** e arraste-o para o painel ao lado do mapa da loja. 

1. Clique em **Salvar**. 

1. Exiba seu painel completo da Contoso. 

    ![Personalização completa do painel – Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Opcionalmente, escolha o bloco **Reinicializar** para executar o comando de reinicialização no gateway.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

* Alterar o nome do painel
* Personalizar blocos de imagem no painel
* Organizar blocos para modificar o layout
* Adicionar blocos de telemetria para exibir condições
* Adicionar blocos de propriedade para exibir detalhes do dispositivo
* Adicionar blocos de comando para executar comandos

Agora que você personalizou o painel no aplicativo de análise na loja no Azure IoT Central, veja as próximas etapas sugeridas:

> [!div class="nextstepaction"]
> [Exportar dados e visualizar insights](./tutorial-in-store-analytics-export-data-visualize-insights.md)
