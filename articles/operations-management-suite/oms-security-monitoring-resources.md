---
title: "Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento o ajuda a usar as funcionalidades de Segurança e Auditoria do OMS para monitorar seus recursos e identificar problemas de segurança."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite
Este documento o ajuda a usar as funcionalidades de Segurança e Auditoria do OMS para monitorar seus recursos e identificar problemas de segurança.

## <a name="what-is-oms"></a>O que é o OMS?
O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem. Para obter mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Monitorando recursos
Sempre que possível e acima de tudo, será conveniente prevenir a ocorrência incidentes de segurança. No entanto, é impossível prevenir todos os incidentes de segurança. Quando ocorrer, de fato, um incidente de segurança, você precisará garantir que seu impacto seja minimizado.  Há três recomendações críticas que podem ser usadas para minimizar a quantidade e o impacto dos incidentes de segurança:

* Avalie regularmente as vulnerabilidades em seu ambiente.
* Verifique regularmente todos os sistemas de computadores e dispositivos de rede para assegurar que eles têm todos os patches mais recentes instalados.
* Verifique regularmente todos os logs e mecanismos de log, incluindo logs de eventos do sistema operacional, logs específicos de aplicativos e logs do sistema de detecção de intrusões.

A solução de Segurança e Auditoria do OMS permite que o departamento de TI monitore de forma ativa todos os recursos, o que pode ajudar a minimizar o impacto dos incidentes de segurança. A Segurança e Auditoria do OMS tem domínios de segurança que podem ser usados para monitorar os recursos. Os domínios de segurança fornecem um acesso rápido a várias opções; para o monitoramento de segurança, os seguintes domínios serão abordados mais detalhadamente:

* Avaliação de malware
* Avaliação de atualização
* Identidade e Acesso

> [!NOTE]
> para obter uma visão geral de todas essas opções, leia [Introdução à solução de Segurança e Auditoria do Operations Management Suite](oms-security-getting-started.md).
> 
> 

### <a name="monitoring-system-protection"></a>Monitorando a proteção do sistema
Em uma abordagem de defesa em profundidade, cada camada de proteção é importante para o estado de segurança geral de seu ativo. Os computadores com ameaças detectadas e os computadores sem proteção suficiente são mostrados no bloco Avaliação de Malware em Domínios de Segurança. Usando as informações contidas em Avaliação de Malware, é possível identificar um plano para aplicar proteção aos servidores que precisam dessa proteção. Para acessar essa opção, siga as etapas abaixo:

