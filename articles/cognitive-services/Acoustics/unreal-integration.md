---
title: Projeto acústica Unreal e integração de Wwise
titlesuffix: Azure Cognitive Services
description: Estas instruções descrevem a integração do projeto acústica Unreal e plug-ins de Wwise ao seu projeto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435890"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projeto acústica Unreal e integração de Wwise
Estas instruções fornecem as etapas detalhadas de integração do pacote de plug-in do projeto acústica em seu projeto de jogo existente Unreal e Wwise. 

Requisitos de software:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 ou 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Plug-in de Wwise para Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Se você estiver usando uma integração direta do SDK Wwise em vez de usar os plug-ins Wwise Unreal, consulte o plug-in do projeto acústica Unreal e ajustar as chamadas à API Wwise.

Se você quiser usar acústica do projeto com um mecanismo de áudio que não seja Wwise, entre em contato conosco sobre o [fóruns do projeto acústica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Você pode usar o plug-in do projeto acústica Unreal para consultar dados acústica e, em seguida, fazer chamadas à API ao seu mecanismo.

## <a name="download-project-acoustics"></a>Baixe o projeto acústica
Se você ainda não o fez, baixe o [pacote de plug-in do projeto acústica Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Incluímos um plug-in do Unreal Engine e um plug-in do Wwise mixer no pacote. O plug-in Unreal fornece integração de editor e o tempo de execução. Durante o jogo, o plug-in do projeto acústica Unreal calcula parâmetros como oclusão para cada objeto do jogo a cada quadro. Esses parâmetros são convertidos em chamadas à API Wwise.

## <a name="review-integration-steps"></a>Examine as etapas de integração

Há essas etapas principais para instalar o pacote e implantá-lo em seu jogo.
1. Instalar o plug-in do projeto acústica Wwise mixer
2. (Re) implante Wwise seu jogo. Esta etapa propaga o plug-in do mixer em seu projeto de jogo.
3. Adicionar o plug-in do projeto acústica Unreal para seu jogo
4. Estender a funcionalidade de plug-in Unreal do Wwise
5. Compile o jogo e verifique o que Python está habilitado
6. Configurar seu projeto Wwise para usar o projeto acústica
7. Configuração de áudio no Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalar o plug-in do projeto acústica mixer
* Abra o inicializador de Wwise, em seguida, na guia **Plugins**, em **Instalar novos plug-ins**, selecione **Adicionar do diretório**. 

    ![Captura de tela de instalação de um plug-in no iniciador de Wwise](media/wwise-install-new-plugin.png)

* Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote baixado. Ele contém o pacote de plug-in do mixer Wwise.

* Wwise instalará o plug-in. Projeto acústica agora deve aparecer na lista de plug-ins instalados no Wwise.
![Lista de plug-ins de captura de tela de Wwise instalado após a instalação de projeto acústica](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) implantar Wwise em seu jogo
Implante novamente Wwise para seu jogo, mesmo se você já tenha integrado Wwise. Isso seleciona o plug-in do projeto acústica Wwise.

* **Plug-in do mecanismo:** Se você tiver Wwise instalado como um plug-in do jogo em um projeto C++ Unreal, ignore esta etapa. Se ele é instalado como um plug-in do mecanismo, para a instância porque seu projeto Unreal é única, implantação de Wwise Blueprint com nossos plug-in do mixer é mais complexa. Criar um projeto de C++ Unreal fictício, vazio, fechá-la se Unreal editor é aberto e siga o procedimento restante para implantar Wwise para este projeto fictício. Em seguida, copie o plug-in do Wwise implantado.
 
* No inicializador do Wwise, clique na **Unreal Engine**, em seguida, clique no menu de hambúrguer próximo a **Projetos recentes do Unreal Engine** e selecione **Procurar projeto**. Abrir projeto de seus jogos Unreal `.uproject` arquivo.

    ![Guia do captura de tela de Wwise iniciador Unreal](media/wwise-unreal-tab.png)

* Em seguida, clique em **Wwise integrar no projeto** ou **Wwise modificar no projeto**. Esta etapa (re) integra Wwise binários no seu projeto, agora, incluindo o plug-in do projeto acústica mixer.

* **Plug-in do mecanismo:** Se você estiver usando Wwise como um plug-in de mecanismo e criou o projeto fictício acima, copie a pasta Wwise implantado: `[DummyUProject]\Plugins\Wwise` e cole-o sobre `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` é o caminho do projeto C++ Unreal vazio, e `[UESource]` é quando você tem as fontes do Unreal Engine instaladas. Quando você terminar de copiar, você pode excluir o projeto fictício.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Adicionar o plug-in do projeto acústica Unreal para seu jogo
 
* Cópia a `Unreal\ProjectAcoustics` pasta em que o plug-in do pacote e crie uma nova pasta `[UProjectDir]\Plugins\ProjectAcoustics`, onde `UProjectDir` é a pasta do projeto do jogo que contém o `.uproject` arquivo.
  * **Plug-in do mecanismo**: Se você estiver usando Wwise como um plug-in do mecanismo, você deve usar acústica do projeto como um plug-in do Unreal engine também. O diretório de destino acima, em vez de usar: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Confirme se você vir uma `Wwise` pasta junto com o `ProjectAcoustics` pasta. Ele contém o plug-in de Wwise juntamente com os binários para o plug-in do mixer (re-) implantado na etapa 2 acima.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Estender a funcionalidade de plug-in Unreal do Wwise
* O plug-in do projeto acústica Unreal requer um comportamento adicional seja exposto a partir o Wwise Unreal plug-in API por [estas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um arquivo em lotes para automatizar o procedimento de aplicação de patch. 
* Dentro `Plugins\ProjectAcoustics\Resources`, execute `PatchWwise.bat`. Imagem de exemplo abaixo usa o nosso projeto de exemplo AcousticsGame.

    ![Realce de janela de captura de tela do Windows Explorer fornecido o script para patch Wwise](media/patch-wwise-script.png)

* Se você não tiver instalado o SDK do DirectX, precisará de comentar a linha que contém DXSDK_DIR no `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Captura de tela do editor de código mostrando o DXSDK comentado](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Compile o jogo e verifique o que Python está habilitado

* Compilar seu jogo e verifique se ele é compilado corretamente. Caso contrário, verifique as etapas anteriores com cuidado antes de continuar. 
* Abra seu projeto no Editor de Unreal. 
* **Plug-in do mecanismo:** Se usar ProjectAcoustics como plug-in do mecanismo, também Certifique-se de que ela está habilitada, listado em "internas" plug-ins.
* Você deve ver um novo modo, o que indica o que projeto acústica foi integrada.

    ![Captura de tela de Unreal mostrando acústica modo completo](media/acoustics-mode-full.png)

* Confirme se que você tiver o plug-in do Python para Unreal habilitado. Isso é necessário para a integração de editor funcionar corretamente.

    ![Captura de tela da habilitação de extensões do Python no editor Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Configuração do projeto Wwise

Um exemplo de projeto Wwise está incluído no download de exemplos. É recomendável examinar juntamente com estas instruções. As capturas de tela abaixo são feitas com base nesse projeto.

### <a name="bus-setup"></a>Configuração de barramento
* O plug-in do projeto acústica Unreal procurará o plug-in do mixer associados em um barramento com este ***exata*** nome: `Project Acoustics Bus`. Crie um novo barramento de áudio com este nome. O plug-in do mixer pode funcionar em várias configurações, mas agora vamos supor que ele será usado para reverberação apenas o processamento. Esse barramento trará o sinal de reverberação mistas para todas as fontes que usam acústica. Pode misturar upstream em qualquer barramento misturar a estrutura, um exemplo é mostrado abaixo, obtido do nosso projeto de exemplo Wwise incluído no download de exemplo.

    ![Barramentos de captura de tela de Wwise que mostra os projeto acústica barramento](media/acoustics-bus.png)

* A configuração de canal no barramento precisa ser definido como um dos: `1.0, 2.0, 4.0, 5.1 or 7.1`. Outras configurações resultará em nenhuma saída neste barramento.

    ![Captura de tela das opções de configuração de canal para o projeto acústica barramento](media/acoustics-bus-channel-config.png)

* Agora vá para o projeto acústica detalhes de barramento e certifique-se de que você pode ver a guia de plug-in do Mixer

    ![Captura de tela de Wwise mostrando como habilitar a guia de plug-in do Mixer para o barramento de acústica do projeto](media/mixer-tab-enable.png)

* Em seguida, vá para a guia de plug-in do Mixer e adicionar plug-in de mixer acústica do projeto para o barramento

    ![Barramento de imagem de Wwise mostrando como adicionar o plug-in do projeto acústica Mixer](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Configuração da hierarquia de ator mixer
* Por motivos de desempenho, projeto acústica aplica-se DSP de áudio para todas as fontes simultaneamente. Isso exige que o plug-in para operar como um plug-in do mixer. Wwise requer o plug-ins do mixer estar no barramento de saída, embora o barramento de saída normalmente transporta o sinal de saída de dry. Projeto acústica requer o sinal de dry ser roteada por meio de barramentos de aux, enquanto o sinal molhado é transferido a `Project Acoustics Bus`. O processo a seguir dá suporte à migração gradual para esse fluxo de sinal.

* Digamos que você tenha um projeto existente com uma hierarquia de ator mixer contendo trilha, armas e outras pessoas no nível superior. Cada um tem um barramento de saída correspondente para sua combinação de dry. Permite que você deseja migrar trilha para usar acústica. Primeiro, crie um barramento aux correspondente para transportar sua submix dry que é um filho do barramento de saída de trilha. Por exemplo, usamos um prefixo "Dry" na imagem abaixo para organizar essas, embora o nome exato não é importante. Qualquer medidores ou efeitos que você tinha no barramento trilha continuarão a funcionar como antes.

    ![Captura de tela do programa de instalação de combinação Dry Wwise recomendada](media/wwise-dry-mix-setup.png)

* Em seguida, modificar a estrutura de saída de barramento para o ator-mixer trilha da seguinte maneira, com o barramento de acústica projeto definir como o barramento de saída e Dry_Footsteps definido como um barramento aux definidos pelo usuário.

    ![Captura de tela de recomendado Wwise ator Mixer do barramento de instalação](media/actor-mixer-bus-settings.png)

* Agora trilha todos recebem tratamento acústica e seu reverberação no projeto acústica barramento de saída. O sinal de dry é roteado por meio de Dry_Footsteps e spatialized como de costume.

* Projeto acústica só se aplica a sons que têm um local de 3D no mundo. A seguir [Wwise documentação](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), as propriedades de posicionamento devem ser definida como mostrado. A configuração de "funcionamento em 3D" pode ser "Posição" ou "Posição + orientação" conforme necessário.

    ![Captura de tela de configurações de posicionamento do ator de Wwise recomendadas](media/wwise-positioning.png)

* Definindo o barramento de saída para outro barramento que misturam upstream em **projeto acústica barramento** não funcionará. Wwise impõe esse requisito no plug-ins do mixer.

* Se você quiser um filho na hierarquia de ator mixer de trilha para não usar acústica, você pode sempre usar "substituição pai" nele para recusá-lo.

* Se você estiver usando o jogo - ou definidas pelo usuário envia para reverberação em qualquer Misturador de ator no jogo, desative aqueles esse Misturador de ator para evitar a aplicação de reverberação duas vezes.

### <a name="spatialization"></a>Funcionamento em
Por padrão, o plug-in do projeto acústica Wwise mixer aplica reverberação convolução, deixando Wwise para fazer o funcionamento em panorâmica. Ao usar o projeto acústica nessa configuração padrão de somente reverberação, você está livre para usar qualquer método de configuração e em funcionamento em canal combinação do dry, permitindo que você misturar e combinar quase qualquer spatializer com reverberação dos acústica do projeto. As opções incluem [com base em Ambisonics binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projeto acústica inclui um spatializer opcional que dá suporte à renderização baseada em objeto HRTF alta resolução, tanto de movimento panorâmico. Marque a caixa de seleção "Executar funcionamento em" configurações de plug-in do mixer e escolher entre HRTF ou Panorâmica e desabilitar envia aux definidos pelo usuário definida acima para todos os barramentos de dry para evitar spatializing duas vezes, com plug-in do projeto acústica mixer e Wwise. O modo de funcionamento em não pode ser alterado em tempo real, porque ele requer uma nova geração de som bank. Você deve reiniciar o Unreal e regenerar soundbanks antes de atingir play para acompanhar as alterações de configuração de plug-in do mixer, como a caixa de seleção 'Executar funcionamento em'.

![Configurações de captura de tela de Wwise Mixer plug-in funcionamento em](media/mixer-spatial-settings.png)

Infelizmente, outros plug-ins spatializer baseado em objeto não têm suporte no momento como eles são implementados como plug-ins do mixer e Wwise atualmente não permite que vários mixer plug-ins atribuído a um único ator-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Configuração de áudio no Unreal
* Primeiro você precisará inserir seu nível de jogo para produzir um ativo acústica, que será colocado em `Content\Acoustics`. Consulte a [Unreal Tutorial tortas](unreal-baking.md) e retomar aqui. Alguns níveis previamente preparadas são incluídos no pacote de exemplo.
* Crie um ator acústica espaço na sua cena. Crie apenas um desses atores em um nível porque ele representa a acústica para o nível de inteiro. 

    ![Captura de tela de Unreal editor mostrando a criação de ator acústica espaço](media/create-acoustics-space.png)

* Agora, atribua o ativo de dados acústicos assado para o slot de dados acústica no ator acústica espaço. Agora, sua cena tem acústica!

    ![Captura de tela de Unreal acústica de howing editor s atribuição ativo](media/acoustics-asset-assign.png)

* Agora, adicione um ator vazio e faça o seguinte:

    ![Captura de tela de Unreal editor mostrando o uso do componente acústica em um ator vazio](media/acoustics-component-usage.png)

1. Adicione um componente de áudio acústica para o ator. Esse componente estende o componente de áudio Wwise com a funcionalidade para acústica do projeto.
2. Play na caixa Iniciar é marcada por padrão, o que irá disparar o evento Wwise associado na inicialização do nível.
3. Use a caixa de seleção Mostrar acústica parâmetros para imprimir na tela informações sobre a origem de depuração.
    ![Captura de tela do Unreal painel do editor acústica na fonte de som com valores de depuração habilitados](media/debug-values.png)
4. Atribuir um evento Wwise pelo fluxo de trabalho normal do Wwise
5. Certifique-se de que usar espacial áudio está desativado. Neste momento, se você usar acústica do projeto para um determinado componente de áudio, você não pode usar simultaneamente mecanismo de áudio espacial do Wwise para acústica.

Você está pronto. Mover ao redor do cenário e explore os efeitos acústicos!

## <a name="next-steps"></a>Próximas etapas
* [Design](unreal-workflow.md) tutorial para projeto acústica no Unreal/Wwise
* [Saiba como fazer prepara](unreal-baking.md) para suas cenas de jogos 
