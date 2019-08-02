---
title: Implantar um conjunto de dimensionamento de máquinas virtuais usando o Visual Studio | Microsoft Docs
description: Implantar um conjunto de escala de máquina virtual usando o Visual Studio e um modelo do Resource Manager
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204709"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um Conjunto de Dimensionamento de Máquinas Virtuais com o Visual Studio
Este artigo mostra como implantar um Conjunto de Escala de Máquina Virtual do Azure usando uma Implantação de Grupo de Recursos do Visual Studio.

Os [Conjuntos de Dimensionamento de Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) são um recurso de Computação do Azure para implantar e gerenciar uma coleção de máquinas virtuais semelhantes com escala automática e balanceamento de carga. É possível provisionar e implantar os Conjuntos de Dimensionamento de Máquinas Virtuais usando os [Modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Os Modelos do Azure Resource Manager podem ser implantados usando a CLI do Azure, o PowerShell, a REST e também diretamente por meio do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, que podem ser implantados como parte de um projeto de Implantação de Grupo de Recursos do Azure.

As implantações de Grupo de Recursos do Azure são uma maneira de agrupar e publicar um conjunto de recursos relacionados do Azure em uma única operação de implantação. Você pode aprender mais sobre eles aqui: [Criar e implantar grupos de recursos do Azure por meio do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Pré-requisitos
Para começar a implantar Conjuntos de Dimensionamento de Máquinas Virtuais no Visual Studio, você precisa do seguinte:

* Visual Studio 2013 ou posterior.
* SDK do Azure 2.7, 2.8 ou 2.9

>[!NOTE]
>Essas instruções pressupõem que você esteja usando o Visual Studio com o [SDK do Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Criando um projeto
1. Crie um novo projeto no Visual Studio escolhendo **Arquivo | Novo | Projeto**.
   
    ![Arquivo novo][file_new]

2. Em **Visual C# | Nuvem**, escolha **Azure Resource Manager** para criar um projeto para implantar um Modelo do Azure Resource Manager.
   
    ![Criar projeto][create_project]

3. Na lista de Modelos, selecione o Modelo de Conjunto de Escala de Máquina Virtual do Windows ou do Linux.
   
   ![Selecionar modelo][select_Template]

4. Depois de criar o projeto, você verá scripts de implantação do PowerShell, um Modelo do Azure Resource Manager e um arquivo de parâmetros para o Conjunto de Dimensionamento de Máquinas Virtuais.
   
    ![Gerenciador de soluções][solution_explorer]

## <a name="customize-your-project"></a>Personalizar seu projeto
Agora você pode editar o Modelo para personalizá-lo de acordo com as necessidades de seu aplicativo, como adicionar propriedades de extensão de VM ou editar regras de balanceamento de carga. Por padrão, os Modelos do Conjunto de Dimensionamento de Máquinas Virtuais são configurados para implantar a extensão AzureDiagnostics, que facilita a adição de regras de escala automática. Ela também implanta um balanceador de carga com um endereço IP público, configurado com regras NAT de entrada. 

O balanceador de carga permite que você se conecte às instâncias da VM com o SSH (Linux) ou o RDP (Windows). O intervalo de portas de front-end começa em 50000. Para o Linux, isso significa que, se você usar o SSH para a porta 50000, será roteado para a porta 22 da primeira VM no Conjunto de Dimensionamento. A conexão à porta 50001 é roteada para a porta 22 da segunda VM e assim por diante.

 Uma boa maneira de editar os Modelos com o Visual Studio é usar a Estrutura de Tópicos JSON para organizar os parâmetros, as variáveis e os recursos. Com uma compreensão do esquema do Visual Studio, é possível detectar erros em seu Modelo antes de implantá-lo.

![Gerenciador JSON][json_explorer]

## <a name="deploy-the-project"></a>Implantar o projeto
1. Implante o Modelo do Azure Resource Manager para criar o recurso Conjunto de Dimensionamento de Máquinas Virtuais. Clique com o botão direito do mouse no nó do projeto e escolha **Implantar | Nova Implantação**.
   
    ![Implantar o modelo][5deploy_Template]
    
2. Selecione sua assinatura no diálogo “Implantar no Grupo de Recursos”.
   
    ![Implantar o modelo][6deploy_Template]

3. Aqui, é possível criar um Grupo de Recursos do Azure no qual o Modelo será implantado.
   
    ![Novo grupo de recursos][new_resource]

4. Em seguida, clique em **Editar Parâmetros** para inserir parâmetros que são passados para o Modelo. Forneça o nome de usuário e a senha do sistema operacional, que é necessária para criar a implantação. Se não tiver as Ferramentas PowerShell para Visual Studio instaladas, será recomendável marcar **Salvar senhas** para evitar um prompt oculto de linha de comando do PowerShell ou usar o [suporte do keyvault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Editar Parâmetros][edit_parameters]

5. Agora clique em **Implantar**. A janela **Saída** mostra o progresso da implantação. Observe que a ação executa o script **Deploy-AzureResourceGroup.ps1**.
   
   ![Janela de saída][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Explorando o Conjunto de Dimensionamento de Máquinas Virtuais
Depois que a implantação for concluída, é possível exibir o novo Conjunto de Dimensionamento de Máquinas Virtuais no **Cloud Explorer** do Visual Studio (basta atualizar a lista). O Gerenciador de Nuvem permite que você gerencie recursos do Azure no Visual Studio ao mesmo tempo que desenvolve aplicativos. Você também pode exibir o Conjunto de Dimensionamento de Máquinas Virtuais no [portal do Azure](https://portal.azure.com) e no [Gerenciador de Recursos do Azure](https://resources.azure.com/).

![Gerenciador de Nuvem][cloud_explorer]

 O portal fornece a melhor maneira de gerenciar visualmente sua infraestrutura do Azure com um navegador da Web, enquanto o Gerenciador de Recursos do Azure oferece uma maneira fácil de explorar e depurar recursos do Azure, proporcionando uma janela para a “exibição de instância”, além de mostrar comandos do PowerShell para os recursos que você está examinando.

## <a name="next-steps"></a>Próximas etapas
Depois de implantar os Conjuntos de Dimensionamento de Máquinas Virtuais com êxito por meio do Visual Studio, é possível personalizar o projeto ainda mais para atender às necessidades do aplicativo. Por exemplo, configurar a escala automática adicionando um recurso do **Insights**, adicionar a infraestrutura ao Modelo (como VMs independentes) ou implantar aplicativos usando a extensão de script personalizado. Encontre bons modelos de exemplo no repositório GitHub de [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) (pesquise “vmss”).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
