---
title: 'Tutorial: Criar um aplicativo HoloLens Unity'
description: Neste tutorial, você aprenderá como criar um novo aplicativo HoloLens Unity usando Âncoras Espaciais do Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee0bf9b4ce009f37dd1931d4ed030defa24e7d38
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95996246"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar um novo aplicativo HoloLens Unity usando Âncoras Espaciais do Azure

Este tutorial mostrará a você como criar um novo aplicativo HoloLens Unity com Âncoras Espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

1. Um computador Windows com o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 ou posterior</a> instalado com a carga de trabalho de **desenvolvimento da Plataforma Universal do Windows**, o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** e o <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
2. A [VSIX (Extensão do Visual Studio) C++/WinRT](https://aka.ms/cppwinrt/vsix) para o Visual Studio deve ser instalada do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Um dispositivo do HoloLens com o [modo de desenvolvedor](/windows/mixed-reality/using-visual-studio) habilitado. Este artigo requer um dispositivo HoloLens com a [Atualização do Windows 10 de maio de 2020](/windows/mixed-reality/whats-new/release-notes-may-2020). Para atualizar para a versão mais recente no HoloLens, abra o aplicativo de **Configurações**, acesse **Atualização e Segurança** e, em seguida, selecione o botão **Verificar se há atualizações**.

## <a name="getting-started"></a>Introdução

Vamos primeiro configurar nosso projeto e a cena do Unity:
1. Inicie o Unity.
2. Selecione **Novo**.
4. Certifique-se de que **3D** está selecionado.
5. Nomeie o projeto e insira uma **Localização** para salvamento.
6. Selecione **Criar projeto**.
7. Salve a cena padrão vazia em um novo arquivo usando: **Arquivo** > **Salvar Como**.
8. Nomeie a nova cena como **Principal** e pressione o botão **Salvar**.

**Definir as Configurações do Projeto**

Vamos agora definir algumas configurações de projeto do Unity que nos ajudam a direcionar ao SDK do Windows Holographic para desenvolvimento.

Primeiro, vamos definir as configurações de qualidade do aplicativo.
1. Selecione **Editar** > **Configurações do Projeto** > **Qualidade**
2. Na coluna sob o logotipo da **Windows Store**, clique na seta na linha **Padrão** e selecione **Muito Baixa**. Você saberá que a configuração terá sido aplicada corretamente quando a caixa na coluna **Windows Store** e na linha **Muito Baixa** estiver verde.

Precisamos configurar nosso aplicativo Unity com uma exibição imersiva em vez de uma exibição 2D. Podemos criar uma exibição imersiva habilitando o suporte para Realidade Virtual no Unity, direcionado ao SDK do Windows 10.
1. Vá para **Editar** > **Configurações do Projeto** > **Player**.
2. No **Painel Inspetor** para **Configurações do Player**, selecione o ícone do **Windows**.
3. Expanda o grupo **Configurações de XR**.
4. Na seção **Renderização**, marque a caixa de seleção **Realidade Virtual Compatível** para adicionar uma nova lista de **SDKs de Realidade Virtual**.
5. Verifique se **Windows Mixed Reality** aparece na lista. Se não aparecer, selecione o botão **+** na parte inferior da lista e escolha **Windows Mixed Reality**.

> [!NOTE]
> Se não vir o ícone do Windows, verifique novamente se você selecionou o Back-end de Script .NET do Windows antes da instalação. Caso contrário, talvez seja necessário reinstalar o Unity com a instalação correta do Windows.

**Verificar a configuração de back-end do script**
1. Vá para **Edite** > **Configurações do Projeto** > **Player** (você ainda poderá ter **Player** aberto da etapa anterior).
2. No **Painel Inspetor** para **Configurações do Player**, selecione o ícone da **Windows Store**.
3. Na seção de configuração **Outras Configurações**, verifique se o **Back-end de Script** está definido como **IL2CPP**.

**Definir capacidades**
1. Vá para **Edite** > **Configurações do Projeto** > **Player** (você ainda poderá ter **Player** aberto da etapa anterior).
2. No **Painel Inspetor** para **Configurações do Player**, selecione o ícone da **Windows Store**.
3. Na seção de configuração **Configurações de Publicação**, marque **InternetClientServer** e **SpatialPerception**.

**Configurar a câmera virtual principal**
1. No **Painel de Hierarquia**, selecione **Câmera Principal**.
2. No **Inspetor**, defina sua posição de transformação para **0,0,0**.
3. Localize a propriedade **Limpar Sinalizadores** e altere a lista suspensa de **Skybox** para **Cor Sólida**.
4. Clique no campo **Tela de fundo** para abrir um seletor de cor.
5. Defina **R, G, B e A** para **0**.
6. Selecione **Adicionar Componente**, pesquise por **Colisor de Mapeamento Espacial** e adicione-o.

**Criar nosso script**
1. No painel **Projeto**, crie uma pasta chamada **Scripts** sob a pasta **Ativos**.
2. Clique com o botão direito do mouse na pasta e selecione **Criar >** , **Script C#** . Dê a ela o título **AzureSpatialAnchorsScript**.
3. Vá para **GameObject** -> **Criar Vazio**.
4. Selecione-a e, em **Inspetor**, renomeie-a de **GameObject** para **MixedRealityCloud**. Selecione **Adicionar Componente** e pesquise por **AzureSpatialAnchorsScript** e adicione-o.

**Criar a esfera pré-fabricada**
1. Vá para **GameObject** -> **Objeto 3D** -> **Esfera**.
2. No **Inspector**, defina a escala como **0.25, 0.25, 0.25**.
3. Localize o objeto de **Esfera** no painel de **Hierarquia**. Clique nele e arraste-o para a pasta **Ativos** no painel **Projeto**.
4. Clique com o botão direito do mouse para **Excluir** a esfera original criada no painel **Hierarquia**.

Agora, você deve ter uma esfera pré-fabricada em seu painel de **Projeto**.

## <a name="trying-it-out"></a>Experimentá-lo
Para testar se tudo está funcionando, compile o seu aplicativo **Unity** e implante-o do **Visual Studio**. Siga o Capítulo 6 do [curso **Noções básicas do MR 100: Introdução ao Unity**](/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) para fazer isso. Você deve ver a tela inicial do Unity e, em seguida, uma tela clara.

## <a name="place-an-object-in-the-real-world"></a>Colocar um objeto no mundo real
Vamos criar e dispor um objeto usando o aplicativo. Abra a solução do Visual Studio que criamos quando [implantamos nosso aplicativo](#trying-it-out).

Primeiro, adicione as seguintes importações ao seu `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Em seguida, adicione as seguintes variáveis de membro à classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Antes de continuarmos, precisamos definir a esfera pré-fabricada que criamos em nossa variável de membro spherePrefab. Volte para o **Unity**.
1. No **Unity**, selecione o objeto **MixedRealityCloud** no painel **Hierarquia**.
2. Clique na **Esfera** pré-fabricada que foi salva no painel **Projeto**. Arraste a **Esfera** em que você clicou na área **Esfera Pré-fabricada** sob **Script de Âncoras Espaciais do Azure (Script)** no painel **Inspetor**.

Agora, você deve ter a **Esfera** definida como o objeto pré-fabricado em seu script. Compile no **Unity** e, em seguida, abra a solução resultante do **Visual Studio** novamente, conforme detalhado em [Experimentando](#trying-it-out).

No **Visual Studio**, abra `AzureSpatialAnchorsScript.cs` novamente. Adicione o código a seguir ao método `Start()`. Esse código conectará `GestureRecognizer`, que chamará `HandleTap` quando detectar um gesto de fechar e abrir dedos indicador e polegar.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Agora, precisamos adicionar o seguinte método `HandleTap()` abaixo de `Update()`. Ele fará uma conversão de raio e obterá um ponto no qual colocar uma esfera.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

Agora, precisamos criar a esfera. A esfera inicialmente estará em branco, mas esse valor será ajustado posteriormente. Em seguida, adicione o método `CreateAndSaveSphere()` a seguir:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Execute seu aplicativo do **Visual Studio** valide-o mais uma vez. Desta vez, toque na tela para criar e colocar seu círculo branco sobre a superfície de sua escolha.

## <a name="set-up-the-dispatcher-pattern"></a>Configurar o padrão do dispatcher

Ao trabalhar com o Unity, todas as APIs do Unity (por exemplo, as APIs usadas para fazer atualizações da interface do usuário) precisam ocorrer no thread principal. No código que escreveremos, no entanto, obtemos os retornos de chamada em outros threads. Queremos atualizar a interface do usuário nesses retornos de chamada, portanto, precisamos de uma maneira para ir de um thread secundário para o thread principal. Para executar o código no thread principal de um thread secundário, usaremos o padrão de dispatcher.

Vamos adicionar uma variável de membro, `dispatchQueue`, que é uma fila de ações. Vamos enviar por push as ações para a fila e, em seguida, remover as ações da fila e executá-las no thread principal.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Em seguida, vamos adicionar uma forma de adicionar uma ação à fila. Adicione `QueueOnUpdate()` logo após `Update()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Podemos usar o loop Update() para verificar se há uma ação na fila. Se houver, a removeremos da fila e a executaremos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obter o SDK das Âncoras Espaciais do Azure

## <a name="via-unity-package-manager-upm-package"></a>[Usando o pacote do UPM (Gerenciador de Pacotes do Unity)](#tab/UPMPackage)

Esse método é compatível com as versões do Unity 2019.1+.

### <a name="add-the-registry-to-your-unity-project"></a>Adicionar o registro ao projeto do Unity

1. Em um explorador de arquivos, navegue até a pasta `Packages` do projeto do Unity. Abra o arquivo de manifesto do projeto, `manifest.json`, em um editor de texto.
2. Na parte superior do arquivo, no mesmo nível que a seção `dependencies`, adicione a entrada a seguir para incluir o registro de Âncoras Espaciais do Azure no projeto. A entrada `scopedRegistries` informa ao Unity onde procurar os pacotes do SDK de Âncoras Espaciais do Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

### <a name="add-the-sdk-package-to-your-unity-project"></a>Adicionar o pacote do SDK ao projeto do Unity

1. Adicione uma entrada com o nome do pacote do SDK do Windows de Âncoras Espaciais do Azure (`com.microsoft.azure.spatial-anchors-sdk.windows`) e a versão do pacote à seção `dependencies` no manifesto do projeto. Veja um exemplo abaixo.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-20&highlight=12)]

