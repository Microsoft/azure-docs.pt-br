---
title: Controles de aplicativo adaptáveis na Central de Segurança do Azure
description: Este documento ajuda você a usar o controle de aplicativo adaptável na central de segurança do Azure para permitir que aplicativos de lista em execução em computadores do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: a7ad5f661d4eca07deb0942c7684b89ac59931a2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834384"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Use controles de aplicativo adaptáveis para reduzir as superfícies de ataque de suas máquinas

Saiba mais sobre os benefícios dos controles de aplicativo adaptáveis da central de segurança do Azure e como você pode aprimorar sua segurança com esse recurso inteligente e controlado por dados.


## <a name="what-are-security-centers-adaptive-application-controls"></a>O que são controles de aplicativo adaptáveis da central de segurança?

Os controles de aplicativo adaptáveis são uma solução inteligente e automatizada para definir as listas de permissões de aplicativos conhecidos para seus computadores. 

Muitas vezes, as organizações têm coleções de computadores que executam rotineiramente os mesmos processos. A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e criar uma lista de softwares conhecidos. As listas de permissões são baseadas em suas cargas de trabalho específicas do Azure e você pode personalizar ainda mais as recomendações usando as instruções abaixo.

Quando você habilitar e configurar os controles de aplicativo adaptáveis, obterá alertas de segurança se qualquer aplicativo for executado além daqueles que você definiu como seguros.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Quais são os benefícios dos controles de aplicativo adaptáveis?

Definindo listas de aplicativos conhecidos e gerando alertas quando qualquer outra coisa é executada, você pode alcançar várias metas de proteção:

- Identificar possíveis malwares, até mesmo quaisquer que possam ser perdidos por soluções Antimalware
- Melhore a conformidade com as políticas de segurança local que determinam o uso somente de software licenciado
- Evite executar aplicativos antigos ou sem suporte
- Evitar software específico que é banido por sua organização
- Aumente a supervisão de aplicativos que acessam dados confidenciais

Não há opções de imposição disponíveis no momento. Os controles de aplicativo adaptáveis se destinam a fornecer alertas de segurança se qualquer aplicativo for executado além daqueles que você definiu como seguros.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer [Azure Defender para Servidores](defender-for-servers-introduction.md)|
|Computadores com suporte:|![Sim ](./media/icons/yes-icon.png) computadores Azure e não Azure executando Windows e Linux<br>![Sim, ](./media/icons/yes-icon.png) computadores [do Arc do Azure](../azure-arc/index.yml)|
|Funções e permissões necessárias:|O **leitor de segurança** e as funções de **leitor** podem exibir grupos e as listas de aplicativos conhecidos confiáveis<br>As funções de administrador de **colaborador** e de **segurança** podem editar grupos e as listas de aplicativos conhecidos confiáveis|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Habilitar controles de aplicativo em um grupo de computadores

Se a central de segurança tiver identificado grupos de computadores em suas assinaturas que executam consistentemente um conjunto de aplicativos semelhantes, você será solicitado a seguir a recomendação: **controles de aplicativo adaptáveis para definir aplicativos seguros devem ser habilitados em seus computadores**.

