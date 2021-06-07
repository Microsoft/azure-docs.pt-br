---
title: Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário
description: Este artigo descreve o processo de implantação de integração de sistemas de RH de nuvem, como workday e SuccessFactors, com Azure Active Directory. A integração do Azure AD ao seu sistema de RH na nuvem resulta em um sistema completo de gerenciamento do ciclo de vida da identidade.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256704"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário

Historicamente, a equipe de TI dependia de métodos manuais para criar, atualizar e excluir funcionários. Eles usavam métodos como upload de arquivos CSV ou scripts personalizados para sincronizar dados de funcionários. Esses processos de provisionamento são propensos a erros, inseguros e difíceis de gerenciar.

Para gerenciar os ciclos de vida de identidade de funcionários, fornecedores ou funcionários contingentes, o [serviço de provisionamento de usuários do Azure AD (Azure Active Directory)](../app-provisioning/user-provisioning.md) oferece integração a aplicativos de RH (recursos humanos) baseados em nuvem. Os aplicativos de exemplo incluem o Workday ou o SuccessFactors.

O Azure AD usa essa integração para habilitar os seguintes fluxos de trabalho do aplicativo de RH na nuvem:

- **Provisionar usuários para o Active Directory:** provisione conjuntos de usuários selecionados de um aplicativo de RH na nuvem em um ou mais domínios do Active Directory.
- **Provisionar usuários somente na nuvem para o Azure AD:** em cenários em que o Active Directory não é usado, provisione usuários diretamente do aplicativo de RH na nuvem para o Azure AD.
- **Fazer write-back para o aplicativo de RH na nuvem:** grave os endereços de email e os atributos de nome de usuário do Azure AD de volta no aplicativo de RH na nuvem.

