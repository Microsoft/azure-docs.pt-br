---
title: Criar a solução VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como criar uma nuvem privada CloudSimple para estender as cargas de trabalho do VMware para a nuvem com a continuidade e a flexibilidade operacional
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332965"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem privada de CloudSimple

Criando uma nuvem privada ajuda você a resolver uma variedade de necessidades comuns de infraestrutura de rede:

* **Crescimento**. Se você atingir um ponto de atualização de hardware para sua infraestrutura existente, uma nuvem privada permite que você expanda com nenhum novo investimento de hardware necessários.

* **Rápida expansão**. Se qualquer capacidade temporária ou não planejada precisar surgir, uma nuvem privada permite que você crie capacidade adicional sem atraso.

* **Maior proteção**. Com uma nuvem privada de três ou mais nós, você pode obter redundância automática e proteção de alta disponibilidade.

* **Infraestrutura de longo prazo precisa**. Se seus data centers são a capacidade máxima ou é desejável reestruturar para reduzir os custos, uma nuvem privada permite que você desative datacenters e migrar para uma baseado em nuvem solução enquanto permanecem compatível com as operações do enterprise.

Quando você cria uma nuvem privada, você obtém um cluster do vSphere única e todas as VMs que são criados no cluster de gerenciamento.

## <a name="before-you-begin"></a>Antes de começar

Nós devem ser provisionados antes de criar sua nuvem privada.  Para obter mais informações sobre o provisionamento de nós, consulte [provisionar nós para a solução VMware por CloudSimple - Azure](create-nodes.md) artigo.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Sobre o **recursos** , clique em **nova nuvem privada**.

    ![Criar uma nuvem privada - como iniciar](media/create-pc-button.png)

2. Selecione o local para hospedar os recursos de nuvem privada.

3. Escolha o you'ev de tipo de nó CS28 ou CS36 provisionada para a nuvem privada. A última opção inclui a capacidade máxima de memória e computação.

4. Selecione o número de nós para a nuvem privada. Você pode selecionar no máximo o número de nós disponíveis que you'ev [provisionado](create-nodes.md).

    ![Criar uma nuvem privada – configurações básicas](media/create-private-cloud-basic-info.png)

5. Clique em **Avançar: Opções avançadas**.

6. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não coincide com qualquer uma das suas instalações ou outras sub-redes do Azure (redes virtuais) ou com a sub-rede de gateway.  Não use qualquer intervalo CIDR definido em redes virtuais do Azure.
    
    **Opções de intervalo de CIDR:** /24, /23, /22 ou /21. Um/24 intervalo CIDR dá suporte a até nove nós, um /23 intervalo CIDR dá suporte a até 41 nós e um /22 e /21 intervalo CIDR dá suporte a até 64 nós (o número máximo de nós em uma nuvem privada).

    > [!CAUTION]
    > Endereços IP no intervalo CIDR do vSphere/vSAN é reservado para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo, em qualquer máquina virtual.

7. Clique em **Avançar: Examinar e criar**.

8. Examine as configurações. Se você precisar alterar quaisquer configurações, clique em **anterior**.

9. Clique em **Criar**.

Provisionamento de nuvem privada será iniciada depois que você clicar em criar.  Você pode monitorar o progresso da [tarefas](https://docs.azure.cloudsimple.com/activity/#tasks) página CloudSimple do portal.  O provisionamento pode levar 30 minutos para duas horas.  Depois que o provisionamento for concluído, você receberá um email.

## <a name="next-steps"></a>Próximas etapas

* [Expanda a nuvem privada](expand-private-cloud.md)