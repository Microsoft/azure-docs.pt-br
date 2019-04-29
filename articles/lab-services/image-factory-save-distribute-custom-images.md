---
title: Salvar e distribuir imagens no Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622588"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Salvar imagens personalizadas e distribuí-las para vários laboratórios
Este artigo aborda fornece as etapas para salvar as imagens personalizadas de máquinas virtuais (VMs) já criadas. Ele também aborda como distribuir essas imagens personalizadas para outros laboratórios de desenvolvimento/teste na organização.

## <a name="prerequisites"></a>Pré-requisitos
Os itens a seguir já devem estar em vigor:

- Um laboratório para a fábrica de imagem no Azure DevTest Labs.
- Um projeto de DevOps do Azure que é usado para automatizar a fábrica de imagem.
- Local do código fonte que contém os scripts e configuração (no nosso exemplo, no mesmo projeto de DevOps mencionado na etapa anterior).
- Definição de compilação a orquestrar as tarefas do Powershell do Azure.

Se necessário, siga as etapas na [executar uma fábrica de imagem do Azure DevOps](image-factory-set-up-devops-lab.md) para criar ou configurar esses itens. 

## <a name="save-vms-as-generalized-vhds"></a>Salvar máquinas virtuais como VHDs generalizados
Salve as VMs existentes como VHDs generalizados.  Há um exemplo de script do PowerShell para salvar as VMs existentes como VHDs generalizados. Para usá-lo, primeiro, adicione outra **Azure Powershell** tarefa à definição de compilação, conforme mostrado na imagem a seguir:

![Adicionar etapa do Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Depois que a nova tarefa na lista, selecione o item, portanto, podemos pode preencher todos os detalhes conforme mostrado na imagem a seguir: 

![Configurações do PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generalizado versus especializadas imagens personalizadas
No [portal do Azure](https://portal.azure.com), ao criar uma imagem personalizada de uma máquina virtual, você pode escolher ter uma imagem personalizada especializada ou um generalizado.

- **Imagem personalizada especializada:** Sysprep/desprovisionamento não foi executado no computador. Isso significa que a imagem é uma cópia exata do disco do sistema operacional na máquina virtual existente (um instantâneo).  O mesmo arquivos, aplicativos, contas de usuário, nome do computador e assim por diante, são todos presentes quando criamos uma nova máquina usando essa imagem personalizada.
- **Imagem personalizada generalizada:** Sysprep/desprovisionamento foi executado no computador.  Quando esse processo é executado, ele remove contas de usuário, remove o nome do computador, remove os hives do registro de usuário, etc., com o objetivo de generalizar a imagem para que ele pode ser personalizado durante a criação de outra máquina virtual.  Ao generalizar uma máquina virtual (executando o sysprep), o processo destrói a máquina virtual atual – não estará funcional.

O script para ajuste imagens personalizadas na fábrica de imagem salvará VHDs para máquinas virtuais criadas na etapa anterior (identificado com base em uma marca de recurso no Azure).

## <a name="update-configuration-for-distributing-images"></a>Atualizar a configuração para a distribuição de imagens
A próxima etapa no processo é enviar por push as imagens personalizadas do laboratório de fábrica de imagem horizontalmente para todos os laboratórios que precisam delas. A parte principal desse processo é o **labs.json** arquivo de configuração. Você pode encontrar esse arquivo na **configuração** pasta incluída na fábrica de imagem.

Há duas coisas principais listadas no arquivo de configuração labs.json:

- Identifica exclusivamente um laboratório de destino específico usando a ID da assinatura e o nome do laboratório.
- O conjunto específico de imagens que devem ser enviados por push para o laboratório como caminhos relativos para a raiz de configuração. Você pode especificar uma pasta inteira (para obter todas as imagens na pasta) muito.

Aqui está um exemplo de arquivo labs.json com dois laboratórios listados. Nesse caso, você está distribuindo imagens para dois laboratórios diferentes.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Criar uma tarefa de build
Usando as mesmas etapas que você viu neste artigo, adicionar um **Azure Powershell** definição de compilação de tarefa de compilação para você. Preencha os detalhes conforme mostrado na imagem a seguir: 

![Tarefa para distribuir imagens de compilação](./media/save-distribute-custom-images/second-build-task-powershell.png)

Os parâmetros são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Essa tarefa usa as imagens personalizadas presentes na fábrica de imagem e envia-os para todos os laboratórios definidos no arquivo Labs.json.

## <a name="queue-the-build"></a>Enfileirar o build
Após a conclusão da tarefa de compilação de distribuição, enfileirar uma nova compilação para certificar-se de que tudo está funcionando. Depois que a compilação for concluída com êxito, as novas imagens personalizadas aparecerá no laboratório de destino que foi inserido no arquivo de configuração Labs.json.

## <a name="next-steps"></a>Próximas etapas
No próximo artigo na série, você pode atualizar a fábrica de imagem com um etapas de limpeza e a política de retenção: [Definir política de retenção e executar scripts de limpeza](image-factory-set-retention-policy-cleanup.md).
