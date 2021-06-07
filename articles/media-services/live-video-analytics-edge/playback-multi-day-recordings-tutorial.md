---
title: Reproduzir gravações de vários dias – Azure
description: Neste tutorial, você aprenderá a usar as APIs do Serviço de Mídia do Azure para reproduzir uma gravação de vídeo contínua de vários dias.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 81a778b40649c1318b3738a289f0db37fd35376a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492782"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Tutorial: Reproduzir gravações de vários dias  

Este tutorial se baseia no tutorial CVR [(Gravação de vídeo contínua)](continuous-video-recording-concept.md). Neste tutorial, você aprenderá a usar as APIs do Serviço de Mídia do Azure para reproduzir uma gravação de vídeo contínua de vários dias da nuvem. 

Isso é útil para cenários como segurança pública, em que há a necessidade de manter uma gravação da sequência de imagens de uma câmera por vários dias (ou semanas), e há uma necessidade ocasional de observar partes específicas dessa sequência.

Este tutorial mostra como:

> [!div class="checklist"]
> * Execute o aplicativo de exemplo que demonstra como você pode procurar o conteúdo de uma gravação de vários dias
> * Exibir as partes selecionadas dessa gravação

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

É recomendável que você leia as seguintes páginas da documentação:

* [Visão geral da Análise de Vídeo ao vivo no IoT Edge](overview.md)
* [Análise de Vídeo ao vivo na terminologia do IoT Edge](terminology.md)
* [Conceito de grafo de mídia](media-graph-concept.md)
* [Gravação de vídeo contínua](continuous-video-recording-concept.md) 
* [Guia de instruções: reprodução de gravações](playback-recordings-how-to.md)
* [Tutorial: gravação de vídeo contínua](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o [tutorial de CVR](continuous-video-recording-tutorial.md). Embora este tutorial e as APIs relevantes discutidas no [Tutorial: gravação de vídeo contínua](continuous-video-recording-tutorial.md) se apliquem a gravações que são de cinco minutos ou mais, é recomendável que você registre cinco horas de vídeo, se não mais. As APIs usadas para procurar gravações são melhor demonstradas com gravações longas.
* Recomendamos que você execute este tutorial enquanto o [Tutorial: gravação de vídeo contínua](continuous-video-recording-tutorial.md) ainda está em execução, ou seja, você ainda está gravando vídeo na nuvem.

## <a name="run-the-sample"></a>Execute o exemplo 

Como parte do tutorial do [CVR](continuous-video-recording-tutorial.md), você teria criado uma conta do Serviço de Mídia. Para esse tutorial, você precisará ter acesso completo à API para essa conta. Você pode usar as etapas em [Obter credenciais para acessar a API dos Serviços de Mídia](../latest/access-api-howto.md?tabs=portal) para criar uma entidade de serviço. Você deve ser capaz de obter um bloco JSON do portal do Azure semelhante a este:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Em seguida, no Visual Studio Code, abra src/ams-asset-player. Esta pasta contém os arquivos necessários para este tutorial. Abra o arquivo appsettings.json e copie o conteúdo dele em um novo arquivo, appsettings.development.json. Faça as seguintes edições no appsettings.development.json criado recentemente:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. No Visual Studio Code, abra a guia **Extensões** (ou pressione Ctrl+Shift+X) e pesquise pelo Hub IoT do Azure.
1. Clique com o botão direito do mouse e selecione **Configurações da Extensão**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Configurações da Extensão":::
1. Pesquise e habilite “Mostrar Mensagem Detalhada”.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Mostrar Mensagem Detalhada":::
1. <!--In Visual Studio Code, you can click-->Clique no ícone Executar à esquerda (ou Ctrl+Shift+D) para abrir os aplicativos disponíveis a serem executados:

    ![A captura de tela mostra um menu no Visual Studio Code com o item Executar selecionado.](./media/playback-multi-day-recordings-tutorial/run.png)
1. Selecione o aplicativo Player de Ativos do AMS na caixa suspensa conforme mostrado abaixo e pressione F5 para iniciar a depuração.

    ![A captura de tela mostra um menu no Visual Studio Code com o Player de Ativos do AMS selecionado.](./media/playback-multi-day-recordings-tutorial/debug.png)

O aplicativo de exemplo criará e iniciará seu aplicativo de navegador padrão e abrirá a página do Player de Ativos do AMS.

> [!NOTE]
> Dependendo das configurações de segurança no seu navegador, você poderá ver uma mensagem de aviso. Já que a página da Web está sendo executada localmente, você pode optar por ignorar o aviso.

O Player de Ativos do AMS solicita que você insira o nome de um ativo do Serviço de Mídia. Você deve usar o nome do Ativo que usou para gravar vídeo no [Tutorial: gravação de vídeo contínua](continuous-video-recording-tutorial.md).

Ao digitar o nome do ativo e pressionar enviar, o código do Player carregará a URL de streaming. Para obter mais informações, confira [Guia de instruções: reprodução de gravações](playback-recordings-how-to.md). Se, como recomendado, você ainda estiver gravando no ativo, o player detectará isso e tentará direcionar a reprodução para a parte mais recente do vídeo gravado. Você pode ver o carimbo de data/hora (em UTC) na parte superior esquerda do player. Na captura de tela abaixo, observe como o botão "Ao vivo" está selecionado.

![STREAM](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
No lado direito do player, você pode ver os controles para procurar o arquivo morto. Os anos, meses e datas nesse controle são populados usando a API availableMedia documentada em [Guia de instruções: reprodução de gravações](playback-recordings-how-to.md).
Quando você expandir o dia, se permitir que o tutorial CVR seja executado por várias horas, verá um resultado como este:

![Procurar arquivo morto](./media/playback-multi-day-recordings-tutorial/results.png)

A origem do feed de vídeo no tutorial é um arquivo MKV. Quando o simulador de RSTP (confira [Servidor de Mídia Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) alcança o final do arquivo, ele encerra o fluxo. O nó de origem RTSP no grafo de mídia detecta isso e restabelece a conexão, então o vídeo é retomado. Entre cada um desses fins de arquivo e a respectiva reconexão, há uma lacuna no arquivo morto gravado, que aparece como uma nova entrada nos resultados de availableMedia.

![Resultados](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Quando você clica em uma entrada qualquer na lista, o aplicativo cria uma URL de streaming com o filtro apropriado, como https://{nomeDoHost}/{IDdoLocalizador}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS). O Player carregará essa URL e a reprodução começará no startTime desejado.

Como alternativa, você pode usar horários de início e de término específicos, por meio dos controles na parte inferior da página. Você pode usar os resultados da chamada availableMedia, conforme listado no lado direito, como um guia para os valores permitidos de hora de início e término. As restrições detalhadas nos filtros startTime e endTime são documentadas no [Guia de instruções: reprodução de gravações](playback-recordings-how-to.md). Depois de selecionar os valores de tempo, ao clicar em enviar, o aplicativo carregará o player com uma URL de streaming como: https://{nomeDoHost}/{IDdoLocalizador}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS) a reprodução começará no startTime desejado.

## <a name="next-steps"></a>Próximas etapas

[Gravação de vídeo baseada em eventos para nuvem e reprodução com origem na nuvem](event-based-video-recording-tutorial.md)
