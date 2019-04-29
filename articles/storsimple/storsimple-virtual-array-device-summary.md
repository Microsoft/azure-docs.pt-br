---
title: Folha de resumo de dispositivo da StorSimple Virtual Array | Microsoft Docs
description: Descreve a folha de resumo do dispositivo do Gerenciador de Dispositivos StorSimple e explica como usá-lo para monitorar a integridade da Matriz Virtual StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408467"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Use a folha de resumo do dispositivo do Gerenciador de Dispositivos StorSimple conectado à Matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

A folha do dispositivo Gerenciador de Dispositivos StorSimple fornece uma exibição resumida de uma Matriz Virtual StorSimple que está registrada em determinado Gerenciador de Dispositivos StorSimple, destacando os problemas de dispositivos que precisam da atenção do administrador do sistema. Este tutorial apresenta a folha de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que podem ser realizadas nessa folha.

A folha de resumo do dispositivo exibe as seguintes informações:

![Painel do dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gerenciamento

Na folha do dispositivo StorSimple, veja as opções para gerenciar seu dispositivo StorSimple. Você vê os comandos de gerenciamento na parte superior da folha e no lado esquerdo. Use essas opções para adicionar compartilhamentos ou volumes, ou atualizar ou fazer failover da sua matriz virtual.

A área de conceitos básicos captura algumas das propriedades importantes, como o status, o modelo, a versão de software e um link para a **interface do usuário da Web** da matriz. Se você está em uma rede interna, pode iniciar diretamente a [interface do usuário da Web local](storsimple-ova-web-ui-admin.md) para administrar sua matriz virtual.

![Conceitos básicos do dispositivo](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumo do dispositivo StorSimple

* O bloco **Alertas** fornece um instantâneo de todos os alertas ativos na sua matriz virtual agrupados por severidade do alerta. Clique no bloco para abrir a folha **Alertas** e clique em um alerta individual para exibir detalhes adicionais sobre ele, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.

* O bloco **Capacidade** mostra o armazenamento primário provisionado e restante no dispositivo virtual em relação ao armazenamento total disponível nele. **Provisionado** refere-se à quantidade de armazenamento que é preparada e alocada para uso, enquanto **Restante** refere-se à capacidade restante que pode ser provisionada nesse dispositivo. A capacidade **Restante em Camadas** é a capacidade disponível que pode ser provisionada, incluindo a nuvem, enquanto a capacidade **Restante Local** é a capacidade restante nos discos anexados à matriz virtual.

* No gráfico **Uso**, você pode ver o armazenamento primário usado na sua matriz virtual, bem como o armazenamento em nuvem consumido nos últimos sete dias, o período padrão. Use a opção **Editar** no canto superior direito do gráfico para escolher uma escala de tempo diferente.

* O bloco **Compartilhamentos** ou **Volumes** fornece um resumo do número de compartilhamentos ou volumes no seu dispositivo agrupado por status. Clique no bloco para abrir a folha de lista **Compartilhamentos** ou **Volumes** e clique em um compartilhamento ou volume individual para exibir ou modificar suas propriedades. Para saber mais, confira como [gerenciar compartilhamentos](storsimple-virtual-array-manage-shares.md) ou [gerenciar volumes](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Próximas etapas
Saiba como:
- [Gerenciar compartilhamentos em uma matriz virtual StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gerenciar volumes em uma matriz virtual StorSimple](storsimple-virtual-array-manage-volumes.md)

