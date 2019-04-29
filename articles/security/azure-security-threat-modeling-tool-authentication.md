---
title: Autenticação - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
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
ms.openlocfilehash: 56620dc1d3e315caa3e259715ed84a539b91356d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610871"
---
# <a name="security-frame-authentication--mitigations"></a>Quadro de segurança: Autenticação | Mitigações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicativo Web**    | <ul><li>[Considere o uso de um mecanismo de autenticação padrão para autenticação no aplicativo Web](#standard-authn-web-app)</li><li>[Os aplicativos devem lidar com cenários de autenticação com falha com segurança](#handle-failed-authn)</li><li>[Habilitar upgrade ou autenticação adaptável](#step-up-adaptive-authn)</li><li>[Verifique se as interfaces administrativas estão adequadamente bloqueadas](#admin-interface-lockdown)</li><li>[Implemente funcionalidades de senha esquecida com segurança](#forgot-pword-fxn)</li><li>[Verifique se as políticas de conta e senha são implementadas](#pword-account-policy)</li><li>[Implemente controles para impedir a enumeração de nome de usuário](#controls-username-enum)</li></ul> |
| **Banco de dados** | <ul><li>[Quando possível, use a Autenticação do Windows para se conectar ao SQL Server](#win-authn-sql)</li><li>[Quando possível, use a Autenticação do Azure Active Directory para se conectar ao banco de dados SQL](#aad-authn-sql)</li><li>[Quando o modo de autenticação do SQL for usado, verifique se as políticas de conta e senha são impostas no SQL server](#authn-account-pword)</li><li>[Não use a Autenticação do SQL em bancos de dados independentes](#autn-contained-db)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Use credenciais de autenticação por dispositivo usando tokens SaS](#authn-sas-tokens)</li></ul> |
| **Limite de Confiança do Azure** | <ul><li>[Habilitar Autenticação Multifator do Azure para administradores do Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de confiança do Service Fabric** | <ul><li>[Restrinja o acesso anônimo ao Cluster de Service Fabric](#anon-access-cluster)</li><li>[Verifique se o certificado de cliente para o nó de Service Fabric serviço é diferente do certificado de nó para nó](#fabric-cn-nn)</li><li>[Use o AAD para autenticar clientes para clusters do service fabric](#aad-client-fabric)</li><li>[Verifique se os certificados de service fabric são obtidos de uma CA (autoridade de certificação) aprovada](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Use cenários de autenticação padrão com suporte no Servidor de Identidade](#standard-authn-id)</li><li>[Substitua o cache de token de identidade de servidor padrão por uma alternativa escalonável](#override-token)</li></ul> |
| **Limite de confiança de computador** | <ul><li>[Verifique se os binários do aplicativo implantado são assinados digitalmente](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Habilite a autenticação ao se conectar às filas MSMQ do WCF](#msmq-queues)</li><li>[WCF - não defina clientCredentialType de mensagem como none](#message-none)</li><li>[WCF - não defina clientCredentialType de transporte como none](#transport-none)</li></ul> |
| **API da Web** | <ul><li>[Verifique se técnicas de autenticação padrão são usadas para proteger as APIs Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Use cenários de autenticação padrão com suporte no Azure Active Directory](#authn-aad)</li><li>[Substitua o cache de token ADAL padrão por uma alternativa escalonável](#adal-scalable)</li><li>[Verifique se TokenReplayCache é usado para evitar a repetição de tokens de autenticação ADAL](#tokenreplaycache-adal)</li><li>[Use bibliotecas ADAL para gerenciar solicitações de token de clientes OAuth2 ao AAD (ou AD local)](#adal-oauth2)</li></ul> |
| **Gateway de Campo de IoT** | <ul><li>[Autenticar dispositivos que se conectam ao Gateway de Campo](#authn-devices-field)</li></ul> |
| **Gateway de Nuvem IoT** | <ul><li>[Verifique se os dispositivos conectados ao gateway de nuvem são autenticados](#authn-devices-cloud)</li><li>[Usar credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Verifique se apenas os contêineres necessários e os blobs recebem acesso de leitura anônimo](#req-containers-anon)</li><li>[Conceda acesso limitado a objetos no armazenamento do Azure usando SAS ou SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Considere o uso de um mecanismo de autenticação padrão para se autenticar no Aplicativo Web

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A autenticação é o processo em que uma entidade comprova sua identidade, normalmente por meio de credenciais, como um nome de usuário e uma senha. Há vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles são listados abaixo:</p><ul><li>Certificados do cliente</li><li>Baseado no Windows</li><li>Baseado em formulários</li><li>Federação - ADFS</li><li>Federação – Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>Considere usar um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a id="handle-failed-authn"></a>Os aplicativos devem lidar com segurança com os cenários de autenticação com falha

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Aplicativos que autenticam usuários explicitamente devem controlar os cenários de autenticação com falha com segurança. O mecanismo de autenticação deve:</p><ul><li>Negar acesso a recursos privilegiados quando a autenticação falhar</li><li>Exibir uma mensagem de erro genérica após falha na autenticação e acesso negado</li></ul><p>Teste de:</p><ul><li>Proteção de recursos privilegiados após logons com falha</li><li>Será exibida uma mensagem de erro genérica em evento(s) de autenticação com falha e acesso negado</li><li>As contas são desabilitadas após um número excessivo de tentativas com falha</li><ul>|

## <a id="step-up-adaptive-authn"></a>Habilitar upgrade ou autenticação adaptável

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Verifique se o aplicativo tem autorização adicional (como step-up ou autenticação adaptável, por meio de autenticação multifator como o envio de OTP no SMS, email etc. ou solicitação de reautenticação) para que o usuário receba um desafio antes de obter acesso a informações confidenciais. Essa regra também se aplica para fazer alterações importantes em uma conta ou uma ação</p><p>Isso também significa que a adaptação de autenticação deve ser implementada de forma que o aplicativo imponha corretamente a autorização contextual para não permitir uma manipulação não autorizada por meio de violação de parâmetros, como no exemplo</p>|

## <a id="admin-interface-lockdown"></a>Verifique se as interfaces administrativas estão adequadamente bloqueadas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | A primeira solução é conceder acesso apenas de determinado intervalo IP de origem para a interface administrativa. Se essa solução não for possível, sempre será recomendável impor uma autenticação step-up ou adaptável para fazer logon na interface administrativa |

## <a id="forgot-pword-fxn"></a>Implemente funcionalidades de senha esquecida com segurança

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A primeira coisa é verificar se você esqueceu a senha, e outros caminhos de recuperação enviam um link com um token de ativação de tempo limitado, em vez da própria senha. A autenticação adicional com base em tokens de software (por exemplo, token de SMS, aplicativos móveis nativos etc.) também pode ser necessária antes que o link seja enviado. Em segundo lugar, você não deverá bloquear a conta de usuários enquanto o processo de obtenção de uma nova senha estiver em andamento.</p><p>Isso poderá levar a um ataque de Negação de serviço sempre que um invasor decidir bloquear intencionalmente os usuários com um ataque automatizado. Em terceiro lugar, sempre que a nova solicitação de senha for definida em andamento, a mensagem que será exibida deverá ser generalizada para evitar a enumeração de nome de usuário. Em quarto lugar, sempre proíba o uso de senhas antigas e implemente uma política de senha forte.</p> |

## <a id="pword-account-policy"></a>Verifique se as políticas de conta e senha são implementadas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A política de conta e senha em conformidade com a política organizacional e as práticas recomendadas deve ser implementadas.</p><p>Para defender-se contra ataque de detecção baseada em dicionário e força bruta: É necessário implementar uma diretiva de senha forte para garantir que os usuários criem uma senha complexa (por exemplo, tamanho mínimo de 12 caracteres, caracteres alfanuméricos e especiais).</p><p>Políticas de bloqueio de conta podem ser implementadas da seguinte maneira:</p><ul><li>**Bloqueio flexível:** Essa pode ser uma boa opção para proteger os usuários contra ataques de força bruta. Por exemplo, sempre que o usuário insere uma senha incorreta três vezes, o aplicativo pode bloquear a conta por um minuto para tornar mais lento o processo de obtenção da senha por força bruta, tornando-o menos rentável para o invasor. Se implementar contramedidas de bloqueio rígidas neste exemplo, você obterá um "Dos" bloqueando permanentemente as contas. Como alternativa, o aplicativo pode gerar uma OTP (senha única) e enviá-la fora de banda (por email, sms etc.) ao usuário. Outra abordagem seria implementar CAPTCHA depois que um número limite de tentativas for atingido.</li><li>**Bloqueio rígido:** Esse tipo de bloqueio deve ser aplicado sempre que você detectar um usuário atacando seu aplicativo e contra-atacá-lo, bloqueando permanentemente sua conta até que uma equipe de resposta tenha tempo de fazer as análises. Após esse processo, que você pode optar por dar a conta de volta ao usuário ou tomar outras medidas legais contra ele. Esse tipo de abordagem impede que o invasor penetre ainda mais em seu aplicativo e infraestrutura.</li></ul><p>Para se proteger contra ataques em contas previsíveis e padrão, verifique se todas as chaves e senhas são substituíveis e são geradas ou substituídas após a instalação.</p><p>Se o aplicativo precisar gerar senhas automaticamente, verifique se as senhas geradas são aleatórias e têm alta entropia.</p>|

## <a id="controls-username-enum"></a>Implemente controles para impedir a enumeração de nome de usuário

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Todas as mensagens de erro devem ser generalizadas para evitar a enumeração de nome de usuário. Além disso, às vezes você não pode evitar o vazamento de recursos, como uma página de registro de informações. Aqui, você precisa usar métodos de limitação de taxa, como CAPTCHA, para evitar um ataque automatizado por um invasor. |

## <a id="win-authn-sql"></a>Quando possível, use a Autenticação do Windows para se conectar ao SQL Server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL - Todas |
| **Referências**              | [SQL Server - escolher um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Etapas** | A Autenticação do Windows usa o protocolo de segurança Kerberos, fornece imposição de política de senha com relação à validação de complexidade para senhas fortes e dá suporte ao bloqueio de conta e à expiração de senha.|

## <a id="aad-authn-sql"></a>Quando possível, use a Autenticação do Azure Active Directory para se conectar ao banco de dados SQL

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Etapas** | **Versão mínima:** É necessário ter o Banco de Dados SQL do Azure V12 para permitir que o Banco de Dados SQL do Azure use a Autenticação AAD no Microsoft Directory |

## <a id="authn-account-pword"></a>Quando o modo de autenticação do SQL for usado, verifique se as políticas de conta e senha são impostas no SQL server

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Política de senha do SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Etapas** | Ao usar a Autenticação do SQL Server, são criados logons no SQL Server que não se baseiam em contas de usuário do Windows. O nome de usuário e a senha são criados usando o SQL Server e são armazenados no SQL Server. O SQL Server pode usar mecanismos de política de senha do Windows. Ele pode aplicar a mesma complexidade e as políticas de expiração usadas no Windows para senhas usadas no SQL Server. |

## <a id="autn-contained-db"></a>Não use a Autenticação do SQL em bancos de dados independentes

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem, SQL Azure |
| **Atributos**              | Versão do SQL - MSSQL2012, Versão do SQL - V12 |
| **Referências**              | [Práticas recomendadas de segurança com bancos de dados contidos](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Etapas** | A ausência de uma política de senha imposta pode aumentar a probabilidade de que uma credencial fraca seja estabelecida em um banco de dados independente. Aproveite a Autenticação do Windows. |

## <a id="authn-sas-tokens"></a>Use credenciais de autenticação por dispositivo usando tokens SaS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança dos Hubs de Eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Etapas** | <p>O modelo de segurança dos Hubs de Eventos se baseia em uma combinação de tokens SAS (Assinatura de Acesso Compartilhado) e de editores de eventos. O nome do publicador representa o DeviceID que recebe o token. Isso ajudaria a associar os tokens gerados aos respectivos dispositivos.</p><p>Todas as mensagens são marcadas com o originador no lado do serviço, o que permite a detecção de tentativas de falsificação de origem na carga. Na autenticação de dispositivos, gere um token SaS por dispositivo com escopo para um publicador exclusivo.</p>|

## <a id="multi-factor-azure-admin"></a>Habilitar Autenticação Multifator do Azure para administradores do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de Confiança do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [O que é a Autenticação Multifator do Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Etapas** | <p>A autenticação multifator (MFA) é um método de autenticação que exige mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir:</p><ul><li>Algo que você sabe (normalmente, uma senha)</li><li>Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)</li><li>Algo seu (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Restrinja o acesso anônimo ao Cluster de Service Fabric

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure  |
| **Referências**              | [Cenários de segurança do cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Etapas** | <p>Os clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução.</p><p>Ao criar um cluster de service fabric, verifique se o modo de segurança está definido como "seguro" e configure o certificado de servidor X.509 necessário. Criar um cluster "inseguro" permitirá que qualquer usuário anônimo se conecte a ele se ele expuser pontos de extremidade de gerenciamento para a Internet pública.</p>|

## <a id="fabric-cn-nn"></a>Verifique se o certificado de cliente para o nó de Service Fabric serviço é diferente do certificado de nó para nó

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure, ambiente - autônomo |
| **Referências**              | [Segurança de certificado de cliente para nó do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Conectar-se a um cluster seguro usando um certificado de cliente](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Etapas** | <p>A segurança de certificado de cliente para nó é configurada durante a criação do cluster por meio do portal do Azure, dos modelos do Resource Manager ou de um modelo JSON autônomo, especificando um certificado de cliente do administrador e/ou um certificado de cliente do usuário.</p><p>Os certificados de cliente administrador e certificados de cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários que você especifica para a Segurança de nó para nó.</p>|

## <a id="aad-client-fabric"></a>Use o AAD para autenticar clientes para clusters do service fabric

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Cenários de segurança - recomendações de segurança de cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Etapas** | Clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o AAD (Azure Active Directory), além de certificados de cliente. Para clusters do Azure, é recomendável usar a segurança do AAD para autenticar clientes e certificados para a segurança de nó para nó.|

## <a id="fabric-cert-ca"></a>Verifique se os certificados de service fabric são obtidos de uma CA (autoridade de certificação) aprovada

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Certificados X.509 e Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Etapas** | <p>O Service Fabric usa certificados de servidor X.509 para autenticar clientes e nós.</p><p>Alguns pontos importantes a considerar ao usar certificados em service fabrics:</p><ul><li>Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados por meio de um serviço de certificado do Windows Server configurado corretamente ou obtidos por meio de uma AC (Autoridade de Certificação) aprovada. A CA pode ser uma CA externa aprovada ou um PKI (Public Key Infrastructure) gerenciado adequadamente</li><li>Nunca use nenhum certificado temporário ou de teste em produção criado com ferramentas como MakeCert.exe</li><li>Você pode usar um certificado autoassinado, mas deve fazer isso somente para clusters de teste e não em produção</li></ul>|

## <a id="standard-authn-id"></a>Use cenários de autenticação padrão com suporte no Servidor de Identidade

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - visão geral](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Etapas** | <p>Abaixo estão as interações típicas com suporte no Servidor de Identidade:</p><ul><li>Os navegadores se comunicam com aplicativos Web</li><li>Os aplicativos Web se comunicam com APIs Web (às vezes, por conta própria e, às vezes, em nome do usuário)</li><li>Aplicativos baseados em navegador se comunicam com APIs Web</li><li>Aplicativos nativos se comunicam com APIs Web</li><li>Aplicativos baseados em servidor se comunicam com APIs Web</li><li>As APIs Web se comunicam com as APIs Web (às vezes, por conta própria e, às vezes, em nome do usuário)</li></ul>|

## <a id="override-token"></a>Substitua o cache de token de identidade de servidor padrão por uma alternativa escalonável

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Implantação de Servidor de Identidade - cache](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Etapas** | <p>IdentityServer tem um cache na memória interno simples. Embora seja adequado para aplicativos nativos de pequena escala, ele não é dimensionado para aplicativos de camada intermediária e back-end pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários usuários simultaneamente. Salvar todos os tokens de acesso no mesmo repositório cria problemas de isolamento e apresenta desafios ao operar em grande escala: vários usuários, cada um com o mesmo número de tokens que os recursos que o aplicativo acessa em seu nome, podem significar numerosas operações de pesquisa muito caras</li><li>Esses aplicativos normalmente são implantados em topologias distribuídas, em que vários nós devem ter acesso ao mesmo cache</li><li>Os tokens em cache devem sobreviver a desativações e reciclagem de processo</li><li>Por todos os motivos acima, durante a implementação de aplicativos Web, é recomendável substituir o cache de token padrão do Identity Server por uma alternativa escalonável como o Cache do Azure para Redis</li></ul>|

## <a id="binaries-signed"></a>Verifique se os binários do aplicativo implantado são assinados digitalmente

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Verifique se os binários do aplicativo implantado são assinados digitalmente para que seja possível verificar a integridade dos binários|

## <a id="msmq-queues"></a>Habilite a autenticação ao se conectar às filas MSMQ do WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Etapas** | O programa não habilita a autenticação ao conectar-se às filas do MSMQ. Um invasor anônimo pode enviar mensagens à fila para processamento. Se a autenticação não for usada para se conectar a uma fila do MSMQ usada para enviar uma mensagem a outro programa, um invasor poderá enviar uma mensagem anônima mal-intencionada.|

### <a name="example"></a>Exemplo
O elemento `<netMsmqBinding/>` do arquivo de configuração WCF abaixo instrui o WCF a desabilitar a autenticação ao se conectar a uma fila do MSMQ para entrega de mensagens.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configure o MSMQ para exigir sempre autenticação de Domínio do Windows ou de Certificado para todas as mensagens de entrada ou saída.

### <a name="example"></a>Exemplo
O elemento `<netMsmqBinding/>` do arquivo de configuração WCF abaixo instrui o WCF a habilitar a autenticação de certificado ao se conectar a uma fila do MSMQ. O cliente é autenticado usando certificados X.509. O certificado do cliente deve estar presente no repositório de certificados do servidor.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF - não defina clientCredentialType de mensagem como none

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | Tipo de Credencial de Cliente - Nenhuma |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortalecer](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Etapas** | A ausência de autenticação significa que todos podem acessar o serviço. Um serviço que não autentica seus clientes permite acesso a todos os usuários. Configure o aplicativo para autenticação em relação às credenciais do cliente. Isso pode ser feito definindo a mensagem clientCredentialType para Windows ou Certificado. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF - não defina clientCredentialType de transporte como none

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Tipo de Credencial de Cliente - Nenhuma |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortalecer](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Etapas** | A ausência de autenticação significa que todos podem acessar o serviço. Um serviço que não autentica seus clientes permite que todos os usuários acessem sua funcionalidade. Configure o aplicativo para autenticação em relação às credenciais do cliente. Isso pode ser feito definindo o transporte clientCredentialType para Windows ou Certificado. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Verifique se técnicas de autenticação padrão são usadas para proteger as APIs Web

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação e Autorização na ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [Serviços de Autenticação Externa com a API Web do ASP.NET (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Etapas** | <p>A autenticação é o processo em que uma entidade comprova sua identidade, normalmente por meio de credenciais, como um nome de usuário e uma senha. Há vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles são listados abaixo:</p><ul><li>Certificados do cliente</li><li>Baseado no Windows</li><li>Baseado em formulários</li><li>Federação - ADFS</li><li>Federação – Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>Os links na seção de referências fornecem detalhes de baixo nível sobre como cada um dos esquemas de autenticação pode ser implementado para proteger uma API Web.</p>|

## <a id="authn-aad"></a>Use cenários de autenticação padrão com suporte no Azure Active Directory

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | AD do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Cenários de Autenticação do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Exemplos de Código do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Guia do Desenvolvedor do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Etapas** | <p>O Azure AD (Azure Active Directory) simplifica a autenticação para os desenvolvedores fornecendo identidade como um serviço, com suporte para protocolos padrão do setor, como OAuth 2.0 e OpenID Connect. Abaixo estão os cinco cenários de aplicativo principais suportados pelo Azure AD:</p><ul><li>Navegador da Web para aplicativo Web: Um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD</li><li>SPA (Aplicativo de Página Única): Um usuário precisa entrar em um aplicativo de página única protegido pelo Azure AD</li><li>Aplicativo nativo para API Web: Um aplicativo nativo que é executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD</li><li>Aplicativo Web para API Web: Um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD</li><li>Daemon ou aplicativo para servidores para API Web: Um aplicativo daemon ou aplicativo para servidores sem interface do usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD</li></ul><p>Confira os links na seção de referências para obter detalhes sobre a implementação de nível baixo</p>|

## <a id="adal-scalable"></a>Substitua o cache de token ADAL padrão por uma alternativa escalonável

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | AD do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação Moderna com o Azure Active Directory para Aplicativos Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [Usando o Redis como cache de token ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Etapas** | <p>O cache padrão que usa a ADAL (Biblioteca de Autenticação do Active Directory) é um cache na memória que se baseia em um armazenamento estático, disponível em todo o processo. Embora isso funcione para aplicativos nativos, ele não é dimensionado para aplicativos de camada intermediária e back-end pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários usuários simultaneamente. Salvar todos os tokens de acesso no mesmo repositório cria problemas de isolamento e apresenta desafios ao operar em grande escala: vários usuários, cada um com o mesmo número de tokens que os recursos que o aplicativo acessa em seu nome, podem significar numerosas operações de pesquisa muito caras</li><li>Esses aplicativos normalmente são implantados em topologias distribuídas, em que vários nós devem ter acesso ao mesmo cache</li><li>Os tokens em cache devem sobreviver a desativações e reciclagem de processo</li></ul><p>Por todos os motivos acima, durante a implementação de aplicativos Web, é recomendável substituir o cache de token de ADAL padrão por uma alternativa escalonável como o Cache do Azure para Redis.</p>|

## <a id="tokenreplaycache-adal"></a>Verifique se TokenReplayCache é usado para evitar a repetição de tokens de autenticação ADAL

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | AD do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação Moderna com o Azure Active Directory para Aplicativos Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Etapas** | <p>A propriedade TokenReplayCache permite aos desenvolvedores definir um cache de reprodução de token, um repositório que pode ser usado para salvar os tokens com a finalidade de verificar se nenhum token pode ser usado mais de uma vez.</p><p>Essa é uma medida contra um ataque comum, o ataque de reprodução de token, cujo nome é bastante apropriado: um invasor que intercepta o token enviado na entrada pode tentar enviá-la novamente ao aplicativo ("repeti-lo") para estabelecer uma nova sessão. Por exemplo, no fluxo de concessão de código do OIDC, após a autenticação bem-sucedida do usuário, uma solicitação para "/signin-oidc" do ponto de extremidade da terceira parte confiável é feita com os parâmetros "id_token", "code" e "state".</p><p>A terceira parte confiável valida a solicitação e estabelece uma nova sessão. Se um adversário capturar essa solicitação e a repetir, poderá estabelecer uma sessão bem-sucedida e falsificar o usuário. A presença de nonce em OpenID Connect pode limitar, mas não elimina totalmente as circunstâncias em que o ataque pode ser realizado com êxito. Para proteger os aplicativos, os desenvolvedores podem fornecer uma implementação de ITokenReplayCache e atribuir uma instância de TokenReplayCache.</p>|

### <a name="example"></a>Exemplo
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Exemplo
Aqui está uma implementação de exemplo da interface ITokenReplayCache. (Personalize e implemente sua estrutura de cache específica do projeto)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
O cache implementado precisa ser referenciado em opções de OIDC por meio da propriedade "TokenValidationParameters" da maneira a seguir.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Observe que, para testar a eficácia dessa configuração, faça logon no aplicativo local protegido por OIDC e capture a solicitação para o ponto de extremidade `"/signin-oidc"` no fiddler. Quando a proteção não estiver em vigor, repetir essa solicitação no fiddler definirá um novo cookie de sessão. Quando a solicitação for repetida depois que a proteção de TokenReplayCache for adicionada, o aplicativo gerará uma exceção da seguinte maneira:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Use bibliotecas ADAL para gerenciar solicitações de token de clientes OAuth2 ao AAD (ou AD local)

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | AD do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Etapas** | <p>A Biblioteca de autenticação do AD do Azure (ADAL) permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API.</p><p>A ADAL tem muitos recursos que tornam a autenticação mais fácil para os desenvolvedores, como o suporte assíncrono, um cache de token configurável que armazena os tokens de acesso e de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e muito mais.</p><p>Controlando a maior parte da complexidade, a ADAL pode ajudar o desenvolvedor a se concentrar na lógica de negócios em seus aplicativos e a proteger facilmente os recursos sem ser um especialista em segurança. Bibliotecas separadas estão disponíveis para .NET, JavaScript (cliente e Node.js), iOS, Android e Java.</p>|

## <a id="authn-devices-field"></a>Autentique dispositivos que se conectam ao Gateway de Campo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Verifique se cada dispositivo é autenticado pelo Gateway de Campo antes de aceitar dados deles e antes de facilitar as comunicações upstream com o Gateway de Nuvem. Além disso, verifique se os dispositivos se conectam com uma credencial por dispositivo, para que dispositivos individuais possam ser identificados exclusivamente.|

## <a id="authn-devices-cloud"></a>Verifique se os dispositivos conectados ao gateway de Nuvem são autenticados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, C#, Node.JS,  |
| **Atributos**              | N/A, Opção de gateway - Hub IoT do Azure |
| **Referências**              | N/d, [hub IoT do Azure com o .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Introdução ao hub IoT e nó JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [protegendo a IoT com SAS e certificados](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repositório Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Etapas** | <ul><li>**Genérico:** Autentique o dispositivo usando TLS (Transport Layer Security) ou IPSec. A infraestrutura deve dar suporte ao uso da PSK (chave pré-compartilhada) nos dispositivos que não conseguem lidar com a criptografia assimétrica completa. Aproveite o Azure AD e o OAuth.</li><li>**C#:** Ao criar uma instância do DeviceClient, por padrão, o método Create cria uma instância do DeviceClient que usa o protocolo AMQP para comunicar-se com o Hub IoT. Para usar o protocolo HTTPS, use a substituição do método Create que permite especificar o protocolo. Se usar o protocolo HTTPS, você também deverá adicionar o pacote do NuGet `Microsoft.AspNet.WebApi.Client` ao projeto para incluir o namespace `System.Net.Http.Formatting`.</li></ul>|

### <a name="example"></a>Exemplo
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Exemplo
**Node.JS: Autenticação**
#### <a name="symmetric-key"></a>Chave simétrica
* Criar um hub IoT no azure
* Criar uma entrada no registro de identidade de dispositivo
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Criar um dispositivo simulado
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>Token SAS
* É gerado internamente ao usar a chave simétrica, mas também pode gerá-la e usá-la explicitamente
* Defina um protocolo: `var Http = require('azure-iot-device-http').Http;`
* Crie um token sas:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Conecte-se usando um token sas: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certificados
* Gerar um certificado X509 autoassinado usando qualquer ferramenta, como OpenSSL, para gerar arquivos .cert e .key para armazenar o certificado e a chave, respectivamente
* Provisione um dispositivo que aceite a conexão segura usando certificados.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Conectar um dispositivo usando um certificado
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Usar credenciais de autenticação por dispositivo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT  | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - Hub IoT do Azure |
| **Referências**              | [Tokens de segurança do Hub IoT do Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Etapas** | Use credenciais de autenticação por dispositivo usando tokens SaS com base na chave do dispositivo ou no certificado de cliente, em vez de políticas de acesso compartilhado de nível de Hub IoT. Isso impede a reutilização de tokens de autenticação de um gateway de campo ou dispositivo por outro |

## <a id="req-containers-anon"></a>Verifique se apenas os contêineres necessários e os blobs recebem acesso de leitura anônimo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Gerenciar o acesso de leitura anônimo aos contêineres e blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [Assinaturas de Acesso Compartilhado, Parte 1: Noções básicas sobre o modelo de SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Etapas** | <p>Por padrão, um contêiner e todos os blobs dentro dele podem ser acessados somente pelo proprietário da conta de armazenamento. Para dar aos usuários anônimos permissões de leitura a um contêiner e seus blobs, é possível definir as permissões de contêiner para permitir acesso público. Usuários anônimos podem ler blobs dentro de um contêiner publicamente acessível sem autenticar a solicitação.</p><p>Os contêineres fornecem as seguintes opções para gerenciar o acesso do contêiner:</p><ul><li>Acesso de leitura público completo: Dados de blob e contêiner podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.</li><li>Acesso de leitura público somente para blobs: Os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima</li><li>Nenhum acesso de leitura público: Dados de blob e contêiner podem ser lidos apenas pelo proprietário da conta</li></ul><p>O acesso anônimo é ideal para cenários em que determinados blobs sempre devem estar disponíveis para acesso de leitura anônimo. Para um controle mais refinado, é possível criar uma assinatura de acesso compartilhado, que permite delegar o acesso restrito usando diferentes permissões e em um intervalo de tempo especificado. Verifique se contêineres e blobs, que podem conter dados confidenciais, não recebem acesso anônimo acidentalmente</p>|

## <a id="limited-access-sas"></a>Conceda acesso limitado a objetos no armazenamento do Azure usando SAS ou SAP

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D |
| **Referências**              | [Assinatura de Acesso Compartilhado, Parte 1: Noções básicas sobre o modelo de SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [Assinatura de Acesso Compartilhado, Parte 2: Criar e usar uma SAS com armazenamento de Blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Como delegar acesso a objetos em sua conta usando Assinaturas de Acesso Compartilhado e Políticas de Acesso Armazenadas](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Etapas** | <p>O uso de SAS (assinatura de acesso compartilhado) é uma maneira eficiente de conceder acesso limitado a objetos na conta de armazenamento a outros clientes, sem precisar expor a chave de acesso da conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado.</p><p>Você pode usar uma SAS quando desejar fornecer acesso aos recursos de sua conta de armazenamento a um cliente não confiável com a chave de conta. As chaves de conta de armazenamento incluem uma chave primária e uma chave secundária, que concedem acesso administrativo à sua conta e a todos os recursos que ela contém. A exposição de qualquer uma de suas chaves de conta torna sua conta vulnerável a um possível uso mal-intencionado ou negligente. As assinaturas de acesso compartilhado são uma alternativa segura que permite que outros clientes leiam, gravem e excluam dados da sua conta de armazenamento de acordo com as permissões que você tiver concedido a eles e sem que eles precisem da chave de conta.</p><p>Se você tiver um conjunto lógico de parâmetros que são sempre semelhantes, o uso de SAP (política de acesso armazenada) será uma ideia melhor. Como usar uma SAS derivada de uma Política de Acesso Armazenado dá a possibilidade de revogar essa SAS imediatamente, a prática recomendada é, sempre que possível, usar Políticas de Acesso Armazenado.</p>|
