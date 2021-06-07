---
title: Configurar o dimensionamento para os serviços de nuvem do Azure (suporte estendido)
description: Como habilitar opções de dimensionamento para os serviços de nuvem do Azure (suporte estendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728154"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Configurar opções de dimensionamento com os serviços de nuvem do Azure (suporte estendido) 

As condições podem ser configuradas para habilitar implantações de serviços de nuvem (suporte estendido) para reduzir e reduzir horizontalmente. Essas condições podem ser baseadas no uso da CPU, carga de disco e carga de rede. 

Considere as seguintes informações ao configurar o dimensionamento de suas implantações de serviço de nuvem:
- A colocação em escala afeta o uso principal. As instâncias de função maiores consomem mais núcleos e você só pode dimensionar dentro do limite de núcleos de sua assinatura. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Há suporte para o dimensionamento com base no limite de mensagens da fila. Para obter mais informações, consulte [Introdução ao Armazenamento de Filas do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Para garantir a alta disponibilidade de seus aplicativos de serviço de nuvem (suporte estendido), certifique-se de implantar com duas ou mais instâncias de função.
- O dimensionamento automático personalizado só pode ocorrer quando todas as funções estiverem em um estado **pronto** .

## <a name="configure-and-manage-scaling"></a>Configurar e gerenciar o dimensionamento

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Selecione a implantação do serviço de nuvem (suporte estendido) na qual você deseja configurar o dimensionamento. 
3. Selecione a folha **escala** . 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Imagem mostra a seleção da opção Área de Trabalho Remota no portal do Azure":::

4. Uma página exibirá uma lista de todas as funções nas quais o dimensionamento pode ser configurado. Selecione a função que você deseja configurar. 
5. Selecione o tipo de escala que você deseja configurar
    - A **escala manual** definirá a contagem absoluta de instâncias.
        1. Selecione **escala manual**.
        2. Insira o número de instâncias para as quais você deseja escalar ou reduzir verticalmente.
        3. Clique em **Salvar**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Imagem mostra a configuração do dimensionamento manual no portal do Azure":::

        4. A operação de dimensionamento será iniciada imediatamente. 
        
    - O **dimensionamento automático personalizado** permitirá que você defina regras que regem a quantidade ou o mínimo de escala. 
        1. Selecionar **dimensionamento automático personalizado**
        2. Escolha para dimensionar com base em uma métrica ou contagem de instâncias.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Imagem mostra a configuração de dimensionamento automático personalizado no portal do Azure":::

        3. Se o dimensionamento for baseado em uma métrica, adicione regras conforme necessário para atingir os resultados de dimensionamento desejados.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Imagem mostra a configuração de regras de dimensionamento automático personalizadas no portal do Azure":::

        4. Clique em **Salvar**.
        5. As operações de dimensionamento começarão assim que uma regra for disparada.
        
6. Você pode exibir ou ajustar as regras de dimensionamento existentes aplicadas às implantações selecionando a guia **escala** .

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Imagem mostra o ajuste de uma regra de dimensionamento existente no portal do Azure":::

## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).