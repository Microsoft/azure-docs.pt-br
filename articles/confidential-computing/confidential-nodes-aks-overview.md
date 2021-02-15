---
title: Visualização pública dos nós de computação confidencial no AKS (Serviço de Kubernetes do Azure)
description: Nós de computação confidencial no AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564167"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Nós de computação confidencial no Serviço de Kubernetes do Azure (visualização pública)

A [computação confidencial do Azure](overview.md) permite que você proteja seus dados confidenciais enquanto eles estão sendo usados. As infraestruturas subjacentes protegem esses dados de outros aplicativos, administradores e provedores de nuvem usando ambientes de contêiner de execução confiável com suporte de hardware.

## <a name="overview"></a>Visão geral

O AKS (Serviço de Kubernetes do Azure) dá suporte à adição de [nós de computação confidencial DCsv2](confidential-computing-enclaves.md) desenvolvidos pelo Intel SGX. Esses nós podem executar cargas de trabalho confidenciais em um TEE (ambiente de execução confiável) baseado em hardware, permitindo que o código no nível do usuário seja alocado a regiões de memória privada. Essas regiões de memória privada são chamadas de enclaves. Os enclaves têm a finalidade de proteger o código e os dados contra processos em execução com privilégios mais elevados. O modelo de execução SGX remove as camadas intermediárias do SO convidado, do sistema operacional do host e do hipervisor. O modelo de *execução isolada por contêiner baseado em hardware* permite que os aplicativos sejam executados diretamente com a CPU, mantendo o bloco especial de memória criptografado. Os nós de computação confidencial ajudam com a postura geral de segurança dos aplicativos de contêiner no AKS e é uma excelente adição à estratégia de contêiner de defesa em profundidade. 

![Visão geral do nó SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Recursos dos Nós Confidenciais do AKS

- Isolamento de contêineres no nível do processo e baseado em hardware por meio de um TEE (ambiente de execução confiável) SGX 
- Clusters de pools de nós heterogêneos (mistura de pools de nós confidenciais e não confidenciais)
- Agendamento de pod baseado em memória EPC (Cache de Página Criptografada)
- Driver SGX DCAP pré-instalado
- Patch Intel FSGS pré-instalado
- Dá suporte ao dimensionamento automático de pod horizontal baseado em consumo e ao dimensionamento automático de cluster
- Auxiliar de atestado fora do proc por meio do DaemonSet do AKS
- Suporte para Contêineres Linux por meio dos nós de trabalho de VM do Ubuntu 18.04 Gen 2

## <a name="aks-provided-daemon-sets-addon"></a>Conjuntos de daemon fornecidos pelo AKS (complemento)

#### <a name="sgx-device-plugin"></a>Plug-in de dispositivo SGX <a id="sgx-plugin"></a>

O Plug-in de dispositivo SGX implementa a interface de plug-in de dispositivo do Kubernetes para a memória EPC. Efetivamente, esse plug-in faz da memória EPC um tipo de recurso adicional no Kubernetes. Os usuários podem especificar limites para esse recurso, assim como ocorre com outros recursos. Além da função de agendamento, o plug-in do dispositivo ajuda a atribuir permissões do driver do dispositivo SGX aos contêineres de carga de trabalho confidenciais. Veja um exemplo de implementação da amostra de implantação baseada em memória EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) [aqui](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Serviço de auxiliar de cotação do SGX <a id="sgx-quote"></a>

Os aplicativos de enclave que executam o atestado remoto precisam gerar uma COTAÇÃO. A COTAÇÃO fornece uma prova criptográfica da identidade e do estado do aplicativo, bem como o ambiente no qual o enclave está sendo executado. A geração da COTAÇÃO depende de determinados componentes de software confiáveis da Intel, que fazem parte dos Componentes de software da plataforma SGX (PSW/DCAP). Esse PSW é empacotado como um DaemonSet que é executado por nó. Ele pode ser usado ao solicitar uma COTAÇÃO de atestado dos aplicativos de enclave. O uso do serviço fornecido pelo AKS ajudará a manter melhor a compatibilidade entre o PSW e os outros componentes de SW no host. [Leia mais](confidential-nodes-out-of-proc-attestation.md) sobre os detalhes de seu uso e seus recursos.

## <a name="programming--application-models"></a>Programação e modelos de aplicativo

### <a name="confidential-containers"></a>Contêineres confidenciais

Os [contêineres confidenciais](confidential-containers.md) executam programas existentes e a maioria dos runtimes de **linguagens de programação comuns** (Python, Node, Java etc.), bem como suas dependências de bibliotecas existentes, sem nenhuma modificação nem recompilação do código-fonte. Esse é o modelo de confidencialidade mais rápido habilitado por meio de Projetos de software livre e de Parceiros do Azure. As imagens de contêiner criadas e prontas para serem executadas nos enclaves seguros são chamadas de contêineres confidenciais.

### <a name="enclave-aware-containers"></a>Contêineres com reconhecimento de enclave

O AKS dá suporte a aplicativos programados para serem executados em nós confidenciais e utilizam **conjuntos de instruções especiais** disponibilizados por meio de SDKs e estruturas. Esse modelo de aplicativo fornece o maior controle para seus aplicativos com a menor TCB (Base de computação confiável). [Leia mais](enclave-aware-containers.md) sobre os contêineres com reconhecimento de enclave.

## <a name="next-steps"></a>Próximas etapas

[Implantar o Cluster do AKS com nós de computação confidencial](./confidential-nodes-aks-get-started.md)

[Exemplos de contêiner confidencial para início rápido](https://github.com/Azure-Samples/confidential-container-samples)

[Lista de SKUs do DCsv2](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions