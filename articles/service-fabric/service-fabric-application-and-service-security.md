---
title: Saiba mais sobre a segurança de aplicativos do Azure Service Fabric | Microsoft Docs
description: Uma visão geral de como executar com segurança aplicativos de microsserviço no Service Fabric. Saiba como executar um script de inicialização e serviços em diferentes contas de segurança, autenticar e autorizar usuários, gerenciar segredos de aplicativo, proteger as comunicações de serviço, usar um gateway de API e proteger dados em repouso de aplicativos.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: aljo
ms.openlocfilehash: b4d3699c0327bb2771a358d3e3c2921bdc39ee5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621533"
---
# <a name="service-fabric-application-and-service-security"></a>Segurança de serviço e aplicativo do Service Fabric
Uma arquitetura de microsserviços pode trazer [muitos benefícios](service-fabric-overview-microservices.md). Porém, o gerenciamento da segurança de microsserviços é um desafio que difere do gerenciamento da segurança de aplicativos monolítica tradicional. 

Com um monolito, o aplicativo normalmente é executado em um ou mais servidores em uma rede e é mais fácil identificar as portas expostas e as APIs e o endereço IP. Geralmente há um perímetro ou limite e um banco de dados para proteger. Se o sistema estiver comprometido devido a uma violação de segurança ou ataque, é provável que todos os itens dentro do sistema estarão disponíveis para o invasor. Com os microsserviços, o sistema é mais complexo.  Os serviços são descentralizados e distribuídos em vários hosts e migram de um host para outro.  Com a segurança adequada, limite os privilégios que um invasor pode ter e a quantidade de dados disponíveis em um único ataque se houver invasão de um serviço.  A comunicação não é interna, mas ocorre em uma rede e há muitas portas expostas e interações entre serviços. Saber quais são essas interações de serviço e quando elas ocorrem é crucial para a segurança de seu aplicativo.

Este artigo não é um guia de segurança de microsserviços, há muitos recursos disponíveis online, mas descreve como os diferentes aspectos de segurança podem ser realizados no Service Fabric.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
É geralmente necessário limitar os recursos e as APIs expostos por um serviço a determinados usuários ou clientes confiáveis. A autenticação é o processo de atestar de maneira confiável a identidade de um usuário.  A autorização é o processo que disponibiliza APIs ou serviços para alguns usuários autenticados, mas não para outros.

### <a name="authentication"></a>Autenticação
A primeira etapa para tomar decisões de confiança de nível de API é a autenticação. A autenticação é o processo de atestar de maneira confiável a identidade de um usuário.  Em cenários de microsserviço, a autenticação é normalmente feita de forma centralizada. Se você estiver usando um Gateway de API, poderá [descarregar a autenticação](/azure/architecture/patterns/gateway-offloading) para o gateway. Se você usar essa abordagem, certifique-se de que os serviços individuais não possam ser acessados diretamente (sem o Gateway da API), a menos que uma segurança extra seja adicionada para autenticar mensagens, independentemente de serem ou não provenientes do gateway.

Se os serviços puderem ser acessados diretamente, um serviço de autenticação, como o Azure Active Directory ou um microsserviço de autenticação dedicado atuando como um serviço de token de segurança (STS), poderá ser usado para autenticar os usuários. As decisões de confiança são compartilhadas entre os serviços com tokens de segurança ou cookies. 

Para o ASP.NET Core, o mecanismo principal para [autenticar usuários](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) é o sistema de associação da Identidade do ASP.NET Core. A Identidade do ASP.NET Core armazena informações de usuário (incluindo informações de logon, funções e declarações) em um repositório de dados configurado pelo desenvolvedor. A Identidade do ASP.NET Core dá suporte à autenticação de dois fatores.  Os provedores de autenticação externa também têm suporte, para que os usuários possam fazer logon usando os processos de autenticação existentes de fornecedores como Microsoft, Google, Facebook ou Twitter. 

### <a name="authorization"></a>Autorização
Após a autenticação, os serviços precisam autorizar o acesso do usuário ou determinar o que um usuário é capaz de fazer. Esse processo permite que um serviço disponibilize APIs para alguns usuários autenticados, mas não para todos. A autorização é ortogonal e independente de autenticação, que é o processo de verificação de quem é um usuário. A autenticação pode criar uma ou mais identidades para o usuário atual.

A [autorização do ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) pode ser feita com base nas funções dos usuários ou com base em política personalizada, que pode incluir a inspeção de declarações ou outros fatores.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restringir e proteger o acesso usando um gateway de API
Os aplicativos em nuvem geralmente precisam de um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. Um [gateway de API](/azure/architecture/microservices/gateway) fica entre clientes e serviços e é o ponto de entrada para todos os serviços que o seu aplicativo fornece. Ele atua como um proxy reverso, encaminhando as solicitações de clientes para serviços. Ele também pode executar várias tarefas detalhadas, como autenticação e autorização, terminação de SSL e a limitação de taxa. Se você não implantar um gateway, os clientes deverão enviar solicitações diretamente aos serviços front-end.

