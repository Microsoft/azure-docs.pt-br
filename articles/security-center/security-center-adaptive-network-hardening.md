---
title: Proteção de rede adaptável na central de segurança do Azure | Microsoft Docs
description: Saiba como usar os padrões de tráfego reais para proteger suas regras de NSG (grupos de segurança de rede) e melhorar ainda mais sua postura de segurança.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: 14523e814c85469aa02a860e87b86defd7823c16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439588"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Melhorar sua postura de segurança de rede com o fortalecimento de rede adaptável
Saiba como configurar a proteção de rede adaptável na central de segurança.

## <a name="availability"></a>Disponibilidade
|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|Permissões de gravação nos NSGs do computador|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>O que é a proteção de rede adaptável?
A aplicação de [NSG (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md) para filtrar o tráfego de e para os recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui pelo NSG seja um subconjunto das regras NSG definidas. Nesses casos, é possível aprimorar ainda mais a postura de segurança protegendo as regras NSG, com base nos padrões reais de tráfego.

A proteção de rede adaptável fornece recomendações para proteger ainda mais as regras de NSG. Ela usa um algoritmo de machine learning que inclui o tráfego real, a configuração confiável conhecida, a inteligência contra ameaças e outros indicadores de comprometimento e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas de IP/porta específicas.

Por exemplo, digamos que a regra NSG existente seja permitir o tráfego de 140.20.30.10/24 na porta 22. Com base na análise de tráfego, a proteção de rede adaptável pode recomendar restringir o intervalo para permitir o tráfego de 140.23.30.10/29 e negar todos os outros tráfegos para essa porta.

>[!Note]
> As recomendações de proteção de rede adaptável só têm suporte nas seguintes portas específicas (para UDP e TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 e 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Exibir e gerenciar alertas e regras de proteção

1. No menu da central de segurança, abra o painel do **Azure defender** e selecione o bloco de proteção de rede adaptável (1) ou o item do painel insights relacionado à proteção de rede adaptável (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Acessando as ferramentas de proteção de rede adaptável" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > O painel insights mostra a porcentagem de suas VMs atualmente defendedas com o fortalecimento de rede adaptável. 

1. A página de detalhes das **recomendações de proteção de rede adaptável deve ser aplicada em máquinas virtuais voltadas** para a Internet é aberta com suas VMs de rede agrupadas em três guias:
   * **Recursos não íntegros**: VMs que atualmente têm recomendações e alertas que foram disparados executando o algoritmo de proteção de rede adaptável. 
   * **Recursos íntegros**: VMs sem alertas e recomendações.
   * **Recursos não verificados**: VMs para as quais o algoritmo de proteção de rede adaptável não pode ser executado devido a um dos seguintes motivos:
      * As VMs **são VMs clássicas**: há suporte apenas para VMs Azure Resource Manager.
      * **Não há dados suficientes disponíveis**: para gerar recomendações precisas de proteção de tráfego, a central de segurança requer pelo menos 30 dias de dados de tráfego.
      * A **VM não está protegida pelo Azure defender**: somente as VMs protegidas com o [Azure defender para servidores](defender-for-servers-introduction.md) são elegíveis para esse recurso.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="A página de detalhes das recomendações de proteção de rede adaptável de recomendação deve ser aplicada em máquinas virtuais voltadas para a Internet":::

1. Na guia **recursos não íntegros** , selecione uma VM para exibir seus alertas e as regras de proteção recomendadas a serem aplicadas.

    - A guia **regras** lista as regras que o fortalecimento de rede adaptável recomenda que você adicione
    - A guia **alertas** lista os alertas que foram gerados devido ao tráfego, fluindo para o recurso, que não está dentro do intervalo de IP permitido nas regras recomendadas.

1. Opcionalmente, edite as regras:

    - [Modificar uma regra](#modify-rule)
    - [Excluir uma regra](#delete-rule) 
    - [Adicionar uma regra](#add-rule)

3. Selecione as regras que você deseja aplicar no NSG e clique em **impor**.

    > [!TIP]
    > Se os intervalos de IP de origem permitidos aparecerem como ' nenhum ', isso significa que a regra recomendada é uma regra de *negação* , caso contrário, é uma regra de *permissão* .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Gerenciando regras de proteção de rede adaptável":::

      > [!NOTE]
      > As regras impostas são adicionadas às NSG que protegem a VM. (Uma VM pode ser protegida por um NSG associado à sua NIC, ou a sub-rede na qual a VM reside, ou ambos)

### <a name="modify-a-rule"></a>Modificar uma regra <a name ="modify-rule"></a>

Talvez você queira modificar os parâmetros de uma regra que tenha sido recomendada. Por exemplo, talvez você queira alterar os intervalos de IP recomendados.

Algumas diretrizes importantes para modificar uma regra de proteção de rede adaptável:

* Você pode modificar os parâmetros de "permitir" somente regras. 
* Você não pode alterar as regras "permitir" para se tornarem regras "Deny". 

  > [!NOTE]
  > Criar e modificar regras "Deny" é feito diretamente no NSG. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

* Uma regra **negar todo o tráfego** é o único tipo de regra "negar" que seria listado aqui e não pode ser modificado. Você pode, no entanto, excluí-lo (consulte [excluir uma regra](#delete-rule)).
  > [!NOTE]
  > Uma regra **negar todo o tráfego** é recomendada quando, como resultado da execução do algoritmo, a central de segurança não identifica o tráfego que deve ser permitido, com base na configuração existente do NSG. Portanto, a regra recomendada é negar todo o tráfego para a porta especificada. O nome desse tipo de regra é exibido como "*gerado pelo sistema*". Depois de impor essa regra, seu nome real no NSG será uma cadeia de caracteres composta pelo protocolo, direção do tráfego, "negar" e um número aleatório.

*Para modificar uma regra de proteção de rede adaptável:*

1. Para modificar alguns dos parâmetros de uma regra, na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **Editar**.

   ![Editando a regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela **Editar regra** , atualize os detalhes que você deseja alterar e clique em **salvar**.

   > [!NOTE]
   > Depois de clicar em **salvar**, você alterou a regra com êxito. *No entanto, você não o aplicou ao NSG.* Para aplicá-lo, você deve selecionar a regra na lista e selecionar **impor** (conforme explicado na próxima etapa).

   ![Selecionando salvar](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Adicionar uma nova regra <a name ="add-rule"></a>

Você pode adicionar uma regra "permitir" que não foi recomendada pela central de segurança.

> [!NOTE]
> Somente as regras "permitir" podem ser adicionadas aqui. Se você quiser adicionar regras "Deny", poderá fazer isso diretamente no NSG. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

*Para adicionar uma regra de proteção de rede adaptável:*

1. Clique em **Adicionar regra** (localizado no canto superior esquerdo).

   ![adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na janela **nova regra** , insira os detalhes e clique em **Adicionar**.

   > [!NOTE]
   > Depois de clicar em **Adicionar**, você adicionou a regra com êxito e ela está listada com as outras regras recomendadas. No entanto, você não o aplicou no NSG. Para ativá-lo, você deve selecionar a regra na lista e clicar em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Excluir uma regra <a name ="delete-rule"></a>

Quando necessário, você pode excluir uma regra recomendada para a sessão atual. Por exemplo, você pode determinar que a aplicação de uma regra sugerida poderia bloquear o tráfego legítimo.

*Para excluir uma regra de proteção de rede adaptável para sua sessão atual:*

1. Na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **excluir**.  

    ![Excluindo uma regra](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)