Selecione a recomendação ou abra a página controles de aplicativo adaptáveis para exibir a lista de aplicativos e grupos de computadores conhecidos e sugeridos.

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **controles de aplicativo adaptáveis**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Abrindo controles de aplicativo adaptáveis no painel do Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    A página **controles de aplicativo adaptáveis** é aberta com suas VMs agrupadas nas seguintes guias:

    - **Configurado** -grupos de computadores que já têm uma lista de permissões definida de aplicativos. Para cada grupo, a guia configurada mostra:
        - o número de computadores no grupo
        - alertas recentes

    - **Recomendado** – grupos de computadores que executam consistentemente os mesmos aplicativos e não têm uma lista de permissões configurada. Recomendamos que você habilite controles de aplicativo adaptáveis para esses grupos.
    
      > [!TIP]
      > Se você vir um nome de grupo com o prefixo "Review Group", ele conterá computadores com uma lista parcialmente consistente de aplicativos. A central de segurança não pode ver um padrão, mas recomenda revisar esse grupo para ver se _você_ pode definir manualmente algumas regras de controles de aplicativo adaptáveis, conforme descrito em [editando a regra de controles de aplicativo adaptáveis de um grupo](#edit-a-groups-adaptive-application-controls-rule).
      >
      > Você também pode mover computadores deste grupo para outros grupos, conforme descrito em [mover um computador de um grupo para outro](#move-a-machine-from-one-group-to-another).

    - **Nenhuma recomendação** – máquinas sem uma lista de permissões definida de aplicativos e que não dão suporte ao recurso. Seu computador pode estar nesta guia pelos seguintes motivos:
      - Falta um agente de Log Analytics
      - O agente de Log Analytics não está enviando eventos
      - É um computador Windows com uma política [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) já existente habilitada por um GPO ou uma política de segurança local

      > [!TIP]
      > A central de segurança precisa de pelo menos duas semanas de dados para definir as recomendações exclusivas por grupo de computadores. As máquinas que foram criadas recentemente ou que pertencem a assinaturas que foram habilitadas recentemente com o Azure defender serão exibidas na guia **sem recomendação** .


1. Abra a guia **recomendado** . Os grupos de computadores com as listas de permissões recomendadas são exibidos.

   ![Guia recomendada](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selecione um grupo. 

1. Para configurar sua nova regra, examine as várias seções desta página **configurar regras de controle de aplicativo** e o conteúdo, que será exclusivo para esse grupo específico de computadores:

   ![Configurar uma nova regra](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Selecionar computadores** -por padrão, todos os computadores no grupo identificado são selecionados. Desmarque qualquer para removê-los desta regra.
   
   1. **Aplicativos recomendados** -examine esta lista de aplicativos que são comuns aos computadores dentro deste grupo e recomendamos a execução.
   
   1. **Mais aplicativos** – examine esta lista de aplicativos que são vistos com menos frequência nos computadores dentro deste grupo ou que são conhecidos por serem exploráveis. Um ícone de aviso indica que um aplicativo específico pode ser usado por um invasor para ignorar uma lista de permissões de aplicativo. Recomendamos que você revise cuidadosamente esses aplicativos.

      > [!TIP]
      > Ambas as listas de aplicativos incluem a opção de restringir um aplicativo específico a determinados usuários. Adote o princípio de privilégios mínimos sempre que possível.
      > 
      > Os aplicativos são definidos por seus publicadores, se um aplicativo não tiver informações do Publicador (não assinado), uma regra de caminho será criada para o caminho completo do aplicativo específico.

   1. Para aplicar a regra, selecione **auditoria**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Editar a regra de controles de aplicativo adaptáveis de um grupo

Você pode optar por editar a lista de permissões para um grupo de computadores devido a alterações conhecidas em sua organização. 

Para editar as regras para um grupo de computadores:

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **controles de aplicativo adaptáveis**.

1. Na guia **configurada** , selecione o grupo com a regra que você deseja editar.

1. Examine as várias seções da página **configurar regras de controle de aplicativo** , conforme descrito em [habilitar controles de aplicativo adaptáveis em um grupo de computadores](#enable-application-controls-on-a-group-of-machines).

1. Opcionalmente, adicione uma ou mais regras personalizadas:

   1. Selecione **Adicionar regra**.

      ![Adicionar uma regra personalizada](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Se você estiver definindo um caminho seguro conhecido, altere o **tipo de regra** para ' caminho ' e insira um único caminho. Você pode incluir curingas no caminho.
   
      > [!TIP]
      > Alguns cenários para os quais curingas em um caminho podem ser úteis:
      > 
      > * Usando um caractere curinga no final de um caminho para permitir todos os executáveis dentro desta pasta e de subpastas.
      > * Usando um caractere curinga no meio de um caminho para habilitar um nome executável conhecido com um nome de pasta de alteração (por exemplo, pastas de usuário pessoais contendo um executável conhecido, nomes de pastas gerados automaticamente, etc.).
  
   1. Defina os usuários permitidos e os tipos de arquivo protegidos.

   1. Quando terminar de definir a regra, selecione **Adicionar**.

1. Para aplicar as alterações, selecione **salvar**.


## <a name="review-and-edit-a-groups-settings"></a>Examinar e editar as configurações de um grupo

1. Para exibir os detalhes e as configurações do grupo, selecione **configurações de grupo**

    Esse painel mostra o nome do grupo (que pode ser modificado), o tipo de sistema operacional, o local e outros detalhes relevantes.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="A página Configurações de grupo para controles de aplicativo adaptáveis" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Opcionalmente, modifique os modos de proteção de tipo de arquivo ou nome do grupo.

1. Selecione **aplicar** e **salvar**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Responder à recomendação "as regras de permissão daList em sua política de controle de aplicativo adaptável devem ser atualizadas"

Você verá essa recomendação quando o Machine Learning da central de segurança identificar o comportamento potencialmente legítimo que não era permitido anteriormente. A recomendação sugere novas regras para suas definições existentes para reduzir o número de alertas falsos positivos.

Para corrigir os problemas:

1. Na página recomendações, selecione as **regras de permissão daList em sua política de controle de aplicativo adaptável deve ser** uma recomendação atualizada para ver grupos com comportamento recentemente identificado e potencialmente legítimo.

1. Selecione o grupo com a regra que você deseja editar.

1. Examine as várias seções da página **configurar regras de controle de aplicativo** , conforme descrito em [habilitar controles de aplicativo adaptáveis em um grupo de computadores](#enable-application-controls-on-a-group-of-machines).

1. Para aplicar as alterações, selecione **auditoria**.




## <a name="audit-alerts-and-violations"></a>Alertas e violações de auditoria

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **controles de aplicativo adaptáveis**.

1. Para ver grupos com computadores que têm alertas recentes, examine os grupos listados na guia **configurado** .

1. Para investigar melhor, selecione um grupo.

   ![Alertas recentes](./media/security-center-adaptive-application/recent-alerts.png)

1. Para obter mais detalhes e a lista de computadores afetados, selecione um alerta.



## <a name="move-a-machine-from-one-group-to-another"></a>Mover um computador de um grupo para outro

Quando você move um computador de um grupo para outro, a política de controle de aplicativo aplicada a ele muda para as configurações do grupo para o qual você o moveu. Você também pode mover um computador de um grupo configurado para um grupo não configurado, fazendo com que você remova todas as regras de controle de aplicativo que foram aplicadas ao computador.

1. Abra o painel do Azure defender e, na área proteção avançada, selecione **controles de aplicativo adaptáveis**.

1. Na página **controles de aplicativo adaptáveis** , na guia **configurada** , selecione o grupo que contém o computador a ser movido.

1. Abra a lista de  **computadores configurados**.

1. Abra o menu do computador de três pontos no final da linha e selecione **mover**. O painel **mover computador para um grupo diferente** é aberto.

1. Selecione o grupo de destino e selecione **mover computador**.

1. Selecione **Salvar** para salvar as alterações.





## <a name="manage-application-controls-via-the-rest-api"></a>Gerenciar controles de aplicativo por meio da API REST 

Para gerenciar seus controles de aplicativo adaptáveis de forma programática, use nossa API REST. 

A documentação completa da API está [aqui](/rest/api/securitycenter/adaptiveapplicationcontrols).

Algumas das funções que estão disponíveis na API REST:

* A **lista** recupera todas as suas recomendações de grupo e fornece um JSON com um objeto para cada grupo.

* **Get** recupera o JSON com os dados de recomendação completos (ou seja, lista de computadores, regras de caminho/editor e assim por diante).

* **Put** configura sua regra (use o JSON que você recuperou com **Get** como o corpo para esta solicitação).
 
   > [!IMPORTANT]
   > A função **Put** espera menos parâmetros do que o JSON retornado pelo comando Get.
   >
   > Remova as propriedades a seguir antes de usar o JSON na solicitação Put: recommendationStatus, configurationStatus, issues, Location e sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Perguntas frequentes – controles de aplicativo adaptáveis

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Há alguma opção para impor os controles do aplicativo?
Não há opções de imposição disponíveis no momento. Os controles de aplicativo adaptáveis se destinam a fornecer **alertas de segurança** se qualquer aplicativo for executado além daqueles que você definiu como seguros. Eles têm uma variedade de benefícios ([quais são os benefícios dos controles de aplicativo adaptáveis?](#what-are-the-benefits-of-adaptive-application-controls)) e são extremamente personalizáveis, conforme mostrado nesta página.

 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a usar o controle de aplicativo adaptável na central de segurança do Azure para definir as listas de permissões de aplicativos em execução em seus computadores Azure e não Azure. Para saber mais sobre alguns dos outros recursos de proteção de carga de trabalho de nuvem da central de segurança, confira:

* [Noções básicas sobre acesso à VM JIT (just-in-time)](just-in-time-explained.md)
* [Protegendo seus clusters kubernetes do Azure](defender-for-kubernetes-introduction.md)