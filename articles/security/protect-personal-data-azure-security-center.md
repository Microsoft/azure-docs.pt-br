---
title: "Proteger dados pessoais com a Central de Segurança do Azure | Microsoft Docs"
description: "proteger dados pessoais usando a central de segurança do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Proteger dados pessoais contra violações e ataques: Central de Segurança do Azure

Este artigo ajudará você a entender como usar a Central de Segurança do Azure para proteger dados pessoais contra violações e ataques.

## <a name="scenario"></a>Cenário 

Uma empresa de cruzeiro de grande porte, com sede nos Estados Unidos, está expandindo suas operações para oferecer roteiros nos mares Mediterrâneo e Báltico, bem como nas Ilhas Britânicas. Para ajudar nesses esforços, ela adquiriu várias linhas de cruzeiro menores localizadas na Itália, na Alemanha, na Dinamarca e no Reino Unido.

A empresa usa o Microsoft Azure para armazenar dados corporativos na nuvem. Eles incluem informações de identificação pessoal, como nomes, endereços, números de telefone e informações de cartão de crédito. Também incluem informações de Recursos Humanos, como:

- Endereços
- Números de telefone
- Números de identificação fiscal
- Informações de saúde

A linha de cruzeiro também mantém um banco de dados grande de membros do programa de recompensa e fidelidade. Os funcionários corporativos acessam a rede de escritórios remotos da empresa e os agentes de viagem localizados no mundo todo têm acesso a alguns recursos da empresa.
Os dados pessoais viajam pela rede entre essas localizações e o data center da Microsoft.

## <a name="problem-statement"></a>Problema declarado

A empresa se preocupa com a ameaça de ataques a seus recursos do Azure. Ela deseja prevenir a exposição dos dados pessoais de clientes e funcionários a pessoas não autorizadas. Também deseja obter diretrizes de prevenção e resposta/correção, bem como uma maneira eficaz de monitorar a segurança contínua de seus recursos de nuvem.
Ela precisa de uma linha forte de proteção contra os invasores sofisticados e organizados de hoje.

## <a name="company-goal"></a>Meta da empresa

Uma das metas da empresa é garantir a privacidade dos dados pessoais de clientes e funcionários protegendo-os contra ameaças. Uma de suas metas é responder imediatamente aos sinais de violação para atenuar o impacto. Ela precisa de uma maneira para avaliar o estado atual da segurança, identificar configurações vulneráveis e corrigi-las.

## <a name="solutions"></a>Soluções

O ASC (Central de Segurança do Microsoft Azure) fornece uma solução integrada de gerenciamento de política e monitoramento da segurança. Ele oferece funcionalidades eficazes e fáceis de usar de prevenção, detecção e resposta a ameaças.

### <a name="prevention"></a>Prevenção

O ASC ajuda a prevenir violações, permitindo definir políticas de segurança, fornecer acesso Just-In-Time e implementar recomendações de segurança.

Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura especificada. O acesso Just-In-Time pode ser usado para bloquear o tráfego de entrada nas VMs do Azure, reduzindo a exposição a ataques. Recomendações de segurança são criadas pelo ASC depois de analisar o estado de segurança dos recursos do Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Como fazer para definir políticas de segurança no ASC?

É possível configurar políticas de segurança para cada assinatura. Para modificar uma política de segurança, você deve ser um proprietário ou colaborador dessa assinatura. No portal do Azure, faça o seguinte:

1. Selecione **Política** no painel do ASC.

2. Selecione a assinatura na qual você deseja habilitar a política.

3. Escolha **Política prevenção** para configurar as políticas por assinatura. A opção **Coletar dados de máquinas virtuais** deve ser definida como **Ativado.**

