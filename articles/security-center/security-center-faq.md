---
title: "Perguntas frequentes sobre a Central de Segurança do Azure | Microsoft Docs"
description: "Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 35aa45ce09b756dd7413a1df3d3c7b0c428b7a97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a Central de Segurança do Azure
Estas perguntas frequentes estão relacionadas à Central de Segurança do Azure, um serviço que ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Microsoft Azure.

> [!NOTE]
> A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md). As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como posso obter a Central de Segurança do Azure?
A Central de Segurança do Azure é habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **Procurar** e role até a **Central de Segurança**).  

## <a name="billing"></a>Cobrança
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona o faturamento para a Central de Segurança do Azure?
A Central de Segurança é oferecida em duas camadas:

A **Camada gratuita** fornece visibilidade do estado de segurança de seus recursos do Azure, a política de segurança básica, recomendações de segurança e integração a produtos de segurança e serviços de parceiros.

A **camada Standard** adiciona recursos de detecção avançada de ameaças, inclusive inteligência de ameaças, análise comportamental, detecção de anomalias, incidentes de segurança e relatórios de atribuição de ameaças. A camada Standard é gratuita pelos 60 primeiros dias. Se você escolher continuar a usar o serviço por mais 60 dias, nós começaremos automaticamente a cobrar pelo serviço.  Para atualizar, selecione [Tipo de Preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) na política de segurança.

## <a name="permissions"></a>Permissões
A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md), que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

## <a name="data-collection"></a>Coleta de dados
A Central de Segurança coleta dados das máquinas virtuais para avaliar seu estado de segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. Você também pode habilitar a coleta de dados na política da Central de Segurança.

### <a name="how-do-i-disable-data-collection"></a>Como desabilitar a coleta de dados?
Se você estiver usando a camada gratuita da Central de Segurança do Azure, você poderá desabilitar a coleta de dados de máquinas virtuais a qualquer momento. A coleta de dados é necessária para as assinaturas na camada Standard. Você pode desabilitar a coleta de dados para uma assinatura na política de Segurança. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**.)  Ao selecionar uma assinatura, uma nova folha será aberta e fornecerá a opção para desligar a **Coleta de dados**.

### <a name="how-do-i-enable-data-collection"></a>Como habilitar a coleta de dados?
É possível habilitar a coleta de dados para suas assinaturas do Azure na Política de segurança. Para habilitar a coleta de dados. [Entre no Portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**. Configurar a **Coleta de dados** como **Ligada**.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados é habilitada?
Quando a coleta de dados é habilitada, o Microsoft Monitoring Agent é provisionado automaticamente em todas as máquinas virtuais existentes e quaisquer máquinas virtuais novas com suporte que sejam implantada na assinatura.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Monitoring Agent afeta o desempenho dos meus servidores?
O agente consome uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho, bem como o agente e a extensão, consulte o [guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde meus dados são armazenados?
Os dados coletados desse agente são armazenados no análise de Log espaço de trabalho do Log Analytics existente associado à sua assinatura do Azure ou a novos espaços de trabalho. Para obter mais informações, consulte [Segurança de Dados](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Como usar a Central de Segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança do Azure, você pode definir as políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, você deve ser um Administrador de Segurança ou o Proprietário ou Colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>O que é são recomendações de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

* Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
* Como configurar [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e as regras para controlar o tráfego para máquinas virtuais
* Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
* Como implantar atualizações de sistema ausentes
* Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como é possível ver o estado atual da segurança dos meus recursos do Azure?
A folha **Visão Geral da Central de Segurança** mostra a postura de segurança geral do ambiente dividida em Computação, Rede, Armazenamento e Dados e, por fim, Aplicativos. Cada tipo de recurso tem um indicador mostrando se possíveis vulnerabilidades de segurança foram identificadas. Clicar em cada bloco exibe uma lista dos problemas de segurança identificados pela Central de Segurança, junto com um inventário dos recursos em sua assinatura.

### <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
A Central de Segurança do Azure automaticamente coleta, analisa e funde os dados de log de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

* As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
* Malware avançado detectado com o relatório de erros do Windows
* Ataques por força bruta contra máquinas virtuais
* Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center versus pela Central de Segurança do Azure?
O MSRC (Microsoft Security Response Center) executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal ou que o uso do cliente do Azure não está de acordo com os termos de Uso Aceitável, um gerente de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre com o envio de um email para os contatos de segurança especificados na Central de Segurança do Azure ou para o proprietário da assinatura do Azure, caso um contato de segurança não seja especificado.

A Central de Segurança é um serviço do Azure que monitora o ambiente do cliente do Azure continuamente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da Central de Segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorados pela Central de Segurança do Azure?
A Central de Segurança do Azure monitora os seguintes recursos do Azure:

* VMs (máquinas virtuais) (incluindo os [Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md))
* Redes Virtuais do Azure
* Serviço do SQL Azure
* Conta de Armazenamento do Azure
* Aplicativos Web do Azure (em um [Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md))
* Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web em VMs e no Ambiente do Serviço de Aplicativo

## <a name="virtual-machines"></a>Máquinas Virtuais
### <a name="what-types-of-virtual-machines-are-supported"></a>Quais tipos de máquinas virtuais têm suporte?
O monitoramento e as recomendações estão disponíveis para VMs (máquinas virtuais) criadas usando os [modelos de implantação clássico e do Resource Manager](../azure-classic-rm.md).

Consulte [Plataformas com suporte na Central de Segurança do Azure](security-center-os-coverage.md) para obter uma lista de plataformas com suporte.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que a Central de Segurança do Azure não reconhece a solução antimalware em execução em minha VM do Azure?
A Central de Segurança do Azure só tem visibilidade de antimalware instalado por meio de extensões do Azure. Por exemplo, a Central de Segurança não será capaz de detectar antimalware que foi pré-instalado em uma imagem fornecida por você ou se você tiver instalado antimalware em suas máquinas virtuais usando seus próprios processos (como sistemas de gerenciamento de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "Dados de verificação ausentes" para minha VM?
Esta mensagem aparece quando não há nenhum dado de verificação para uma VM. Pode levar algum tempo (menos de uma hora) para que os dados de exame sejam populados depois que a Coleta de Dados é habilitada na Central de Segurança do Azure. Após a população inicial de dados de verificação, você receberá esta mensagem porque não há nenhum dado de verificação ou não há dados de verificação recentes. Os exames não são populados para uma VM em um estado parado. Essa mensagem também pode aparecer se dados de verificação não foram populados recentemente (de acordo com a política de retenção para o agente do Windows, que tem um valor padrão de 30 dias).

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "O Agente de VM está ausente?"
O Agente de VM deve ser instalado nas VMs para habilitar a Coleta de Dados. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. Para obter informações sobre como instalar o Agente da VM em outras VMs, consulte a postagem de blog sobre o [Agente de VM e Extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
