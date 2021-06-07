---
title: Criar o jumpbox da Solução VMware no Azure
description: Etapas para criar a caixa de salto da Solução VMware no Azure.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462230"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. No grupo de recursos, selecione **+ Adicionar**. Depois, pesquise e selecione **Microsoft Windows 10** e escolha **Criar**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Adicionar uma nova VM do Windows 10 para uma caixa de salto." border="true":::

1. Insira as informações necessárias nos campos e selecione **Examinar + criar**. 

   Para obter mais informações sobre os campos, confira a tabela a seguir.

   | Campo | Valor |
   | --- | --- |
   | **Assinatura** | O valor já vem preenchido com a Assinatura pertencente ao Grupo de recursos. |
   | **Grupo de recursos** | O valor já vem preenchido para o Grupo de recursos atual, que você criou no tutorial anterior.  |
   | **Nome da máquina virtual** | Insira um nome exclusivo para a VM. |
   | **Região** | Selecione a localização geográfica da VM. |
   | **Opções de disponibilidade** | Deixe o valor padrão selecionado. |
   | **Imagem** | Selecione a imagem da VM. |
   | **Tamanho** | Deixar o valor de tamanho padrão. |
   | **Tipo de autenticação**  | Selecione **Senha**. |
   | **Nome de usuário** | Insira o nome de usuário para fazer logon na VM. |
   | **Senha** | Insira a senha para fazer logon na VM. |
   | **Confirmar senha** | Insira a senha para fazer logon na VM. |
   | **Portas de entrada públicas** | Selecione **Nenhum**. Se você selecionar Nenhum, poderá usar o [acesso JIT](../../security-center/security-center-just-in-time.md#jit-configure) para controlar o acesso à VM somente quando quiser acessá-la. Como alternativa, você poderá usar um [Azure Bastion](../../bastion/tutorial-create-host-portal.md) se quiser acessar o servidor da caixa de salto com segurança da Internet sem expor nenhuma porta de rede.  |


1. Após a aprovação da validação, selecione **Criar** para iniciar o processo de criação da máquina virtual.

