---
title: Autorização - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Saiba mais sobre a mitigação de autorização no Threat Modeling Tool. Consulte uma lista de possíveis ameaças e instruções de mitigação.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 38b147a85a26fd1e0be4f5dc6b63ae4c1331d348
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094003"
---
# <a name="security-frame-authorization--mitigations"></a>Estrutura de segurança: Autorização | Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança de computador** | <ul><li>[Verifique se as ACLs apropriadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo](#acl-restricted-access)</li><li>[Garantir que os conteúdos confidenciais do usuário armazenados no aplicativo sejam armazenados no diretório do perfil do usuário](#sensitive-directory)</li><li>[Verifique se os aplicativos implantados são executados com privilégios mínimos](#deployed-privileges)</li></ul> |
| **Aplicativo Web** | <ul><li>[Impor ordem de etapa sequencial ao processar fluxos de lógica de negócios](#sequential-logic)</li><li>[Implementar um mecanismo de limitação de taxa para evitar a enumeração](#rate-enumeration)</li><li>[Verifique se a autorização adequada está em vigor e se o princípio de privilégios mínimos é seguido](#principle-least-privilege)</li><li>[As decisões de autorização de acesso a recursos e a lógica de negócios não devem ser baseadas em parâmetros de solicitação de entrada](#logic-request-parameters)</li><li>[Garantir que conteúdos e recursos não sejam enumeráveis ou estejam acessíveis por meio da navegação forçada](#enumerable-browsing)</li></ul> |
| **Backup de banco de dados** | <ul><li>[Garantir que contas com privilégios mínimos sejam usadas para conexão com o servidor de banco de dados](#privileged-server)</li><li>[Implemente Segurança em Nível de Linha RLS para impedir que os locatários acessem os dados uns dos outros](#rls-tenants)</li><li>[A função sysadmin deve ter apenas usuários necessários válidos](#sysadmin-users)</li></ul> |
| **Gateway de Nuvem IoT** | <ul><li>[Conectar ao Gateway de Nuvem usando tokens com privilégios mínimos](#cloud-least-privileged)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Usar uma chave SAS com a permissão somente envio para gerar tokens de dispositivo](#sendonly-sas)</li><li>[Não use tokens de acesso que forneçam acesso direto ao Hub de eventos](#access-tokens-hub)</li><li>[Conectar ao Hub de Eventos usando chaves SAS que tenham as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **Azure DocumentDB** | <ul><li>[Usar tokens de recurso para se conectar ao Azure Cosmos DB sempre que possível](#resource-docdb)</li></ul> |
| **Limite de Confiança do Azure** | <ul><li>[Habilitar o gerenciamento de acesso refinado à assinatura do Azure usando o RBAC do Azure](#grained-rbac)</li></ul> |
| **Limite de confiança do Service Fabric** | <ul><li>[Restringir o acesso do cliente às operações de cluster usando o Azure RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Executar a modelagem de segurança e usar a segurança em nível de campo quando necessário](#modeling-field)</li></ul> |
| **Portal do Dynamics CRM** | <ul><li>[Execute a modelagem de segurança das contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM.](#portal-security)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Conceder permissões refinadas em um intervalo de entidades no Armazenamento de Tabelas do Azure](#permission-entities)</li><li>[Habilitar o controle de acesso baseado em função do Azure (RBAC do Azure) para a conta de armazenamento do Azure usando Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Cliente móvel** | <ul><li>[Implementar detecção de modificação ou desbloqueio implícito](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fraca no WCF](#weak-class-wcf)</li><li>[Controle de autorização de implementação do WCF](#wcf-authz)</li></ul> |
| **API da Web** | <ul><li>[Implementar o mecanismo de autorização adequado no ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Executar verificações de autorização no dispositivo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão](#device-permission)</li></ul> |
| **Gateway de Campo de IoT** | <ul><li>[Executar verificações de autorização no Gateway de Campo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Garantir que as ACLs apropriadas estejam configuradas para restringir o acesso não autorizado aos dados no dispositivo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que as ACLs apropriadas estejam configuradas para restringir o acesso não autorizado aos dados no dispositivo.|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Garantir que os conteúdos confidenciais do usuário armazenados no aplicativo sejam armazenados no diretório do perfil do usuário

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que os conteúdos confidenciais do usuário armazenados no aplicativo sejam armazenados no diretório do perfil do usuário. Isso impede que os múltiplos usuários do computador acessem dados uns dos outros.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Garantir que os aplicativos implantados sejam executados com privilégios mínimos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que os aplicativos implantados sejam executados com privilégios mínimos. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Impor uma ordem sequencial de etapas durante o processamento dos fluxos de lógica de negócios

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Para verificar se esse estágio foi executado por um usuário genuíno, force o aplicativo a processar os fluxos de lógica de negócios seguindo a ordem sequencial das etapas, permitindo que todas as etapas sejam processadas em um tempo humanamente realista e evitando que a ordem das etapas seja desrespeitadas, que alguma etapa seja ignorada, que as etapas de outro usuário sejam processadas ou que as transações sejam enviadas rápido demais.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementar um mecanismo de limitação de taxa para evitar a enumeração

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Verifique se os identificadores confidenciais são aleatórios. Implemente o controle CAPTCHA em páginas anônimas. Assegure que erros e exceções não revelem dados específicos.|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Garantir que um sistema de autorização adequado esteja em vigor e que o princípio dos privilégios mínimos seja seguido

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Seguir esse princípio significa fornecer a uma conta de usuário apenas os privilégios essenciais ao trabalho desse usuário. Por exemplo, um usuário de backup não precisa instalar o software, logo ele terá direitos somente para fazer backups e executar os aplicativos de backup relacionados. Quaisquer outros privilégios, como instalar um novo software, serão bloqueados. Esse princípio também se aplica a um usuário de computador pessoal que geralmente trabalha com uma conta de usuário comum e que abre uma conta com privilégios e protegida por senha (ou seja, a de um superusuário) somente quando for absolutamente necessário. </p><p>É possível aplicar esse princípio a aplicativos Web. Em vez de depender apenas dos métodos de autenticação baseada em função usando sessões, queremos atribuir privilégios a usuários por meio de um sistema de autenticação baseado em banco de dados. As sessões continuarão sendo usadas para identificar se o usuário fez logon corretamente, mas agora, em vez de atribuir uma função específica a esse usuário, você pode atribuir privilégios a ele para verificar quais ações ele tem permissão para executar no sistema. Esse método tem uma outra grande vantagem: sempre que for necessário atribuir menos privilégios a um usuário, suas alterações serão aplicadas imediatamente, porque a atribuição não depende da sessão. Se esse fosse o caso, a sessão precisaria expirar primeiro.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>As decisões de autorização de acesso a recursos e a lógica de negócios não devem ser baseadas em parâmetros de solicitação de entrada

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Sempre que você estiver verificando se um usuário não tem permissão para acessar alguns dados, as restrições de acesso devem ser processadas no servidor. O identificador de usuário (userID) deve ser armazenado dentro de uma variável de sessão no logon e ser usado para recuperar dados do usuário no banco de dados. |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Isso impede que invasores adulterem e modifiquem a operação do aplicativo, porque a identificador de recuperação de dados é processado pelo servidor.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Garantir que conteúdos e recursos não sejam enumeráveis ou estejam acessíveis por meio da navegação forçada

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Arquivos de configuração estáticos e com informações confidenciais não devem ser mantidos no diretório base. Para conteúdos que não precisam ser divulgados, aplique os controles de acesso adequados ou remova esses conteúdos.</p><p>Além disso, a navegação forçada geralmente é combinada com técnicas de força bruta para coletar informações durante a tentativa de acessar o máximo de URLs possível para enumerar os diretórios e os arquivos em um servidor. Os invasores podem procurar todas as variações dos arquivos mais comuns existentes. Por exemplo, uma pesquisa de arquivo de senha incluiria os arquivos psswd.txt, password.htm, password.dat e outras variações.</p><p>Para atenuar isso, recursos de detecção de tentativas de força bruta tentativas devem ser incluídos.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Garantir que contas com privilégios mínimos sejam usadas para conexão com o servidor de banco de dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hierarquia de permissões do SQL](/sql/relational-databases/security/permissions-hierarchy-database-engine), [protegíveis do SQL](/sql/relational-databases/security/securables) |
| **Etapas** | As contas com privilégios mínimos devem ser usadas para conexão com o banco de dados. O logon em aplicativos deve ser restrito no banco de dados, só deve executar os procedimentos armazenados selecionados e não deve conceder acesso direto à tabela. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementar a RLS (Segurança em Nível de Linha) para impedir que locatários acessem os dados uns dos outros

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL - V12, Versão do SQL - MsSQL2016 |
| **Referências**              | [Segurança em Nível de Linha (RLS) do SQL Server](/sql/relational-databases/security/row-level-security) |
| **Etapas** | <p>A Segurança em Nível de Linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução).</p><p>O RLS (nível de linha de segurança) simplifica o design e a codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários tenham acesso somente às linhas de dados relevantes aos seus respectivos departamento ou restringindo o acesso de um cliente apenas aos dados pertinentes à sua empresa.</p><p>A lógica de restrição de acesso é localizado na camada de banco de dados, em vez de longe dos dados em outra camada de aplicativo. O sistema de banco de dados aplica as restrições de acesso toda vez que há tentativa de acesso a dados a partir de qualquer camada. Isso torna o sistema de segurança mais robusto e confiável, reduzindo a área de superfície do sistema de segurança.</p><p>|

Observe que a RLS como um recurso de banco de dados pronto para uso é aplicável somente a SQL Server iniciando 2016, banco de dados SQL do Azure e SQL Instância Gerenciada. Se o recurso RLS pronto para uso não for implementado, assegure que o acesso aos dados seja restringido com modos de exibição e procedimentos

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>A função sysadmin deve ter apenas usuários necessários válidos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hierarquia de permissões do SQL](/sql/relational-databases/security/permissions-hierarchy-database-engine), [protegíveis do SQL](/sql/relational-databases/security/securables) |
| **Etapas** | Os membros com a função de servidor fixa sysadmin devem ser muito limitados e não podem possuir contas usadas por aplicativos.  Revise a lista de usuários com essa função e remova todas as contas desnecessárias.|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Conectar ao Gateway de Nuvem usando tokens com privilégios mínimos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - Hub IoT do Azure |
| **Referências**              | [Controle de acesso do Hub IOT](../../iot-hub/iot-hub-devguide.md) |
| **Etapas** | Forneça permissões com privilégios mínimos para vários componentes que se conectam ao Gateway de Nuvem (Hub IoT). Um exemplo típico: o componente de gerenciamento/provisionamento de dispositivos usa os recursos de leitura/gravação do Registro, e o processador de eventos (ASA) usa o recurso de conexão de serviço. Dispositivos individuais se conectam usando as credenciais do dispositivo.|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Usar uma chave SAS com a permissão somente envio para gerar tokens de dispositivo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](../../event-hubs/authenticate-shared-access-signature.md) |
| **Etapas** | Uma chave SAS é usada para gerar tokens para dispositivos individuais. Use uma chave SAS com a permissão somente envio ao gerar o token do dispositivo para um editor específico.|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Não usar tokens de acesso que fornecem acesso direto ao Hub de Eventos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](../../event-hubs/authenticate-shared-access-signature.md) |
| **Etapas** | Um token que concede acesso direto ao Hub de Eventos não deve ser fornecido para o dispositivo. Usar um token com privilégios mínimos para o dispositivo que fornece acesso somente a um Publicador ajudaria a identificar e não permitir, se for considerado um dispositivo não autorizado ou comprometido.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Conectar ao Hub de Eventos usando chaves SAS que tenham as permissões mínimas necessárias

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](../../event-hubs/authenticate-shared-access-signature.md) |
| **Etapas** | Forneça permissões de privilégio mínimo para vários aplicativos back-end que se conectam ao Hub de Eventos. Gere chaves SAS separadas para cada aplicativo de back-end e forneça apenas as permissões necessárias (Envio, Recebimento ou Gerenciamento) a eles.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Usar tokens de recurso para se conectar ao Cosmos DB sempre que possível

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Document DB | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Um token de recurso é associado a um recurso de permissão do Azure Cosmos DB e captura a relação entre o usuário de um banco de dados e a permissão que ele tem para acessar um recurso de aplicativo específico do Azure Cosmos DB (por exemplo, uma coleção ou um documento). Use sempre um token de recurso para acessar o Azure Cosmos DB se o cliente não puder ser confiável para manipular a chave mestra ou chaves somente leitura, por exemplo, um aplicativo de usuário final, como cliente de dispositivo móvel ou de área de trabalho. Use a chave Mestra ou chaves somente leitura em aplicativos de back-end que podem armazenar essas chaves com segurança.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-azure-rbac"></a><a id="grained-rbac"></a>Habilitar o gerenciamento de acesso refinado à assinatura do Azure usando o RBAC do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de Confiança do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md)  |
| **Etapas** | O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC do Azure, você pode conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos.|

## <a name="restrict-clients-access-to-cluster-operations-using-service-fabric-rbac"></a><a id="cluster-rbac"></a>Restringir o acesso do cliente às operações de cluster usando o Service Fabric RBAC

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Service Fabric o controle de acesso baseado em função para clientes Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **Etapas** | <p>O Service Fabric dá suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.</p><p>Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.</p><p>As duas funções de clientes (administrador ou cliente) são especificadas no momento da criação do cluster, com o fornecimento de certificados separados para cada um.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Executar a modelagem de segurança e usar a segurança no nível do campo onde for necessário

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Execute a modelagem de segurança e use a segurança no nível do campo onde for necessário.|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Execute a modelagem de segurança das contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM.

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Portal do Dynamics CRM | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Execute a modelagem de segurança das contas do portal tendo em mente que o modelo de segurança do portal é diferente do restante do CRM.|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Conceder permissões refinadas em um intervalo de entidades no Armazenamento de Tabelas do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de armazenamento - Tabela |
| **Referências**              | [Como conceder acesso a objetos em sua conta de armazenamento do Azure usando SAS](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Etapas** | Em alguns cenários de negócios, o Armazenamento de Tabelas do Azure pode ser necessário para armazenar dados confidenciais que precisam ser acessados por várias pessoas diferentes. Por exemplo, dados confidenciais pertencentes a países/regiões diferentes. Nesses casos, as assinaturas SAS podem ser construídas especificando os intervalos de chaves de partição e de linha, de modo que um usuário pode acessar dados específicos para um país/região específico.| 

## <a name="enable-azure-role-based-access-control-azure-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Habilitar o controle de acesso baseado em função do Azure (RBAC do Azure) para a conta de armazenamento do Azure usando Azure Resource Manager

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como proteger sua conta de armazenamento com o controle de acesso baseado em função do Azure (RBAC do Azure)](../../storage/blobs/security-recommendations.md) |
| **Etapas** | <p>Ao criar uma nova conta de armazenamento, você seleciona o modelo de implantação Clássico ou o do Azure Resource Manager. O modelo Clássico de criação de recursos no Azure permite apenas acesso tudo ou nada à assinatura e, por sua vez, à conta de armazenamento.</p><p>Com o modelo do Azure Resource Manager, você coloca a conta de armazenamento em um grupo de recursos e controla o acesso ao plano de gerenciamento dessa conta de armazenamento específica usando o Azure Active Directory. Por exemplo, é possível permitir que usuários específicos acessem as chaves da conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar suas chaves.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementar detecção de modificação ou desbloqueio implícito

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os aplicativos devem proteger seus próprios dados de configuração e usuários no caso de o telefone ser modificado ou desbloqueado. A modificação ou o desbloqueio consistem em um acesso não autorizado, que os usuários normalmente não fazem em seus próprios telefones. Portanto, o aplicativo deve ter uma lógica de detecção implícita na inicialização do aplicativo para detectar se o telefone foi modificado.</p><p>A lógica de detecção pode ser simplesmente acessar os arquivos que normalmente apenas o usuário original acessaria, por exemplo:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Se o aplicativo puder acessar qualquer um desses arquivos, isso indica que o aplicativo está sendo executado como um usuário raiz.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Referência de classe fraca no WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Etapas** | <p>O sistema usa uma referência de classe fraca, permitindo que um invasor execute código não autorizado. O programa consulta uma classe definida pelo usuário que não é exclusivamente identificada. Quando .NET carrega essa classe de identificação fraca, o carregador do tipo CLR procura a classe nos seguintes locais e na ordem especificada:</p><ol><li>Se o assembly do tipo for conhecido, o carregador pesquisará os locais de redirecionamento do arquivo de configuração, GAC, o assembly atual que está usando as informações de configuração e o diretório base do aplicativo.</li><li>Se o assembly for desconhecido, o carregador pesquisará o assembly atual, mscorlib, e o local retornado pelo manipulador de eventos TypeResolve.</li><li>Essa ordem de pesquisa do CLR pode ser modificada com ganchos, como o mecanismo de encaminhamento de tipo e o evento AppDomain.TypeResolve.</li></ol><p>Se um invasor aproveitar a ordem de pesquisa do CLR para criar uma outra classe com o mesmo nome e colocá-la em um lugar que o CLR carregará primeiro, o CLR poderá acidentalmente executar o código fornecido pelo invasor.</p>|

### <a name="example"></a>Exemplo
O elemento `<behaviorExtensions/>` do arquivo de configuração WCF abaixo solicita que o WCF adicione uma classe de comportamento personalizada para uma extensão específica do WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Usar nomes totalmente qualificados (fortes) identifica exclusivamente um tipo e aumenta ainda mais a segurança do sistema. Use nomes de assembly totalmente qualificados ao registrar tipos nos arquivos machine.config e app.config.

### <a name="example"></a>Exemplo
O elemento `<behaviorExtensions/>` do arquivo de configuração do WCF abaixo solicita que o WCF adicione uma classe de comportamento personalizada para uma extensão específica do WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Controle de autorização implementado no WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Etapas** | <p>Esse serviço não usa um controle de autorização. Quando um cliente chama um determinado serviço do WCF, o WCF fornece vários esquemas de autorização que verificar se o chamador tem permissão para executar o método de serviço no servidor. Se os controles de autorização não estiverem habilitados para os serviços do WCF, um usuário autenticado pode ter seus privilégios elevados.</p>|

### <a name="example"></a>Exemplo
A configuração mostrada abaixo solicita que o WCF não verifique o nível de autorização do cliente ao executar o serviço:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Use um esquema de autorização de serviço para verificar se o chamador do método de serviço tem autorização para fazer isso. O WCF oferece dois modos e permite a definição de um esquema de autorização personalizado. O modo UseWindowsGroups utiliza funções e usuários do Windows, e o modo de UseAspNetRoles utiliza um provedor de função do ASP.NET, como o SQL Server, para autenticar.

### <a name="example"></a>Exemplo
A configuração mostrada abaixo solicita que o WCF se certifique de que o cliente faz parte do grupo Administradores antes de executar o serviço Adicionar:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Então, o serviço é declarado da seguinte forma:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementar mecanismos de autorização apropriados na ASP.NET Web API

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/D, Provedor de Identidade - ADFS, Provedor de Identidade - Azure AD |
| **Referências**              | [Autenticação e autorização na ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Etapas** | <p>As informações de função dos usuários do aplicativo podem ser derivadas do Azure AD ou de declarações do ADFS, se o aplicativo os considerar como provedores de identidade, ou o próprio aplicativo pode fornecê-las. Em qualquer um desses casos, a implementação de autorização personalizada deve validar as informações de função de usuário.</p><p>As informações de função dos usuários do aplicativo podem ser derivadas do Azure AD ou de declarações do ADFS, se o aplicativo os considerar como provedores de identidade, ou o próprio aplicativo pode fornecê-las. Em qualquer um desses casos, a implementação de autorização personalizada deve validar as informações de função de usuário.</p>

### <a name="example"></a>Exemplo
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Todos os controladores e métodos de ação que precisam ser protegidos devem ser decorados com o atributo acima.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Executar verificações de autorização no dispositivo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>O dispositivo deve autorizar o chamador para verificar se ele tem as permissões necessárias para executar a ação solicitada. Por exemplo, digamos que o dispositivo seja uma fechadura de porta inteligente que pode ser monitorada na nuvem e que permite, entre outras coisas, trancar uma porta remotamente.</p><p>A fechadura de porta inteligente só permitirá que uma porta seja destrancada quando alguém se aproximar fisicamente dela com um cartão. Nesse caso, a implementação do controle e do comando remoto deve ser feita de modo a não disponibilizar recursos para destrancar uma porta, visto que o gateway de nuvem não está autorizado a enviar um comando para destrancar portas.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Executar verificações de autorização no Gateway de Campo se ele oferecer suporte a várias ações que exigem diferentes níveis de permissão

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | O Gateway de Campo deve autorizar o chamador para verificar se ele tem as permissões necessárias para executar a ação solicitada. Por exemplo, deve haver permissões diferentes para uma API/interface utilizada por um usuário administrador para configurar um gateway de campo e para os dispositivos que se conectam a ele.|