---
title: Configurar ferramentas do Azure Stream Analytics para Visual Studio
description: Este artigo descreve os requisitos de instalação e como configurar as ferramentas de Azure Stream Analytics para o Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018693"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalar o Azure Stream Analytics Tools para Visual Studio

O Visual Studio 2019 e o Visual Studio 2017 oferecem suporte a ferramentas de Azure Data Lake e Stream Analytics. Este artigo descreve como instalar e desinstalar as ferramentas.

Para obter mais informações sobre como usar as ferramentas, consulte [início rápido: criar um trabalho de Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalar

Visual Studio Enterprise (Ultimate/Premium), Professional e Community Editions dão suporte às ferramentas. O Express Edition e o Visual Studio para Mac não dão suporte a eles.

Recomendamos o Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalar para o Visual Studio 2019 e 2017<a name="recommended-visual-studio-2019-and-2017"></a>

As Ferramentas Azure Data Lake e Stream Analytics fazem parte das cargas de trabalho de **processamento e armazenamento de dados** e **desenvolvimento do Azure** . Habilite uma dessas duas cargas de trabalho durante a instalação. Se o Visual Studio já estiver instalado, selecione **ferramentas**  >  **obter ferramentas e recursos** para adicionar cargas de trabalho.

Baixe o [visual studio 2019 (visualização 2 ou superior) ou o visual studio 2017 (15,3 ou superior)](https://www.visualstudio.com/) e siga as instruções para instalar o.

Selecione a carga de trabalho de **armazenamento e processamento de dados** , conforme mostrado:

![A carga de trabalho de armazenamento e processamento de dados é selecionada](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selecione a carga de trabalho de **desenvolvimento do Azure** , conforme mostrado:

![A carga de trabalho de desenvolvimento do Azure é selecionada](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Depois de adicionar a carga de trabalho, atualize as ferramentas. Este procedimento refere-se ao Visual Studio 2019:

1. Selecione **extensões**  >  **gerenciar extensões**.

1. Em **gerenciar extensões**, selecione **atualizações** e escolha **Azure data Lake e Stream Analytics ferramentas**.

1. Selecione **Atualizar** para instalar a extensão mais recente.

![Extensões e atualizações do Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalar para o Visual Studio 2015 e 2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional e Community Editions dão suporte às ferramentas. A Express Edition não dá suporte a eles.

* Instale o Visual Studio 2015 ou o Visual Studio 2013 Atualização 4.
* Instale o SDK do Microsoft Azure para .NET versão 2.7.1 ou posterior usando o [Web platform installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Instale o [Microsoft Azure data Lake e as ferramentas de Stream Analytics para Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Cumulativo<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Para o Visual Studio 2019 e o Visual Studio 2017, um novo lembrete de versão aparece como uma notificação do Visual Studio.

Para o Visual Studio 2015 e Visual Studio 2013, as ferramentas verificam novas versões automaticamente. Siga as instruções para instalar a versão mais recente.

## <a name="uninstall"></a>Desinstalar

Você pode desinstalar as Ferramentas Azure Data Lake e Stream Analytics. Para o Visual Studio 2019 ou o Visual Studio 2017, selecione **ferramentas**  >  **obter ferramentas e recursos**. Em **modificando**, desmarque **Azure data Lake e Stream Analytics ferramentas**. Ele aparece sob a carga de trabalho de **armazenamento e processamento de dados** ou a carga de trabalho de **desenvolvimento do Azure** .

Para desinstalar o do Visual Studio 2015 ou Visual Studio 2013, acesse **painel de controle**  >  **programas e recursos**. Desinstale o **Microsoft Azure data Lake e as ferramentas de Stream Analytics para Visual Studio**.
