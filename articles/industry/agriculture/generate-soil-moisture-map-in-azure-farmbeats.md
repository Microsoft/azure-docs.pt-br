---
title: Gerar calor de umidade de solo
description: Descreve como gerar calor de umidade de solo no Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: dd8d688355e0f71ce77cdbc6012d788c7b16d825
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173776"
---
# <a name="generate-soil-moisture-heatmap"></a>Gerar calor de umidade de solo

A umidade de solo é a água que é mantida nos espaços entre as partículas de solo.A calor de umidade de solo ajuda você a entender os dados de umidade em qualquer profundidade e em alta resolução em seus farms. Para gerar um calor de umidade de solo preciso e utilizável, uma implantação uniforme de sensores do mesmo provedor é necessária. Provedores diferentes terão diferenças na maneira como a umidade de solo é medida junto com as diferenças na calibragem. O calor é gerado para uma profundidade específica usando os sensores implantados nessa profundidade.

Este artigo descreve o processo de geração de um calor de umidade de solo para seu farm, usando o acelerador de FarmBeats do Azure. Neste artigo, você aprenderá a:

- [Criar farms](#create-a-farm)
- [Atribuir sensores a farms](#get-soil-moisture-sensor-data-from-partner)
- [Gerar calor de umidade de solo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Antes de começar

Verifique o seguinte:  

- Uma assinatura do Azure.
- Uma instância em execução do Azure FarmBeats.
- Um mínimo de três sensores de umidade de solo estão disponíveis para o farm.

## <a name="create-a-farm"></a>Criar um farm

Um farm é uma área geográfica de interesse para a qual você deseja criar uma calor de umidade de solo. Você pode criar um farm usando a [API farms](https://aka.ms/FarmBeatsDatahubSwagger) ou na [interface do usuário do acelerador de FarmsBeats](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Implantar sensores

Você deve implantar fisicamente os sensores de umidade de solo no farm. Você pode comprar sensores de umidade de solo de qualquer um dos nossos parceiros aprovados – [instrumentos Davis](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). Você deve coordenar com seu provedor de sensor para fazer a configuração física em seu farm.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Obter dados de sensor de umidade de solo do parceiro

À medida que os sensores começam a streaming, os dados no painel de dados do parceiro, eles habilitam os dados no Azure FarmBeats. Isso pode ser feito no aplicativo de parceiro.

Por exemplo, se você comprou sensores Davis, fará logon em sua conta de link do clima e fornecerá as credenciais necessárias para habilitar o streaming de dados no Azure FarmBeats. Para obter as credenciais necessárias, siga as instruções em [obter dados do sensor](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Depois de inserir suas credenciais e selecionar **Enviar** no aplicativo parceiro, você poderá fazer com que os dados fluam para o Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Atribuir sensores de umidade de solo ao farm

Depois de vincular sua conta de sensor ao Azure FarmBeats, você precisará atribuir os sensores de umidade de solo ao farm de interesse.

1.  No home page, selecione **farms** no menu, a página lista de **farms** é exibida.
2.  Selecione **myfarm**  >  **Adicionar dispositivos**.
3.  A janela **Adicionar dispositivos** é exibida. Selecione qualquer dispositivo que esteja vinculado aos sensores de umidade de solo do seu farm.

    ![Captura de tela que mostra o botão Adicionar dispositivos.](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar dispositivos**.     

## <a name="generate-soil-moisture-heatmap"></a>Gerar calor de umidade de solo

Esta etapa é criar um trabalho ou uma operação de execução longa que irá gerar calor de umidade de solo para seu farm.

1.  Na home page, vá para **farms** no menu de navegação à esquerda para exibir a página farms.
2.  Selecione **myfarm**.
3.  Na página **detalhes do farm** , selecione **gerar mapa de precisão**.
4.  No menu suspenso, selecione **umidade de solo**.
5.  Na janela **umidade de solo** , selecione **esta semana**.
6.  Na medida **selecionar** **sensor** de umidade de solo, insira a medida que você deseja usar para o mapa.
    Para localizar a medida do sensor, em **sensores**, selecione qualquer sensor de umidade do solo. Em **Propriedades do sensor**, use o valor **nome da medida** .

    ![Captura de tela que mostra o solo de umidade.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecione **gerar mapas**.
    Uma mensagem de confirmação com detalhes do trabalho é exibida. Para obter mais informações, consulte status do trabalho em trabalhos.

    >[!NOTE]
    > O trabalho leva cerca de três a quatro horas para ser concluído.

### <a name="download-the-soil-moisture-heatmap"></a>Baixe o calor de umidade de solo

Use as seguintes etapas:

1. Na página **trabalhos** , verifique o **status do trabalho** que você criou no último procedimento.
2. Quando o status do trabalho for exibido com **êxito**, selecione **mapas** no menu.
3. Pesquise o mapa até o dia em que foi criado no formato <solo-moisture_MyFarm_YYYY-MM-DD>.
4. Selecione um mapa na coluna **nome** , uma janela pop-up é exibida com a visualização do mapa selecionado.
5. Selecione **Baixar**. O mapa é baixado e armazenado na pasta local do seu computador.

    ![Projeto do FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você gerou com êxito um calor de umidade de solo, saiba como [gerar o posicionamento do sensor](generate-maps-in-azure-farmbeats.md#sensor-placement-map) e [ingerir dados de telemetria históricos](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
