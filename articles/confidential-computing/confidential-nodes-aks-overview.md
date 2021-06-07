---
title: Nós de computação confidencial no AKS (Serviço de Kubernetes do Azure)
description: Nós de computação confidencial no AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 203185d9f6def2204906b8722f1969b14eee8787
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933144"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Nós de computação confidencial no Serviço de Kubernetes do Azure

A [computação confidencial do Azure](overview.md) permite que você proteja seus dados confidenciais enquanto eles estão sendo usados. A infraestrutura subjacente da computação confidencial protegerá esses dados de outros aplicativos, administradores e provedores de nuvem usando ambientes de contêiner de execução confiável com suporte de hardware. Adicionar nós de computação confidencial permite direcionar o aplicativo contêiner para ser executado em um ambiente isolado, atestado e protegido por hardware.

## <a name="overview"></a>Visão geral

O AKS (Serviço de Kubernetes do Azure) dá suporte à adição de [nós de computação confidencial DCsv2](confidential-computing-enclaves.md) desenvolvidos pelo Intel SGX. Esses nós permitem executar cargas de trabalho confidenciais em um TEE (ambiente de execução confiável) baseado em hardware. Os TEEs permitem que um código em nível de usuário de contêineres aloque regiões privadas de memória para executar o código diretamente na CPU. Essas regiões de memória privada que são executadas diretamente na CPU são chamadas de enclaves. Os enclaves ajudam a proteger a integridade e a confidencialidade dos dados, bem como a integridade do código de outros processos em execução nos mesmos nós. O modelo de execução do Intel SGX também remove camadas intermediárias do SO Convidado, do SO Host e do Hipervisor, reduzindo a área da superfície de ataque. O modelo de *execução isolada por contêiner baseado em hardware* em um nó permite que aplicativos sejam executados diretamente na CPU, mantendo um bloco especial de memória criptografada por contêiner. Nós de computação confidencial com contêineres confidenciais são um excelente complemento para executar um planejamento de segurança de confiança zero e uma estratégia de contêiner de defesa completa.

![Visão geral do nó SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Recursos dos Nós Confidenciais do AKS

- Isolamento de contêiner em nível de processo e baseado em hardware por meio do TEE (ambiente de execução confiável) do Intel SGX 
- Clusters de pools de nós heterogêneos (mistura de pools de nós confidenciais e não confidenciais)
- Agendamento de pod baseado em memória EPC (Cache de Página Criptografada) – requer um complemento
- Driver Intel SGX DCAP pré-instalado
- Dimensionamento automático de pod horizontal e dimensionamento automático de cluster baseado no consumo da CPU
- Suporte para Contêineres Linux por meio dos nós de trabalho de VM do Ubuntu 18.04 Gen 2

## <a name="confidential-computing-add-on-for-aks"></a>Complemento de computação confidencial para o AKS
O recurso complementar permite obter uma funcionalidade adicional no AKS durante a execução de pools de nós de computação confidencial no cluster. Esse complemento habilitará os recursos abaixo.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Plug-in de dispositivo do Azure para o Intel SGX <a id="sgx-plugin"></a>

O plug-in do dispositivo implementa uma interface de plug-in do dispositivo do Kubernetes para a memória EPC (Cache de Página Criptografada) e expõe os drivers de dispositivo dos nós. De modo efetivo, esse plug-in transforma a memória EPC em um outro tipo de recurso no Kubernetes. Os usuários podem especificar limites para esse recurso, assim como ocorre com outros recursos. Além da função de agendamento, o plug-in do dispositivo ajudará você a atribuir permissões de driver de dispositivo SGX aos contêineres confidenciais de cargas de trabalho. Com esse plugin, os desenvolvedores poderão evitar a montagem de volumes do driver Intel SGX nos arquivos de implantação. Veja um exemplo de implementação da amostra de implantação baseada em memória EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) [aqui](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modelos de programação

### <a name="confidential-containers"></a>Contêineres confidenciais

Os [contêineres confidenciais](confidential-containers.md) ajudarão você a executar aplicativos contêiner não modificados e existentes dos runtimes de **linguagens de programação mais comuns** (Python, Node, Java etc.) de modo confidencial. Esse modelo de empacotamento não precisa de nenhuma modificação ou recompilação de código-fonte. Esse é o método mais rápido de obter confidencialidade que poderá ser alcançado empacotando contêineres padrão do Docker com Projetos de Software Livre ou Soluções de Parceiros do Azure. Neste modelo de empacotamento e execução, todas as partes do aplicativo contêiner serão carregadas em um limite confiável (enclave). Esse modelo funciona de modo adequado para aplicativos contêiner prontos para uso e disponíveis no mercado ou aplicativos personalizados que estão atualmente em execução em nós de uso geral.

### <a name="enclave-aware-containers"></a>Contêineres com reconhecimento de enclave
Os nós de computação confidencial no AKS também são compatíveis com contêineres programados para serem executados em um enclave a fim de usar um **conjunto de instruções especiais** disponível na CPU. Esse modelo de programação permite obter um controle mais rígido do fluxo de execução e requer o uso de SDKs e estruturas especiais. Esse modelo de programação fornece maior controle do fluxo de aplicativo com a menor TCB (Base de Computação Confiável). O desenvolvimento de contêineres com reconhecimento de enclave envolve partes confiáveis e não confiáveis para o aplicativo contêiner, permitindo gerenciar a memória regular e a memória EPC (Cache de Página Criptografada) em que o enclave será executado. [Leia mais](enclave-aware-containers.md) sobre os contêineres com reconhecimento de enclave.

## <a name="next-steps"></a>Próximas etapas

[Implantar o Cluster do AKS com nós de computação confidencial](./confidential-nodes-aks-get-started.md)

[Exemplos de contêiner confidencial para início rápido](https://github.com/Azure-Samples/confidential-container-samples)

[Lista de SKUs do DCsv2](../virtual-machines/dcv2-series.md)

[Sessão de webinar de defesa completa usando contêineres confidenciais](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
