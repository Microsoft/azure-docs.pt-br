---
title: Auditoria e Log - Ferramenta de Modelagem de Ameaças da Microsoft - Azure | Microsoft Docs
description: atenuações de ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 4843828c89b04e36b0bcc73dcedf9c5735b73729
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610853"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Quadro de segurança: Auditoria e log | Mitigações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificar entidades sensíveis em sua solução e implementar a auditoria de alteração](#sensitive-entities)</li></ul> |
| **Aplicativo Web** | <ul><li>[Certifique-se de que a auditoria e registro em log é imposto no aplicativo](#auditing)</li><li>[Certifique-se de que a rotação de log e separação estão em vigor](#log-rotation)</li><li>[Certifique-se de que o aplicativo não registra dados confidenciais do usuário](#log-sensitive-data)</li><li>[Verifique se os arquivos de Log e auditoria tem acesso restrito](#log-restricted-access)</li><li>[Certifique-se de que os eventos de gerenciamento de usuário estão registrados](#user-management)</li><li>[Verifique se o sistema tem embutidas defesas contra uso indevido](#inbuilt-defenses)</li><li>[Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](#diagnostics-logging)</li></ul> |
| **Banco de dados** | <ul><li>[Verifique se a auditoria de logon é habilitada no SQL Server](#identify-sensitive-entities)</li><li>[Ativar a detecção de ameaças no SQL Azure](#threat-detection)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Uso da Análise de Armazenamento do Azure para auditar o acesso do Armazenamento do Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementar o log suficiente](#sufficient-logging)</li><li>[Implementar o tratamento de falha de auditoria suficiente](#audit-failure-handling)</li></ul> |
| **API da Web** | <ul><li>[Certifique-se de que a auditoria e registro em log é imposto no API da Web](#logging-web-api)</li></ul> |
| **Gateway de Campo de IoT** | <ul><li>[Certifique-se de que a auditoria e log apropriado é imposta no campo Gateway](#logging-field-gateway)</li></ul> |
| **Gateway de Nuvem IoT** | <ul><li>[Certifique-se de que a auditoria e log apropriado é imposta no Gateway de nuvem](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identificar entidades sensíveis em sua solução e implementar a auditoria de alteração

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | Identificar as entidades em sua solução que contém dados confidenciais e implementar a auditoria de alteração nessas entidades e campos |

## <a id="auditing"></a>Certifique-se de que a auditoria e o registro em log são impostos no aplicativo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | Habilite a auditoria e log em todos os componentes. Os logs de auditoria devem capturar o contexto do usuário. Identifique todos os eventos importantes e registre em log esses eventos. Implementar o log centralizado |

## <a id="log-rotation"></a>Certifique-se de que a rotação de log e separação estão em vigor

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | <p>Rotação de log é um processo automatizado usado na administração do sistema no qual os arquivos de log datado são arquivados. Servidores que executam grandes aplicativos muitas vezes registrar cada solicitação: diante de grandes logs, rotação do log é uma forma de limitar o tamanho total dos logs e ainda permitir a análise dos eventos recentes. </p><p>Separação de log basicamente significa que você deve armazenar seu log de arquivos em uma partição diferente em que o sistema operacional/aplicativo é executado para avert um ataque de negação de serviço ou o downgrade do seu aplicativo o desempenho</p>|

## <a id="log-sensitive-data"></a>Certifique-se de que o aplicativo não registra dados confidenciais do usuário

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | <p>Verifique se você não faça quaisquer dados confidenciais que um usuário envia ao seu site. Verifique se há registro intencional, bem como os efeitos colaterais causados por problemas de design. Exemplos de dados confidenciais:</p><ul><li>Credenciais do Usuário</li><li>Número do seguro social ou outras informações de identificação</li><li>Números de cartão de crédito ou outras informações financeiras</li><li>Informações de integridade</li><li>As chaves particulares ou outros dados que pode ser usados para descriptografar informações criptografadas</li><li>Informações do sistema ou aplicativo que podem ser usadas para atacar com mais eficiência o aplicativo</li></ul>|

## <a id="log-restricted-access"></a>Verifique se os arquivos de Log e auditoria tem acesso restrito

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | <p>Verifique se os direitos de acesso a arquivos de log são configurados de forma apropriada. Contas de aplicativo devem ter acesso somente gravação e operadores e equipe de suporte deve ter acesso somente leitura, conforme necessário.</p><p>Contas de administradores são as únicas contas que devem ter acesso completo. Verifique a ACL do Windows em arquivos de log para garantir que estejam adequadamente restritos:</p><ul><li>Contas de aplicativos devem ter acesso somente gravação</li><li>Operadores e a equipe de suporte deve ter acesso somente leitura, conforme necessário</li><li>Os administradores são as únicas contas que devem ter acesso completo</li></ul>|

## <a id="user-management"></a>Certifique-se de que os eventos de gerenciamento de usuário estão registrados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | <p>Certifique-se de que o aplicativo monitora eventos de gerenciamento de usuário, como logons de usuário bem-sucedidas e com falha, redefinições de senha, alteração de senha, bloqueio de conta, registro de usuário. Fazer isso ajuda a detectar e reagir a comportamentos potencialmente suspeitos. Ele também permite a coleta de dados de operações; Por exemplo, para controlar quem está acessando o aplicativo</p>|

## <a id="inbuilt-defenses"></a>Verifique se o sistema tem embutidas defesas contra uso indevido

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas**                   | <p>Controles devem estar em vigor que lançam a exceção de segurança em caso de uso incorreto do aplicativo. Por exemplo, se a validação de entrada está em vigor e um invasor tente injetar código mal-intencionado que não corresponda ao regex, uma exceção de segurança pode ser gerada que pode ser um indicativo de uso indevido do sistema</p><p>Por exemplo, é recomendável ter exceções de segurança registradas e ações realizadas para os seguintes problemas:</p><ul><li>Validação da entrada</li><li>Violações de CSRF</li><li>Força bruta (limite superior para o número de solicitações por usuário por recurso)</li><li>Violações de carregamento de arquivo</li><ul>|

## <a id="diagnostics-logging"></a>Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de ambiente - Azure |
| **Referências**              | N/D  |
| **Etapas** | <p>O Azure oferece diagnóstico integrado para ajudar na depuração de um aplicativo Web do Serviço de Aplicativo. Ele também se aplica a aplicativos de API e aplicativos móveis. Os aplicativos Web do Serviço de Aplicativo oferecem funcionalidade de diagnóstico para registro em log tanto de informações do servidor Web quanto do aplicativo Web.</p><p>Estes estão logicamente separados em diagnóstico de servidor Web e diagnóstico de aplicativos</p>|

## <a id="identify-sensitive-entities"></a>Verifique se a auditoria de logon é habilitada no SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Configurar a auditoria de logon](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Etapas** | <p>Auditoria de logon do servidor de banco de dados deve ser habilitada para ataques de adivinhação de senha/confirmar detectar. É importante capturar as tentativas de logon com falha. Capturar as duas tentativas de logon bem-sucedidas e malsucedidas fornece o benefício adicional durante investigações jurídicas</p>|

## <a id="threat-detection"></a>Ativar a detecção de ameaças no SQL Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Introdução à Detecção de Ameaças do Banco de Dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Etapas** |<p>A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Ela fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança nas atividades anormais.</p><p>Os usuários podem explorar os eventos suspeitos usando a Auditoria do Banco de Dados SQL do Azure para determinar se eles resultam de uma tentativa de acesso, violação ou exploração dos dados no banco de dados.</p><p>A Detecção de Ameaças torna simples tratar as possíveis ameaças no banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar os sistemas de monitoramento de segurança avançados</p>|

## <a id="analytics"></a>Uso da Análise de Armazenamento para auditar o acesso do Armazenamento do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D |
| **Referências**              | [Uso da Análise de Armazenamento para monitorar o tipo de autorização](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Etapas** | <p>Para cada conta de armazenamento, é possível habilitar a Análise de Armazenamento do Azure para realizar o registro em log e armazenar dados de métricas. Os logs de análise de armazenamento fornecem informações importantes, como método de autenticação usado por uma pessoa ao acessar o armazenamento.</p><p>Isso pode ser muito útil se você estiver protegendo rigorosamente o acesso ao armazenamento. Por exemplo, no Armazenamento de Blobs, é possível definir todos os contêineres para privado e implementar o uso de um serviço SAS em todos os aplicativos. Desse modo, você pode verificar os logs regularmente para ver se seus blobs são acessados usando as chaves da conta de armazenamento, que pode indicar uma violação de segurança, ou se os blobs são públicos, mas não deveriam ser.</p>|

## <a id="sufficient-logging"></a>Implementar o log suficiente

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Etapas** | <p>A falta de uma trilha de auditoria adequada após um incidente de segurança pode atrapalhar os esforços forenses. Windows Communication Foundation (WCF) oferece a capacidade de registrar tentativas de autenticação bem-sucedidas e/ou com falha.</p><p>Registro em log as tentativas de autenticação com falha pode avisar os administradores de possíveis ataques de força bruta. Da mesma forma, o log de eventos de autenticação bem-sucedida pode fornecer uma trilha de auditoria útil quando uma conta legítima seja comprometida. Habilitar o recurso de auditoria de segurança de serviço do WCF |

### <a name="example"></a>Exemplo
A seguir está um exemplo de configuração com a auditoria ativada
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Implementar o tratamento de falha de auditoria suficiente

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Etapas** | <p>Solução desenvolvida está configurada para não gerar uma exceção quando ele falha ao gravar em um log de auditoria. Se o WCF está configurado para não lançar uma exceção quando não é possível gravar em um log de auditoria, o programa não será notificado da falha e auditoria de eventos de segurança crítica pode não ocorrer.</p>|

### <a name="example"></a>Exemplo
O `<behavior/>` elemento do arquivo de configuração WCF abaixo instrui o WCF não notificar o aplicativo quando o WCF não consegue gravar um log de auditoria.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Configure o WCF para notificar o programa sempre que não é possível gravar em um log de auditoria. O programa deve ter um esquema de notificação alternativo em vigor para a organização que trilhas de auditoria não estão sendo mantidos de alerta. 

## <a id="logging-web-api"></a>Certifique-se de que a auditoria e registro em log é imposto no API da Web

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Habilite a auditoria e log em APIs da Web. Os logs de auditoria devem capturar o contexto do usuário. Identifique todos os eventos importantes e registre em log esses eventos. Implementar o log centralizado |

## <a id="logging-field-gateway"></a>Certifique-se de que a auditoria e log apropriado é imposta no campo Gateway

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Quando vários dispositivos se conectam a um Gateway de campo, certifique-se de que as tentativas de conexão e o status de autenticação (êxito ou falha) de dispositivos individuais são registradas e mantidas no campo Gateway.</p><p>Além disso, nos casos em que o Gateway de campo é manter as credenciais de IoT Hub para dispositivos individuais, certifique-se de que a auditoria é executada quando essas credenciais são recuperadas. Desenvolva um processo para carregar periodicamente os logs em IoT Hub/armazenamento do Azure para retenção a longo prazo.</p> |

## <a id="logging-cloud-gateway"></a>Certifique-se de que a auditoria e log apropriado é imposta no Gateway de nuvem

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Introdução ao monitoramento de operações do Hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Etapas** | <p>Design para coletar e armazenar dados de auditoria coletados por meio do monitoramento de operações de Hub IoT. Habilite monitoramentos categorias a seguir:</p><ul><li>Operações de identidade do dispositivo</li><li>Comunicações do dispositivo para a nuvem</li><li>Comunicações da nuvem para o dispositivo</li><li>conexões</li><li>Carregamentos de arquivos</li></ul>|