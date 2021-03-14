---
title: Configurar a política de retenção no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar uma política de retenção, limpar a fábrica e desativar imagens antigas do DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550018"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Configurar a política de retenção no Azure DevTest Labs
Este artigo aborda a definição de uma política de retenção, a limpeza da fábrica e a desativação de imagens antigas de todos os outros DevTest Labs da organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de ter seguido esses artigos antes de continuar:

- [Criar uma fábrica de imagem](image-factory-create.md)
- [Executar uma fábrica de imagem do Azure DevOps](image-factory-set-up-devops-lab.md)
- [Salvar imagens personalizadas e distribuí-las para vários laboratórios](image-factory-save-distribute-custom-images.md)

Os itens a seguir já devem estar em vigor:

- Um laboratório para a fábrica de imagens no Azure DevTest Labs
- Um ou mais Azure DevTest Labs de destino onde a fábrica distribuirá imagens Golden
- Um projeto DevOps do Azure usado para automatizar a fábrica de imagens.
- Local do código-fonte que contém os scripts e a configuração (em nosso exemplo, no mesmo projeto DevOps usado acima)
- Uma definição de compilação para orquestrar as tarefas do Azure PowerShell
 
## <a name="setting-the-retention-policy"></a>Configurando a política de retenção
Antes de configurar as etapas de limpeza, defina quantas imagens históricas você deseja reter nos laboratórios de DevTest. Quando você seguiu o artigo [executar uma fábrica de imagem do Azure DevOps](image-factory-set-up-devops-lab.md) , configurou várias variáveis de compilação. Um deles foi **ImageRetention**. Você define essa variável como `1` , o que significa que os laboratórios de DevTest não manterão um histórico das imagens personalizadas. Somente as imagens distribuídas mais recentes estarão disponíveis. Se você alterar essa variável para `2` , a imagem distribuída mais recente mais as anteriores serão mantidas. Você pode definir esse valor para definir o número de imagens históricas que deseja manter em seus laboratórios de DevTest.

## <a name="cleaning-up-the-factory"></a>Limpando a fábrica
A primeira etapa na limpeza da fábrica é remover as VMs de imagem Golden da fábrica de imagens. Há um script para realizar essa tarefa, assim como nossos scripts anteriores. A primeira etapa é adicionar outra tarefa **do Azure PowerShell** à definição de compilação, conforme mostrado na imagem a seguir:

![Etapa do PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Quando você tiver a nova tarefa na lista, selecione o item e preencha todos os detalhes, conforme mostrado na imagem a seguir:

![Tarefa limpar imagens antigas do PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros de script são: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Desativar imagens antigas 
Essa tarefa remove todas as imagens antigas, mantendo apenas um histórico correspondente à variável de Build **ImageRetention** . Adicione uma tarefa de compilação **do Azure PowerShell** adicional à nossa definição de compilação. Depois de adicionado, selecione a tarefa e preencha os detalhes, conforme mostrado na imagem a seguir: 

![Tarefa desativar o PowerShell de imagens antigas](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros de script são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Enfileirar a compilação
Agora que você concluiu a definição de compilação, enfileirar uma nova compilação para certificar-se de que tudo está funcionando. Depois que a compilação for concluída com êxito, as novas imagens personalizadas aparecerão no laboratório de destino e, se você verificar o laboratório da fábrica de imagens, você não verá nenhuma VM provisionada. Além disso, se você enfileirar outras compilações, verá que as tarefas de limpeza desativam imagens personalizadas antigas dos laboratórios de DevTest de acordo com o valor de retenção definido nas variáveis de compilação.

> [!NOTE]
> Se você tiver executado o pipeline de compilação no final do último artigo da série, exclua manualmente as máquinas virtuais que foram criadas no laboratório da fábrica de imagens antes de enfileirar uma nova compilação.  A etapa de limpeza manual só é necessária enquanto configuramos tudo e verificamos se ela funciona.



## <a name="summary"></a>Resumo
Agora você tem uma fábrica de imagens em execução que pode gerar e distribuir imagens personalizadas para seus laboratórios sob demanda. Neste ponto, é apenas uma questão de fazer suas imagens serem configuradas corretamente e identificar os laboratórios de destino. Conforme mencionado no artigo anterior, o **Labs.jsno** arquivo localizado em sua pasta de **configuração** especifica quais imagens devem ser disponibilizadas em cada um dos laboratórios de destino. Ao adicionar outros laboratórios de DevTest à sua organização, basta adicionar uma entrada no Labs.jspara o novo laboratório.

A adição de uma nova imagem à sua fábrica também é simples. Quando desejar incluir uma nova imagem em sua fábrica, abra o [portal do Azure](https://portal.azure.com), navegue até o Factory DevTest Labs, selecione o botão para adicionar uma VM e escolha a imagem do Marketplace desejada e os artefatos. Em vez de selecionar o botão **criar** para criar a nova VM, selecione **exibir modelo de Azure Resource Manager**"e salve o modelo como um arquivo. JSON em algum lugar dentro da pasta **GoldenImages** em seu repositório. Na próxima vez em que você executar o seu alocador de imagem, ele criará sua imagem personalizada.


## <a name="next-steps"></a>Próximas etapas
1. [Agende sua compilação/versão](/azure/devops/pipelines/build/triggers?tabs=designer) para executar a fábrica de imagens periodicamente. Ele atualiza suas imagens geradas de fábrica regularmente.
2. Faça mais imagens Golden para sua fábrica. Você também pode considerar a [criação de artefatos](devtest-lab-artifact-author.md) para partes adicionais de script de suas tarefas de configuração de VM e incluir os artefatos em suas imagens de fábrica.
4. Crie um [Build/versão separado](/azure/devops/pipelines/overview?view=azure-devops-2019) para executar o script **DistributeImages** separadamente. Você pode executar esse script ao fazer alterações no Labs.jse obter imagens copiadas para os laboratórios de destino sem precisar recriar todas as imagens novamente.

