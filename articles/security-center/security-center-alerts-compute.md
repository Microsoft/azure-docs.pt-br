---
title: Detecção de ameaças para computação nativa de nuvem na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os alertas de computação nativos de nuvem disponíveis na central de segurança do Azure. na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295836"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Detecção de ameaças para computação nativa de nuvem na central de segurança do Azure

Como um provedor de nuvem, a central de segurança aproveita a visibilidade exclusiva que ele tem para analisar logs internos e identificar a metodologia de ataque em vários destinos. Este tópico apresenta os alertas disponíveis para os seguintes serviços do Azure:

* [Serviço de Aplicativo do Azure](#app-services)
* [Contêineres](#azure-containers) 

## Serviço de Azure App<a name="app-services"></a>

A central de segurança aproveita a escala da nuvem para identificar os ataques que visam os aplicativos de clientes em execução em Azure App serviço. Com aplicativos Web sendo praticamente em qualquer rede moderna, os invasores investigam a localização e a exploração de vulnerabilidades. Antes de ser roteado para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways onde são inspecionados e registrados. Esses dados são usados para identificar explorações, invasores e para aprender novos padrões que serão usados posteriormente.

Aproveitando a visibilidade que o Azure tem como um provedor de nuvem, a central de segurança analisa os logs internos do serviço de aplicativo para identificar a metodologia de ataque em vários destinos. Por exemplo, varredura generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e exibe padrões de rastreamento para pontos de extremidade semelhantes em vários hosts, pesquisando uma página ou um plug-in vulnerável. Isso pode ser detectado usando a nuvem, mas não pode ser identificado no ponto de vista de um único host.

A central de segurança também tem acesso às áreas restritas e VMs subjacentes. Junto com a análise forense de memória, a infra-estrutura pode contar a história, desde um novo ataque que está circulando de forma a comprometimento nas máquinas dos clientes. Portanto, a central de segurança pode detectar ataques contra aplicativos Web longos depois de serem explorados.

> [!div class="mx-tableFixed"]

|Alerta|DESCRIÇÃO|
|---|---|
|**Invocação suspeita de tema do WordPress detectada**|O log de atividades do serviço Azure App indica uma possível atividade de injeção de código no recurso do serviço de aplicativo.<br/> Essa atividade suspeita se assemelha à atividade que manipula um tema do WordPress para dar suporte à execução do código no lado do servidor, seguida de uma solicitação da Web direta para invocar o arquivo de tema manipulado. Esse tipo de atividade foi visto no passado como parte de uma campanha de ataque no WordPress.|
|**Conexão com a página da Web do endereço IP anômala detectado**|O log de atividades do serviço Azure App indica uma conexão com uma página da Web confidencial de um endereço de origem que nunca se conectou a ela antes. Isso pode indicar que alguém está tentando um ataque de força bruta em suas páginas de administração de aplicativo Web. Também pode ser o resultado de um novo endereço IP que está sendo usado por um usuário legítimo.|
|**Um IP que se conectou à interface de FTP do serviço de Azure App foi encontrado em inteligência contra ameaças**|A análise de logs de FTP do serviço Azure App detectou uma conexão de um endereço de origem que foi encontrado no feed de inteligência contra ameaças. Durante essa conexão, um usuário acessou as páginas listadas abaixo.|
|**Impressão digital da Web detectada**|O log de atividades do serviço Azure App indica uma possível atividade de impressão digital na Web em seu recurso do serviço de aplicativo. <br/>Essa atividade suspeita detectada está associada a uma ferramenta chamada elefante cego. A ferramenta servidores Web de impressões digitais e tenta detectar os aplicativos instalados e suas versões. Os invasores geralmente usam essa ferramenta para investigar os aplicativos Web para encontrar vulnerabilidades.|
|**Acesso suspeito a uma página da Web possivelmente vulnerável detectada**|O Azure App log de atividades do serviço indica que uma página da Web que parece ser confidencial foi acessada. <br/>Essa atividade suspeita originada de um endereço de origem cujo padrão de acesso é semelhante ao de um scanner da Web. Esse tipo de atividade geralmente está associado a uma tentativa de um invasor verificar sua rede para tentar obter acesso a páginas da Web sensíveis ou vulneráveis.|
|**Arquivo PHP na pasta de carregamento**|O log de atividades do serviço Azure App indica que algo foi acessado em uma página do PHP suspeita localizada na pasta de carregamento. <br/>Esse tipo de pasta geralmente não contém arquivos PHP. A existência desse tipo de arquivo pode indicar uma exploração aproveitando vulnerabilidades de carregamento de arquivo arbitrários.|
|**Uma tentativa de executar comandos do Linux em um serviço de aplicativo do Windows**|A análise dos processos do serviço de aplicativo detectou uma tentativa de executar um comando do Linux em um serviço de aplicativo do Windows. Esta ação estava sendo executada pelo aplicativo Web. Esse comportamento geralmente é visto durante as campanhas que exploram uma vulnerabilidade em um aplicativo Web comum.|
|**Execução de PHP suspeita detectada**|Os logs de computador indicam que um processo PHP suspeito está em execução. A ação incluía uma tentativa de executar comandos do sistema operacional ou código PHP na linha de comando usando o processo PHP. Embora esse comportamento possa ser legítimo, em aplicativos Web, esse comportamento também é observado em atividades mal-intencionadas, como tentativas de infectar sites com shells da Web.|
|**Execução do processo da pasta temporária**|A análise de processos do serviço de aplicativo detectou uma execução de um processo da pasta temporária do aplicativo. Embora esse comportamento possa ser legítimo, em aplicativos Web esse comportamento também é observado em atividades mal-intencionadas.|
|**Tentativa de executar o comando de alto privilégio detectado**|A análise dos processos do serviço de aplicativo detectou uma tentativa de executar um comando que requer privilégios altos. O comando foi executado no contexto do aplicativo Web. Embora esse comportamento possa ser legítimo, em aplicativos Web esse comportamento também é observado em atividades mal-intencionadas.|

> [!NOTE]
> A detecção de ameaças da central de segurança para o serviço de aplicativo não está disponível atualmente nas regiões do Azure governamental e do soberanas Cloud.

Para obter mais informações sobre alertas de detecção de ameaças do serviço de aplicativo, visite proteger serviço de aplicativo com a central de segurança do Azure e examine como habilitar o monitoramento e a proteção de suas cargas de trabalho do serviço de aplicativo.

## Recipientes<a name="azure-containers"></a>

A central de segurança fornece detecção de ameaças em tempo real para seus contêineres em computadores Linux com base na estrutura auditada. Os alertas identificam várias atividades suspeitas do Docker, como a criação de um contêiner privilegiado em um host, uma indicação do servidor de Secure Shell (SSH) em execução dentro de um contêiner do Docker ou o uso de crypto mineradores. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres. Além das detecções do Linux, a central de segurança também oferece análises mais específicas para implantações de contêineres.