2. Salve e feche o arquivo `manifest.json`. Quando você retornar ao Unity, ele deverá detectar automaticamente a alteração no manifesto do projeto e recuperar os pacotes especificados. Você pode expandir a pasta `Packages` na exibição do Projeto para verificar se os pacotes corretos foram importados.

## <a name="via-unity-asset-package"></a>[Usando o Pacote de Ativos do Unity](#tab/UnityAssetPackage)

> [!WARNING]
> A distribuição do Pacote de Ativos do Unity do SDK de Âncoras Espaciais do Azure será preterida após a versão 2.5.0 do SDK.

Vamos baixar o SDK de Âncoras Espaciais do Azure. Acesse a [página de versões das Âncoras Espaciais do Azure no GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). Em **Ativos**, baixe o arquivo **AzureSpatialAnchors.unitypackage**. No Unity, vá até **Ativos**, selecione **Importar Pacote** > **Pacote Personalizado...** . Navegue até o pacote e selecione **Abrir**.

Na nova janela **Importar Pacote do Unity** exibida, desmarque a opção **Plug-ins** e selecione **Importar** no canto inferior direito.

---

Na solução do **Visual Studio**, adicione a importação a seguir em seu `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Em seguida, adicione as seguintes variáveis de membro na classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Anexar uma Âncora Espacial do Azure à âncora local

Vamos configurar CloudSpatialAnchorSession da Âncora Espacial do Azure. Começaremos adicionando o método `InitializeSession()` a seguir dentro da classe `AzureSpatialAnchorsScript`. Quando chamado, ele garantirá que uma sessão das Âncoras Espaciais do Azure seja criada e inicializada corretamente durante a inicialização do aplicativo.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

Agora, precisamos escrever código para manipular chamadas de delegados. Vamos adicionar mais a eles conforme continuamos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Agora, vamos conectar o método `initializeSession()` ao método `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Por fim, adicione o código a seguir ao método `CreateAndSaveSphere()`. Ele anexará uma âncora espacial do Azure local à esfera que estamos colocando no mundo real.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Antes de avançar, será necessário criar uma conta das Âncoras Espaciais do Azure para obter o Identificador, a Chave e o Domínio da conta. Se ainda não tiver esses valores, siga a próxima seção para obtê-los.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregar sua âncora local na nuvem

Depois que você tiver o identificador, a chave e o domínio da conta das Âncoras Espaciais do Azure, cole `Account Id` em `SpatialAnchorsAccountId`, `Account Key` em `SpatialAnchorsAccountKey` e `Account Domain` em `SpatialAnchorsAccountDomain`.

Por fim, vamos interligar tudo. Em seu método `CreateAndSaveSphere()`, adicione o código a seguir. Ele invocará o método `CreateAnchorAsync()` assim que a esfera for criada. Após o método retornar, o código a seguir atualizará a esfera pela última vez, alterando sua cor para azul.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Execute seu aplicativo do **Visual Studio** mais uma vez. Mova sua cabeça e, em seguida, feche e abra os dedos indicador e polegar para posicionar a esfera. Assim que tivermos quadros suficientes, a esfera ficará amarela e o upload para a nuvem começará. Depois que o upload for concluído, a esfera ficará azul. Opcionalmente, você também pode usar a [janela de Saída](/visualstudio/ide/reference/output-window) durante a depuração no **Visual Studio** para monitorar as mensagens de log que o aplicativo está enviando. Certifique-se de implantar a configuração de `Debug` do seu aplicativo do Visual Studio para ver as mensagens de log. Você pode assistir ao `RecommendedForCreateProgress` e, quando o upload for concluído, poderá ver o identificador de âncora retornado da nuvem.

> [!NOTE]
> Se você receber "DllNotFoundException: Não é possível carregar a DLL 'AzureSpatialAnchors': O módulo especificado não pôde ser encontrado.", você deverá **Limpar** e **Compilar** sua solução novamente.

## <a name="locate-your-cloud-spatial-anchor"></a>Localizar a âncora espacial de nuvem

Após a âncora ser carregada para a nuvem, estamos prontos para tentar localizá-la novamente. Vamos adicionar o código a seguir ao método `HandleTap()`. Esse código vai:

* Chame `ResetSession()`, que interromperá o `CloudSpatialAnchorSession` e removerá nossa esfera azul existente da tela.
* Inicialize `CloudSpatialAnchorSession` novamente. Fazemos isso para garantir que a âncora que vamos localizar vem da nuvem em vez de ser a âncora local que criamos.
* Crie um **Observador** que procurará a âncora que carregamos para as Âncoras Espaciais do Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Agora, vamos adicionar nossos métodos `ResetSession()` e `CleanupObjects()`. Você pode colocá-los abaixo de `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Agora, precisamos conectar o código que será invocado quando a âncora que estamos consultando for localizada. Dentro de `InitializeSession()`, adicione os seguintes retornos de chamada:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Agora adicionaremos código que criará uma esfera verde e a posicionará quando a CloudSpatialAnchor for localizada. Ele também habilitará o toque na tela novamente, portanto, você poderá repetir o cenário completo mais uma vez: criar outra âncora local, fará o upload dela e a localizará novamente.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

É isso! Execute seu aplicativo do **Visual Studio** uma última vez para experimentar o cenário completo de ponta a ponta. Mova seu dispositivo e posicione sua esfera branca. Em seguida, continue movendo sua cabeça para capturar dados do ambiente até que a esfera fique amarela. A âncora local será carregada e a esfera ficará azul. Por fim, toque na tela mais uma vez para remover a âncora local e inicie uma consulta por sua equivalente de nuvem. Continue movendo seu dispositivo até que a âncora espacial de nuvem seja localizada. Uma esfera verde deve aparecer na localização correta e você pode repetir o cenário completo.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]