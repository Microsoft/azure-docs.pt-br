---
title: Como proteger servidores do centro de administração do Windows com a central de segurança do Azure
description: Este artigo explica como integrar a central de segurança do Azure com o centro de administração do Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: ca3dd410474af0a42a3cd5b4217c3bba90b8ff1a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511977"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Proteger os recursos do centro de administração do Windows com a central de segurança

O centro de administração do Windows é uma ferramenta de gerenciamento para seus servidores Windows. É um único local para que os administradores de sistema acessem a maioria das ferramentas de administração mais usadas. No centro de administração do Windows, você pode integrar diretamente seus servidores locais à central de segurança do Azure. Você pode exibir um resumo de suas recomendações de segurança e alertas diretamente na experiência do centro de administração do Windows.

> [!NOTE]
> Sua assinatura do Azure e o espaço de trabalho Log Analytics associado precisam ter o Azure defender habilitado para habilitar a integração do centro de administração do Windows.
> O Azure defender será gratuito pelos primeiros 30 dias se você ainda não o tiver usado na assinatura e no espaço de trabalho. Para obter mais informações, consulte [a página de informações de preços](security-center-pricing.md).
>

Quando você tiver integrado com êxito um servidor do centro de administração do Windows à central de segurança do Azure, poderá:

* Exibir alertas de segurança e recomendações dentro da extensão da central de segurança no centro de administração do Windows
* Exibir a postura de segurança e recuperar informações detalhadas adicionais dos servidores gerenciados do centro de administração do Windows na central de segurança dentro do portal do Azure (ou por meio de uma API)

Ao combinar essas duas ferramentas, a central de segurança se torna seu único painel para exibir todas as suas informações de segurança, qualquer que seja o recurso: proteger o centro de administração do Windows gerenciado por servidores locais, suas VMs e quaisquer cargas de trabalho adicionais de PaaS.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Carregar servidores gerenciados do centro de administração do Windows na central de segurança

1. No centro de administração do Windows, selecione um dos servidores e, no painel **ferramentas** , selecione a extensão da central de segurança do Azure:

    ![Extensão da central de segurança do Azure no centro de administração do Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se o servidor já estiver integrado à central de segurança, a janela de configuração não será exibida.

1. Clique em **entrar no Azure e configurar**.
    ![Integração da extensão do centro de administração do Windows à central de segurança do Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga as instruções para conectar o servidor à central de segurança. Depois de inserir os detalhes e a confirmação necessários, a central de segurança fará as alterações de configuração necessárias para garantir que todas as seguintes opções sejam verdadeiras:
    * Um gateway do Azure está registrado.
    * O servidor tem um espaço de trabalho para relatar e uma assinatura associada.
    * A solução de Log Analytics da central de segurança está habilitada no espaço de trabalho. Essa solução fornece recursos do Azure defender para *todos os* servidores e máquinas virtuais que se reportam a este espaço de trabalho.
    * O Azure defender para servidores está habilitado na assinatura.
    * O agente de Log Analytics está instalado no servidor e configurado para relatar para o espaço de trabalho selecionado. Se o servidor já se reportar a outro espaço de trabalho, ele também será configurado para relatar para o espaço de trabalho selecionado recentemente.

    > [!NOTE]
    > Pode levar algum tempo após a integração para que as recomendações apareçam. Na verdade, dependendo da atividade do servidor, você não receberá *nenhum* alerta. Para gerar alertas de teste para testar se os alertas estão funcionando corretamente, siga as instruções no [procedimento de validação de alerta](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Exibir recomendações e alertas de segurança no centro de administração do Windows

Uma vez integrado, você pode exibir seus alertas e recomendações diretamente na área da central de segurança do Azure do centro de administração do Windows. Clique em uma recomendação ou um alerta para exibi-los no portal do Azure. Lá, você obterá informações adicionais e aprenderá a corrigir problemas.

[![Recomendações e alertas da central de segurança, como visto no centro de administração do Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Exibir recomendações e alertas de segurança para servidores gerenciados do centro de administração do Windows na central de segurança
Na central de segurança do Azure:

* Para exibir as recomendações de segurança para todos os seus servidores do centro de administração do Windows, abra [inventário de ativos](asset-inventory.md) e filtre para o tipo de computador que você deseja investigar. Selecione a guia **VMs e computadores** .

* Para exibir alertas de segurança para todos os servidores do centro de administração do Windows, abra **alertas de segurança**. Clique em **Filtrar** e verifique se **somente** "não Azure" está selecionado:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrar alertas de segurança para servidores gerenciados do centro de administração do Windows" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::