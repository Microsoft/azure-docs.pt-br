---
title: Atualização de Dispositivo para o sistema operacional em tempo real do Azure | Microsoft Docs
description: Introdução à Atualização de Dispositivo para o sistema operacional em tempo real do Azure
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629046"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Tutorial da Atualização de Dispositivo para o Hub IoT do Azure usando o Azure RTOS (sistema operacional em tempo real)

Este tutorial explicará como criar a Atualização de Dispositivo para o Agente do Hub IoT no Azure RTOS NetX Duo. Também fornece APIs simples para que os desenvolvedores integrem a funcionalidade de Atualização de Dispositivo aos aplicativos. Explore os [exemplos](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) dos principais painéis de avaliação de semicondutores, que incluem os guias de introdução para saber como configurar, criar e implantar as atualizações OTA (over-the-air) em dispositivos.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Introdução
> * Marcar seu dispositivo
> * Criar um grupo de dispositivos
> * Implantar uma atualização de imagem
> * Monitorar a implantação da atualização

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Acesso a um Hub IoT. É recomendável usar uma camada S1 (Standard) ou posterior.
* Uma instância da Atualização de Dispositivo e uma conta vinculada ao Hub IoT. Siga o guia para [criar e vincular](http://create-device-update-account.md/) uma conta de atualização de dispositivo, caso não tenha feito isso anteriormente.

## <a name="get-started"></a>Introdução

Cada exemplo de projeto do Azure RTOS específico do painel contém código e documentação sobre como usar a Atualização de Dispositivo para o Hub IoT nele. 
1. Baixe os arquivos de exemplo específicos do painel nos [Exemplos do Azure RTOS e da Atualização de Dispositivo](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Localize a pasta de documentação do exemplo baixado.
3. Na documentação, siga as etapas para preparar os recursos do Azure, a conta e registrar dispositivos IoT nele.
5. Em seguida, siga a documentação para criar uma imagem de firmware e importar o manifesto no painel.
6. Depois, publique a imagem de firmware e o manifesto na Atualização de Dispositivo para Hub IoT.
7. Por fim, baixe e execute o projeto no seu dispositivo.

Saiba mais sobre o [Azure RTOS](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Marcar seu dispositivo

1. Mantenha o aplicativo do dispositivo em execução como na etapa anterior.
2. Entre no [portal do Azure](https://portal.azure.com) e navegue até Hub IoT.
3. Em 'Dispositivos IoT' no painel de navegação à esquerda, encontre seu dispositivo IoT e acesse o Dispositivo Gêmeo.
4. Em Dispositivo gêmeo, exclua todos os valores de marca existentes da Atualização de dispositivo definindo-os como null.
5. Adicione um novo valor de marca da Atualização de dispositivo, conforme mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Criar grupo de atualizações

1. Vá para o Hub IoT que você conectou anteriormente à sua instância de Atualização de dispositivo.
2. Selecione a opção Atualizações de dispositivo em Gerenciamento de dispositivo automático na barra de navegação à esquerda.
3. Selecione a guia Grupos na parte superior da página. 
4. Clique no botão Adicionar para criar um novo grupo.
5. Selecione a marca do Hub IoT que você criou na etapa anterior com a lista. Selecione Criar grupo de atualizações.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Captura de tela mostrando a seleção de marca." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre como adicionar marcas e criar grupos de atualização

## <a name="deploy-new-firmware"></a>Implantar um novo firmware

1. Depois que o grupo for criado, será possível ver uma nova atualização disponível para o grupo de dispositivos, juntamente com um link para a atualização em Atualizações Pendentes. Talvez seja necessário atualizar a página. 
2. Clique na atualização disponível.
3. Confirme se o grupo correto foi selecionado como o grupo de destino. Agende uma implantação e selecione Implantar atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione Atualizar" lightbox="media/deploy-update/select-update.png":::

4. Exiba o gráfico de conformidade. Será possível ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em andamento" lightbox="media/deploy-update/update-in-progress.png":::

5. Depois de atualizar seu dispositivo com êxito, será possível conferir o gráfico de conformidade e a atualização dos detalhes da implantação para refletir as mesmas informações. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização realizada com êxito" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorar uma implantação de atualização

1. Selecione a guia Implantações na parte superior da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Guia Implantações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implantação criada para exibir os detalhes da implantação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Atualizar para exibir os detalhes mais recentes do status. Continue esse processo até que o status seja alterado para Bem-sucedido.

Agora você concluiu com êxito uma atualização de imagem de ponta a ponta usando a Atualização de dispositivo do Hub IoT em um dispositivo Raspberry Pi 3 B+. 

## <a name="cleanup-resources"></a>Recursos de limpeza

Limpe a conta da atualização de dispositivo, a instância, o Hub IoT e o dispositivo IoT quando eles não forem mais necessários. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure RTOS e como ele funciona com a IoT do Azure, veja https://azure.com/rtos.
