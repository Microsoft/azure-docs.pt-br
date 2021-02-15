---
title: Contêineres confidenciais no serviço kubernetes do Azure (AKS)
description: Saiba mais sobre o suporte a contêiner não modificado em contêineres confidenciais.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 35518a90ff3db2b951e0310970afd6d78dd25807
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122196"
---
# <a name="confidential-containers"></a>Contêineres confidenciais

## <a name="overview"></a>Visão geral

Permita que os desenvolvedores tragam um **aplicativo Docker existente (novo ou existente)** e execute-o com segurança no AKs (serviço kubernetes do Azure) por meio de nós de computação confidencial.

Os contêineres confidenciais ajudam a proteger:

- integridade de dados 
- confidencialidade dos dados
- integridade do código
- proteção de código de contêiner por meio de criptografia
- garantias baseadas em hardware
- permitir a execução de aplicativos existentes
- criar a raiz de hardware de confiança

Um sistema de execução confiável baseado em hardware ("t") é um componente importante que é usado para fornecer garantias fortes por meio de medidas de hardware e software de componentes TCB (Trusted Computing base). As verificações dessas medidas ajudam na validação da computação esperada e na verificação de qualquer violação dos aplicativos de contêiner.

Os contêineres confidenciais dão suporte a aplicativos personalizados desenvolvidos com **Python, Java, Node js, etc. ou aplicativos de software empacotados, como Nginx, cache Redis, memcache**e assim por diante, para serem executados sem modificações no AKs.

Os contêineres confidenciais são o caminho mais rápido para a confidencialidade do contêiner, incluindo a proteção do contêiner por meio da criptografia, permitindo a elevação e a mudança sem alterações mínimas na lógica de negócios.

![A conversão de contêiner confidencial](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Habilitadores de contêiner confidenciais

Para executar um contêiner existente do Docker, os aplicativos em nós de computação confidencial exigem uma camada de abstração ou um software SGX para aproveitar o conjunto de instruções de CPU especiais. O software SGX também permite que o código de seus aplicativos confidenciais seja protegido e crie uma execução direta à CPU para remover o SO convidado, o sistema operacional host ou o hipervisor. Essa proteção reduz as áreas e vulnerabilidades de ataque de superfície geral com camadas do sistema operacional ou do hipervisor.

Os contêineres confidenciais têm suporte total no AKS e habilitados por meio de parceiros do Azure e projetos OSS (software livre). Os desenvolvedores podem escolher provedores de software com base nos recursos, integração com os serviços do Azure e suporte de ferramentas.

## <a name="partner-enablers"></a>Habilitadores de parceiros
> [!NOTE]
> As soluções a seguir são oferecidas por meio de parceiros do Azure e podem incorrer em taxas de licenciamento. Verifique os termos do software do parceiro de forma independente. 

### <a name="fortanix"></a>Fortanix

O [Fortanix](https://www.fortanix.com/) oferece aos desenvolvedores a opção de um portal e uma experiência baseada na CLI para trazer seus aplicativos em contêineres e abordá-los em contêineres confidenciais com capacidade de SGX sem a necessidade de modificar ou recompilar o aplicativo. O Fortanix fornece a flexibilidade para executar e gerenciar o conjunto mais amplo de aplicativos, incluindo aplicativos existentes, novos aplicativos nativos do enclave e aplicativos pré-configurados. Os usuários podem começar com a interface do usuário do [enclave Manager](https://em.fortanix.com/) ou [APIs REST](https://www.fortanix.com/api/em/) para criar contêineres confidenciais seguindo o guia de [início rápido](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) para o serviço kubernetes do Azure.

![Processo de implantação do Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scontain (

O o o o o o seédor [dá suporte](https://scontain.com/index.html?lang=en) a políticas de segurança que podem gerar certificados, chaves e segredos e garante que eles fiquem visíveis apenas para serviços atestados de um aplicativo. Dessa forma, os serviços de um aplicativo atestam automaticamente por meio de TLS, sem a necessidade de modificar os aplicativos nem o TLS. Isso é explicado com a ajuda de um aplicativo Flask simples aqui: https://sconedocs.github.io/flask_demo/  

O o enclaves pode converter os binários mais existentes em aplicativos que são executados dentro de uma execução sem a necessidade de alterar o aplicativo ou recompilar esse aplicativo. O o prote também protege linguagens interpretadas como Python, criptografando arquivos de dados, bem como arquivos de código Python. Com a ajuda de uma política de segurança do se?, uma pode proteger os arquivos criptografados contra acessos, modificações e reversões não autorizadas. Como "sconify" um aplicativo Python existente é explicado [aqui](https://sconedocs.github.io/sconify_image/)

![Fluxo de Scontain](./media/confidential-containers/scone-workflow.png)

As implantações de o AKS em nós de computação confidencial com o inconsistente são totalmente compatíveis e integradas. Introdução a um aplicativo de exemplo aqui https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

O [Anjuna](https://www.anjuna.io/) fornece software de plataforma de SGX que permite executar contêineres não modificados no AKs. Saiba mais sobre a funcionalidade e confira os aplicativos de exemplo [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Introdução a um cache Redis de exemplo e um aplicativo personalizado do Python [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Processo de Anjuna](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Habilitadores de OSS 
> [!NOTE]
> As soluções a seguir são oferecidas por meio de projetos de software livre e não são diretamente afiliadas à ACC (computação confidencial) do Azure ou à Microsoft.  

### <a name="graphene"></a>Graphene

O [Graphene](https://grapheneproject.io/) é um sistema operacional convidado leve, projetado para executar um único aplicativo Linux com requisitos mínimos de host. O Graphene pode executar aplicativos em um ambiente isolado com benefícios comparáveis à execução de um sistema operacional completo e tem uma boa ferramenta de suporte para converter o aplicativo de contêiner do Docker existente em GSC (contêineres blindados do Graphene).

Introdução a um aplicativo de exemplo e implantação em AKS [aqui](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) é um sistema operacional de biblioteca de vários processos, com segurança de memória (LibOS) para o Intel SGX. Ele permite que os aplicativos herdados sejam executados em SGX com pouca ou nenhuma modificação no código-fonte. O Occlum protege de forma transparente a confidencialidade das cargas de trabalho do usuário, permitindo, ao mesmo tempo, uma rápida migração para os aplicativos existentes do Docker.

O Occlum dá suporte a implantações de AKS. Siga as instruções de implantação com vários aplicativos de exemplo [aqui](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demonstração de contêineres confidenciais
Veja a demonstração de saúde confidencial com contêineres confidenciais. O exemplo está disponível [aqui](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Entrar em contato

Tem dúvidas com sua implementação ou deseja se tornar um habilitador? Enviar um email para acconaks@microsoft.com

## <a name="reference-links"></a>Links de referência

[Atestado do Microsoft Azure](../attestation/overview.md)

[DCsv2 máquinas virtuais](virtual-machine-solutions.md)

[AKS (Serviço de Kubernetes do Azure)](../aks/intro-kubernetes.md)
