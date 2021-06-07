---
title: Renderizar um modelo com o Unity
description: Início rápido que orienta o usuário pelas etapas para renderizar um modelo
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 3f565f456dde1d802a82faffb4a23f7a6e54d950
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031538"
---
# <a name="quickstart-render-a-model-with-unity"></a>Início Rápido: Renderizar um modelo com o Unity

Este início rápido aborda como executar um exemplo do Unity que renderiza um modelo interno remotamente usando o serviço do ARR (Azure Remote Rendering).

Não entraremos em detalhes sobre a própria API do ARR ou como configurar um novo projeto do Unity. Esses tópicos são abordados no [Tutorial: Como exibir modelos renderizados remotamente](../tutorials/unity/view-remote-models/view-remote-models.md).

Neste início rápido, você aprenderá a:
> [!div class="checklist"]
>
>* Configurar seu ambiente de desenvolvimento
>* Obter e criar o aplicativo de exemplo do Início Rápido do ARR para o Unity
>* Renderizar um modelo no aplicativo de exemplo do Início Rápido do ARR

## <a name="prerequisites"></a>Pré-requisitos

Para obter acesso ao serviço do Azure Remote Rendering, primeiro você precisa [criar uma conta](../how-tos/create-an-account.md).

O seguinte software deve ser instalado:

* SDK do Windows 10.0.18362.0 [(baixar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(baixar)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Ferramentas do Visual Studio para Realidade Misturada](/windows/mixed-reality/install-the-tools). Especificamente, as seguintes instalações de *carga de trabalho* são obrigatórias:
  * **Desenvolvimento para desktop com C++**
  * **Desenvolvimento da UWP (Plataforma Universal do Windows)**
* GIT [(baixar)](https://git-scm.com/downloads)
* Unity (confira [requisitos do sistema](../overview/system-requirements.md#unity) para obter as versões compatíveis)

## <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Abra um prompt de comando (digite `cmd` no menu iniciar do Windows) e mude para um diretório no qual é interessante armazenar o projeto de exemplo do ARR.

Execute os seguintes comandos:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
powershell azure-remote-rendering\Scripts\DownloadUnityPackages.ps1
```

O último comando cria um subdiretório no diretório do ARR que contém os vários projetos de exemplo para o Azure Remote Rendering.

O aplicativo de exemplo de início rápido para Unity é encontrado no subdiretório *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Renderizar um modelo com o projeto de exemplo do Unity

Abra o Hub do Unity e adicione o projeto de exemplo, que é a pasta *ARR\azure-remote-rendering\Unity\Quickstart*.
Abra o projeto. Se necessário, permita que o Unity atualize o projeto para a versão instalada.

O modelo padrão que renderizamos é um [modelo de exemplo interno](../samples/sample-model.md). Mostraremos como converter um modelo personalizado usando o serviço de conversão do ARR no [próximo início rápido](convert-model.md).

### <a name="enter-your-account-info"></a>Inserir suas informações de conta

1. No navegador de ativos do Unity, navegue até a pasta *Cenas* e abra a cena **Início Rápido**.
1. Na *Hierarquia*, selecione o objeto de jogo **RemoteRendering**.
1. No *Inspetor*, insira suas [credenciais de conta](../how-tos/create-an-account.md). Se ainda não tiver uma conta, [crie uma](../how-tos/create-an-account.md).

![Informações de Conta do ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Defina **RemoteRenderingDomain** como `<region>.mixedreality.azure.com`, em que `<region>` é [uma das regiões disponíveis perto de você](../reference/regions.md).\
> Defina **AccountDomain** como [domínio de contas](../how-tos/create-an-account.md#retrieve-the-account-information), conforme exibido no portal do Azure.

Posteriormente, convém implantar esse projeto em um HoloLens e conectar-ao serviço do Remote Rendering nesse dispositivo. Como não temos uma forma fácil de inserir as credenciais no dispositivo, o exemplo de início rápido **salvará as credenciais na cena do Unity**.

> [!WARNING]
> Não verifique o projeto com suas credenciais salvas em algum repositório no qual poderia haver vazamento de informações de logon secretas!

### <a name="create-a-session-and-view-the-default-model"></a>Criar uma sessão e ver o modelo padrão

Pressione o botão **Reproduzir** do Unity para iniciar a sessão. Você deve ver uma sobreposição com texto de status na parte inferior do visor no painel *Jogo*. A sessão passará por uma série de transições de estado. No estado **Inicial**, o servidor é criado, o que leva vários minutos. Após o êxito, ela faz a transição para o estado **Pronto**. Agora, a sessão entra no estado **Conectando**, em que ela tenta atingir o runtime de renderização nesse servidor. Quando bem-sucedido, o exemplo faz a transição para o estado **Conectado**. Neste ponto, ele começará a baixar o modelo para renderização. Devido ao tamanho do modelo, o download pode levar alguns minutos. Em seguida, o modelo renderizado remotamente será exibido.

![Saída do exemplo](media/arr-sample-output.png)

Parabéns! Agora você está exibindo um modelo processado remotamente!

## <a name="inspecting-the-scene"></a>Inspecionar a cena

Após a execução da conexão da renderização remota, o painel Inspetor será atualizado com informações de status adicionais: ![Reprodução de exemplo do Unity](./media/arr-sample-configure-session-running.png)

Agora você pode explorar o grafo de cena selecionando o novo nó e clicando em **Mostrar filhos** no Inspetor.

![Hierarquia do Unity](./media/unity-hierarchy.png)

Há um objeto de [plano de curte](../overview/features/cut-planes.md) na cena. Tente habilitá-lo nas propriedades dele e movê-lo ao redor:

![Alterar o plano de corte](media/arr-sample-unity-cutplane.png)

Para sincronizar as transformações, clique em **Sincronizar agora** ou marque a opção **Sincronizar todos os quadros**. Para as propriedades do componente, alterá-las é o suficiente.

## <a name="next-steps"></a>Próximas etapas

No próximo início rápido, implantaremos o exemplo em um HoloLens para ver o modelo renderizado remotamente no tamanho original.

> [!div class="nextstepaction"]
> [Início Rápido: Implantar o exemplo do Unity no HoloLens](deploy-to-hololens.md)

Como alternativa, o exemplo também pode ser implantado em um PC desktop.

> [!div class="nextstepaction"]
> [Início Rápido: Implantar o exemplo do Unity no Desktop](deploy-to-desktop.md)