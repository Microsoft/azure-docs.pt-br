---
title: Usar Serviços de Nuvem (suporte estendido) (versão prévia)
description: Saiba como criar e implantar um Serviço de Nuvem do Azure usando o Azure Resource Manager com o Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722663"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Criar e implantar um Serviço de Nuvem do Azure (suporte estendido) usando o Visual Studio

Do [Visual Studio 2019 versão 16.9](https://visualstudio.microsoft.com/vs/preview/) (atualmente em versão prévia) em diante, você pode trabalhar com os serviços de nuvem usando o ARM (Azure Resource Manager), que simplifica e moderniza bastante a manutenção e o gerenciamento de recursos do Azure. Isso é habilitado por um novo serviço do Azure denominado Serviços de Nuvem (suporte estendido). Você pode publicar um serviço de nuvem existente nos Serviços de Nuvem (suporte estendido). Para obter informações sobre esse serviço do Azure, confira [Documentação dos Serviços de Nuvem (suporte estendido)](overview.md).

> [!IMPORTANT]
> No momento, os Serviços de Nuvem (suporte estendido) estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrar recurso para sua assinatura
No momento, os Serviços de Nuvem (suporte estendido) estão em versão prévia. Registrar recurso para sua assinatura como a seguir:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Para obter mais informações, confira [Pré-requisitos para implantar os Serviços de Nuvem (suporte estendido)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Criar um projeto

O Visual Studio fornece um modelo de projeto que permite criar um Serviço de Nuvem do Azure com suporte estendido, chamado de **Serviço de Nuvem do Azure (suporte estendido)** . Um Serviço de Nuvem é um serviço do Azure de uso geral simples. Após a criação do projeto, o Visual Studio permite configurar, depurar e implantar o Serviço de Nuvem no Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Para criar um projeto do Serviço de Nuvem do Azure (suporte estendido) no Visual Studio

Esta seção explica como criar um projeto do Serviço de Nuvem do Azure no Visual Studio com uma ou mais funções Web.

1. Na janela de início, escolha **Criar um novo projeto**.

1. Na caixa de pesquisa, insira *Nuvem* e escolha **Serviço de Nuvem do Azure (suporte estendido)** .

   ![Novo Serviço de Nuvem do Azure com suporte estendido](./media/choose-project-template.png)

1. Dê um nome ao projeto e escolha **Criar**.

   ![Dê um nome ao projeto](./media/configure-new-project.png)

1. Na caixa de diálogo **Novo Serviço de Nuvem do Microsoft Azure**, selecione as funções que você deseja adicionar e escolha o botão de seta para a direita para adicioná-las à solução.

    ![Selecionar novas funções do Serviço de Nuvem do Azure](./media/choose-roles.png)

1. Para renomear uma função adicionada, focalize a função na caixa de diálogo **Novo Serviço de Nuvem do Microsoft Azure** e, no menu de contexto, selecione **Renomear**. Você também pode renomear uma função na solução (no **Gerenciador de Soluções**) depois de adicioná-la.

    ![Renomear função do Serviço de Nuvem do Azure](./media/new-cloud-service-rename.png)

O projeto do Azure no Visual Studio tem associações aos projetos de função na solução. Ele também inclui o *arquivo de definição de serviço* e o *arquivo de configuração de serviço*:

- **Arquivo de definição de serviço** – define as configurações em tempo de execução do aplicativo, incluindo quais funções são necessárias, pontos de extremidade e tamanho da máquina virtual.
- **Arquivo de configuração de serviço** – configura quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função.

Para obter mais informações sobre esses arquivos, confira [Configurar as funções para um Serviço de Nuvem do Azure com o Visual Studio](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publicar um Serviço de Nuvem

1. Crie ou abra um projeto de Serviço de Nuvem do Azure no Visual Studio.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Publicar**.

   ![Página de entrada](./media/publish-step-1.png)

1. **Conta** - selecione uma conta ou **Adicionar uma conta** na lista suspensa de contas.

1. **Escolha sua assinatura** - escolha a assinatura a ser usada na sua implantação. A assinatura usada para implantar as necessidades dos Serviços de Nuvem (suporte estendido) precisa ter as funções Proprietário ou Colaborador atribuídas por meio do RBAC (controle de acesso baseado em função). Se a sua assinatura não tiver nenhuma dessas funções, confira [Etapas para adicionar uma atribuição de função](../role-based-access-control/role-assignments-steps.md) para adicionar isso antes de prosseguir.

1. Escolha **Avançar** para passar para a página **Configurações**.

   ![Configurações Comuns](./media/publish-settings.png)

1. **Serviço de nuvem** – usando a lista suspensa, selecione um Serviço de Nuvem existente ou selecione **Criar** e crie um Serviço de Nuvem. O data center é exibido entre parênteses para cada Serviço de Nuvem. É aconselhável que a localização do data center para o Serviço de Nuvem seja a mesma localização do data center para a conta de armazenamento.

   Se optar por criar um Serviço de Nuvem, você verá a caixa de diálogo **Criar Serviço de Nuvem (suporte estendido)** . Especifique a localização e o grupo de recursos que você deseja usar para o Serviço de Nuvem.

   ![Criar um Serviço de Nuvem com o suporte estendido](./media/extended-support-dialog.png)

1. **Configuração da compilação** - selecione **Depurar** ou **Liberar**.

1. **Configuração de serviço** - selecione **Nuvem** ou **Local**.

1. **Conta de armazenamento** – selecione a conta de armazenamento a ser usada para essa implantação ou **Criar** para criar uma conta de armazenamento. A região é exibida entre parênteses para cada conta de armazenamento. É recomendável que a localização do data center da conta de armazenamento seja a mesma localização do data center do Serviço de Nuvem (Configurações Comuns).

   A conta de armazenamento do Azure armazena o pacote para a implantação do aplicativo. Depois que o aplicativo é implantado, o pacote é removido da conta de armazenamento.

1. **Key Vault** – especifique o Key Vault que contém os segredos desse Serviço de Nuvem. Isso é habilitado se a Área de Trabalho Remota está habilitada ou se os certificados são adicionados à configuração.

1. **Habilitar Área de Trabalho Remota para todas as funções** – marque essa opção se desejar se conectar remotamente ao serviço. Você deverá especificar as credenciais.

   ![Configurações da Área de Trabalho Remota](./media/remote-desktop-configuration.png)

1. Escolha **Avançar** para passar para a página **Configurações de diagnóstico**.

   ![Configurações de diagnóstico](./media/diagnostics-settings.png)

   O diagnóstico permite solucionar problemas de um Serviço de Nuvem do Azure (ou da máquina virtual do Azure). Para saber mais sobre diagnósticos, confira [Configuração do Diagnóstico para os Serviço de Nuvem e as Máquinas Virtuais do Azure](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Para saber mais sobre o Application Insights, confira [O que é o Application Insights?](../azure-monitor/app/app-insights-overview.md).

1. Escolha **Avançar** para passar para a página **Resumo**.

   ![Resumo](./media/publish-summary.png)

1. **Perfil de destino** - você pode optar por criar um perfil de publicação usando as configurações que escolheu. Por exemplo, você pode criar um perfil para um ambiente de teste e outro para produção. Para salvar esse perfil, escolha o ícone **Salvar** . O assistente cria o perfil e o salva no projeto do Visual Studio. Para modificar o nome do perfil, abra a lista **Perfil de destino** e, em seguida, escolha **Gerenciar…**.

   > [!Note]
   > O perfil de publicação aparece no Gerenciador de Soluções no Visual Studio, e as configurações do perfil são gravadas em um arquivo com a extensão .azurePubxml. As configurações são salvas como atributos de marcas XML.

1. Depois de definir todas as configurações da implantação do projeto, selecione **Publicar** na parte inferior da caixa de diálogo. Você pode monitorar o status do processo na Janela de Saída do **Log de Atividades do Azure** no Visual Studio.

Parabéns! Você publicou o projeto do Serviço de Nuvem com suporte estendido no Azure. Para fazer a publicação novamente com as mesmas configurações, você pode reutilizar o perfil de publicação ou repetir essas etapas para criar um.

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

Para limpar os recursos do Azure que você criou seguindo este tutorial, acesse o [portal do Azure](https://portal.azure.com), escolha **Grupos de recursos**, localize e abra o grupo de recursos usado para criar o serviço e escolha **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Configure a CI (integração contínua) usando o botão **Configurar** na tela **Publicar**. Para obter mais informações, confira a [documentação do Azure Pipelines](/azure/devops/pipelines).