4. Nas opções de **Política de prevenção**, selecione **Ativado** para habilitar as recomendações de segurança que são relevantes para a assinatura.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Para obter instruções mais detalhadas e uma explicação de cada uma das recomendações de política que podem ser habilitadas, consulte [Definir políticas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Como fazer para configurar o acesso JIT (Just-In-Time)?

Quando o JIT está habilitado, a Central de Segurança bloqueia o tráfego de entrada às VMs do Azure, criando uma regra do NSG. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. Para usar o acesso JIT, faça o seguinte:

1. Selecione o **bloco de acesso de VM Just-In-Time** na folha do ASC.

2. Selecione a guia **Recomendado**.

3. Em **VMs**, selecione as VMs que você deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM. 
4. Selecione **Habilitar JIT** nas VMs.
5. Selecione **Salvar**.

Em seguida, você pode ver as portas padrão que o ASC recomenda que sejam habilitadas para o JIT. Também adicione e configure uma nova porta na qual você deseja habilitar a solução Just-In-Time. O bloco **Acesso de VM Just-In-Time** na Central de Segurança mostra o número de VMs configuradas para o acesso JIT. Ele também mostra o número de solicitações de acesso aprovadas feitas na última semana.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Para obter instruções sobre como fazer isso, além de mais informações sobre o acesso Just-In-Time, consulte [Gerenciar o acesso de máquina virtual usando o Just-In-Time.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Como fazer para implementar as recomendações de segurança do ASC?

Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários. 
1. Selecione o bloco **Recomendações** no painel do ASC.

2. Exiba as recomendações, que são mostradas em um formato de tabela, em que cada linha representa uma recomendação.

3. Para filtrar recomendações, selecione **Filtrar** e selecione os valores de gravidade e estado que você deseja ver.

4. Para ignorar uma recomendação que não é aplicável, clique com o botão direito do mouse e selecione **Ignorar.**

5. Avalie qual recomendação deve ser aplicada primeiro.

6. Aplique as recomendações em ordem de prioridade.

Para obter uma lista de possíveis recomendações e passos a passos sobre como aplicar cada uma, consulte [Gerenciando recomendações de segurança na Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Detecção e resposta

A detecção e resposta funcionam juntas, quando você deseja responder o mais rápido possível após a detecção de uma ameaça.
A detecção de ameaças do ASC funciona com a coleta automática das informações de segurança dos recursos do Azure, da rede e das soluções de parceiros conectados. O ASC pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Para obter informações mais detalhadas sobre como funciona a detecção de ameaças do ASC, consulte [Funcionalidades de detecção da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Como fazer para gerenciar e responder a alertas de segurança?

Uma lista de alertas de segurança priorizados é mostrada na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema. A Central de Segurança também inclui recomendações sobre como corrigir um ataque. Para gerenciar os alertas de segurança, faça o seguinte:

1. Selecione o bloco **Alertas de segurança** no painel do ASC. Isso mostra detalhes de cada alerta.

2. Para filtrar os alertas com base na data, no estado e na gravidade, selecione **Filtrar** e, em seguida, os valores que você deseja ver.

3. Para responder a um alerta, selecione-o, examine as informações e, em seguida, selecione o recurso que foi atacado.

4. No campo **Descrição**, você verá os detalhes, incluindo a correção recomendada.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Para obter instruções mais detalhadas sobre como responder a alertas de segurança, consulte [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

Para obter ajuda na investigação de alertas de segurança, a empresa pode integrar os alertas do ASC à sua própria solução SIEM, usando a [Integração de Logs do Azure](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Como fazer para gerenciar incidentes de segurança?

No ASC, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da cadeia de encerramento. Um Incidente revelará a lista de alertas relacionados, o que permite a obtenção de mais informações sobre cada ocorrência. Os incidentes são exibidos no bloco e na folha Alertas de Segurança.

Para examinar e gerenciar incidentes de segurança, faça o seguinte:

1. Selecione o bloco **Alertas de segurança**. Se um incidente de segurança for detectado, ele será exibido no gráfico de alertas de segurança. Ele terá um ícone diferente dos outros alertas.

2. Selecione o incidente para ver mais detalhes sobre esse incidente de segurança. Detalhes adicionais incluem sua descrição completa, sua gravidade, seu estado atual, o recurso atacado, as etapas de correção do incidente e os alertas que foram incluídos nesse incidente.

Filtre para ver **somente incidentes**, **somente alertas** ou **ambos**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Como fazer para acessar o Relatório de Inteligência contra Ameaças?

O ASC analisa informações de várias fontes para identificar ameaças. Para ajudar as equipes de resposta a incidentes a investigar e a corrigir ameaças, a Central de Segurança inclui um relatório de inteligência contra ameaças que contém informações sobre a ameaça detectada.

A Central de Segurança tem três tipos de relatórios de ameaça, que podem variar de acordo com o ataque.
Os relatórios disponíveis são:

- Relatório de Grupo de Atividade: fornece análises avançadas sobre os invasores, seus objetivos e táticas.

- Relatório de Campanha: concentra-se nos detalhes de campanhas de ataque específicas.

- Relatório de Resumo de Ameaças: abrange todos os itens dos dois relatórios anteriores.

Esse tipo de informação é muito útil durante o processo de resposta a incidentes, em que há uma investigação em andamento para compreender a origem do ataque, as motivações do invasor e o que fazer para atenuar esse problema no futuro.

1. Para acessar o relatório de inteligência contra ameaças, faça o seguinte:

2. Selecione o bloco **Alertas de segurança** no painel do ASC.

3. Selecione o alerta de segurança do qual você deseja obter mais informações.

4. No campo **Relatórios**, clique no link para o relatório de inteligência contra ameaças.

5. Isso abrirá o arquivo PDF, que pode ser baixado.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Para obter mais informações sobre o relatório de inteligência contra ameaças do ASC, consulte [Relatório de Inteligência contra Ameaças da Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Avaliação

Para ajudar no teste, na avaliação e na avaliação de sua postura de segurança, o ASC fornece uma avaliação de vulnerabilidade integrada com agentes de nuvem Qualys, como parte de seu componente de recomendações de máquina virtual.

O agente Qualys relata os dados de vulnerabilidade para a plataforma de gerenciamento do Qualys, que, por sua vez, envia os dados de monitoramento de integridade e vulnerabilidade novamente para o ASC. A recomendação para adicionar uma solução de avaliação de vulnerabilidade é exibida na folha **Recomendações** do painel do ASC.

Após a instalação da solução de avaliação de vulnerabilidade na VM de destino, a Central de Segurança examinará a VM para detectar e identificar vulnerabilidades de aplicativos e do sistema. Os problemas detectados são exibidos na opção **Recomendações de Máquinas Virtuais**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Como fazer para implementar uma solução de avaliação de vulnerabilidade? 

Se uma Máquina Virtual não tem uma solução de avaliação de vulnerabilidade integrada já implantada, a Central de Segurança recomenda que ela seja instalada.

1. No painel do ASC, na folha **Recomendações**, selecione **Adicionar uma solução de avaliação de vulnerabilidade.**

2. Selecione as VMs em que você deseja instalar a solução de avaliação de vulnerabilidade.

3. Clique em **Instalar em [número de] VMs.**

4. Selecione uma solução de parceiro no Azure Marketplace ou, em **Usar solução existente**, selecione **Qualys.**

5. Ative ou desative as configurações de atualização automática na folha **Soluções de Parceiros**.

Para obter mais instruções sobre como implementar uma solução de avaliação de vulnerabilidade, consulte [Avaliação de vulnerabilidade na Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Próximas etapas

- [Guia de início rápido da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Introdução à Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Integrando alertas da Central de Segurança do Azure com a integração de logs do Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Melhorar a Central de Segurança do Azure com a avaliação de vulnerabilidade integrada](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
