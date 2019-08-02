---
title: Detecção de ameaças para VMs & servidores na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os alertas de VM e servidor disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295784"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Detecção de ameaças para VMs & servidores na central de segurança do Azure

Este tópico apresenta os diferentes tipos de métodos de detecção e alertas disponíveis para VMs e servidores com os sistemas operacionais a seguir. Para obter uma lista de versões com suporte, consulte [plataformas e recursos com suporte na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

A central de segurança se integra aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows.  A central de segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

### Microsoft Server defender ATP<a nanme="windows-atp"></a>

A central de segurança do Azure estende suas plataformas de proteção de carga de trabalho de nuvem integrando com a ATP (proteção avançada contra ameaças) do Windows Defender. Isso fornece recursos de EDR (detecção e resposta de ponto de extremidade) abrangentes.

> [!NOTE]
> O sensor do Windows Server defender ATP é habilitado automaticamente em servidores Windows que são integrados à central de segurança do Azure.

Quando o Windows Server defender ATP detecta uma ameaça, ele dispara um alerta. O alerta é mostrado no painel da central de segurança. No painel, você pode dinamizar para o console do Windows Defender ATP para executar uma investigação detalhada para descobrir o escopo do ataque. Para obter mais informações sobre o Windows Server defender ATP, consulte [servidores integrados ao serviço do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Análise de despejo de memória<a nanme="windows-dump"></a>

Quando o software falha, um despejo de memória captura uma parte da memória no momento da falha.

Uma falha pode ter sido causada por malware ou conter malware. Para evitar ser detectado por produtos de segurança, várias formas de malware usam um ataque de arquivo, o que evita a gravação em disco ou a criptografia de componentes de software gravados no disco. Esse tipo de ataque é difícil de detectar usando abordagens tradicionais baseadas em disco.

No entanto, esse tipo de ataque pode ser detectado usando a análise de memória. Analisando a memória no despejo de falha, a central de segurança pode detectar as técnicas que o ataque está usando para explorar vulnerabilidades no software, acessar dados confidenciais e maneira furtiva manter em um computador comprometido. Isso é feito pelo back-end da central de segurança com impacto mínimo sobre o desempenho dos hosts.

> [!div class="mx-tableFixed"]

|Alerta|DESCRIÇÃO|
|---|---|
|**Injeção de código descoberta**|A injeção de código é a inserção de módulos executáveis em processos ou threads em execução. Essa técnica é usada por malware para acessar dados, enquanto se oculta com êxito para evitar que sejam encontradas e removidas. <br/>Esse alerta indica que um módulo injetado está presente no despejo de memória. Para diferenciar entre módulos injetados mal-intencionados e não mal-intencionados, a central de segurança verifica se o módulo injetado está em conformidade com um perfil de comportamento suspeito.|
|**Segmento de código suspeito descoberto**|Indica que um segmento de código foi alocado usando métodos não padrão, como injeção reflexiva e cancelamento de processo. O alerta fornece características adicionais do segmento de código que foram processados para fornecer contexto para os recursos e comportamentos do segmento de código relatado.|
|**Shellcode descoberto**|Shellcode é a carga executada depois que o malware explorou uma vulnerabilidade do software.<br/>Esse alerta indica que a análise de despejo de memória detectou código executável que exibe o comportamento normalmente executado por cargas mal-intencionadas. Embora um software não mal-intencionado também possa executar esse comportamento, não é típico das práticas normais de desenvolvimento de software.|

### Detecção de ataque de não-arquivo<a nanme="windows-fileless"></a>

No Azure, vemos regularmente ataques de não-arquivo direcionados aos pontos de extremidade de nossos clientes.

Para evitar a detecção, os ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória. As cargas de invasor persistem na memória de processos comprometidos e realizam uma ampla gama de atividades mal-intencionadas.

Com a detecção de ataque sem arquivo, as técnicas forenses de memória automatizada identificam os kits de informática, técnicas e comportamentos de ataque sem arquivo. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

Ele encontra evidências de exploração, injeção de código e execução de cargas mal-intencionadas. A detecção de ataques de não-arquivo gera alertas de segurança detalhados para acelerar a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos, fornecendo maior cobertura de detecção.

> [!NOTE]
> Você pode simular alertas do Windows baixando o [guia estratégico da central de segurança do Azure](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Alertas de segurança e siga as diretrizes fornecidas

> [!div class="mx-tableFixed"]

|Alerta|DESCRIÇÃO|
|---|---|
|**Técnica de ataque de não-arquivo detectada**|A memória do processo especificado abaixo contém um kit de ferramentas de ataque de não-arquivo: Meterpreter. Os kits de tarefas de ataque sem arquivo normalmente não têm uma presença no sistema de arquivos, dificultando a detecção por antivírus tradicional.|

### <a name="further-reading"></a>Leitura adicional

Para obter exemplos e mais informações sobre a detecção da central de segurança:

* [Como a central de segurança do Azure automatiza a detecção de ataques cibernéticos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Como a central de segurança do Azure detecta vulnerabilidades usando ferramentas administrativas](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

A central de segurança coleta registros de auditoria de computadores Linux usando **auditoria**, uma das estruturas de auditoria do Linux mais comuns. auditado tem a vantagem de ter sido por muito tempo e viver no kernel principal. 

### MMA (alertas de auditoria do Linux e integração de Microsoft Monitoring Agent)<a name="linux-auditd"></a>

O sistema auditado consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema, filtrá-las por um determinado conjunto de regras e gravar mensagens para um soquete. A central de segurança integra as funcionalidades do pacote auditado dentro do Microsoft Monitoring Agent (MMA). Essa integração habilita a coleta de eventos auditados em todas as distribuições Linux com suporte sem nenhum pré-requisito.  

os registros auditados são coletados, aprimorados e agregados em eventos usando o agente MMA do Linux. A central de segurança está trabalhando constantemente para adicionar uma nova análise, que aproveita os sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, essas análises se estendem por processos suspeitos, tentativas de logon duvidosa, carregamento de módulo kernel e outras atividades, que indicam que um computador está sob ataque ou foi violado.  

Abaixo estão vários exemplos de análise, que demonstram como abrangemos diferentes estágios do ciclo de vida de ataque.

> [!div class="mx-tableFixed"]

|Alerta|DESCRIÇÃO|
|---|---|
|**Processo visto acessando o arquivo de chaves autorizadas SSH de maneira incomum**|Um arquivo de chaves autorizadas SSH foi acessado em um método semelhante a campanhas de malware conhecidas. Esse acesso pode indicar que um invasor está tentando obter acesso persistente a um computador|
|**Tentativa de persistência detectada**|A análise de dados do host detectou que um script de inicialização para o modo de usuário único foi instalado. <br/>Como é raro que qualquer processo legítimo seja necessário para ser executado nesse modo, isso pode indicar que um invasor adicionou um processo mal-intencionado a cada nível de execução para garantir a persistência.|
|**Manipulação de tarefas agendadas detectadas**|A análise de dados do host detectou uma possível manipulação de tarefas agendadas. Os invasores geralmente adicionam tarefas agendadas a computadores que estão comprometidos a obter persistência.|
|**Modificação de carimbo de hora de arquivo suspeito**|A análise de dados do host detectou uma modificação suspeita de carimbo de hora Os invasores geralmente copiam carimbos de data/hora de arquivos legítimos existentes para novas ferramentas para evitar a detecção desses arquivos recentemente descartados|
|**Um novo usuário foi adicionado ao grupo sudoers**|A análise de dados do host detectou que um usuário foi adicionado ao grupo sudoers, que permite que seus membros executem comandos com altos privilégios.|
|**Provável exploração da vulnerabilidade do DynoRoot no cliente DHCP**|A análise de dados do host detectou a execução de um comando incomum com o processo pai do script dhclient.|
|**Módulo de kernel suspeito detectado**|A análise de dados do host detectou um arquivo de objeto compartilhado sendo carregado como um módulo de kernel. Isso pode ser uma atividade legítima ou uma indicação de que uma de suas máquinas foi comprometida.|
|**Processo associado à mineração de moeda digital detectado**|A análise de dados do host detectou a execução de um processo normalmente associado à mineração de moedas digitais|
|**Potencial encaminhamento de porta para endereço IP externo**|A análise de dados do host detectou o início do encaminhamento de porta para um endereço IP externo.|

> [!NOTE]
> Você pode simular alertas do Windows [baixando o guia estratégico da central de segurança do Azure: Alertas](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) de segurança e siga as diretrizes fornecidas.


Para obter mais informações, consulte estes artigos:  

* [Aproveite a central de segurança do Azure para detectar quando os ataques de computadores Linux comprometidos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [A central de segurança do Azure pode detectar vulnerabilidades emergentes no Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 