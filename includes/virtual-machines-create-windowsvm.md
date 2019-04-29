---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b0b5e817d5e39dd7800a1482d40c56db5f2be6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127137"
---
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Iniciando no canto superior esquerdo, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**.

    ![Navegar até as imagens de VM do Azure no portal](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. No Datacenter do Windows Server 2016, selecione o modelo de implantação Clássico. Clique em Criar.

    ![Captura de tela que mostra as imagens da VM do Azure disponíveis no portal](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Folha de Noções básicas

A folha de Noções básicas solicita informações administrativas da máquina virtual.

1. Insira um **Nome** para a máquina virtual. Neste exemplo, o nome da máquina virtual é _HeroVM_. O nome deve ter de 1 a 15 caracteres e não pode conter caracteres especiais.

2. Insira um **Nome de usuário** e uma **Senha** forte que serão usados para criar uma conta local na VM. A conta local é usada para conectar e gerenciar a VM. Neste exemplo, o nome de usuário é _azureuser_.

   A senha deve ter de 8 a 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Confira mais sobre os [requisitos de nome de usuário e senha](../articles/virtual-machines/windows/faq.md).

3. A **Assinatura** é opcional. Uma configuração comum é "Pré-pago".

4. Selecione um **Grupo de recursos** existente ou digite o nome de um novo. Neste exemplo, o nome do grupo de recursos é _HeroVMRG_.

5. Selecione um **Local** de datacenter do Azure no qual você deseja que a VM seja executada. Neste exemplo, o local é **Leste dos EUA**.

6. Ao terminar, clique em **Avançar** para continuar na próxima folha.

    ![Captura de tela que mostra as configurações na folha Noções básicas para configurar uma VM do Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Folha Tamanho

A folha Tamanho identifica os detalhes de configuração da VM e lista várias opções que incluem o sistema operacional, o número de processadores, o tipo de armazenamento em disco e os custos mensais estimados de uso.  

Escolha um tamanho de VM e, em seguida, clique em **Selecionar** para continuar. Neste exemplo, o tamanho da VM é _DS1_\__V2 Standard_.

  ![Captura de tela da folha Tamanho que mostra a VM do Azure que você pode selecionar](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Folha de configurações

A folha de Configurações solicita opções de armazenamento e de rede. Você pode aceitar as configurações padrão. O Azure cria entradas apropriadas quando necessário.

Se você selecionou um tamanho de máquina virtual que dá suporte a isso, poderá experimentar o Armazenamento Premium do Azure, selecionando Premium (SSD) em Tipo de disco.

Quando terminar de fazer as alterações, clique em **OK**.

## <a name="4-summary-blade"></a>4. Folha de Resumo

A folha de Resumo lista as configurações especificadas nas folhas anteriores. Clique em **OK** quando estiver pronto para criar a imagem.

 ![Relatório da folha de Resumo fornecendo as configurações especificadas da máquina virtual](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Depois que a máquina virtual é criada, o portal lista a nova máquina virtual em **Todos os recursos** e exibe um bloco da máquina virtual no painel. O serviço de nuvem correspondente e a conta de armazenamento também são criados e listados. A máquina virtual e o serviço de nuvem são iniciados automaticamente, e seu status é listado como **Em Execução**.

 ![Configurar Agente de VM e os pontos de extremidade da máquina virtual](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
