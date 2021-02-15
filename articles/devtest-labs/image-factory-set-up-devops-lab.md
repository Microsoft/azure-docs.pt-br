---
title: Executar uma fábrica de imagens do Azure DevOps no Azure DevTest Labs
description: Este artigo aborda todas as preparações necessárias para executar a fábrica de imagens do Azure DevOps (anteriormente Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476131"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagem do Azure DevOps
Este artigo aborda todas as preparações necessárias para executar a fábrica de imagens do Azure DevOps (anteriormente Visual Studio Team Services).

> [!NOTE]
> Qualquer mecanismo de orquestração funcionará! O Azure DevOps não é obrigatório. A fábrica de imagens é executada usando Azure PowerShell scripts, portanto, pode ser executada manualmente, usando o Windows Agendador de Tarefas, outros sistemas de CI/CD e assim por diante.

## <a name="create-a-lab-for-the-image-factory"></a>Criar um laboratório para a fábrica de imagens
A primeira etapa na configuração da fábrica de imagens é criar um laboratório no Azure DevTest Labs. Este laboratório é o laboratório de fábrica de imagens onde criamos as máquinas virtuais e salvamos imagens personalizadas. Este laboratório é considerado como parte do processo de fábrica de imagem geral. Depois de criar um laboratório, certifique-se de salvar o nome, pois você precisará dele mais tarde.

## <a name="scripts-and-templates"></a>Scripts e modelos
A próxima etapa na adoção da fábrica de imagens para sua equipe é entender o que está disponível. Os modelos e os scripts do Image Factory estão disponíveis publicamente no [repositório GitHub do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Aqui está um contorno das partes:

- Fábrica de imagens. É a pasta raiz.
    - Configuração. As entradas para a fábrica de imagens
        - GoldenImages. Essa pasta contém arquivos JSON que representam as definições de imagens personalizadas.
        - Labs.jsem. Arquivo em que as equipes se inscrevem para receber imagens personalizadas específicas.
- Deles. O mecanismo para a fábrica de imagens.

Os artigos nesta seção fornecem mais detalhes sobre esses scripts e modelos.

## <a name="create-an-azure-devops-team-project"></a>Criar um projeto de equipe do Azure DevOps
O Azure DevOps permite armazenar o código-fonte, executar o Azure PowerShell em um único local. Você pode agendar execuções recorrentes para manter as imagens atualizadas. Há boas instalações para registrar os resultados para diagnosticar problemas.  No entanto, usar o Azure DevOps não é um requisito, você pode usar qualquer equipamento/mecanismo que possa se conectar ao Azure e executar Azure PowerShell.

Se você tiver uma conta ou projeto DevOps existente que gostaria de usar em vez disso, ignore esta etapa.

Para começar, crie uma conta gratuita no Azure DevOps. Visite https://www.visualstudio.com/ e selecione introdução **para o gratuito** diretamente em **Azure DevOps** (anteriormente VSTS). Você precisará escolher um nome de conta exclusivo e ter certeza de que deseja gerenciar o código usando o git. Depois que isso for criado, salve a URL em seu projeto de equipe. Aqui está uma URL de exemplo: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>Fazer check-in da fábrica de imagens para git
Todos os modelos e a configuração do PowerShell para o Image Factory estão localizados no [repositório GitHub público do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). A maneira mais rápida de colocar o código em seu novo projeto de equipe é importar um repositório. Isso efetua pull em todo o repositório do DevTest Labs (portanto, você obterá documentos e amostras adicionais).

1. Visite o projeto DevOps do Azure que você criou na etapa anterior (a URL é semelhante a **https: \/ / \<accountname> . VisualStudio.com/MyFirstProject**).
2. Selecione **importar um repositório**.
3. Insira a **URL de clone** para o repositório do DevTest Labs: `https://github.com/Azure/azure-devtestlab` .
4. Selecione **Importar**.

    ![Importar repositório git](./media/set-up-devops-lab/import-git-repo.png)

Se você decidir apenas fazer check-in exatamente do que é necessário (os arquivos de fábrica de imagem), siga as etapas [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar o repositório git e enviar por push somente os arquivos localizados no diretório **scripts/ImageFactory** .

## <a name="create-a-build-and-connect-to-azure"></a>Criar uma compilação e conectar-se ao Azure
Neste ponto, você tem os arquivos de origem armazenados em um repositório git no Azure DevOps. Agora, você precisa configurar um pipeline para executar o Azure PowerShell. Há muitas opções para executar essas etapas. Neste artigo, você usa a definição de Build para simplificar, mas funciona com a compilação DevOps, a versão DevOps (ambientes únicos ou múltiplos), outros mecanismos de execução como o Windows Agendador de Tarefas ou qualquer outro conjunto que possa executar Azure PowerShell.

> [!NOTE]
> Um ponto importante para ter em mente que alguns dos arquivos do PowerShell demoram muito para serem executados quando há muito (mais de 10) imagens personalizadas a serem criadas. Os agentes de Build/versão do DevOps hospedados gratuitamente têm um tempo limite de 30 min, portanto, você não pode usar o agente hospedado gratuito depois de começar a criar várias imagens. Esse desafio de tempo limite se aplica a qualquer conjunto que você decida usar, é bom verificar antecipadamente que você pode estender os tempos limite típicos para scripts de Azure PowerShell de longa execução. No caso do Azure DevOps, você pode usar agentes hospedados pagos ou usar seu próprio agente de compilação.

1. Para iniciar, selecione **Configurar compilação** na página inicial do seu projeto DevOps:

    ![Botão configurar compilação](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a compilação (por exemplo: compilar e entregar imagens para DevTest Labs).
3. Selecione uma definição de compilação **vazia** e selecione **aplicar** para criar sua compilação.
4. Neste estágio, você pode escolher **hospedado** para o agente de compilação.
5. **Salve** a definição de compilação.

    ![Definição da compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurar as variáveis de compilação
Para simplificar os parâmetros de linha de comando, encapsular os valores de chave que orientam o alocador de imagem a um conjunto de variáveis de compilação. Selecione a guia **variáveis** e você verá uma lista de várias variáveis padrão. Aqui está a lista de variáveis a serem inseridas no Azure DevOps:


| Nome da variável | Valor | Observações |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Este é o caminho completo no repositório para a pasta de **configuração** . Se você importou todo o repositório acima, o valor à esquerda estará correto. Caso contrário, atualize para apontar para o local de configuração. |
| DevTestLabName | MyImageFactory | O nome do laboratório em Azure DevTest Labs usado como a fábrica para produzir imagens. Se você não tiver um, crie um. Verifique se o laboratório está na mesma assinatura à qual o ponto de extremidade de serviço tem acesso. |
| ImageRetention | 1 | O número de imagens que você deseja salvar de cada tipo. Defina o valor padrão como 1. |
| MachinePassword | ******* | A senha da conta de administrador interna para as máquinas virtuais. Essa é uma conta transitória, portanto, certifique-se de que ela seja segura. Selecione o pequeno ícone de bloqueio à direita para garantir que seja uma cadeia de caracteres segura. |
| MachineUserName | ImageFactoryUser | O nome de usuário da conta de administrador interno para as máquinas virtuais. Esta é uma conta transitória. |
| StandardTimeoutMinutes | 30 | O tempo limite que devemos aguardar para operações regulares do Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | A ID da assinatura na qual o laboratório existe e a qual o ponto de extremidade de serviço tem acesso. |
| VMSize | Standard_A3 | O tamanho da máquina virtual a ser usado para a etapa de **criação** . As VMs criadas são transitórias. O tamanho deve ser aquele que está [habilitado para o laboratório](devtest-lab-set-lab-policy.md). Confirme se há cota de [núcleos de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md)suficiente.

![Variáveis de compilação](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Conectar-se ao Azure
A próxima etapa é configurar a entidade de serviço. Essa é uma identidade no Azure Active Directory que permite que o agente de compilação do DevOps opere no Azure em nome do usuário. Para configurá-lo, comece com a adição de você primeiro Azure PowerShell etapa de Build.

1. Selecione **Adicionar tarefa**.
2. Procure **Azure PowerShell**.
3. Depois de encontrá-lo, selecione **Adicionar** para adicionar a tarefa à compilação. Ao fazer isso, você verá que a tarefa aparece no lado esquerdo como adicionada.

![Configurar etapa do PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A maneira mais rápida de configurar uma entidade de serviço é deixar que o Azure DevOps faça isso para nós.

1. Selecione a **tarefa** que você acabou de adicionar.
2. Para **tipo de conexão do Azure**, escolha **Azure Resource Manager**.
3. Selecione o link **gerenciar** para configurar a entidade de serviço.

Para saber mais, consulte esta [postagem em blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Ao selecionar o link **gerenciar** , você vai para o lugar certo no DevOps (segunda captura de tela na postagem do blog) para configurar a conexão com o Azure. Certifique-se de escolher **Azure Resource Manager ponto de extremidade de serviço** ao configurá-lo.

## <a name="complete-the-build-task"></a>Concluir a tarefa de compilação
Se você selecionar a tarefa de compilação, verá todos os detalhes no painel à direita que devem ser preenchidos.

1. Primeiro, nomeie a tarefa de compilação: **criar máquinas virtuais**.
2. Escolha a **entidade de serviço** que você criou escolhendo **Azure Resource Manager**
3. Escolha o **ponto de extremidade de serviço**.
4. Para **caminho de script**, selecione **... (reticências)** à direita.
5. Navegue até **MakeGoldenImageVMs.ps1** script.
6. Os parâmetros do script devem ter a seguinte aparência: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Concluir a definição de compilação](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Enfileirar a compilação
Vamos verificar se tudo está configurado corretamente ao enfileirar uma nova compilação. Enquanto a compilação estiver em execução, alterne para o [portal do Azure](https://portal.azure.com) e selecione em **todas as máquinas virtuais** no laboratório da fábrica de imagens para confirmar se tudo está funcionando corretamente. Você deve ver três máquinas virtuais serem criadas no laboratório.

![VMs no laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Próximas etapas
A primeira etapa na configuração da fábrica de imagens com base em Azure DevTest Labs foi concluída. No próximo artigo da série, você obtém essas VMs generalizadas e salvas em imagens personalizadas. Em seguida, você os tem distribuído para todos os seus outros laboratórios. Consulte o próximo artigo da série: [salvar imagens personalizadas e distribuir para vários laboratórios](image-factory-save-distribute-custom-images.md).
