---
title: Automatizar a resposta a ameaças com guias estratégicos no Azure Sentinel | Microsoft Docs
description: Este artigo explica a automação no Azure Sentinel e mostra como usar os guias estratégicos para automatizar a prevenção e a resposta de ameaças.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609024"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatizar a resposta a ameaças com guias estratégicos no Azure Sentinel

Este artigo explica o que são os guias estratégicos do Azure Sentinel e como usá-los para implementar suas operações de orquestração de segurança, automação e resposta (disparar), obtendo resultados melhores enquanto economiza tempo e recursos.

## <a name="what-is-a-playbook"></a>O que é um guia estratégico?

As equipes SIEM/SOC normalmente são inundadas com alertas de segurança e incidentes regularmente, em volumes tão grandes que a equipe disponível está sobrecarregada. Isso resulta muito frequentemente em situações em que muitos alertas são ignorados e muitos incidentes não são investigados, deixando a organização vulnerável a ataques que passam despercebidos.

Muitos, se não a maioria, desses alertas e incidentes estão em conformidade com padrões recorrentes que podem ser tratados por conjuntos específicos e definidos de ações de correção.

Um guia estratégico é uma coleção dessas ações de correção que podem ser executadas do Azure Sentinel como uma rotina. Um guia estratégico pode ajudar a automatizar e orquestrar a resposta contra ameaças; Ele pode ser executado manualmente ou definido para ser executado automaticamente em resposta a alertas ou incidentes específicos, quando disparado por uma regra de análise ou uma regra de automação, respectivamente.

Os guias estratégicos são criados e aplicados no nível da assinatura, mas a guia **Guias estratégicos** (na folha nova **automação** ) exibe todos os guias estratégicos disponíveis em todas as assinaturas selecionadas.

### <a name="azure-logic-apps-basic-concepts"></a>Conceitos básicos do aplicativo lógico do Azure

Os guias estratégicos no Azure Sentinel se baseiam em fluxos de trabalho criados em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), um serviço de nuvem que ajuda a agendar, automatizar e orquestrar tarefas e fluxos de trabalho entre sistemas em toda a empresa. Isso significa que os guias estratégicos podem aproveitar toda a capacidade e a personalização dos modelos internos dos aplicativos lógicos.

