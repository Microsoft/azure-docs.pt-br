---
title: Criar uma fábrica de imagem no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622520"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada no Azure DevTest Labs
Este artigo aborda a configuração de uma política de retenção, limpando a fábrica e desativação imagens antigas de todos os outros laboratórios de desenvolvimento/teste na organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que você seguiu esses artigos antes de continuar:

- [Criar uma fábrica de imagem](image-factory-create.md)
- [Executar uma fábrica de imagem de DevOps do Azure](image-factory-set-up-devops-lab.md)
- [Salvar imagens personalizadas e distribuir a vários laboratórios](image-factory-save-distribute-custom-images.md)

Os itens a seguir já devem estar em vigor:

- Um laboratório para a fábrica de imagem no Azure DevTest Labs
- Azure DevTest Labs em que a fábrica distribui imagens de ouro de destino de um ou mais
- Um projeto de DevOps do Azure usados para automatizar a fábrica de imagem.
- Local do código fonte que contém os scripts e configuração (no nosso exemplo, no mesmo projeto de DevOps usado acima)
- Uma definição de compilação para orquestrar as tarefas do Powershell do Azure
 
## <a name="setting-the-retention-policy"></a>Configurar a política de retenção
Antes de configurar as etapas de limpeza, defina quantas imagens históricas que você deseja reter no DevTest Labs. Quando você tiver seguido as [executar uma fábrica de imagem de DevOps do Azure](image-factory-set-up-devops-lab.md) artigo, você tiver configurado várias variáveis de compilação. Um deles estava **ImageRetention**. Definir essa variável como `1`, o que significa que o DevTest Labs não manterá um histórico das imagens personalizadas. Somente as imagens distribuídas mais recente estará disponíveis. Se você alterar essa variável para `2`, a versão mais recente distribuído imagem além dos anteriores serão mantidos. Você pode definir esse valor para definir o número de imagens históricos que você deseja manter em seus laboratórios de desenvolvimento/teste.

## <a name="cleaning-up-the-factory"></a>Limpando a fábrica
É a primeira etapa na fábrica de limpeza remover as VMs de imagem dourada da fábrica de imagem. Há um script para realizar essa tarefa, assim como nossos scripts anteriores. A primeira etapa é adicionar outra **Azure Powershell** tarefa à definição de compilação, conforme mostrado na imagem a seguir:

![Etapa do PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Depois que a nova tarefa na lista, selecione o item e preencha todos os detalhes, conforme mostrado na imagem a seguir:

![Limpar tarefa antiga do PowerShell de imagens](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros de script são: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Desativar as imagens antigas 
Esta tarefa remove quaisquer imagens antigas, mantendo apenas uma correspondência de histórico a **ImageRetention** criar variável. Adicionar um **Azure Powershell** tarefa à nossa definição de compilação de compilação. Depois que ele seja adicionado, selecione a tarefa e preencha os detalhes conforme mostrado na imagem a seguir: 

![Desativar a tarefa de imagens antiga do PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros de script são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Enfileirar o build
Agora que você concluiu a definição de compilação, colocar na fila uma nova compilação para certificar-se de que tudo está funcionando. Depois que a compilação for concluída com êxito a novas imagens personalizadas aparecem no laboratório de destino e se você verificar o laboratório de fábrica de imagem, você verá que não há máquinas virtuais provisionados. Além disso se você enfileirar compilações adicionais, você verá as tarefas de limpeza desativado antigo imagens personalizadas do DevTest Labs de acordo com o valor de retenção definido nas variáveis de compilação.

> [!NOTE]
> Se você tiver executado o pipeline de build no final do último artigo da série, exclua manualmente as máquinas virtuais que foram criadas no laboratório de fábrica de imagem antes de enfileirar uma nova compilação.  A etapa de limpeza manual será necessária apenas enquanto registramos configurar tudo e verifique se que ele funciona.



## <a name="summary"></a>Resumo
Agora você tem uma fábrica de imagem em execução que pode gerar e distribuir imagens personalizadas para seus laboratórios sob demanda. Neste ponto, ele é apenas uma questão de obtenção de suas imagens configurada corretamente e identificando os laboratórios de destino. Conforme mencionado no artigo anterior, o **Labs.json** arquivo localizado no seu **configuração** pasta especifica quais imagens devem ser disponibilizadas em cada um dos laboratórios de destino. Conforme você adiciona outros laboratórios de desenvolvimento/teste para sua organização, basta adicionar uma entrada no Labs.json para o novo laboratório.

Adicionar uma nova imagem para sua fábrica também é simple. Quando você deseja incluir uma nova imagem em sua fábrica que você abrir o [portal do Azure](https://portal.azure.com), navegue até sua fábrica DevTest Labs, selecione o botão para adicionar uma VM e escolha a imagem do marketplace desejado e os artefatos. Em vez de selecionar o **criar** botão para tornar a nova VM, selecione **modelo de exibição do Azure Resource Manager**"e salve o modelo como um arquivo. JSON em algum lugar dentro do **GoldenImages** pasta no seu repositório. Na próxima vez que você executar sua fábrica de imagem, ele criará sua imagem personalizada.


## <a name="next-steps"></a>Próximas etapas
1. [Agendar seu build/versão](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) para executar a fábrica de imagem periodicamente. Atualiza as imagens geradas de fábrica regularmente.
2. Verifique mais imagens de ouro para sua fábrica. Você também pode considerar [criação de artefatos](devtest-lab-artifact-author.md) partes extras de suas tarefas de configuração VM de script e incluir os artefatos em suas imagens de fábrica.
4. Criar uma [build/versão de separar](/azure/devops/pipelines/overview?view=azure-devops-2019) para executar o **DistributeImages** script separadamente. Você pode executar esse script quando você fizer alterações Labs.json e obter imagens copiadas para laboratórios de destino sem precisar recriar todas as imagens novamente.

