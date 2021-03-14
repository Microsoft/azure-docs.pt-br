---
title: 'Início rápido: criar um aplicativo do HoloLens com o Unity e o MRTK'
description: Neste início rápido, você aprenderá a criar um aplicativo do HoloLens no Unity usando o MRTK e as Âncoras de Objeto.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049675"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Início rápido: criar um aplicativo do HoloLens com as Âncoras de Objeto do Azure, no Unity com o MRTK

Neste início rápido, você criará um aplicativo do HoloLens no Unity que usa as [Âncoras de Objeto do Azure](../overview.md). As Âncoras de Objeto do Azure são um serviço de nuvem gerenciado que converte ativos 3D em modelos de IA que habilitam experiências de realidade misturada com reconhecimento de objeto para o HoloLens. Quando tiver terminado, você terá um aplicativo do HoloLens criado com o Unity que poderá detectar objetos no mundo físico.

Você aprenderá a:

> [!div class="checklist"]
> * Preparar as configurações de criação do Unity.
> * Exportar o projeto do Visual Studio do HoloLens.
> * Implantar o aplicativo e executá-lo em um dispositivo do HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

No Unity, abra o projeto `quickstarts/apps/unity/mrtk`.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Quando uma caixa de diálogo "Importador TMP" solicitar que você importe recursos do TextMesh Pro, selecione "Importar TMP Essentials" para fazer isso.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importar recursos do TextMesh Pro":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Ative o dispositivo, selecione **Todos os aplicativos** e depois localize e inicie o aplicativo. Após a tela inicial do Unity, você deverá ver uma caixa delimitadora branca. Você pode usar as mãos para mover, redimensionar ou girar a caixa delimitadora. Posicione a caixa de modo a cobrir o objeto que você deseja detectar.

Abra o <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu manual</a> e selecione **Bloquear SearchArea** para evitar movimentações adicionais da caixa delimitadora. Selecione **Iniciar pesquisa** para iniciar a detecção de objetos. Quando o objeto for detectado, a malha será renderizada sobre o objeto. Os detalhes de uma instância detectada serão mostrados na tela, como o carimbo de data/hora atualizado e a taxa de cobertura da superfície. Selecione **Interromper pesquisa** para interromper o acompanhamento e todas as instâncias detectadas serão removidas.

#### <a name="the-app-menus"></a>Os menus do aplicativo

Você também pode executar outras ações usando o <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu manual</a>.

##### <a name="primary-menu"></a>Menu principal

* **Iniciar pesquisa/Interromper pesquisa** – Inicia ou interrompe o processo de detecção de objetos.
* **Alternar a exibição do mapeamento espacial** – Mostra/oculta a renderização do mapeamento espacial. Essa opção pode ser usada para depurar se o exame está concluído ou não.
* **Configurações do rastreador** – Alterna a ativação do menu de configurações do rastreador.
* **Configurações da área de pesquisa** – Alterna a ativação do menu de configurações da área de pesquisa.
* **Iniciar rastreamento** – Captura dados de diagnóstico e os salva no dispositivo. Veja mais detalhes na seção **Problemas de detecção na depuração e diagnóstico de captura**.
* **Carregar rastreamento** – Carrega dados de diagnóstico para o serviço de Âncoras de Objeto. Um usuário deve fornecer sua conta de assinatura no `subscription.json` e carregá-la na pasta `LocalState`. Um arquivo de exemplo `subscription.json` pode ser encontrado abaixo.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Menu manual principal do Unity":::

##### <a name="tracker-settings-menu"></a>Menu de configurações do rastreador

* **Alta precisão** – Um recurso experimental usado para obter uma pose mais precisa. Habilitar essa opção exigirá mais recursos de sistema durante a detecção de objetos. A malha de objetos será renderizada em rosa quando estiver nesse modo. Selecione este botão novamente para voltar ao modo de rastreamento normal.
* **Alinhamento vertical relaxado** – Quando habilitado, permite que um objeto seja detectado em um ângulo não vertical. Útil para detectar objetos em rampas.
* **Permitir alteração de escala** – Permite que o rastreador altere o tamanho do objeto detectado com base em informações do ambiente.
* **Controle deslizante da taxa de cobertura** – Ajusta a proporção de pontos de superfície que devem corresponder ao rastreador para detectar um objeto.  Valores mais baixos permitem que o rastreador detecte melhor objetos que são desafiadores para a detecção dos sensores do HoloLens, como objetos escuros ou altamente reflexíveis. Valores mais altos reduzirão a frequência de detecções falsas.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Menu manual e rastreador do Unity":::

##### <a name="search-area-settings-menu"></a>Menu de configurações da área de pesquisa

* **Bloquear área de pesquisa** – Bloqueie a área da caixa delimitadora para evitar a movimentação acidental com as mãos.
* **Ajustar a área de pesquisa automaticamente** – Permite que a área de pesquisa se reposicione sozinha durante a detecção de objetos.
* **Percorrer a malha** – Percorre a visualização das malhas carregadas dentro da área de pesquisa.  Essa opção pode ajudar os usuários a alinharem a caixa de pesquisa dos objetos difíceis de detectar.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Menu manual da área de pesquisa do Unity":::

Exemplo `subscription.json`:

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceitos: visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [perguntas frequentes](../faq.md)
