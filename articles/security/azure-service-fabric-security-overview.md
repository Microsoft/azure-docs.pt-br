---
title: "Visão geral de segurança do Azure Service Fabric | Microsoft Docs"
description: "Este artigo fornece uma visão geral da segurança do Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 908bdaf002e42035567974b204f5b39e73e82024
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-security-overview"></a>Visão geral de segurança do Azure Service Fabric
O [Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implantação e o gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis.

Este artigo de visão geral da Segurança do Azure Service Fabric tem como foco as seguintes áreas:

-   Proteção do cluster
-   Entender o monitoramento e o diagnóstico
-   Criar ambientes mais seguros usando certificados
-   Usar RBAC (Controle de Acesso Baseado em Função)
-   Proteger clusters usando a segurança do Windows
-   Configurar a segurança de aplicativo no Service Fabric
-   Proteger a comunicação para serviços no Azure Service Fabric 

## <a name="secure-your-cluster"></a>Proteger seu cluster
O Azure Service Fabric organiza serviços em um cluster de computadores. Os clusters devem ser protegidos para evitar que usuários não autorizados se conectem a eles, especialmente quando eles tiverem cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele (se ele expuser pontos de extremidade de gerenciamento na Internet pública).

Esta seção fornece uma visão geral dos cenários de segurança para clusters que estão em execução no modo autônomo ou no Azure. Ela também descreve as diversas tecnologias que são usadas para implementar esses cenários. Os cenários de segurança do cluster são:

-   Segurança de nó para nó
-   Segurança de cliente para nó

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó protege a comunicação entre as VMs ou os computadores em um cluster. Com a segurança de nó para nó, somente os computadores autorizados a ingressar no cluster podem hospedar aplicativos e serviços no cluster.

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [segurança de certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

**Entender a segurança de certificado de nó para nó**

O Service Fabric usa os certificados do servidor X.509 que você especifica ao criar um cluster. Para uma visão geral rápida do que são esses certificados e como você pode adquirir ou criá-los, consulte [Trabalhando com certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Configure a segurança de certificado durante a criação do cluster por meio do portal do Azure, dos modelos do Azure Resource Manager ou de um modelo JSON autônomo. É possível especificar um certificado primário e um certificado secundário opcional que é usado para substituições de certificado. Os certificados primários e secundários que você especificar devem ser diferentes dos certificados de cliente administrador e certificados cliente somente leitura especificados para a [segurança de cliente para nó](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Segurança de cliente para nó
Configure a segurança de cliente para nó usando identidades de cliente. Para estabelecer a confiança entre um cliente e um cluster, você deve configurar o cluster para saber em quais identidades de cliente ele pode confiar. Isso pode ser feito de duas maneiras diferentes:

-   Especificar os usuários do grupo de domínio que podem se conectar. 
-   Especificar os usuários do nó de domínio que podem se conectar.

O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric:

-   Administrador
-   Usuário

Usando o controle de acesso, os administradores de cluster podem limitar o acesso a determinados tipos de operações de cluster. Isso torna o cluster mais seguro.

 Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

**Entender a segurança de certificado de cliente para nó**

Configure a segurança de certificado de cliente para nó durante a criação do cluster por meio do portal do Azure, dos modelos do Resource Manager ou de um modelo JSON autônomo. Você precisa especificar um certificado de cliente administrador e/ou um certificado de cliente do usuário. 

Os certificados de cliente administrador e certificados de cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários que você especifica para a segurança de nó para nó.

Clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento. Clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura têm somente acesso de leitura aos recursos de gerenciamento. Em outras palavras, esses certificados são usados para RBAC.

Para saber como configurar a segurança de certificado em um cluster, consulte [Configurar um cluster usando um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Entender a segurança do Azure Active Directory de cliente para nó no Azure**

Clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o Azure AD (Azure Active Directory). Para obter informações sobre como criar os artefatos necessários do Azure Active Directory, como preenchê-los durante a criação do cluster e como se conectar a esses clusters, consulte [Configurar um cluster usando um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Há aplicativos com uma interface do usuário de entrada baseada na Web e os que têm experiência de cliente nativa.

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o Service Fabric Explorer e o Visual Studio baseados na Web. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para clusters do Azure, é recomendável usar a segurança do Azure AD para autenticar clientes e certificados para a segurança de nó para nó.

Para clusters do Windows Server autônomos com Windows Server 2012 R2 e Active Directory, é recomendável usar a segurança do Windows com contas gerenciadas de grupo.  Caso contrário, use a segurança do Windows com contas do Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Entender o monitoramento e o diagnóstico no Azure Service Fabric
[O monitoramento e o diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são essenciais para o desenvolvimento, os testes e a implantação de aplicativos e serviços em qualquer ambiente. As soluções do Service Fabric funcionam melhor quando você implementa o monitoramento e o diagnóstico para garantir que aplicativos e serviços funcionem conforme o esperado em um ambiente de desenvolvimento local ou na produção.

Do ponto de vista da segurança, as principais metas do monitoramento e diagnóstico são:

-   Detectar e diagnosticar problemas de hardware e infraestrutura que podem ser causados por um evento relacionado à segurança.
-   Detectar problemas de software e aplicativo que podem ser IoC (indicadores de comprometimento).
-   Entender o consumo de recursos para ajudar uma negação acidental de serviço.

O fluxo de trabalho geral do monitoramento e diagnóstico consiste em três etapas:

-   **Geração de eventos:** a geração de evento inclui eventos (logs, rastreamentos, eventos personalizados) nos níveis de infraestrutura (cluster) e serviço/aplicativo. Leia mais sobre [eventos de nível de infraestrutura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) e [eventos de nível de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para entender o que é fornecido e como adicionar mais instrumentação.

-   **Agregação de eventos:** os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos. Normalmente, recomendamos usar o [Diagnóstico do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (semelhante à coleta de log com base em agente) ou [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (coleta de log durante o processo).

-   **Análise:** os eventos precisam ser visualizados e estar acessíveis em algum formato, para permitir a análise e a exibição. Há várias plataformas para a análise e a visualização de dados de monitoramento e diagnóstico. Os dois que recomendamos são [Operations Management Suite](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) devido a sua melhor integração com o Service Fabric.

Você também pode usar o [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) para monitorar muitos dos recursos do Azure onde um cluster de Service Fabric é criado.

Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços e informar a integridade de qualquer coisa na hierarquia de modelo de integridade. Usar um watchdog pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. 

Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de watchdog de exemplo na [Amostra de watchdog do Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Entender como proteger a comunicação usando certificados
Os certificados ajudam a proteger a comunicação entre vários nós do cluster autônomo do Windows. Ao usar certificados X.509, você também pode autenticar os clientes que estão se conectando a este cluster. Isso garante que somente usuários autorizados podem acessar o cluster. É recomendável que você habilite um certificado no cluster ao criá-lo.

### <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente as mensagens.

A tabela a seguir lista os certificados que serão necessários na configuração do seu cluster:

|Configuração das informações de certificado |Descrição|
|-------------------------------|-----------|
|ClusterCertificate|    Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um primário e um secundário para atualização.|
|ServerCertificate| Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Você pode usar dois certificados do servidor diferentes, um primário e um secundário para atualização.|
|ClientCertificateThumbprints|  Esse é um conjunto de certificados para instalar nos clientes autenticados.|
|ClientCertificateCommonNames|  Esse é o nome comum do primeiro certificado do cliente para CertificateCommonName. O CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado.|
|ReverseProxyCertificate|   Esse é um certificado opcional que poderá ser especificado para proteger o [proxy reverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para obter mais informações sobre certificados de segurança, consulte [Proteger um cluster autônomo no Windows usando certificados X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Entender o Controle de Acesso Baseado em Função
O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Dois tipos de controle de acesso diferentes têm suporte para clientes que se conectam a um cluster: 

- Função de administrador
- Função de usuário

Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

Especifique as funções de cliente do administrador e do usuário durante a criação do cluster, fornecendo identidades separadas (incluindo certificados) para cada uma. Para saber mais sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [Controle de Acesso Baseado em Função para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Proteger um cluster autônomo usando a segurança do Windows
Para evitar acesso não autorizado a um cluster do Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Descreve como configurar a segurança de nó para nó e de cliente para nó usando a Segurança do Windows no arquivo ClusterConfig.JSON.

**Configurar a segurança do Windows usando gMSA**

Quando o Service Fabric precisar ser executado em gMSA, configure a segurança de nó para nó definindo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros.

Configure a segurança de cliente para nó usando ClientIdentities. Para estabelecer a confiança entre um cliente e o cluster, você deve configurar o cluster para reconhecer em quais identidades de cliente ele pode confiar.

**Configurar a segurança do Windows usando um grupo de computadores**

Se você quiser usar um grupo de computadores em um Domínio do Active Directory, configure a segurança de nó para nó ao definir ClusterIdentity. Para saber mais, consulte [Criar um grupo de computadores no Active Directory](https://msdn.microsoft.com/library/aa545347).

Configure a segurança de cliente para nó usando ClientIdentities. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para reconhecer em quais identidades de cliente ele poderá confiar. Você pode estabelecer confiança de duas maneiras diferentes:

-   Especificar os usuários do grupo de domínio que podem se conectar.
-   Especificar os usuários do nó de domínio que podem se conectar.

## <a name="configure-application-security-in-service-fabric"></a>Configuração de segurança de aplicativo no Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gerenciar segredos nos aplicativos do Service Fabric
Este método ajuda a gerenciar segredos em um aplicativo do Service Fabric. Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação.

Esta abordagem usa o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para gerenciar chaves e segredos. No entanto, usar segredos em um aplicativo é independente de plataforma de nuvem. Isso significa que os aplicativos podem ser implantados em um cluster que está hospedado em qualquer lugar. Há quatro etapas principais nesse fluxo:

-   Obtenha um certificado de codificação de dados.
-   Instale o certificado em seu cluster.
-   Criptografe valores do segredo ao implantar um aplicativo com o certificado e coloque-os no arquivo de configuração Settings.xml de um serviço.
-   Leia os valores criptografados de Settings.xml ao descriptografar com o mesmo certificado de criptografia.

>[!NOTE]
>Saiba mais sobre [Gerenciamento de segredos em aplicativos do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para seu aplicativo
Usando a segurança do Azure Service Fabric, é possível ajudar a proteger aplicativos em execução no cluster em contas de usuário diferentes. O Service Fabric Security também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso deixa os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos.

As etapas são as seguintes:

-   Configurar a política para um ponto de entrada de instalação do serviço.
-   Iniciar comandos do PowerShell em um ponto de entrada de instalação.
-   Usar o redirecionamento de console para depuração local.
-   Configurar uma política para pacotes de códigos de serviço.
-   Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Proteção de comunicação para serviços no Azure Service Fabric Security
A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que podem ser usadas para aprimorar a segurança.

-   [Ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Ajudar a proteger um serviço quando você estiver usando uma pilha de comunicação baseada no WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Próximas etapas
- Para obter informações conceituais sobre a segurança de cluster, consulte [Criar um cluster no Service Fabric usando o Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e o [portal do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para saber mais sobre a segurança de cluster no Service Fabric, consulte [Segurança de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