No Service Fabric, um gateway pode ser qualquer serviço sem estado, como um [aplicativo ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para entrada de tráfego, como [Traefik](https://docs.traefik.io/), [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/), [Hub IoT](https://docs.microsoft.com/azure/iot-hub/) ou [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management).

O Gerenciamento de API integra-se diretamente com o Service Fabric, permitindo que APIs sejam publicadas com um conjunto de regras de roteamento avançado para serviços de back-end do Service Fabric .  Você pode proteger o acesso a serviços de back-end, evitar ataques de DOS usando limitação ou verificar chaves de API, tokens de JWT, certificados e outras credenciais. Para saber mais, leia [Service Fabric com visão geral de Gerenciamento de API do Azure](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Gerenciar segredos do aplicativo
Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação. Este artigo usa o Azure Key Vault para gerenciar chaves e segredos. No entanto, o *uso* de segredos em um aplicativo é independente de plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar.

A maneira recomendada de gerenciar as definições de configuração de serviço é por meio de [pacotes de configuração de serviço][config-package]. Os pacotes de configuração são atualizáveis e têm controle de versão por meio de atualizações sem interrupção gerenciadas com reversão automática e validação de integridade. Isso é preferível à configuração global, pois reduz as chances de uma interrupção de serviços globais. Segredos criptografados não são exceção. O Service Fabric tem recursos internos para criptografar e descriptografar valores em um arquivo Settings.XML do pacote de configuração usando a criptografia de certificado.

O diagrama a seguir ilustra o fluxo básico para gerenciamento de segredos em um aplicativo do Service Fabric:

![visão geral do gerenciamento de segredos][overview]

Há quatro etapas principais nesse fluxo:

1. Obtenha um certificado de codificação de dados.
2. Instale o certificado em seu cluster.
3. Criptografe valores do segredo ao implantar um aplicativo com o certificado e coloque-os no arquivo de configuração Settings.xml de um serviço.
4. Leia os valores criptografados de Settings.xml ao descriptografar com o mesmo certificado de codificação. 

O [Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma maneira de obter certificados instalados em clusters do Service Fabric no Azure. Se não estiver implantando no Azure, você não precisará usar o cofre de chaves para gerenciar segredos em aplicativos do Service Fabric.

Para obter um exemplo, veja [Gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Segurança do ambiente de hospedagem
Usando o Azure Service Fabric, é possível proteger aplicativos em execução no cluster em contas de usuário diferentes. O Service Fabric também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.

O manifesto do aplicativo declara as entidades de segurança (usuários e grupos) necessárias para executar os serviços e os recursos de segurança.  Essas entidades são referenciadas em políticas, por exemplo, o executar como, o ponto de extremidade de associação, o compartilhamento de pacote ou as políticas de acesso de segurança.  As políticas são aplicadas aos recursos de serviço na seção **ServiceManifestImport** do manifesto do aplicativo.

Ao declarar entidades de segurança, também é possível definir e criar grupos de usuários para que um ou mais usuários possam ser adicionados a cada grupo para serem gerenciados em conjunto. Isso será útil quando houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo.

Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário local ou em uma conta de sistema local, especificada no manifesto do aplicativo. Para saber mais, veja [Executar um serviço como uma conta de usuário ou conta de sistema local](service-fabric-application-runas-security.md).  Você também pode [Executar um script de inicialização do serviço como uma conta de usuário ou sistema local](service-fabric-run-script-at-service-startup.md).

Quando você estiver executando o Service Fabric em um cluster autônomo do Windows, você pode executar um serviço em [Contas de domínio do Active Directory](service-fabric-run-service-as-ad-user-or-group.md) ou [Contas de serviço gerenciado de grupo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Contêineres de segurança
O Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado que está instalado em nós em um cluster do Windows ou Linux (versão 5.7 ou superior). Esse certificado PFX pode ser usado para autenticar o aplicativo ou o serviço ou proteger a comunicação com outros serviços. Para saber mais, veja [Importar um certificado para um contêiner](service-fabric-securing-containers.md).

Além disso, o Service Fabric também dá suporte a gMSA (Contas de Serviço Gerenciado do grupo) para contêineres do Windows. Para saber mais, veja [Configurar gMSA para contêineres do Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Proteger comunicações de serviço
No Service Fabric, um serviço é executado em algum lugar em um cluster do Service Fabric, normalmente distribuído entre várias VMs. O Service Fabric fornece várias opções para proteger as comunicações do serviço.

Você pode habilitar pontos de extremidade HTTPS em seus serviços Web [ASP.NET Core ou Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).

Você pode estabelecer uma conexão segura entre o proxy reverso e serviços, permitindo um canal seguro de ponta a ponta. A conexão aos serviços seguros tem suporte apenas quando o proxy reverso é configurado para escutar em HTTPS. Para saber mais sobre como configurar o proxy reverso, leia [Proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md).  [Conectar a um serviço seguro](service-fabric-reverseproxy-configure-secure-communication.md) descreve como estabelecer uma conexão segura entre o proxy reverso e os serviços.

A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Aprenda a melhorar a segurança quando você estiver usando a comunicação remota do serviço (em [C#](service-fabric-reliable-services-secure-communication.md) ou [Java](service-fabric-reliable-services-secure-communication-java.md)) ou usando [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Criptografar dados de aplicativos em repouso
Cada [tipo de nó](service-fabric-cluster-nodetypes.md) em um cluster do [Service Fabric](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) sendo executado no Azure é respaldado por um conjunto de dimensionamento de máquinas virtuais. Usando um modelo do Azure Resource Manager, é possível anexar discos de dados para os conjuntos de dimensionamento que compõem o cluster do Service Fabric.  Se os seus serviços salvarem dados em um disco de dados anexado, você poderá [criptografar esses discos de dados](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) para proteger os dados do aplicativo.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Executar um script de configuração na inicialização do serviço](service-fabric-run-script-at-service-startup.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)
* [Saiba mais sobre segurança de cluster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png