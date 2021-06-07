---
title: Tutorial – atualizar um aplicativo de malha de Service Fabric do Azure
description: Este tutorial é a parte quatro de uma série e mostra como atualizar um aplicativo do Azure Service Fabric Mesh diretamente do Visual Studio.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625425"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Aprenda como atualizar um aplicativo do Service Fabric usando o Visual Studio

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a parte quatro de uma série e mostra como atualizar um aplicativo do Azure Service Fabric Mesh diretamente do Visual Studio. A atualização incluirá uma atualização de código e uma atualização de configuração. Você verá que as etapas para fazer upgrade e publicação no Visual Studio são as mesmas.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Atualizar um serviço de malha de malha de serviço usando o Visual Studio

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo da Malha do Service Fabric no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar um aplicativo da Malha do Service Fabric em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implantar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uma malha de malha do serviço de atualização do aplicativo
> * [Limpar os recursos da Malha do Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver implantado o aplicativo de tarefas, siga as instruções em [Publicar um aplicativo Web da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualizar um serviço de malha de malha de serviço usando o Visual Studio

Este artigo mostra como fazer upgrade de um microsserviço em um aplicativo. Neste exemplo, vamos modificar o serviço `WebFrontEnd` para exibir uma categoria da tarefa e aumentar a quantidade de CPU que é fornecido. Em seguida, atualizaremos o serviço implantado.

## <a name="modify-the-config"></a>Modificar a configuração

Quando você cria um aplicativo de Malha do Service Fabric, o Visual Studio adiciona um arquivo **parameters.yaml** para cada ambiente de implantação (nuvem e local). Nesses arquivos, é possível definir parâmetros e seus valores que podem ser referenciados nos arquivos *.yaml da malha como service.yaml ou network.yaml.  O Visual Studio fornece algumas variáveis para você, tais como a quantidade de CPU que o serviço pode usar.

Vamos atualizar o parâmetro `WebFrontEnd_cpu` para atualizar os recursos de CPU para `1.5`, prevendo que o serviço **WebFrontEnd** será usado mais intensamente.

1. No projeto **todolistapp**, em **Ambientes** > **Nuvem**, abra o arquivo **parameters.yaml**. Modifique o valor `WebFrontEnd_cpu` para `1.5`. O nome do parâmetro será precedido com o nome do serviço `WebFrontEnd_` como uma melhor prática para distingui-lo dos parâmetros de mesmo nome que se aplicam a serviços diferentes.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Abra o arquivo **service.yaml** do projeto **WebFrontEnd** em **WebFrontEnd** > **Recursos do Serviço**.

    Observe que na seção `resources:`, `cpu:` é definido como `"[parameters('WebFrontEnd_cpu')]"`. Se o projeto estiver sendo compilado para a nuvem, o valor para `'WebFrontEnd_cpu` será obtido do   >    >  arquivo **Parameters** de nuvem de ambientes. YAML e será `1.5` . Se o projeto estiver sendo criado para ser executado localmente, o valor será obtido do   >    >  arquivo **Parameters. YAML** de ambientes locais e será ' 0,5 '.

> [!Tip]
> Por padrão, o arquivo de parâmetros que é um par do arquivo profile.yaml será usado para fornecer os valores para esse arquivo profile.yaml.
> Por exemplo, Ambientes > Nuvem > parameters.yaml fornece os valores do parâmetro para Ambientes > Nuvem > profile.yaml.
>
> Você pode substituir isso adicionando o seguinte ao arquivo Profile. YAML: `parametersFilePath=”relative or full path to the parameters file”` por exemplo, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma mudança de código, adicione uma propriedade `Category` à classe `ToDoItem` no arquivo `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Em seguida, atualize o método `Load()`, no mesmo arquivo, para definir a categoria como uma string padrão:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modificar o serviço

O projeto `WebFrontEnd` é um aplicativo ASP.NET Core com uma página da Web que mostra itens da lista de tarefas pendentes. No projeto `WebFrontEnd`, abra `Index.cshtml` e adicione as seguintes duas linhas, indicadas abaixo, para exibir a categoria da tarefa:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Crie e execute o aplicativo para verificar se você vê uma nova coluna de categoria na página da Web que lista as tarefas.

## <a name="upgrade-the-app-from-visual-studio"></a>Atualizar o aplicativo do Visual Studio

Se você estiver fazendo um upgrade de código ou um upgrade de configuração (nesse caso, estamos fazendo ambos), faça o upgrade do aplicativo de Malha do Service Fabric no Azure clicando com o botão direito do mouse em **todolistapp** no Visual Studio e, em seguida, selecione **Publicar...**

A seguir, você verá uma caixa de diálogo **Publicar aplicativo do Service Fabric**.

Use a lista suspensa **Perfil de destino** para selecionar o arquivo profile.yaml a ser usado para essa implantação. Estamos fazendo upgrade do aplicativo na nuvem, então selecionamos **cloud.yaml** na lista suspensa, que usará o valor `WebFrontEnd_cpu` de 1.0 definido nesse arquivo.

![Caixa de diálogo de publicação de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Escolha a conta e assinatura do Azure. Defina o **Local** como o local que você usou quando publicou originalmente o aplicativo de tarefas pendentes no Azure. Este artigo usado **Leste dos EUA**.

Defina o **Grupo de recursos** como o grupo de recursos que você usou quando publicou originalmente o aplicativo de tarefas no Azure.

Defina o **Registro de Contêiner do Azure** como o nome do registro de contêiner do Azure que você criou quando publicou originalmente o aplicativo de tarefas pendentes no Azure.

Na caixa de diálogo de publicação, pressione o botão **Publicar** para atualizar o aplicativo de tarefas no Azure.

É possível monitorar o progresso do upgrade, selecionando o painel **Ferramentas do Service Fabric** na janela **Saída** do Visual Studio. 

Depois que a imagem é compilada e enviada por push para o Registro de Contêiner do Azure, um link **Para status** será exibido na saída, onde você pode clicar para monitorar a implantação no portal do Azure.

Quando a atualização for concluída, a saída **Service Fabric Tools** exibirá o endereço IP e a porta do seu aplicativo na forma de uma URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um navegador da Web e navegue até a URL para ver o site em execução no Azure. Agora você deve ver uma página da web que contém uma coluna de categoria.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Como atualizar um aplicativo de serviço do Microsoft Azure Service Fabric usando o Visual Studio

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Limpar os recursos da Malha do Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)