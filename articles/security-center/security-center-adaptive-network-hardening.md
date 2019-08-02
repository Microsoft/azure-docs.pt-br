---
title: Proteção de rede adaptável na central de segurança do Azure | Microsoft Docs
description: " Saiba como habilitar o fortalecimento de rede adaptável na central de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 2f82f3fe6f5cb6808ba606125ee0869475a60274
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295900"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Proteção de rede adaptável na central de segurança do Azure
Saiba como configurar a proteção de rede adaptável na central de segurança do Azure.

## <a name="what-is-adaptive-network-hardening"></a>O que é a proteção de rede adaptável?
A aplicação de [NSG (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview) para filtrar o tráfego de e para os recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui pelo NSG é um subconjunto das regras de NSG definidas. Nesses casos, é possível melhorar ainda mais a postura de segurança ao proteger as regras de NSG, com base nos padrões reais de tráfego.

A proteção de rede adaptável fornece recomendações para proteger ainda mais as regras de NSG. Ele usa um algoritmo de aprendizado de máquina que fatores em tráfego real, configuração confiável conhecida, inteligência contra ameaças e outros indicadores de comprometimento e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas de IP/porta específicas.

Por exemplo, digamos que a regra NSG existente seja permitir o tráfego de 140.20.30.10/24 na porta 22. A recomendação do fortalecimento da rede adaptável, com base na análise, seria restringir o intervalo e permitir o tráfego de 140.23.30.10/29 – que é um intervalo de IP mais estreito e negar todos os outros tráfegos para essa porta.

![exibição de proteção de rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> As recomendações de proteção de rede adaptável têm suporte nas seguintes portas: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Exibir alertas e regras de proteção de rede adaptável

1. Na central de segurança, selecione rede **sistema** -> de**rede adaptável**. As VMs de rede são listadas em três guias separadas:
   * **Recursos**não íntegros: VMs que atualmente têm recomendações e alertas que foram disparados executando o algoritmo de proteção de rede adaptável. 
   * **Recursos íntegros**: VMs sem alertas e recomendações.
   * **Recursos**não verificados: VMs para as quais o algoritmo de proteção de rede adaptável não pode ser executado devido a um dos seguintes motivos:
      * As **VMs são VMs clássicas**: Há suporte apenas para VMs Azure Resource Manager.
      * **Não há dados suficientes disponíveis**: Para gerar recomendações precisas de proteção de tráfego, a central de segurança requer pelo menos 30 dias de dados de tráfego.
      * **A VM não está protegida pelo ASC Standard**: Somente as VMs definidas como o tipo de preço Standard da central de segurança são elegíveis para esse recurso.

     ![recursos não íntegros](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na guia **recursos** não íntegros, selecione uma VM para exibir os alertas e as regras de proteção recomendadas a serem aplicadas.

    ![protegendo alertas](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Examinar e aplicar regras recomendadas de proteção de rede adaptável

1. Na guia **recursos** não íntegros, selecione uma VM. Os alertas e as regras de proteção recomendadas são listados.

     ![regras de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A guia **regras** lista as regras que o fortalecimento de rede adaptável recomenda que você adicione. A guia **alertas** lista os alertas que foram gerados devido ao tráfego, fluindo para o recurso, que não está dentro do intervalo de IP permitido nas regras recomendadas.

2. Se você quiser alterar alguns dos parâmetros de uma regra, poderá modificá-los, conforme explicado em [modificar uma regra](#modify-rule).
   > [!NOTE]
   > Você também pode [excluir](#delete-rule) ou [Adicionar](#add-rule) uma regra.

3. Selecione as regras que você deseja aplicar no NSG e clique em **impor**.

      > [!NOTE]
      > As regras impostas são adicionadas às NSG que protegem a VM. (Uma VM pode ser protegida por um NSG associado à sua NIC, ou a sub-rede na qual a VM reside, ou ambos)

    ![impor regras](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Modificar uma regra <a name ="modify-rule"></a>

Talvez você queira modificar os parâmetros de uma regra que tenha sido recomendada. Por exemplo, talvez você queira alterar os intervalos de IP recomendados.

Algumas diretrizes importantes para modificar uma regra de proteção de rede adaptável:

* Você pode modificar os parâmetros de "permitir" somente regras. 
* Você não pode alterar as regras "permitir" para se tornarem regras "Deny". 

  > [!NOTE]
  > Criar e modificar regras "Deny" é feito diretamente no NSG para obter mais detalhes, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Uma regra **negar todo o tráfego** é o único tipo de regra "negar" que seria listado aqui e não pode ser modificado. Você pode, no entanto, excluí-lo (consulte [excluir uma regra](#delete-rule)).
  > [!NOTE]
  > Uma regra **negar todo o tráfego** é recomendada quando, como resultado da execução do algoritmo, a central de segurança não identifica o tráfego que deve ser permitido, com base na configuração existente do NSG. Portanto, a regra recomendada é negar todo o tráfego para a porta especificada. O nome desse tipo de regra é exibido como "*gerado pelo sistema*". Depois de impor essa regra, seu nome real no NSG será uma cadeia de caracteres composta pelo protocolo, direção do tráfego, "negar" e um número aleatório.

*Para modificar uma regra de proteção de rede adaptável:*

1. Para modificar alguns dos parâmetros de uma regra, na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **Editar**.

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela **Editar regra** , atualize os detalhes que você deseja alterar e clique em **salvar**.

   > [!NOTE]
   > Depois de clicar em **salvar**, você alterou a regra com êxito. *No entanto, você não o aplicou ao NSG.* Para aplicá-lo, você deve selecionar a regra na lista e clicar em **impor** (conforme explicado na próxima etapa).

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Adicionar uma nova regra <a name ="add-rule"></a>

Você pode adicionar uma regra "permitir" que não foi recomendada pela central de segurança.

> [!NOTE]
> Somente as regras "permitir" podem ser adicionadas aqui. Se você quiser adicionar regras "Deny", poderá fazer isso diretamente no NSG. Para obter mais detalhes, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Para adicionar uma regra de proteção de rede adaptável:*

1. Clique em **Adicionar regra** (localizado no canto superior esquerdo).

   ![Adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na janela **nova regra** , insira os detalhes e clique em **Adicionar**.

   > [!NOTE]
   > Depois de clicar em **Adicionar**, você adicionou a regra com êxito e ela está listada com as outras regras recomendadas. No entanto, você não o aplicou no NSG. Para ativá-lo, você deve selecionar a regra na lista e clicar em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Excluir uma regra <a name ="delete-rule"></a>

Quando necessário, você pode excluir uma regra recomendada. Por exemplo, você pode determinar que a aplicação de uma regra sugerida poderia bloquear o tráfego legítimo.

*Para excluir uma regra de proteção de rede adaptável:*

1. Na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **excluir**.  

    ![regras de proteção](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

