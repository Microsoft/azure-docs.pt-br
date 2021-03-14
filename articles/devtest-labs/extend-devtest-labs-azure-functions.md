---
title: Estender Azure DevTest Labs usando Azure Functions | Microsoft Docs
description: Saiba como estender Azure DevTest Labs usando Azure Functions.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 620cda83094ee65f421a5529a9d5b51e505ec48e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501151"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Usar o Azure Functions para estender o DevTest Labs
Você pode usar Azure Functions para dar suporte a cenários adicionais além daqueles que já têm suporte do DevTest Labs. Azure Functions pode ser usado para estender a funcionalidade interna do serviço para atender às suas necessidades específicas de negócios. A lista a seguir fornece alguns dos possíveis cenários. Este artigo mostra como implementar um desses cenários de exemplo.

- Fornecer um resumo de nível superior de VMs (máquinas virtuais) no laboratório
- [Configurar um laboratório para usar um gateway de área de trabalho remota](configure-lab-remote-desktop-gateway.md)
- Relatórios de conformidade na página de suporte interna
- Permitir que os usuários concluam operações que exigem permissões maiores na assinatura
- [Iniciando fluxos de trabalho com base em eventos do DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Visão geral
[Azure Functions](../azure-functions/functions-overview.md) é uma plataforma de computação sem servidor no Azure. O uso de Azure Functions em uma solução com o DevTest Labs nos permite aumentar os recursos existentes com nosso próprio código personalizado. Para obter mais informações sobre Azure Functions, consulte [Azure Functions documentação](../azure-functions/functions-overview.md). Para ilustrar como Azure Functions pode ajudar a atender seus requisitos ou cenários completos no DevTest Labs, este artigo usa um exemplo de como fornecer um resumo de nível superior das VMs no laboratório da seguinte maneira:

**Requisito/cenário de exemplo**: os usuários podem ver detalhes sobre todas as VMs em um laboratório, incluindo o sistema operacional, o proprietário e os artefatos aplicados.  Além disso, se o artefato **aplicar atualizações do Windows** não tiver sido aplicado recentemente, haverá uma maneira fácil de aplicá-lo.

Para concluir o cenário, você usará duas funções, conforme descrito no diagrama a seguir:  

![Fluxo geral](./media/extend-devtest-labs-azure-functions/flow.png)

O código-fonte para essas funções de exemplo está localizado no [repositório GitHub do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (implementações do C# e do PowerShell estão disponíveis).

- **UpdateInternalSupportPage**: essa função consulta o DevTest Labs e atualiza a página de suporte interna diretamente com detalhes sobre as máquinas virtuais.
- **ApplyWindowsUpdateArtifact**: para uma VM em um laboratório, essa função aplica o artefato do **Windows Update** .

## <a name="how-it-works"></a>Como ele funciona
Quando os usuários selecionam a página de **suporte interno** no DevTest Labs, eles têm uma página preenchida previamente com informações sobre VMS, proprietários de laboratório e contatos de suporte.  

Quando você seleciona o botão **selecionar aqui para atualizar** , a página chama a primeira função do Azure: **UpdateInternalSupportPage**. A função consulta o DevTest Labs para obter informações e, em seguida, reescreve a página de **suporte interna** com as novas informações.

Há uma ação adicional que pode ser executada, para todas as VMs nas quais os artefatos de Windows Update não foram aplicados recentemente, haverá um botão para aplicar as atualizações do Windows à VM. Quando você seleciona o botão ***executar o Windows Update** para uma VM, a página chama a segunda função do Azure: **ApplyWindowsUpdateArtifact**. Essa função verifica se a máquina virtual está em execução e, nesse caso, aplica o artefato de [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) diretamente.

## <a name="step-by-step-walkthrough"></a>Instruções passo a passo
Esta seção fornece instruções passo a passo para configurar os recursos do Azure necessários para atualizar a página de **suporte interna** . Este tutorial fornece um exemplo de extensão do DevTest Labs. Você pode usar esse padrão para outros cenários.

### <a name="step-1-create-a-service-principal"></a>Etapa 1: criar uma entidade de serviço 
A primeira etapa é obter uma entidade de serviço com permissão para a assinatura que contém o laboratório. A entidade de serviço deve usar a autenticação baseada em senha. Isso pode ser feito com [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)ou o [portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md). Se você já tiver uma entidade de serviço para usar, poderá ignorar esta etapa.

Anote a **ID do aplicativo**, a **chave** e a ID do **locatário** para a entidade de serviço. Você precisará delas mais tarde neste passo a passos. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Etapa 2: baixar o exemplo e abri-lo no Visual Studio 2019
Baixe uma cópia do [C# Azure Functions amostra](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (seja clonando o repositório ou baixando o repositório [aqui](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Abra a solução de exemplo com o Visual Studio 2019.  
1. Instale a carga de trabalho de **desenvolvimento do Azure** para o Visual Studio se você ainda não o tiver instalado. Ele pode ser instalado por meio das **ferramentas**  ->  **obter item de menu ferramentas e recursos** ).

    ![Carga de trabalho de desenvolvimento do Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compile a solução. Selecione **Compilar** e criar item de menu de **solução** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Etapa 3: implantar o exemplo no Azure
No Visual Studio, na janela **Gerenciador de soluções** , clique com o botão direito do mouse no projeto **AzureFunctions** e selecione **publicar**. Siga o assistente para concluir a publicação em um novo ou em um Aplicativo de funções do Azure existente. Para obter informações detalhadas sobre como desenvolver e implantar o Azure Functions usando o Visual Studio, consulte [desenvolver Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md).

![Caixa de diálogo Publicar](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Etapa 4: coletar configurações do aplicativo
Depois que as funções são publicadas, você precisa obter URLs para essas funções do portal do Azure. 

1. Navegue até o [Portal do Azure](https://portal.azure.com). 
1. Localize o aplicativo de funções.
1. Na página **aplicativos de funções** , selecione a função. 
1. Selecione **obter URL da função** , conforme mostrado na imagem a seguir. 

    ![URLs do Azure Functions](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copie e salve a URL. Repita essas etapas para a outra função do Azure. 

Você também precisará de informações adicionais sobre a entidade de serviço, como ID do aplicativo, chave e ID do locatário.


### <a name="step-5--update-application-settings"></a>Etapa 5: atualizar as configurações do aplicativo
No Visual Studio, depois de publicar a função do Azure, selecione **editar Azure app configurações de serviço** em **ações**. Atualize as seguintes configurações do aplicativo (remoto):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (padrão para 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Configurações do aplicativo](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Etapa 6: testar a função do Azure
A última etapa neste passo a passo é testar a função do Azure.  

1. Navegue até a função **UpdateInternalSupportPage** no aplicativo de funções criado na etapa 3. 
1. Selecione **teste** no lado direito da página. 
1. Insira nas propriedades de rota (LABNAME, RESOURCEGROUPNAME e SUBSCRIPTIONid).
1. Selecione **executar** para executar a função.  

    Esta função atualizará a página de suporte interna do laboratório especificado. Ele também inclui um botão para que os usuários chamem diretamente a função da próxima vez

    ![Função de teste](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Próximas etapas
Azure Functions pode ajudar a estender a funcionalidade do DevTest Labs além do que já está interno e ajudar os clientes a atender seus requisitos exclusivos para suas equipes. Esse padrão pode ser estendido & expandido ainda mais para abranger ainda mais.  Para saber mais sobre o DevTest Labs, consulte os seguintes artigos: 

- [Arquitetura de referência empresarial do DevTest Labs](devtest-lab-reference-architecture.md)
- [Perguntas frequentes](devtest-lab-faq.md)
- [Dimensionamento do DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatizando o DevTest Labs com o PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








