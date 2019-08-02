---
title: Início Rápido do Projeto Acústico com o Unreal
titlesuffix: Azure Cognitive Services
description: Ao usar o conteúdo de exemplo, experimente os controles de design do Projeto Acústico no Unreal e Wwise e implante o Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: a78df2d4d84487399da10ca722550639a92e71bf
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798133"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Início Rápido do Projeto Acústico Unreal/Wwise
Neste início rápido, você experimentará os controles de design do Projeto Acústico usando o conteúdo de amostra fornecido para o Unreal Engine e Wwise.

Requisitos de software:
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>Baixar o pacote de exemplo
Baixe o [pacote de exemplo do Projeto Acústico Unreal + Wwise](https://www.microsoft.com/download/details.aspx?id=58090). O pacote de exemplo contém um projeto do Unreal Engine, o projeto Wwise para esse projeto Unreal e o plug-in do Projeto Acústico Wwise.

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de exemplo do Projeto Acústico
Para configurar o projeto de exemplo do Projeto Acústico Unreal/Wwise, você precisará, em primeiro lugar, instalar o login do Projeto Acústico no Wwise. Em seguida, implante os binários de Wwise ao projeto Unreal e ajuste plugin Unreal do Wwise para dar suporte ao Projeto Acústico.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instale o plugin Wwise do Projeto Acústico
Abra o inicializador de Wwise, em seguida, na guia **Plugins**, em **Instalar novos plug-ins**, selecione **Adicionar do diretório**. Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote baixado.

![Captura de tela do iniciador do Wwise mostrando a opção Instalar plug-in do Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicione os binários de Wwise para o projeto de exemplo Unreal do Projeto Acústico
No inicializador do Wwise, clique na **Unreal Engine**, em seguida, clique no menu de hambúrguer próximo a **Projetos recentes do Unreal Engine** e selecione **Procurar projeto**. Abra o arquivo de projeto do `.uproject` Unreal no pacote `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Captura de tela da guia do Unreal do Iniciador do Wwise](media/wwise-unreal-tab.png)

Em seguida, ao lado do projeto de exemplo do Projeto Acústico, clique em **Integrar Wwise ao Projeto**.

![Captura de tela do Iniciador do Wwise mostrando o Projeto do Unreal de Jogo Acústico](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Estender a funcionalidade de plug-in Unreal do Wwise
O plugin do Unreal do Projeto Acústico requer que o comportamento adicional seja exposto a partir da API de plugin Unreal do Wwise. Execute o arquivo de lote fornecido com o plugin Unreal do Projeto Acústico para automatizar essas modificações:
* Dentro `AcousticsGame\Plugins\ProjectAcoustics\Resources`, execute `PatchWwise.bat`.

    ![Captura de tela da janela do Windows Explorer mostrando o script para aplicar patch no projeto do Wwise](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado, dependendo da versão do Wwise que você está usando, talvez seja preciso comentar na linha que contém `DXSDK_DIR` em `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`:

    ![Captura de tela do editor de código mostrando o DXSDK comentado](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Abra o projeto Unreal. 
Ele solicitará que você recompile os módulos; Clique em Sim.

>Se abrir o projeto falhar em falhas de build, verifique se você instalou o plugin do projeto Wwise do Projeto Acústico para a mesma versão do Wwise usada no projeto de exemplo do Projeto Acústico.

>Se você não estiver usando [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6, precisará regenerar bancos de som antes da reprodução do áudio no projeto de exemplo.

## <a name="experiment-with-project-acoustics-design-controls"></a>Fazer experiências com os controles de design do Projeto Acústico
Ouça como a cena parece clicando no botão reproduzir no editor do editor. Na área de trabalho, use W, A, S, D e o mouse para mover-se. Para ver os atalhos de teclado de outros controles, pressione **F1**. Aqui estão algumas atividades de design para tentar:

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
Há controles de design do Projeto Acústico por código-fonte em cada ator de som Unreal:

![Captura de tela dos controles de design de Acústica do Editor do Unreal](media/demo-scene-sound-source-design-controls.png)

Se o multiplicador de **Oclusão** for maior do que 1 (o padrão é 1), a oclusão será exagerada. Defini-la para menos de 1 torna o efeito da oclusão mais sutil.

Para habilitar a transmissão através da parede, mova o controle deslizante **Transmissão (dB)** para seu nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma fonte
Para alterar como a umidade muda rapidamente com a distância, use a **Distorção de Distância Perceptiva**. O Projeto Acústico calcula os níveis de umidade em todo o espaço a partir da simulação, que variam levemente com a distância e fornecem dicas de distância perceptiva. Aumentar a distorção de distância exagera esse efeito aumentando os níveis de umidade relacionados à distância. Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil. Esse efeito também pode ser ajustado em detalhes mais refinados, ajustando a **Umidade (dB)**.

Aumente o tempo de decaimento por todo o espaço, ajustando a **Escala de Tempo de Decaimento**. Considere o caso em que o resultado da simulação é um tempo de decaimento de 1,5 s. Definir a **Escala de tempo de decaimento** para 2 resultará em um tempo de decaimento aplicado à fonte de 3 s.

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação de distância
O plugin do mixer Wwise do Projeto Acústico respeita a atenuação baseada em distância por fonte incorporado no Wwise. Alterar essa curva alterará o nível de caminho de caminho seco. O plugin do Projeto Acústico irá ajustar o nível úmido para manter a combinação da mistura de úmido-seco especificada pela simulação e controles de design.

![Captura de tela do painel da curva de atenuação do Wwise com atenuação atingindo zero antes do limite de simulação](media/demo-sounds-attenuation.png)

O Projeto Acústico executa o cálculo em uma caixa de “região da simulação” centralizada em torno de cada local simulado do player. Os ativos de acústica no pacote de exemplo foram incorporados com um raio de região de simulação de 45 m e os atenuações foram projetados para 0 antes de 45 m. Embora esse declínio não seja um requisito restrito, carrega a ressalva de que apenas a geometria dentro de 45 m do ouvinte ocluirá os sons.

## <a name="next-steps"></a>Próximas etapas
* [Integrar o plugin do Projeto Acústico](unreal-integration.md) no seu projeto Unreal
* [Criar uma conta do Azure](create-azure-account.md) para seu próprios prepara


