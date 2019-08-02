---
title: Projeto de implantação e integração do Unity acústica
titlesuffix: Azure Cognitive Services
description: Esse manual explica a integração do plug-in do projeto acústica Unity ao seu projeto do Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 2deddfd4e6c03b53306d8fbab3340dce464158b0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612734"
---
# <a name="project-acoustics-unity-integration"></a>Integração do Project acústica Unity
Esse manual explica a integração do plug-in do projeto acústica Unity ao seu projeto do Unity.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows
* [Pacote do projeto Unity acústica](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>O plug-in de importação
Importe a acústica UnityPackage para o seu projeto. 
* No Unity, vá para **ativos > Importar pacote > pacote personalizado...**

    ![Menu de captura de tela do Unity Importar pacote](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se você estiver importando o plug-in para um projeto existente, seu projeto já pode ter um arquivo **mcs.rsp** na raiz do projeto, que especifica opções para o compilador C#. Você precisará mesclar o conteúdo desse arquivo com o arquivo de MCS que vem com o plug-in do projeto acústica.

## <a name="enable-the-plugin"></a>Habilitar o plug-in
A parte de assar do kit de ferramentas de acústica requer a versão de tempo de execução de script do .NET 4.x. A importação de pacotes atualizará as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito.

![Captura de tela do painel Configurações do Player do Unity](media/player-settings.png)

![Captura de tela do painel Configurações do Player do Unity com .NET 4.5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar DSP de áudio
Projeto acústica inclui o tempo de execução áudio DSP que se integra a estrutura de spatializer de mecanismo de áudio do Unity. Ele inclui o funcionamento em com base em HRTF e panorâmica. Habilitar o projeto acústica DSP abrindo as configurações de áudio do Unity usando **Editar > configurações do projeto > áudio**, selecionando **projeto acústica** como o **Spatializer plug-in** para seu projeto. Verifique se o **DSP o tamanho do Buffer** é definido como o melhor desempenho.

![Painel de captura de tela de configurações de projeto do Unity](media/project-settings.png)  

![Painel de configurações de captura de tela do Unity Spatializer com spatializer acústica do projeto selecionado](media/choose-spatializer.png)

Em seguida, abra o Mixer de áudio (**Janela > áudio Mixer**). Certifique-se de ter pelo menos um Mixer, com um grupo. Se você não fizer isso, clique no botão "+" à direita de **Mixers**. A parte inferior da faixa de canal na seção efeitos com o botão direito e, em seguida, adicione a **projeto acústica Mixer** efeito. Observe que apenas um Mixer acústica de projeto é suportado por vez.

![Captura de tela do Unity áudio Mixer mixer acústica do projeto de hospedagem](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Habilitar acústica em fontes de som
Crie uma fonte de áudio. Clique na caixa de seleção na parte inferior do painel de controle do AudioSource que diz **Spatialize**. Certifique-se **Blend espacial** é definido como completo 3D.  

![Painel de captura de tela de fonte de áudio do Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Habilitar design acústico
Anexar o script **AcousticsAdjust** a uma fonte de som na sua cena para habilitar os parâmetros de design de origem adicionais, clicando em **Add Component** e escolhendo **Scripts > Ajustar acústica** :

![Captura de tela do script AcousticsAdjust do Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Próximas etapas
* [Inserir sua cena com acústica do projeto para Unity](unity-baking.md)
* [Criar uma conta do lote do Azure](create-azure-account.md) que inserir sua cena na nuvem
* Explore os [processo de design de projeto acústica Unity](unity-workflow.md).

