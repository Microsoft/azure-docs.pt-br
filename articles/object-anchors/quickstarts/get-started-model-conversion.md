---
title: 'Início Rápido: criar um modelo das Âncoras de Objeto a ser usado em um aplicativo'
description: Neste início rápido, você aprenderá a criar um modelo das Âncoras de Objeto com base em um modelo 3D.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607893"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Início Rápido: criar um modelo das Âncoras de Objeto com base em um modelo 3D

As Âncoras de Objeto do Azure são um serviço de nuvem gerenciado que converte modelos 3D em modelos de IA que habilitam experiências de realidade misturada com reconhecimento de objeto para o HoloLens. Este início rápido aborda como criar um modelo das Âncoras de Objeto com base em um modelo 3D usando o SDK do .NET Core/C#.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta das Âncoras de Objeto
> * Converter um modelo 3D para criar um modelo das Âncoras de Objeto

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

* Um computador Windows com o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git para Windows</a>.
* O <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">SDK do .NET Core 3.1</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Criar uma conta das Âncoras de Objeto

Primeiro, você precisa criar uma conta com o serviço Âncoras de Objeto.

1. Vá para o [portal do Azure](https://portal.azure.com/) e selecione **Criar um recurso**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Criar um novo recurso":::

2. Pesquise o recurso **Âncoras de Objeto**.

   Pesquise “Âncoras de Objeto”.

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Selecionar o recurso Âncoras de Objeto":::

   No recurso **Âncoras de Objeto** nos resultados da pesquisa, selecione **Criar –> Âncoras de Objeto**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Criar um recurso Âncoras de Objeto":::

3. Na caixa de diálogo **Conta das Âncoras de Objeto**:
    * Insira um nome de recurso exclusivo.
    * Selecione a assinatura à qual você deseja anexar o recurso.
    * Crie ou use um grupo de recursos existente.
    * Selecione a região na qual você deseja que o recurso exista.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Inserir detalhes da conta do recurso Âncoras de Objeto":::

    Selecione **Criar** para começar a criar o recurso.

4. Depois de criar o recurso, selecione **Ir para o recurso**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Acessar recurso":::

5. Na página de visão geral:

   Anote o **Domínio da conta**. Você precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Copie o domínio da conta do seu recurso Âncoras de Objeto":::

   Anote a **ID da conta**. Você precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Copiar a ID da conta do recurso Âncoras de Objeto":::

   Vá para a página **Chaves de Acesso** e anote a **Chave primária**. Você precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Copiar a chave de conta para o recurso Âncoras de Objeto":::

## <a name="get-the-sample-project"></a>Obter o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Converter um modelo 3D

Agora você pode converter seu modelo 3D.

1. Abra o `quickstarts/conversion/Conversion.sln` no Visual Studio. Essa solução contém um projeto de console em C#.

2. Abra o arquivo `Configuration.cs` localizado na raiz do projeto e substitua os valores `set-me` nos seguintes campos:

   | Campo         | Descrição                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | O **Domínio da Conta** da conta das Âncoras de Objeto criada acima. |
   | AccountId     | A **ID da Conta** da conta das Âncoras de Objeto criada acima.     |
   | AccountKey    | A **Chave primária** da conta das Âncoras de Objeto criada acima     |

   Há quatro campos adicionais que precisam ser verificados:

    | Campo                    | Descrição                       |
    | ---                      | ---                               |
    | InputAssetPath           | O caminho absoluto para um modelo 3D em seu computador local. Os formatos de arquivo com suporte são `fbx`, `ply`, `obj`, `glb` e `gltf`. |
    | AssetDimensionUnit       | A unidade de medida do modelo 3D. Todas as unidades de medição com suporte podem ser acessadas por meio da enumeração `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit`. |
    | Gravidade                  | A direção do vetor de gravidade do modelo 3D. Esse vetor 3D fornece a direção para baixo no sistema de coordenadas do seu modelo. Por exemplo, se um `y` negativo representa a direção para baixo no espaço 3D do modelo, esse valor é `Vector3(0.0f, -1.0f, 0.0f)`. |

3. Compile e execute o projeto para carregar seu modelo 3D, registre um novo trabalho de conversão no serviço e aguarde sua conclusão. Após a conclusão do trabalho, o modelo das Âncoras de Objeto será baixado ao lado do arquivo especificado em `InputAssetPath`. Você deverá ver algo semelhante à seguinte saída do console:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Anote a **ID do Trabalho** para futura referência. Ela pode ser útil na depuração ou solução de problemas.

4. Quando o trabalho for concluído com êxito, você deverá ver um arquivo com o formato `<Model-Filename-Without-Extension>_<JobID>.ou` no local de saída especificado. Por exemplo se o nome do arquivo do modelo 3D for `chair.ply` e a ID do Trabalho for `00000000-0000-0000-0000-000000000000` no nome do arquivo que o serviço gerará será `chair_00000000-0000-0000-0000-000000000000.ou`.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma conta das Âncoras de Objeto e converteu um modelo 3D para criar um modelo das Âncoras de Objeto. Para saber como integrar esse modelo ao SDK das Âncoras de Objeto em seu aplicativo de realidade misturada, prossiga em qualquer um dos seguintes artigos:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens com MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
