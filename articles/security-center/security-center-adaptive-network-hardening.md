---
title: Proteção de rede adaptáveis na Central de segurança do Azure | Microsoft Docs
description: " Saiba como habilitar a proteção de rede adaptáveis na Central de segurança do Azure. "
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
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706539"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Proteção de rede adaptáveis na Central de segurança do Azure
Saiba como configurar o sistema de proteção de rede adaptável na Central de segurança do Azure.

## <a name="what-is-adaptive-network-hardening"></a>O que é adaptável de proteção de rede?
Aplicando [(NSG) de grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) filtrar o tráfego para e de recursos, melhora sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real fluindo pelo NSG é um subconjunto das regras do NSG definida. Nesses casos, melhorando ainda mais a postura de segurança pode ser obtido pelo sistema de proteção as regras do NSG, com base nos padrões de tráfego real.

Sistema de proteção de rede adaptável fornece recomendações para proteger ainda mais as regras do NSG. Ele usa um algoritmo que considera o tráfego real, configuração confiável, a inteligência contra ameaças e outros indicadores de comprometimento, conhecido de aprendizado de máquina e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas IP/porta específicas.

Por exemplo, digamos que a regra NSG existente é permitir que o tráfego de 140.20.30.10/24 na porta 22. Recomendação do Adaptive rede de proteção, com base na análise, seria restringir o intervalo e permitir o tráfego de 140.23.30.10/29 – que é um intervalo mais estreito de IP, e negar todos os outros tráfegos para essa porta.

![exibição do sistema de proteção de rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Recomendações de sistema de proteção de rede adaptáveis têm suporte nas seguintes portas: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Exibir alertas do sistema de proteção de rede adaptável e regras

1. Na Central de segurança, selecione **Networking** -> **sistema de proteção de rede adaptável**. As VMs da rede são listadas em três guias separadas:
   * **Recursos não íntegros**: VMs que atualmente tem as recomendações e alertas disparados ao executar o algoritmo adaptável de proteção de rede. 
   * **Recursos íntegros**: VMs sem recomendações e alertas.
   * **Não verificadas recursos**: VMs que o algoritmo adaptável de proteção de rede não pode ser executado devido a um dos seguintes motivos:
      * **As VMs são VMs clássicas**:-somente as VMs do Azure Resource Manager têm suporte.
      * **Não há dados suficientes estão disponíveis**: Para gerar recomendações de proteção de tráfego preciso, a Central de segurança exige que pelo menos 30 dias de dados de tráfego.
      * **A VM não está protegida pelo padrão ASC**: Apenas as VMs que são definidas para o tipo de preço Standard da Central de segurança são elegíveis para esse recurso.

     ![recursos não íntegros](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Dos **recursos não íntegros** guia, selecione uma VM para exibir alertas de TI e as regras de proteção recomendada a ser aplicado.

    ![alertas de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Examinar e aplicar proteção adaptável de rede regras recomendadas

1. Dos **recursos não íntegros** guia, selecione uma VM. Os alertas e as regras de proteção recomendados são listadas.
   ![alertas de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > O **regras** guia lista as regras de proteção de rede adaptável recomenda que você adicione. O **alertas** guia lista os alertas que foram gerados devido ao tráfego, que flui para o recurso, que não está dentro do intervalo IP permitido nas regras recomendadas.

   ![regras de proteção](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Se você quiser alterar alguns dos parâmetros de uma regra, você pode modificá-lo, conforme explicado em [modificar uma regra](#modify-rule).
   > [!NOTE]
   > Você também pode [exclua](#delete-rule) ou [adicionar](#add-rule) uma regra.

3. Selecione as regras que você deseja aplicar o NSG e, em seguida, clique em **impor**. 

### Modificar uma regra  <a name ="modify-rule"> </a>

Você talvez queira modificar os parâmetros de uma regra que tenha sido recomendado. Por exemplo, você talvez queira alterar os intervalos IP recomendados.

Algumas diretrizes importantes para modificar uma regra de proteção de rede adaptável:

* Você pode modificar os parâmetros de regras de "Permitir" apenas. 
* Você não pode alterar as regras para se tornar "Negar" regras de "Permitir". 

  > [!NOTE]
  > Criando e modificando as regras de "negação" é feito diretamente no NSG para obter mais detalhes, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Um **Negar todo o tráfego** regra é o único tipo de regra "Negar" que estariam listado aqui e não pode ser modificado. Você pode, no entanto, poderá excluir (consulte [excluir uma regra](#delete-rule)).
  > [!NOTE]
  > Um **Negar todo o tráfego** regra é recomendada quando, como resultado da execução do algoritmo, a Central de segurança não identificar o tráfego que deve ser permitido, com base na configuração de NSG existente. Portanto, a regra recomendada é Negar todo o tráfego para a porta especificada. O nome desse tipo de regra é exibido como "gerada pelo sistema". Depois de aplicar essa regra, seu nome real no NSG será uma cadeia de caracteres composta de um número aleatório, direção do tráfego, "Negar" e o protocolo.

*Para modificar uma regra de proteção de rede adaptável:*

1. Para modificar alguns dos parâmetros de uma regra, o **regras** guia, clique nas reticências (...) no final da linha da regra e clique em **Editar regra de**.

   ![Editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. No **Editar regra** janela, atualize os detalhes que você deseja alterar e, em seguida, clique em **salvar**.

   > [!NOTE]
   > Depois de clicar em **salvar**, alterou com êxito a regra. *No entanto, você não tiver aplicá-la para o NSG.* Para aplicá-lo, você deve selecionar a regra na lista e clique em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique em **impor**.

### Adicionar uma nova regra <a name ="add-rule"> </a>


Você pode adicionar uma regra "Permitir" que não foi recomendada pela Central de segurança.

> [!NOTE]
> Somente "Permitir" regras podem ser incluídas aqui. Se você quiser adicionar regras de "negação", você pode fazer isso diretamente no NSG. Para obter mais detalhes, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Para adicionar uma regra de proteção de rede adaptável:*

1. Clique em **Adicionar regra** (localizado no canto superior esquerdo).

   ![Adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. No **Editar regra** janela, insira os detalhes e clique em **salvar**.

   > [!NOTE]
   > Depois de clicar em **salvar**, você adicionou com êxito a regra e ele é listado com outras regras recomendadas. No entanto, você não tiver aplicá-la no NSG. Para ativá-lo, você deve selecionar a regra na lista e clique em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique em **impor**.



### Excluir uma regra <a name ="delete-rule"> </a>

Quando necessário, você pode excluir uma regra recomendada. Por exemplo, você pode determinar a aplicação de uma regra sugerida poderia bloquear o tráfego legítimo.

*Para excluir uma regra de proteção de rede adaptável:*

1. No **regras** guia, clique nas reticências (...) no final da linha da regra e clique em **regra de exclusão**.

   ![Excluir regra](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

