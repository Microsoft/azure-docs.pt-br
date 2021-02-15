---
title: Gerenciamento da rede de produção do Azure-Microsoft Azure
description: Este artigo descreve como a Microsoft gerencia e opera a rede de produção do Azure para proteger os data centers do Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68727123"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Gerenciamento e operação da rede de produção do Azure    
Este artigo descreve como a Microsoft gerencia e opera a rede de produção do Azure para proteger os data centers do Azure.

## <a name="monitor-log-and-report"></a>Monitorar, registrar e relatar

O gerenciamento e a operação da rede de produção do Azure é um esforço coordenado entre as equipes de operações do Azure e do Banco de Dados SQL do Azure. As equipes usam várias ferramentas de monitoramento de desempenho de sistemas e aplicativos no ambiente. E eles usam ferramentas apropriadas para monitorar dispositivos de rede, servidores, serviços e processos de aplicativos.

Para garantir a execução segura dos serviços em execução no ambiente do Azure, as equipes de operações implementam vários níveis de monitoramento, criação de log e relatórios, incluindo as seguintes ações:

- Primeiramente, o Microsoft Monitoring Agent (MMA) reúne informações de log de monitoramento e diagnóstico de vários locais, incluindo o controlador de malha (FC) e o sistema operacional raiz (SO), e grava esses dados nos arquivos de log. O agente, eventualmente, envia um subconjunto digitado das informações para uma conta de armazenamento do Azure pré-configurada. Além disso, o serviço de monitoramento e diagnóstico independente lê vários dados de registro de monitoramento e diagnóstico e resume as informações. O serviço de monitoramento e diagnóstico grava as informações em um log integrado. O Azure usa o monitoramento de segurança personalizado do Azure, que é uma extensão do sistema de monitoramento do Azure. Ele possui componentes que observam, analisam e relatam eventos pertinentes à segurança de vários pontos da plataforma.

- A plataforma Windows Fabric do Windows Azure SQL Database fornece serviços de gerenciamento, implantação, desenvolvimento e supervisão operacional para o Banco de Dados SQL do Azure. A plataforma oferece serviços distribuídos de implantação em várias etapas, monitoramento de integridade, reparos automáticos e conformidade de versão de serviço. Ela fornece os seguintes recursos:

   - Serviço de recursos com o ambiente de desenvolvimento de alta fidelidade de modelagem (clusters do datacenter são escassos e caro).
   - Implantação de um clique e fluxos de trabalho de atualização para a inicialização de serviço e manutenção.
   - Relatório com fluxos de trabalho de reparo automatizado para habilitar a autorrecuperação de integridade.
   - Monitoramento em tempo real, alertas e recursos de depuração nos nós de um sistema distribuído.
   - Coleção centralizada de dados operacionais e métricas para raiz distribuído causar insight de análise e o serviço.
   - Ferramentas operacionais para implantação, gerenciamento de mudanças e monitoramento.
   - A plataforma Windows Fabric do banco de dados SQL do Azure e os scripts de watchdog são executados continuamente e monitorados em tempo real.

Se ocorrer alguma anomalia, o processo de resposta a incidentes seguido pela equipe de triagem de incidentes do Azure será ativado. A equipe de suporte do Azure apropriada é notificada para responder ao incidente. Acompanhamento de questões e resolução são documentados e gerenciados em um sistema centralizado de emissão de tíquetes. As métricas de tempo de atividade do sistema estão disponíveis de acordo com o contrato de não divulgação (NDA) e mediante solicitação.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Rede corporativa e acesso multifator à produção
A base de usuários da rede corporativa inclui pessoal de suporte do Azure. A rede corporativa oferece suporte a funções corporativas internas e inclui acesso a aplicativos internos usados para suporte ao cliente do Azure. A rede corporativa é logicamente e fisicamente separada da rede de produção do Azure. O pessoal do Azure acessa a rede corporativa usando estações de trabalho e laptops do Azure. Todos os usuários devem ter uma conta do Azure AD (Azure Active Directory), incluindo um nome de usuário e uma senha, para acessar os recursos da rede corporativa. O acesso à rede corporativa usa contas do Azure AD, que são emitidas para todos os funcionários, contratados e fornecedores da Microsoft e gerenciados pela Microsoft Information Technology. Os identificadores exclusivos de usuários distinguem o pessoal com base em seu status de emprego na Microsoft.

O acesso a aplicativos internos do Azure é controlado por meio da autenticação com o AD FS (Serviços de Federação do Active Directory). O AD FS é um serviço hospedado pela Microsoft Information Technology que fornece autenticação de usuários da rede corporativa por meio da aplicação de um token seguro e de declarações do usuário. O AD FS permite que os aplicativos internos do Azure autentiquem usuários no domínio do diretório ativo corporativo da Microsoft. Para acessar a rede de produção a partir do ambiente de rede corporativa, os usuários devem autenticar usando a autenticação multifatores.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](physical-security.md)
- [Disponibilidade da infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](infrastructure-sql.md)
- [Monitoramento de infraestrutura do Microsoft Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](protection-customer-data.md)
