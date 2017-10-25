---
title: "Instruções de instalação para o Azure Stream Analytics Tools para Visual Studio | Microsoft Docs"
description: "Instruções de instalação para o Azure Stream Analytics Tools para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 88abf40cefaca150c67e3a1068006fb0fa254305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instruções de instalação para o Stream Analytics Tools para Visual Studio
O Stream Analytics Tools agora oferece suporte ao Visual Studio 2017, 2015 e 2013. Neste documento, mostramos como instalar e desinstalar as ferramentas.

[Saiba como usar o Stream Analytics Tools para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="install"></a>Instalar
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Baixe o [Visual Studio 2017 (15.3 ou acima)](https://www.visualstudio.com/). As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte; não há suporte para a edição Express. 
* O Stream Analytics Tools é parte da carga de trabalho de Armazenamento de Dados e Processamento e de Desenvolvimento do Azure no Visual Studio 2017. Habilite qualquer uma dessas duas cargas de trabalho como parte da instalação do Visual Studio.

![Instalar o Stream Analytics Tools para Visual Studio 1](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)
![Instalar o Stream Analytics Tools para Visual Studio 2](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instalar o Visual Studio 2015, Visual Studio 2013 atualização 4. As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte; não há suporte para a edição Express. 
* Instalar o SDK do Microsoft Azure para .NET versão 2.7.1 ou posterior usando o [instalador de plataforma da Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instalar o [Azure Stream Analytics Tools para Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Atualização

### <a name="visual-studio-2017"></a>Visual Studio 2017
O novo lembrete de versão aparece na notificação do Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
O Stream Analytics Tools para Visual Studio instalado verifica automaticamente se há novas versões e você pode seguir as instruções na janela pop-up para instalar a versão mais recente. 


## <a name="uninstall"></a>Desinstalar

### <a name="visual-studio-2017"></a>Visual Studio 2017
Clique duas vezes no instalador do Visual Studio e selecione **Modificar**. Desmarque **Azure Data Lake e Stream Analytics Tools** da carga de trabalho de **Armazenamento de Dados e Processamento** ou **Desenvolvimento do Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Vá para o painel de controle e desinstale “Microsoft Azure Data Lake e Stream Analytics Tools para Visual Studio”.





