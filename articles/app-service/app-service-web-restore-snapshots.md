---
title: Restaurar do backup – Serviço de Aplicativo do Azure
description: Saiba como restaurar seu aplicativo a partir de um instantâneo.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.custom: seodec18
ms.openlocfilehash: 8d4290f1411749e2d8d3d27fbd792ceeeea47ef7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851287"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restaurar um aplicativo no Azure a partir de um instantâneo
Este artigo mostra como restaurar um aplicativo no [Serviço de Aplicativo do Azure](../app-service/overview.md) a partir de um instantâneo. Você pode restaurar seu aplicativo para um estado anterior, com base em um dos instantâneos do seu aplicativo. Você não precisa habilitar o backup de instantâneos, a plataforma salva automaticamente um instantâneo de todos os aplicativos para fins de recuperação de dados.

Os instantâneos são cópias de sombra incrementais e oferecem diversas vantagens em relação a [backups](manage-backup.md) comuns:
- Não há erros de cópia de arquivo devido a bloqueios de arquivo.
- Não há limitação de tamanho de armazenamento.
- Nenhuma configuração é necessária.

A restauração usando instantâneos está disponível para aplicativos que são executados na camada **Premium** ou superior. Para obter informações sobre como escalar verticalmente seu aplicativo, veja [Escalar verticalmente um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md).

## <a name="limitations"></a>Limitações

- O recurso está atualmente em versão prévia.
- Você só pode restaurar para o mesmo aplicativo ou para um slot que pertença a esse aplicativo.
- O Serviço de Aplicativo interrompe o aplicativo de destino ou o slot de destino ao realizar a restauração.
- O Serviço de Aplicativo mantém três meses de instantâneos para fins de recuperação de dados de plataforma.
- Você só pode restaurar instantâneos dos últimos 30 dias.
- Serviços de aplicativos em execução em um ambiente de serviço de aplicativo não dão suporte a instantâneos.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restaurar um aplicativo usando um instantâneo

1. Na página **Configurações** de seu aplicativo no [Portal do Azure](https://portal.azure.com), clique em **Backups** para exibir a página **Backups**. Em seguida, clique em **Restaurar** na seção **Instantâneo (Versão prévia)**.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na página **Restaurar**, selecione o instantâneo para restaurar.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique o destino para a restauração de aplicativo em **Destino de restauração**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se você escolher **Substituir**, todos os dados existentes no sistema de arquivos atual do seu aplicativo serão apagados e substituídos. Antes de clicar em **OK**, certifique-se de que é o que você deseja fazer.
   > 
   > 
      
   > [!Note]
   > Devido às limitações técnicas atuais, você pode restaurar apenas para aplicativos na mesma unidade de escala. Essa limitação será removida em uma versão futura.
   > 
   > 
   
    Você pode selecionar **Aplicativo existente** para restaurar para um slot. Antes de usar essa opção, um slot em seu aplicativo já deverá ter sido criado.

4. Você pode optar por restaurar a configuração do site.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Clique em **OK**.