1. No painel principal do **Microsoft Operations Management Suite**, clique no bloco **Segurança e Auditoria**.
   
    ![Segurança e Auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. No painel **Segurança e Auditoria**, clique em **Avaliação de Malware** em **Domínios de Segurança**. O painel **Avaliação de Malware** é exibido, conforme mostrado abaixo:

![Avaliação de malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

É possível usar o painel **Avaliação de Malware** para identificar os seguintes problemas de segurança:

* **Ameaças ativas**: computadores que foram comprometidos e que têm ameaças ativas no sistema.
* **Ameaças corrigidas**: computadores que foram comprometidos, mas cujas ameaças foram corrigidas.
* **Assinatura desatualizada**: computadores que têm a proteção contra malware habilitada, mas cuja assinatura está desatualizada.
* **Sem proteção em tempo real**: computadores que não têm o antimalware instalado.

### <a name="monitoring-updates"></a>Monitorando as atualizações
A aplicação das atualizações mais recentes de segurança é uma melhor prática de segurança, que deve ser incorporada em sua estratégia de gerenciamento de atualizações. O serviço Microsoft Monitoring Agent (HealthService.exe) lê as informações de atualização dos computadores monitorados e envia essas informações atualizadas para o serviço do OMS na nuvem para processamento. O serviço Microsoft Monitoring Agent é configurado como um serviço automático e deve estar sempre em execução no computador de destino.

![Monitorando as atualizações](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Lógica é aplicada aos dados de atualização e o serviço de nuvem registra os dados. Se forem encontradas atualizações ausentes, elas serão mostradas no painel **Atualizações** . Você pode usar o painel **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas. Siga as etapas abaixo para acessar o painel **Atualizações** :

1. No painel principal do **Microsoft Operations Management Suite**, clique no bloco **Segurança e Auditoria**.
2. No painel **Segurança e Auditoria**, clique em **Avaliação de Atualização** em **Domínios de Segurança**. O painel Atualização é exibido, conforme mostrado abaixo:

![Avaliação de atualização](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Neste painel, é possível executar uma avaliação de atualização para entender o estado atual de seus computadores e tratar as ameaças mais críticas. Usando o bloco **Atualizações Críticas ou de Segurança** , os administradores de TI poderão acessar informações detalhadas sobre as atualizações ausentes, conforme mostrado abaixo:

![resultado da pesquisa](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este relatório inclui informações críticas que podem ser usadas para identificar o tipo de ameaça ao qual esse sistema está vulnerável, que inclui os artigos da base de dados da Microsoft associados à atualização de segurança e o Boletim da Microsoft com mais detalhes sobre a vulnerabilidade.

### <a name="monitoring-identity-and-access"></a>Monitorando a identidade e o acesso
Com usuários trabalhando em qualquer lugar, usando diferentes dispositivos e acessando uma grande quantidade de aplicativos na nuvem e locais, é fundamental que suas credenciais sejam protegidas. Ataques de roubo de credenciais são aqueles em que um invasor inicialmente obtém acesso às credenciais de um usuário comum para acessar um sistema na rede. Muitas vezes, esse ataque inicial é apenas uma maneira de obter acesso à rede, cuja meta última é descobrir contas com privilégios. 

Os invasores permanecerão na rede, usando livremente as ferramentas disponíveis para extrair as credenciais das sessões de outras contas conectadas. Dependendo da configuração do sistema, essas credenciais podem ser extraídas na forma de hashes, tíquetes ou até mesmo senhas de texto sem formatação.  

> [!NOTE]
> os computadores que são diretamente expostos à Internet terão muitas tentativas com falha em fazer logon usando todos os tipos de nomes de usuários conhecidos (por exemplo, Administrador). Na maioria dos casos, não haverá problemas se os nomes de usuário conhecidos não forem usados e se a senha for forte o suficiente.
> 
> 

É possível identificar esses invasores antes que eles comprometam uma conta com privilégios. Você pode aproveitar a **solução de Segurança e Auditoria do OMS** para monitorar a identidade e o acesso. Siga as etapas abaixo para acessar o painel **Identidade e Acesso** :

1. No painel principal **Microsoft Operations Management Suite** , clique no bloco Segurança e Auditoria.
2. No painel **Segurança e Auditoria**, clique em **Identidade e Acesso** em **Domínios de Segurança**. O painel **Identidade e Acesso** é exibido, conforme mostrado abaixo:

![identidade e acesso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Como parte de sua estratégia de monitoramento regular, é necessário incluir o monitoramento de identidade. O Administrador de TI deverá examinar se houver um nome de usuário válido específico que apresenta várias tentativas. Isso pode indicar um invasor que obteve o nome de usuário real e tentou realizar um ataque de força bruta ou uma ferramenta automática que usa uma senha embutida em código que expirou.

Este painel possibilita que o departamento de TI identifique rapidamente ameaças potenciais relacionadas à identidade e ao acesso aos recursos da empresa. É especificamente importante identificar também as tendências potencias, por exemplo, no bloco Logons ao longo do Tempo, é possível ver, por período, quantas vezes ocorreu uma tentativa de logon com falha. Nesse caso, o computador **FileServer** recebeu 35 tentativas de logon. É possível explorar mais detalhes sobre esse computador clicando nele. 

![mais detalhes](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

O relatório gerado para este computador traz detalhes importantes sobre este padrão. Observe que a coluna **ACCOUNT** fornece a conta de usuário que foi usada para tentar acessar o sistema, a coluna **TIMEGENERATED** fornece o intervalo de tempo no qual ocorreu a tentativa e a coluna **LOGONTYPENAME** fornece o local em que ocorreu essa tentativa. Caso essas tentativas tenha ocorrido localmente no sistema por um programa, a coluna **PROCESS** poderá mostrar o nome do processo. Em cenários em que a origem da tentativa de logon é um programa, você já terá o nome do processo disponível e será possível realizar agora uma investigação mais detalhada no sistema de destino.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar a solução de Segurança e Auditoria do OMS para monitorar seus recursos. Para saber mais sobre a Segurança do OMS, veja os seguintes artigos:

* [Operations Management Suite (OMS) overview](operations-management-suite-overview.md)
* [Introdução à solução de Segurança e Auditoria do Operations Management Suite](oms-security-getting-started.md)
* [Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)

