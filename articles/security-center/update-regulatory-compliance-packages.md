---
title: Usando o painel de conformidade regulatória na central de segurança do Azure
description: Saiba como adicionar e remover padrões regulatórios do painel de conformidade regulatória na central de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 3fca96c6cf11e0b35cd830fe4c0d8ccac8e66bd2
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548857"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizar o conjunto de padrões em seu painel de conformidade regulatória

A Central de Segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e parâmetros de comparação do setor. O **painel de conformidade regulatória** fornece informações sobre sua postura de conformidade com base em como você está atendendo aos requisitos de conformidade específicos.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Como os padrões de conformidade regulatória são representados na central de segurança?

Os padrões do setor, os padrões regulatórios e os parâmetros de comparação são representados no painel de conformidade regulatória da central de segurança. Cada padrão é uma iniciativa definida em Azure Policy.

Para ver os dados de conformidade mapeados como avaliações no seu painel, adicione um padrão de conformidade ao seu grupo de gerenciamento ou assinatura de dentro da página de **política de segurança** . Para saber mais sobre Azure Policy e iniciativas, consulte [trabalhando com políticas de segurança](tutorial-security-policy.md).

Quando você atribuiu um padrão ou um parâmetro de comparação ao escopo selecionado, o padrão é exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Você também pode baixar relatórios de resumo para qualquer um dos padrões que foram atribuídos.

A Microsoft acompanha os padrões regulatórios e aprimora automaticamente sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lançar novo conteúdo para a iniciativa, ela será exibida automaticamente em seu painel como novas políticas mapeadas para controles no padrão.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Quais padrões de conformidade regulatória estão disponíveis na central de segurança?

Por padrão, cada assinatura tem o parâmetro de **comparação de segurança do Azure** atribuído. Estas são as diretrizes criadas pela Microsoft e específicas do Azure para práticas recomendadas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

Você também pode adicionar padrões como:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- NHS oficial do Reino Unido e Reino Unido
- PBMM Federal do Canadá
- Azure CIS 1.1.0

Os padrões são adicionados ao painel à medida que são disponibilizados.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Adicionar um padrão normativo ao seu painel

As etapas a seguir explicam como adicionar um pacote para monitorar sua conformidade com um dos padrões regulatórios com suporte.

> [!NOTE]
> Para adicionar padrões ao seu painel, a assinatura deve ter o Azure defender habilitado. Além disso, somente os usuários que são proprietário ou colaborador de política têm as permissões necessárias para adicionar padrões de conformidade. 

1. Na barra lateral da Central de Segurança, selecione **Conformidade regulatória** para abrir o painel de conformidade regulatória. Aqui você pode ver os padrões de conformidade atualmente atribuídos às assinaturas atualmente selecionadas.   

1. Na parte superior da página, selecione **Gerenciar políticas de conformidade**. A página Gerenciamento de políticas é exibida.

1. Selecione a assinatura ou grupo de gerenciamento para o qual você deseja gerenciar a postura de conformidade regulatória. 

    > [!TIP]
    > É recomendável selecionar o escopo mais alto para o qual o padrão é aplicável para que os dados de conformidade sejam agregados e acompanhados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique em **Adicionar mais padrões**. 

1. Na página **Adicionar padrões de conformidade regulatória** , você pode pesquisar qualquer um dos padrões disponíveis, incluindo:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO e UK NHS**
    - **PBMM Federal do Canadá**
    - **HIPAA/HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Adicionando padrões regulatórios ao painel de conformidade regulatória da central de segurança do Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selecione **Adicionar** e insira todos os detalhes necessários para a iniciativa específica, como escopo, parâmetros e correção.

1. Na barra lateral da Central de Segurança, selecione **Conformidade regulatória** novamente para voltar para o painel de conformidade regulatória.

    O novo padrão é exibido na sua lista de padrões de regulamentação e do setor. 

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulatória" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="removing-a-standard-from-your-dashboard"></a>Removendo um padrão do seu painel

Se qualquer um dos padrões regulatórios fornecidos não for relevante para sua organização, será um processo simples para removê-los da interface do usuário. Isso permite personalizar ainda mais o painel de conformidade regulatória e se concentrar apenas nos padrões que se aplicam a você.

Para remover um padrão:

1. No menu da central de segurança, selecione **política de segurança**.

1. Selecione a assinatura relevante da qual você deseja remover um padrão.

    > [!NOTE]
    > Você pode remover um padrão de uma assinatura, mas não de um grupo de gerenciamento. 

    A página política de segurança é aberta. Para a assinatura selecionada, ela mostra a política padrão, os padrões do setor e regulamentares e todas as iniciativas personalizadas que você criou.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Removendo um padrão normativo do painel de conformidade regulatória na central de segurança do Azure":::

1. Para o padrão que você deseja remover, selecione **desabilitar**. Uma janela de confirmação é exibida.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirme que você realmente deseja remover o padrão normativo selecionado":::

1. Selecione **Sim** na barra superior. O padrão será removido. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a **Adicionar padrões de conformidade** para monitorar sua conformidade com os padrões regulatórios e do setor.

Para obter material relacionado, consulte as seguintes páginas:

- [Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Painel de conformidade regulatória da central de segurança](security-center-compliance-dashboard.md) -saiba como acompanhar e exportar seus dados de conformidade regulatória com a central de segurança e as ferramentas externas
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)