> [!NOTE]
> Como os aplicativos lógicos do Azure são um recurso separado, encargos adicionais podem ser aplicados. Visite a página de preços dos [aplicativos lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

O aplicativo lógico do Azure se comunica com outros sistemas e serviços usando conectores. Veja a seguir uma breve explicação dos conectores e de alguns dos seus atributos importantes:

- **Conector gerenciado:** Um conjunto de ações e gatilhos que envolvem chamadas à API para um determinado produto ou serviço. O aplicativo lógico do Azure oferece centenas de conectores para se comunicar com serviços da Microsoft e não Microsoft.
  - [Lista de todos os conectores de aplicativos lógicos e sua documentação](/connectors/connector-reference/)

- **Conector personalizado:** Talvez você queira se comunicar com os serviços que não estão disponíveis como conectores pré-criados. Os conectores personalizados atendem a essa necessidade, permitindo que você crie (e até mesmo Compartilhe) um conector e defina seus próprios gatilhos e ações.
  - [Criar seus próprios conectores de aplicativos lógicos personalizados](/connectors/custom-connectors/create-logic-apps-connector)

- **Conector do Azure Sentinel:** Para criar guias estratégicos que interagem com o Azure Sentinel, use o conector do Azure Sentinel.
  - [Documentação do conector do Azure Sentinel](/connectors/azuresentinel/)

- **Gatilho:** Um componente de conector que inicia um guia estratégico. Ele define o esquema que o guia estratégico espera receber quando disparado. O conector do Azure Sentinel atualmente tem dois gatilhos:
  - [Gatilho de alerta](/connectors/azuresentinel/#triggers): o guia estratégico recebe o alerta como sua entrada.
  - [Gatilho de incidente](/connectors/azuresentinel/#triggers): o guia estratégico recebe o incidente como sua entrada, juntamente com todos os seus alertas e entidades incluídos.

    > [!IMPORTANT]
    >
    > - O recurso de **gatilho de incidente** para guias estratégicos está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

- **Ações:** Ações são todas as etapas que acontecem após o gatilho. Eles podem ser organizados em sequência, em paralelo ou em uma matriz de condições complexas.

- **Campos dinâmicos:** Campos temporários, determinados pelo esquema de saída de gatilhos e ações e preenchidos por sua saída real, que podem ser usados nas ações a seguir.

### <a name="permissions-required"></a>Permissões necessárias

 Para dar à equipe do SecOps a capacidade de usar aplicativos lógicos para operações de orquestração de segurança, automação e resposta (disparar) – ou seja, para criar e executar guias estratégicos – no Azure Sentinel, você pode atribuir funções do Azure, seja para membros específicos da sua equipe de operações de segurança ou para toda a equipe. O seguinte descreve as diferentes funções disponíveis e as tarefas para as quais elas devem ser atribuídas:

#### <a name="azure-roles-for-logic-apps"></a>Funções do Azure para aplicativos lógicos

- O **colaborador do aplicativo lógico** permite que você gerencie aplicativos lógicos e execute guias estratégicos, mas não pode alterar o acesso a eles (para que você precise da função de **proprietário** ).
- O **operador de aplicativo lógico** permite que você leia, habilite e desabilite aplicativos lógicos, mas não pode editá-los ou atualizá-los.

#### <a name="azure-roles-for-sentinel"></a>Funções do Azure para sentinela

- A função **colaborador do Azure Sentinel** permite anexar um guia estratégico a uma regra de análise.
- A função de **respondente do Sentinela do Azure** permite que você execute um guia estratégico manualmente.
- O **colaborador de automação do Sentinela do Azure** permite que as regras de automação executem guias estratégicos. Ele não pode ser usado para nenhuma outra finalidade.

#### <a name="learn-more"></a>Saiba mais

- [Saiba mais sobre as funções do Azure nos aplicativos lógicos do Azure](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Saiba mais sobre as funções do Azure no Azure Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Etapas para criar um guia estratégico

- [Defina o cenário de automação](#use-cases-for-playbooks).

- [Crie o aplicativo lógico do Azure](tutorial-respond-threats-playbook.md).

- [Teste seu aplicativo lógico](#run-a-playbook-manually-on-an-alert).

- Anexe o guia estratégico a uma [regra de automação](#incident-creation-automated-response) ou uma regra de [análise](#alert-creation-automated-response)ou [execute manualmente quando necessário](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Casos de uso para guias estratégicos

A plataforma de aplicativos lógicos do Azure oferece centenas de ações e gatilhos, portanto, quase qualquer cenário de automação pode ser criado. O Azure Sentinel recomenda a partir dos seguintes cenários de SOC:

#### <a name="enrichment"></a>Enriquecimento

**Colete dados e anexe-os ao incidente para tomar decisões mais inteligentes.**

Por exemplo:

Um incidente do Azure Sentinel foi criado a partir de um alerta por uma regra de análise que gera entidades de endereço IP.

O incidente dispara uma regra de automação que executa um guia estratégico com as seguintes etapas:

- Iniciar quando um [novo incidente do Azure Sentinel for criado](/connectors/azuresentinel/#triggers). As entidades representadas no incidente são armazenadas nos campos dinâmicos do gatilho de incidente.

- Para cada endereço IP, consulte um provedor de inteligência contra ameaças externas, como [total de vírus](https://www.virustotal.com/), para recuperar mais dados.

- Adicione os dados e as informações retornados como comentários do incidente.

#### <a name="bi-directional-sync"></a>Sincronização bidirecional

**Os guias estratégicos podem ser usados para sincronizar seus incidentes do Azure Sentinel com outros sistemas de emissão de tíquetes.**

Por exemplo:

Crie uma regra de automação para toda a criação de incidentes e anexe um guia estratégico que abre um tíquete no ServiceNow:

- Iniciar quando um [novo incidente do Azure Sentinel for criado](/connectors/azuresentinel/#triggers).

- Crie um novo tíquete no [ServiceNow](/connectors/service-now/).

- Inclua no tíquete o nome do incidente, os campos importantes e uma URL para o incidente do Azure Sentinel para facilitar a dinamização.

#### <a name="orchestration"></a>Orquestração

**Use a plataforma de bate-papo do SOC para controlar melhor a fila de incidentes.**

Por exemplo:

Um incidente do Azure Sentinel foi criado a partir de um alerta por uma regra de análise que gera nome de usuário e entidades de endereço IP.

O incidente dispara uma regra de automação que executa um guia estratégico com as seguintes etapas:

- Iniciar quando um [novo incidente do Azure Sentinel for criado](/connectors/azuresentinel/#triggers).

- Envie uma mensagem ao canal de operações de segurança no [Microsoft Teams](/connectors/teams/) ou na [margem de atraso](/connectors/slack/) para verificar se os analistas de segurança estão cientes do incidente.

- Envie todas as informações no alerta por email para o administrador de rede sênior e administrador de segurança. A mensagem de email incluirá os botões **Bloquear** e **ignorar** a opção do usuário.

- Aguarde até que uma resposta seja recebida dos administradores e continue a execução.

- Se os administradores tiverem escolhido **Bloquear**, envie um comando para o firewall para bloquear o endereço IP no alerta e outro para o Azure ad para desabilitar o usuário.

#### <a name="response"></a>Resposta

**Responda imediatamente a ameaças, com dependências humanas mínimas.**

Dois exemplos:

**Exemplo 1:** Responder a uma regra de análise que indica um usuário comprometido, como descoberto pelo [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Iniciar quando um [novo incidente do Azure Sentinel for criado](/connectors/azuresentinel/#triggers).

   - Para cada entidade de usuário no incidente suspeita de comprometimento:

     - Envie uma mensagem de equipe para o usuário, solicitando confirmação de que o usuário realizou a ação suspeita.

     - Verifique com Azure AD Identity Protection para [confirmar o status do usuário como comprometido](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection rotulará o usuário como **arriscado** e aplicará qualquer política de imposição já configurada-por exemplo, para exigir que o usuário use o MFA na próxima vez que entrar.

        > [!NOTE]
        > O manual não inicia nenhuma ação de imposição no usuário nem inicia nenhuma configuração de política de imposição. Ele apenas informa Azure AD Identity Protection para aplicar as políticas já definidas conforme apropriado. Qualquer imposição depende totalmente das políticas apropriadas que estão sendo definidas no Azure AD Identity Protection.

**Exemplo 2:** Responder a uma regra de análise que indica um computador comprometido, conforme descoberto pelo [Microsoft defender para ponto de extremidade](/windows/security/threat-protection/):

   - Iniciar quando um [novo incidente do Azure Sentinel for criado](/connectors/azuresentinel/#triggers).

   - Use a ação **entidades-obter hosts** no Azure Sentinel para analisar os computadores suspeitos que estão incluídos nas entidades do incidente.

   - Emita um comando para o Microsoft defender for Endpoint para [isolar os computadores](/connectors/wdatp/#actions---isolate-machine) no alerta.

## <a name="how-to-run-a-playbook"></a>Como executar um guia estratégico

Os guias estratégicos podem ser executados de forma **manual** ou **automática**.

Executá-los manualmente significa que, quando você receber um alerta, você pode optar por executar uma guia estratégico sob demanda como uma resposta para o alerta selecionado. Atualmente, esse recurso tem suporte apenas para alertas, não para incidentes.

Executá-los automaticamente significa defini-los como uma resposta automatizada em uma regra de análise (para alertas) ou como uma ação em uma regra de automação (para incidentes). [Saiba mais sobre regras de automação](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Definir uma resposta automatizada

As equipes de operações de segurança podem reduzir significativamente sua carga de trabalho automatizando totalmente as respostas de rotina para tipos recorrentes de incidentes e alertas, permitindo que você se concentre mais em incidentes e alertas exclusivos, na análise de padrões, na busca de ameaças e muito mais.

Definir a resposta automatizada significa que sempre que uma regra de análise for disparada, além de criar um alerta, a regra executará um guia estratégico, que receberá como entrada o alerta criado pela regra.

Se o alerta criar um incidente, o incidente disparará uma regra de automação que pode, por sua vez, executar um guia estratégico, que receberá como entrada o incidente criado pelo alerta.

#### <a name="alert-creation-automated-response"></a>Resposta automatizada de criação de alerta

Para guias estratégicos que são disparados pela criação de alerta e recebem alertas como suas entradas (a primeira etapa é "quando um alerta do Azure Sentinel é disparado"), anexe o manual a uma regra de análise:

1. Edite a [regra de análise](tutorial-detect-threats-custom.md) que gera o alerta para o qual você deseja definir uma resposta automática.

1. Em **automação de alerta** na guia **resposta automática** , selecione o guia estratégico ou os guias estratégicos que esta regra de análise irá disparar quando um alerta for criado.

#### <a name="incident-creation-automated-response"></a>Resposta automatizada de criação de incidentes

Para guias estratégicos que são disparados pela criação de incidentes e recebem incidentes como suas entradas (a primeira etapa é "quando um incidente do Azure Sentinel é disparado"), crie uma regra de automação e defina uma ação de **guia estratégico** nela. Isso pode ser feito de duas maneiras:

- Edite a regra de análise que gera o incidente para o qual você deseja definir uma resposta automática. Em **automação de incidentes** , na guia **resposta automática** , crie uma regra de automação. Isso criará uma resposta automática somente para esta regra de análise.

- Na guia **regras de automação** na folha **automação** , crie uma nova regra de automação e especifique as condições apropriadas e as ações desejadas. Esta regra de automação será aplicada a qualquer regra de análise que atenda às condições especificadas.

    > [!NOTE]
    > Para executar um guia estratégico de uma regra de automação, o Azure Sentinel usa uma conta de serviço especificamente autorizada para fazer isso. O uso dessa conta (em oposição à sua conta de usuário) aumenta o nível de segurança do serviço e permite que a API de regras de automação dê suporte a casos de uso de CI/CD.
    >
    > Essa conta deve receber permissões explícitas para o grupo de recursos em que reside o guia estratégico. Nesse ponto, qualquer regra de automação poderá executar qualquer guia estratégico nesse grupo de recursos.
    >
    > Quando você adicionar a ação **executar guia estratégico** a uma regra de automação, uma lista suspensa de guias estratégicos será exibida. Os guias estratégicos aos quais o Azure Sentinel não tem permissões serão mostrados como indisponíveis ("esmaecido"). Você pode conceder permissão para o Azure Sentinel no local selecionando o link **gerenciar permissões de guias estratégicos** .
    >
    > Em um cenário de multilocatário ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)), você deve definir as permissões no locatário em que o guia estratégico reside, mesmo que a regra de automação que chama o guia estratégico esteja em um locatário diferente. Para fazer isso, você deve ter permissões de **proprietário** no grupo de recursos do guia estratégico.

Consulte as [instruções completas para criar regras de automação](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Executar um guia estratégico manualmente em um alerta

O gatilho manual está disponível no portal do Azure Sentinel nas seguintes folhas:

- No modo de exibição **incidentes** , escolha um incidente específico, abra sua guia **alertas** e escolha um alerta.

- Em **investigação**, escolha um alerta específico.

1. Clique em **Exibir guias estratégicos** para o alerta escolhido. Você obterá uma lista de todos os guias estratégicos que começam com um **quando um alerta do Azure Sentinel é disparado** e ao qual você tem acesso.

1. Clique em **executar** na linha de um guia estratégico específico para dispará-lo.

1. Selecione a guia **execuções** para exibir uma lista de todas as horas em que qualquer guia estratégico foi executado nesse alerta. Pode levar alguns segundos para que qualquer execução apenas concluída apareça na lista.

1. Clicar em uma execução específica abrirá o log de execução completa em aplicativos lógicos.

### <a name="run-a-playbook-manually-on-an-incident"></a>Executar um guia estratégico manualmente em um incidente

Ainda não tem suporte. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Gerencie seus guias estratégicos

Na guia **Guias estratégicos** , aparece uma lista de todos os guias estratégicos aos quais você tem acesso, filtrados pelas assinaturas que são atualmente exibidas no Azure. O filtro de assinaturas está disponível no menu **diretório + assinatura** no cabeçalho da página global.

Clicar em um nome de guia estratégico direciona você para a página principal do guia estratégico nos aplicativos lógicos. A coluna **status** indica se ela está habilitada ou desabilitada.

O **tipo de gatilho** representa o gatilho de aplicativos lógicos que inicia este guia estratégico.

| Tipo de gatilho | Indica os tipos de componentes no manual |
|-|-|
| **Incidente/alerta do Azure Sentinel** | O guia estratégico é iniciado com um dos gatilhos Sentinel (alerta, incidente) |
| **Usando a ação do Azure Sentinel** | O guia estratégico é iniciado com um gatilho não Sentinel, mas usa uma ação do Azure Sentinel |
| **Outras** | O manual não inclui nenhum componente sentinela |
| **Não inicializado** | O guia estratégico foi criado, mas não contém componentes (gatilhos ou ações). |
|

Na página do aplicativo lógico do guia estratégico, você pode ver mais informações sobre o guia estratégico, incluindo um log de todos os horários em que ele foi executado e o resultado (êxito ou falha e outros detalhes). Você também pode inserir o designer de aplicativos lógicos e editar o guia estratégico diretamente, se tiver as permissões apropriadas.

### <a name="api-connections"></a>Conexões de API

As conexões de API são usadas para conectar aplicativos lógicos a outros serviços. Toda vez que uma nova autenticação para um conector de aplicativos lógicos é feita, um novo recurso do tipo **conexão de API** é criado e contém as informações fornecidas ao configurar o acesso ao serviço.

Para ver todas as conexões de API, insira *conexões de API* na caixa de pesquisa de cabeçalho do portal do Azure. Observe as colunas de interesse:

- Nome de exibição-o nome "amigável" que você fornece à conexão toda vez que criar uma.
- Status-indica o status da conexão: erro, conectado.
- Grupo de recursos-as conexões de API são criadas no grupo de recursos do recurso de guias estratégicos (aplicativos lógicos).

Outra maneira de exibir as conexões de API seria ir para a folha **todos os recursos** e filtrá-la por tipo *conexão de API*. Dessa forma, permite a seleção, a marcação e a exclusão de várias conexões de uma vez.

Para alterar a autorização de uma conexão existente, insira o recurso de conexão e selecione **Editar conexão de API**.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: usar guias estratégicos para automatizar as respostas de ameaças no Azure Sentinel](tutorial-respond-threats-playbook.md)
