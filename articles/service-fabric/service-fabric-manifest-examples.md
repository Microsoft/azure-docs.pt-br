---
title: Exemplos de manifesto do aplicativo Service Fabric do Azure
description: Saiba como definir as configurações do aplicativo e do manifesto do serviço para um aplicativo do Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451634"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Exemplos do manifesto de serviço e aplicativo do Service Fabric
Esta seção contém exemplos de manifestos de serviço e aplicativo. Esses exemplos não devem mostrar cenários importantes, mas mostrar as diferentes configurações disponíveis e como usá-las. 

A seguir pode ser visto um índice de recursos e os manifestos de exemplo dos quais eles fazem parte.

|Recurso|Manifest|
|---|---|
|[Governança de recursos](service-fabric-resource-governance.md)|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Executar um serviço como uma conta do administrador local](service-fabric-application-runas-security.md)|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Aplicar uma política padrão a todos os pacotes de códigos de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Criar entidades de segurança de usuário e grupo](service-fabric-application-runas-security.md)|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|compartilhar um pacote de dados entre instâncias de serviço|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Substituir pontos de extremidade de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifesto do aplicativo Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Executar um script na inicialização do serviço](service-fabric-run-script-at-service-startup.md)|[Manifesto do serviço VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definir um ponto de extremidade HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifesto do serviço VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Declarar um pacote de configuração](service-fabric-application-and-service-manifests.md)|[Manifesto do serviço VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Declarar um pacote de dados](service-fabric-application-and-service-manifests.md)|[Manifesto do serviço VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Substituir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar mapeamento de porta para host do contêiner](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir autenticação do registro de contêiner](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Especificar as imagens de contêiner específicas do build do sistema operacional](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifesto do aplicativo de contêiner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto do serviço FrontEndService do contêiner](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifesto do serviço BackEndService do contêiner](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Configurar um ponto de extremidade](service-fabric-get-started-containers.md#configure-communication)|[Manifesto do serviço FrontEndService do contêiner](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifesto do serviço BackEndService do contêiner](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [Manifesto do serviço VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|passar comandos para o contêiner|[Manifesto do serviço FrontEndService do contêiner](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importar um certificado em um contêiner](service-fabric-securing-containers.md)|[Manifesto do serviço FrontEndService do contêiner](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Configurar driver de volume](service-fabric-containers-volume-logging-drivers.md)|[Manifesto do serviço BackEndService do contêiner](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