> [!NOTE]
> Este plano de implantação mostra como implantar seus fluxos de trabalho de aplicativo de RH na nuvem com o provisionamento de usuário do Azure AD. Para obter informações sobre como implantar o provisionamento automático de usuário em aplicativos SaaS (software como serviço), consulte [planejar uma implantação de provisionamento de usuário automático](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de provisionamento de usuários do Azure AD permite a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidades baseado em RH:

- **Contratação de novos funcionários:** quando um novo funcionário é adicionado ao aplicativo de RH na nuvem, uma conta de usuário é criada automaticamente no Active Directory e no Azure AD com a opção de fazer write-back para o endereço de email e os atributos de nome de usuário no aplicativo de RH na nuvem.
- **Atualizações de perfil e atributo de funcionário:** quando um registro de funcionário, como nome, cargo ou gerente, é atualizado no aplicativo de RH na nuvem, a conta de usuário dele é atualizada automaticamente no Active Directory e no Azure AD.
- **Rescisão de funcionários:** quando um funcionário é encerrado no aplicativo de RH na nuvem, a conta de usuário dele é desabilitada automaticamente no Active Directory e no Azure AD.
- **Recontratações de funcionário:** quando um funcionário é recontratado no aplicativo de RH na nuvem, a conta antiga dele ser reativada ou provisionada de novo automaticamente para o Active Directory e o Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem é essa integração é mais adequada?

A integração do aplicativo de RH na nuvem com o provisionamento de usuários do Azure AD é ideal para as organizações que:

- Querem uma solução predefinida baseada em nuvem para o provisionamento de usuários de RH na nuvem.
- Exigem o provisionamento direto de usuário do aplicativo de RH na nuvem para o Active Directory ou o Azure AD.
- Exigem que os usuários sejam provisionados usando os dados obtidos do aplicativo de RH na nuvem.
- Exigem que o ingresso, a movimentação e a saída de usuários sejam sincronizados com uma ou mais florestas, domínios e unidades organizacionais do Active Directory com base apenas em informações de mudança detectadas no aplicativo de RH na nuvem.
- Use Microsoft 365 para email.

## <a name="learn"></a>Learn

O provisionamento de usuário cria uma base para a governança de identidade contínua. Ele aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

- **Sistema de origem**: o repositório de usuários do qual o Azure ad provisiona. Um exemplo é um aplicativo de RH de nuvem, como workday ou SuccessFactors.
- **Sistema de destino**: o repositório de usuários para os quais o Azure ad provisiona. Os exemplos são Active Directory, Azure AD, Microsoft 365 ou outros aplicativos SaaS.
- Emissores **– movimentadores-** permissers processam: um termo usado para novas contratações, transferências e rescisão usando um aplicativo de RH na nuvem como um sistema de registros. O processo é concluído quando o serviço provisiona com êxito os atributos necessários para o sistema de destino.

### <a name="key-benefits"></a>Principais benefícios

Essa funcionalidade de provisionamento de TI controlada por RH oferece os seguintes benefícios de negócios significativos:

- **Aumentar a produtividade:** Agora você pode automatizar a atribuição de contas de usuário e licenças de Microsoft 365 e fornecer acesso a grupos de chaves. A automação de atribuições oferece a novos contratados acesso imediato a ferramentas de trabalho e aumenta a produtividade.
- **Gerenciar riscos:** você pode aumentar a segurança automatizando as alterações com base no status do funcionário ou em associações de grupo com os dados que fluem do aplicativo de RH na nuvem. Automatizar as alterações garante que as identidades do usuário e o acesso a aplicativos de chave sejam atualizados automaticamente quando os usuários fizerem a transição ou saem da organização.
- **Abordar a conformidade e a governança:** o Azure AD dá suporte a logs de auditoria nativos para solicitações de provisionamento de usuário executadas por aplicativos de sistemas de origem e de destino. Com a auditoria, você pode controlar quem tem acesso aos aplicativos de uma única tela.
- **Gerenciar o custo:** o provisionamento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário desenvolvidas personalizadas criadas ao longo do tempo usando plataformas herdadas e desatualizadas.

### <a name="licensing"></a>Licenças

Para configurar o aplicativo de RH na nuvem para a integração de provisionamento de usuário do Azure AD, você precisa de uma [licença de Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para o aplicativo de RH de nuvem, como workday ou SuccessFactors.

Você também precisa de uma licença de assinatura Azure AD Premium P1 ou superior válida para cada usuário que será originado do aplicativo de RH na nuvem e provisionado para o Active Directory ou o Azure AD. Qualquer número inadequado de licenças pertencentes ao aplicativo de RH na nuvem pode levar a erros durante o provisionamento do usuário.

### <a name="prerequisites"></a>Pré-requisitos

- Administrador de [identidade híbrida](../roles/permissions-reference.md#hybrid-identity-administrator)  do Azure ad para configurar o agente de provisionamento do Azure ad Connect.
- Função de [administrador de aplicativos](../roles/permissions-reference.md#application-administrator) do Azure ad para configurar o aplicativo de provisionamento no portal do Azure
- Uma instância de teste e produção do aplicativo de RH de nuvem.
- Permissões de administrador no aplicativo de RH de nuvem para criar um usuário de integração do sistema e fazer alterações para testar dados de funcionários para fins de teste.
- Para o provisionamento de usuário para Active Directory, um servidor executando o Windows Server 2012 ou superior com o .NET 4.7.1 + Runtime é necessário para hospedar o agente de provisionamento de Azure AD Connect
- [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md) para sincronizar usuários entre o Active Directory e o Azure AD.

### <a name="training-resources"></a>Recursos de treinamento

| **Recursos** | **Link e descrição** |
|:-|:-|
| Vídeos | [O que é o provisionamento de usuário no diretório ativo do Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implantar o provisionamento de usuário no Active Directory do Azure](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Configurar o Workday para provisionamento automático do usuário](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Perguntas frequentes | [Provisionamento automatizado de usuários](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Provisionamento do WORKDAY para o Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura da solução

O exemplo a seguir descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns e inclui:

- **Fluxo de dados de RH autoritativo do aplicativo de RH de nuvem para Active Directory.** Nesse fluxo, o evento de RH (emissores-movimentadores-perprocessors-periões) é iniciado no locatário do aplicativo de RH na nuvem. O serviço de provisionamento do Azure AD e o agente de provisionamento do Azure AD Connect provisionar os dados do usuário do locatário do aplicativo de RH na nuvem no Active Directory. Dependendo do evento, ele pode levar a criar, atualizar, habilitar e desabilitar operações no Active Directory.
- **Sincronize com o Azure AD e Write-back de email e nome de usuário do local Active Directory para o aplicativo de RH na nuvem.** Depois que as contas são atualizadas no Active Directory, elas são sincronizadas com o Azure AD por meio de Azure AD Connect. Os endereços de email e os atributos de nome de usuário podem ser gravados no locatário do aplicativo de RH na nuvem.

![Diagrama de fluxo de trabalho](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

As seguintes etapas principais são indicadas no diagrama:  

1. A **equipe de RH** executa as transações no locatário do aplicativo de RH na nuvem.
2. O **serviço de provisionamento do Azure ad** executa os ciclos agendados do locatário do aplicativo de RH na nuvem e identifica as alterações que precisam ser processadas para sincronização com Active Directory.
3. O **serviço de provisionamento do Azure ad** invoca o agente de provisionamento do Azure ad Connect com uma carga de solicitação que contém as operações de Active Directory criar, atualizar, habilitar e desabilitar.
4. **Azure ad Connect agente de provisionamento** usa uma conta de serviço para gerenciar Active Directory dados da conta.
5. **Azure ad Connect** executa a [sincronização](../hybrid/how-to-connect-sync-whatis.md) Delta para efetuar pull de atualizações no Active Directory.
6. **Active Directory** atualizações são sincronizadas com o Azure AD.
7. O **serviço de provisionamento do Azure ad** grava o atributo de email e o nome de usuário do Azure ad para o locatário do aplicativo de RH na nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md). Além disso, certifique-se de que as funções de Stakeholder no projeto sejam bem compreendidas. Documente os participantes e suas entradas e responsabilidades de projeto.

Inclua um representante da organização de RH que possa fornecer entradas sobre processos de negócios e identidade de trabalho existentes, além de requisitos de processamento de dados de trabalho.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente com seus usuários sobre quando e como sua experiência será alterada. Avise-os sobre como obter suporte se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

A integração dos processos de negócios de RH e dos fluxos de trabalho de identidade do aplicativo de RH na nuvem aos sistemas de destino requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e teste de ponta a ponta antes que você possa implantar a solução na produção.

Execute a configuração inicial em um [ambiente piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) antes de dimensioná-lo para todos os usuários em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecionar aplicativos do conector de provisionamento de RH de nuvem

Para facilitar o provisionamento de fluxos de trabalho do Azure AD entre o aplicativo de RH de nuvem e Active Directory, você pode adicionar vários aplicativos de conector de provisionamento da Galeria de aplicativos do Azure AD:

- **Aplicativo de RH de nuvem para Active Directory provisionamento de usuário**: esse aplicativo do conector de provisionamento facilita o provisionamento de contas de usuário do aplicativo de RH na nuvem para um único domínio de Active Directory. Se você tiver vários domínios, será possível adicionar uma instância desse aplicativo da galeria de aplicativos do Azure AD para cada domínio do Active Directory que precisar ser provisionado.
- **Aplicativo de RH de nuvem para provisionamento de usuário do Azure ad**: embora Azure ad Connect seja a ferramenta que deve ser usada para sincronizar Active Directory usuários ao Azure AD, esse aplicativo de conector de provisionamento pode ser usado para facilitar o provisionamento de usuários somente de nuvem do aplicativo de RH de nuvem para um único locatário do Azure AD.
- **Write-back do aplicativo de RH na nuvem**: este aplicativo do conector de provisionamento facilita o Write-back dos endereços de email do usuário do Azure ad para o aplicativo de RH na nuvem.

Por exemplo, a imagem a seguir lista os aplicativos do conector do workday que estão disponíveis na Galeria de aplicativos do Azure AD.

![Galeria de aplicativos do portal Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Gráfico de fluxo de decisão

Use o gráfico de fluxo de decisão a seguir para identificar quais aplicativos de provisionamento de RH de nuvem são relevantes para seu cenário.

![Gráfico de fluxo de decisão](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Criar a topologia de implantação do agente de provisionamento do Azure AD Connect

A integração de provisionamento entre o aplicativo de RH na nuvem e o Active Directory requer quatro componentes:

- Locatário do aplicativo de RH na nuvem
- Aplicativo do conector de provisionamento
- Agente de provisionamento do Azure AD Connect
- Domínio do Active Directory

A topologia de implantação do agente de provisionamento do Azure AD Connect depende do número de locatários do aplicativo de RH de nuvem e Active Directory domínios filho que você planeja integrar. Se você tiver vários domínios de Active Directory, dependerá se os domínios de Active Directory são contíguos [ou separados](/windows-server/identity/ad-ds/plan/disjoint-namespace).

Com base em sua decisão, escolha um dos cenários de implantação:

- Locatário de aplicativo de RH de nuvem única-> direcionar um ou vários domínios filho Active Directory em uma floresta confiável
- Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de Active Directory não contíguo

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Locatário de aplicativo de RH de nuvem única-> direcionar um ou vários domínios filho Active Directory em uma floresta confiável

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect a serem implantados|Dois (para alta disponibilidade e failover)
|Número de aplicativos do conector de provisionamento a serem configurados|Um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão de Active Directory controladores de domínio geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes locais](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de Active Directory não contíguo

Esse cenário envolve o provisionamento de usuários do aplicativo de RH na nuvem para domínios em florestas de Active Directory não contíguos.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect para implantar no local|Dois por floresta de Active Directory não contíguo|
|Número de aplicativos do conector de provisionamento a serem configurados|Um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão de Active Directory controladores de domínio geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Floresta de Active Directory de locatário de aplicativo de RH de nuvem única](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento do Azure AD Connect

O aplicativo de RH na nuvem para Active Directory solução de provisionamento de usuário requer que você implante um ou mais Azure AD Connect agentes de provisionamento em servidores que executam o Windows 2012 R2 ou superior. Os servidores devem ter, no mínimo, 4 GB de RAM e o tempo de execução do .NET 4.7.1 +. Verifique se o servidor host tem acesso à rede para o domínio de Active Directory de destino.

Para preparar o ambiente local, o assistente de configuração do agente de provisionamento do Azure AD Connect registra o agente com seu locatário do Azure AD, [abre portas](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [permite o acesso a URLs](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e dá suporte à [configuração de proxy HTTPS de saída](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

O agente de provisionamento usa uma conta de serviço para se comunicar com os domínios de Active Directory. Antes de instalar o agente, crie uma conta de serviço em Active Directory usuários e computadores que atendam aos seguintes requisitos:

- Uma senha que não expira
- Permissões de controle delegado para ler, criar, excluir e gerenciar contas de usuário

Você pode selecionar controladores de domínio que devem lidar com solicitações de provisionamento. Se você tiver vários controladores de domínio distribuídos geograficamente, instale o agente de provisionamento no mesmo site que seus controladores de domínio preferenciais. Esse posicionamento melhora a confiabilidade e o desempenho da solução de ponta a ponta.

Para alta disponibilidade, você pode implantar mais de um agente de provisionamento do Azure AD Connect. Registre o agente para lidar com o mesmo conjunto de domínios de Active Directory locais.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planejar filtros de escopo e mapeamento de atributos

Quando você habilita o provisionamento do aplicativo de RH na nuvem para Active Directory ou o Azure AD, o portal do Azure controla os valores de atributo por meio do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Definir filtros de escopo

Use [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir as regras baseadas em atributo que determinam quais usuários devem ser provisionados do aplicativo de RH na nuvem para Active Directory ou o Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- O aplicativo de RH em nuvem é usado para trazer tanto funcionários quanto trabalhadores contingentes?
- Você planeja usar o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD para gerenciar funcionários e operadores contingentes?
- Você planeja distribuir o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD apenas para um subconjunto dos usuários do aplicativo de RH na nuvem? Um exemplo pode ser apenas funcionários.

Dependendo de seus requisitos, ao configurar mapeamentos de atributo, você pode definir o campo **escopo do objeto de origem** para selecionar quais conjuntos de usuários no aplicativo de RH em nuvem devem estar no escopo para provisionamento para Active Directory. Para obter mais informações, consulte o tutorial do aplicativo de RH na nuvem para filtros de escopo mais usados.

### <a name="determine-matching-attributes"></a>Determinar atributos correspondentes

Com o provisionamento, você obtém a capacidade de corresponder as contas existentes entre o sistema de origem e de destino. Ao integrar o aplicativo de RH de nuvem com o serviço de provisionamento do Azure AD, você pode [Configurar o mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) para determinar quais dados de usuário devem fluir do aplicativo de RH na nuvem para Active Directory ou o Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- Qual é a ID exclusiva neste aplicativo de RH de nuvem que é usada para identificar cada usuário?
- De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? As recontratações mantêm suas IDs de funcionário antigas?
- Você processa contratações no futuro e cria Active Directory contas com antecedência?
- De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma?
- Os usuários convertidos mantêm suas contas Active Directory antigas ou obtêm novos?

Dependendo dos seus requisitos, o Azure AD dá suporte ao mapeamento direto de atributo para atributo fornecendo valores constantes ou [gravando expressões para mapeamentos de atributo](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá um controle definitivo do que é populado no atributo de aplicativo de destino. Você pode usar a [API Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) e o explorador do Graph para exportar os mapeamentos e o esquema de atributo de provisionamento de usuário para um arquivo JSON e importá-los de volta para o Azure AD.

Por padrão, o atributo no aplicativo de RH de nuvem que representa a ID de funcionário exclusiva é usado como o atributo correspondente *mapeado para o atributo exclusivo em Active Directory.* Por exemplo, no cenário de aplicativo do workday, o atributo **workeraction** do **workday** é mapeado para o atributo **EmployeeID** Active Directory.

Você pode definir vários atributos correspondentes e atribuir precedência correspondente. Elas são avaliadas na precedência correspondente. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.

Você também pode [Personalizar os mapeamentos de atributo padrão](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), como alterar ou excluir mapeamentos de atributo existentes. Você também pode criar novos mapeamentos de atributo de acordo com suas necessidades de negócios. Para obter mais informações, consulte o tutorial do aplicativo de RH na nuvem (como [workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) para obter uma lista de atributos personalizados a serem mapeados.

### <a name="determine-user-account-status"></a>Determinar o status da conta de usuário

Por padrão, o aplicativo do conector de provisionamento mapeia o status do perfil de usuário de RH para o status da conta de usuário no Active Directory ou no Azure AD para determinar se a conta de usuário deve ser habilitada ou desabilitada.

Ao iniciar o processo de Joiners-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Entradas** | De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? As recontratações mantêm suas IDs de funcionário antigas? |
| | Você processa contratações no futuro e cria Active Directory contas com antecedência? Essas contas são criadas em um estado habilitado ou desabilitado? |
| | De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma? |
| | Os usuários convertidos mantêm suas contas Active Directory antigas ou obtêm novos? |
| **Partidas** | Os encerramentos são tratados de forma diferente para funcionários e funcionários contingentes no Active Directory? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador de contingentes afetam contas de Active Directory existentes? |
| | Como processar a operação de rescindir no Active Directory? As operações de rescindir precisam ser manipuladas se contratações com data futuras são criadas no Active Directory como parte do processo do associador. |

Dependendo dos seus requisitos, você pode personalizar a lógica de mapeamento usando [expressões do Azure ad](../app-provisioning/functions-for-customizing-application-data.md) para que a conta de Active Directory seja habilitada ou desabilitada com base em uma combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapear aplicativo de RH de nuvem para Active Directory atributos de usuário

Cada aplicativo de RH na nuvem é fornecido com o aplicativo de RH de nuvem padrão para mapeamentos de Active Directory.

Ao iniciar o processo de Complementos-movimentadores-periões, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Entradas** | O processo de criação de conta Active Directory manual, automatizado ou parcialmente automatizado? |
| | Você planeja propagar atributos personalizados do aplicativo de RH de nuvem para Active Directory? |
| **Chuvas** | Quais atributos você gostaria de processar sempre que uma operação de movimentação ocorre no aplicativo de RH de nuvem? |
| | Você realiza qualquer validação de atributo específica no momento das atualizações do usuário? Em caso afirmativo, forneça detalhes. |
| **Partidas** | Os encerramentos são tratados de forma diferente para funcionários e funcionários contingentes no Active Directory? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador contingent afetam as contas de Active Directory existentes? |

Dependendo dos seus requisitos, você pode modificar os mapeamentos para atender às metas de integração. Para obter mais informações, consulte o tutorial específico do aplicativo de RH de nuvem (como [workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) para obter uma lista de atributos personalizados a serem mapeados.

### <a name="generate-a-unique-attribute-value"></a>Gerar um valor de atributo exclusivo

Ao iniciar o processo dos ASSOCIADORES, talvez seja necessário gerar valores de atributos exclusivos ao definir atributos como CN, samAccountName e o UPN, que tem restrições exclusivas.

A função [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) do Azure ad avalia cada regra e, em seguida, verifica o valor gerado quanto à exclusividade no sistema de destino. Para obter um exemplo, consulte [gerar valor exclusivo para o atributo userPrincipalName (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Atualmente, essa função só tem suporte para o workday Active Directory provisionamento de usuário. Ele não pode ser usado com outros aplicativos de provisionamento.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurar atribuição de contêiner de UO de Active Directory

É um requisito comum para posicionar Active Directory contas de usuário em contêineres com base em unidades de negócios, locais e departamentos. Quando você inicia um processo de movimentação e, se houver uma alteração na organização, talvez seja necessário mover o usuário de uma UO para outra em Active Directory.

Use a função [switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) para configurar a lógica de negócios para a atribuição de UO e mapeie-a para o atributo Active Directory **parentDistinguishedName**.

Por exemplo, se você quiser criar usuários no UO com base no atributo de RH **Municipal**, poderá usar a seguinte expressão:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com essa expressão, se o valor de Municipal for Dallas, Austin, Seattle ou Londres, a conta de usuário será criada na UO correspondente. Se não houver correspondência, a conta será criada na UO padrão.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planejar a entrega de senha de novas contas de usuário

Ao iniciar o processo dos ASSOCIADORES, você precisa definir e entregar uma senha temporária de novas contas de usuário. Com a nuvem de RH para o provisionamento de usuários do Azure AD, você pode distribuir o recurso de [redefinição de senha de autoatendimento](../authentication/tutorial-enable-sspr.md) (SSPR) do Azure ad para o usuário no dia um.

O SSPR é um meio simples para os administradores de ti permitirem que os usuários redefinam suas senhas ou desbloqueiem suas contas. Você pode provisionar o atributo de **número móvel** do aplicativo de RH na nuvem para Active Directory e sincronizá-lo com o Azure AD. Depois que o atributo de **número móvel** estiver no Azure AD, você poderá habilitar o SSPR para a conta do usuário. Em seguida, no primeiro dia, o novo usuário pode usar o número de celular registrado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Planejar o ciclo inicial

Quando o serviço de provisionamento do Azure AD é executado pela primeira vez, ele executa um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle) em relação ao aplicativo de RH na nuvem para criar um instantâneo de todos os objetos de usuário no aplicativo de RH na nuvem. O tempo necessário para os ciclos iniciais é diretamente dependente de quantos usuários estão presentes no sistema de origem. O ciclo inicial de alguns locatários do aplicativo de RH na nuvem com mais de 100.000 usuários pode levar muito tempo.

**Para locatários de aplicativo de RH de nuvem grande (>usuários 30.000),** execute o ciclo inicial em estágios progressivos. Inicie as atualizações incrementais somente depois de validar que os atributos corretos estão definidos em Active Directory para diferentes cenários de provisionamento de usuário. Siga a ordem aqui.

1. Execute o ciclo inicial somente para um conjunto limitado de usuários definindo o [filtro de escopo](#plan-scoping-filters-and-attribute-mapping).
2. Verifique Active Directory provisionamento de conta e os valores de atributo definidos para os usuários selecionados para a primeira execução. Se o resultado atender às suas expectativas, expanda o filtro de escopo para incluir progressivamente mais usuários e verificar os resultados da segunda execução.

Depois que você estiver satisfeito com os resultados do ciclo inicial dos usuários de teste, inicie as [atualizações incrementais](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Teste e segurança de planos

Em cada estágio de sua implantação do piloto inicial por meio da habilitação do provisionamento de usuário, verifique se você está testando se os resultados estão conforme o esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o aplicativo de RH na nuvem para o provisionamento de usuário do Azure AD, execute os casos de teste para verificar se essa solução atende aos requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|O novo funcionário é contratado no aplicativo de RH na nuvem.| -A conta de usuário é provisionada no Active Directory.</br>-O usuário pode fazer logon em aplicativos de Active Directory domínio e executar as ações desejadas.</br>-Se Azure AD Connect sincronização estiver configurada, a conta de usuário também será criada no Azure AD.
|O usuário é encerrado no aplicativo de RH na nuvem.|-A conta de usuário está desabilitada no Active Directory.</br>-O usuário não pode fazer logon em nenhum aplicativo corporativo protegido pelo Active Directory.
|A organização de supervisão do usuário é atualizada no aplicativo de RH de nuvem.|Com base no mapeamento de atributo, a conta de usuário passa de uma UO para outra em Active Directory.|
|O HR atualiza o gerente do usuário no aplicativo de RH na nuvem.|O campo gerente no Active Directory é atualizado para refletir o nome do novo gerente.|
|O HR recontrata um funcionário em uma nova função.|O comportamento depende de como o aplicativo de RH de nuvem está configurado para gerar IDs de funcionário:</br>-Se a ID antiga do funcionário for recontratada, o conector habilitará a conta de Active Directory existente para o usuário.</br>-Se o novo contratação receber uma nova ID de funcionário, o conector criará uma nova conta de Active Directory para o usuário.|
|HR converte o funcionário em um funcionário de contrato ou vice-versa.|Uma nova conta de Active Directory é criada para a nova pessoa e a conta antiga é desabilitada na data de efetivação da conversão.|

Use os resultados anteriores para determinar como fazer a transição de sua implementação de provisionamento automático de usuário para produção com base em suas linhas do tempo estabelecidas.

> [!TIP]
> Use técnicas como a redução de dados e a depuração de dados ao atualizar o ambiente de teste com dados de produção para remover ou mascarar dados pessoais confidenciais para atender aos padrões de privacidade e segurança. 

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja exigida como parte da implantação de um novo serviço. Se uma análise de segurança for necessária ou não tiver sido conduzida, confira muitos [White papers](https://www.microsoft.com/download/details.aspx?id=36391) do Azure AD que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Reversão do plano

A implementação de provisionamento de usuário de RH na nuvem pode falhar ao funcionar conforme desejado no ambiente de produção. Nesse caso, as etapas de reversão a seguir podem ajudá-lo a reverter para um estado bom conhecido anterior.

1. Examine o [relatório de Resumo de provisionamento](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) e [Provisione os logs](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar quais operações incorretas foram executadas nos usuários ou grupos afetados. Para obter mais informações sobre o relatório de Resumo de provisionamento e os logs, consulte [gerenciar provisionamento de usuário do aplicativo de RH de nuvem](#manage-your-configuration).
2. O último estado bom conhecido dos usuários ou grupos afetados pode ser determinado por meio dos logs de auditoria de provisionamento ou revisando os sistemas de destino (Azure AD ou Active Directory).
3. Trabalhe com o proprietário do aplicativo para atualizar os usuários ou grupos afetados diretamente no aplicativo usando os últimos valores de estado válidos conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implantar o aplicativo de RH na nuvem

Escolha o aplicativo de RH de nuvem que se alinha aos seus requisitos de solução.

**Workday**: para importar perfis de trabalho do WORKDAY para o Active Directory e o Azure AD, consulte [tutorial: configurar o WORKDAY para provisionamento automático de usuário](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcionalmente, você pode fazer write-back do endereço de email, nome de usuário e número de telefone para workday.

**SAP SuccessFactors**: para importar perfis de trabalho do SuccessFactors para o Active Directory e o Azure AD, consulte [tutorial: configurar o SAP SuccessFactors para provisionamento automático de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Opcionalmente, você pode gravar novamente o endereço de email e o nome de usuário em SuccessFactors.

## <a name="manage-your-configuration"></a>Gerenciar sua configuração

O Azure AD pode fornecer informações adicionais sobre o uso de provisionamento de usuário da sua organização e a integridade operacional por meio de relatórios e logs de auditoria.

### <a name="gain-insights-from-reports-and-logs"></a>Obter informações de relatórios e logs

Após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle)bem-sucedido, o serviço de provisionamento do Azure ad continua a executar atualizações incrementais de back-to-back indefinidamente, em intervalos definidos nos tutoriais específicos de cada aplicativo, até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente. Um novo ciclo inicial é disparado usando o [portal do Azure](https://portal.azure.com/) ou o comando de [API Microsoft Graph](/graph/api/resources/synchronization-overview) apropriado.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou em filtros de escopo.
- O processo de provisionamento entra em quarentena devido a uma alta taxa de erros. Ele permanece em quarentena por mais de quatro semanas, quando é automaticamente desabilitado.

Para examinar esses eventos e todas as outras atividades executadas pelo serviço de provisionamento, [saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Todas as atividades executadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD. Você pode rotear os logs de auditoria do Azure AD para Azure Monitor logs para análise posterior. Com os logs de Azure Monitor (também conhecidos como Log Analytics espaço de trabalho), você pode consultar dados para localizar eventos, analisar tendências e executar a correlação entre várias fontes de dados. Assista a este [vídeo](https://youtu.be/MP5IaCTwkQg) para aprender os benefícios de usar os logs de Azure monitor para logs do Azure AD em cenários de usuário práticos.

Instale as [exibições do log Analytics para logs de atividades do Azure ad](../reports-monitoring/howto-install-use-log-analytics-views.md) para obter acesso a [relatórios predefinidos](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) sobre o provisionamento de eventos em seu ambiente.

Para obter mais informações, consulte como [analisar os logs de atividade do Azure AD com seus logs de Azure monitor](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gerenciar dados pessoais

O agente de provisionamento do Azure AD Connect instalado no Windows Server cria logs no log de eventos do Windows que podem conter dados pessoais, dependendo do seu aplicativo de RH na nuvem para Active Directory mapeamentos de atributo. Para estar em conformidade com as obrigações de privacidade do usuário, configure uma tarefa agendada do Windows para limpar o log de eventos e garantir que nenhum dado seja mantido além de 48 horas.

O serviço de provisionamento do Azure AD não gera relatórios, executa análises ou fornece informações além de 30 dias, pois o serviço não armazena, processa nem mantém dados além de 30 dias.

### <a name="troubleshoot"></a>Solucionar problemas

Para solucionar quaisquer problemas que possam ser reativados durante o provisionamento, consulte os seguintes artigos:

- [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](application-provisioning-config-problem.md)
- [Sincronizar um atributo do seu Active Directory local com o Azure AD para provisionamento para um aplicativo](user-provisioning-sync-attributes-for-mapping.md)
- [Problema para salvar as credenciais de administrador ao configurar o provisionamento do usuário para um aplicativo de galeria do Azure Active Directory](./user-provisioning.md)
- [Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD](application-provisioning-config-problem-no-users-provisioned.md)
- [O conjunto errado de usuários está sendo provisionado para um aplicativo da Galeria do Azure AD](../manage-apps/add-application-portal-assign-users.md)
- [Configurar o Visualizador de Eventos do Windows para solucionar problemas do agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurar os logs de auditoria do portal do Azure para solucionar problemas de serviço](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Entender logs para operações de criação da conta de usuário do AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Entender logs para operações de atualização do gerente](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Resolver erros comumente encontrados](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Próximas etapas

- [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
- [Visão geral da API de sincronização do Azure AD](/graph/api/resources/synchronization-overview)
- [Ignorar a exclusão de contas de usuário que saem do escopo](skip-out-of-scope-deletions.md)
- [Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão](provisioning-agent-release-version-history.md)