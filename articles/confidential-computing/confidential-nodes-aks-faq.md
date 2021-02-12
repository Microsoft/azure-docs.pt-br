---
title: Perguntas frequentes sobre o suporte a nós confidenciais no serviço de kubernetes do Azure (AKS)
description: Encontre respostas para algumas das perguntas mais comuns sobre o AKS (serviço kubernetes do Azure) & suporte aos nós de computação confidencial do Azure (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874477"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre nós de computação confidencial no AKS (serviço kubernetes do Azure)

Este artigo aborda perguntas frequentes sobre nós de computação confidencial baseados em Intel SGX no serviço de kubernetes do Azure (AKS). Se você tiver mais perguntas, envie um email para acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Que Contrato de Nível de Serviço (SLA) e suporte do Azure são fornecidos durante a versão prévia? 

O SLA não é fornecido durante a visualização do produto, conforme definido [aqui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). No entanto, o suporte ao produto é fornecido por meio do suporte do Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>O que é atestado e como podemos fazer atestado de aplicativos em execução no enclaves? 

O atestado é o processo de demonstrar e validar que uma parte do software foi instanciada corretamente na plataforma de hardware específica. Ele também garante que sua evidência seja verificável para fornecer garantias de que ela está sendo executada em uma plataforma segura e que não foi violada. [Leia mais](attestation.md) sobre como o atestado é feito para aplicativos enclave.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Posso habilitar a rede acelerada com clusters AKS de computação confidencial do Azure? 

Não. A rede acelerada não tem suporte em nós de computação confidencial no AKS. Verifique se a rede acelerada está desabilitada na sua implantação. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Posso colocar meus aplicativos em contêineres existentes e executá-los em AKS com a computação confidencial do Azure? 

Sim, examine a [página de contêineres confidenciais](confidential-containers.md) para obter mais detalhes sobre os habilitadores de plataforma.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Qual versão do Intel SGX driver está instalada na imagem do AKS? 

Atualmente, as VMs DCSv2 de computação confidencial do Azure são instaladas com o Intel SGX DCAP 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Posso abrir um tíquete de suporte do Azure se encontrar problemas? 

Sim. O suporte do Azure é fornecido durante a versão prévia. Não há nenhum SLA anexado porque o produto está no estágio de visualização.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Posso injetar scripts de pós-instalação/personalizar drivers para os nós provisionados pelo AKS? 

Não. Os [nós de computação confidencial baseados em AKs-Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) dão suporte a nós de computação confidencial que permitem instalações personalizadas.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Devo usar uma imagem base do Docker para começar nos aplicativos enclave? 

Vários habilitadores (ISVs e projetos OSS) fornecem maneiras de habilitar contêineres confidenciais. Examine a [página de contêineres confidenciais](confidential-containers.md) para obter mais detalhes e referências individuais a implementações.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Posso executar nós ACC com outros SKUs AKS padrão (criar um cluster de pool de nós heterogêneo)? 

Sim, você pode executar diferentes pools de nós no mesmo cluster AKS, incluindo nós ACC. Para direcionar seus aplicativos enclave em um pool de nós específico, considere adicionar seletores de nó ou aplicar limites EPC. Consulte mais detalhes sobre o início rápido em nós confidenciais [aqui](confidential-nodes-aks-get-started.md).

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Posso executar nós do Windows e contêineres do Windows com o ACC? 

Não no momento. Entre em contato conosco se você tiver nós do Windows ou necessidades de contêiner. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>E se o tamanho do meu contêiner for maior do que a memória EPC disponível? 

A memória EPC aplica-se à parte do aplicativo que está programada para ser executada no enclave. O tamanho total do seu contêiner não é a maneira correta de compará-lo com a memória EPC máxima disponível. Na verdade, os computadores DCSv2 com SGX, permitem o máximo de memória VM de 32 GB em que a parte não confiável do aplicativo utilizaria. No entanto, se o contêiner consumir mais do que a memória EPC disponível, o desempenho da parte do programa em execução no enclave poderá ser afetado.

Para gerenciar melhor a memória EPC nos nós de trabalho, considere o gerenciamento de limites baseados em memória EPC por meio de kubernetes. Siga o exemplo abaixo como referência

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>O que acontece se meu enclave consumir mais do que a memória EPC máxima disponível? 

A memória EPC total disponível é compartilhada entre os aplicativos enclave nas mesmas VMs ou nós de trabalho. Se seu aplicativo usar a memória EPC mais do que o disponível, o desempenho do aplicativo poderá ser afetado. Por esse motivo, recomendamos que você defina toleration por aplicativo em seu arquivo de implantação YAML para gerenciar melhor a memória EPC disponível por nós de trabalho, conforme mostrado nos exemplos acima. Como alternativa, você sempre pode optar por mover para cima nos tamanhos de VM do pool de nós de trabalho ou adicionar mais nós. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Por que não posso fazer bifurcações () e exec para executar vários processos em meu aplicativo enclave? 

Atualmente, as VMs do SKU DCsv2 de computação confidencial do Azure dão suporte a um único espaço de endereço para o programa em execução em um enclave. Um único processo é uma limitação atual projetada em relação à alta segurança. No entanto, os habilitadores de contêiner confidenciais podem ter implementações alternativas para superar essa limitação.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Você instala automaticamente qualquer daemonsets adicional para expor os drivers de SGX? 

Sim. O nome do daemonset é SGX-Device-plug-in e SGX-quote-Helper. Leia mais sobre suas respectivas finalidades [aqui](confidential-nodes-aks-overview.md).  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>O que é a SKU da VM que devo escolher para nós de computação confidencial? 

SKUs DCSv2. Os [SKUs do DCSv2](../virtual-machines/dcv2-series.md) estão disponíveis nas [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Ainda posso agendar e executar contêineres não enclave em nós de computação confidencial? 

Sim. As VMs também têm uma memória normal que pode executar cargas de trabalho de contêiner padrão. Considere o modelo de segurança e risco de seus aplicativos antes de decidir sobre os modelos de implantação.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Posso provisionar AKS com pools de nós DCSv2 por meio de portal do Azure? 

Sim. CLI do Azure também pode ser usado como uma alternativa, conforme documentado [aqui](confidential-nodes-aks-get-started.md).

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Quais versões do Ubuntu e a geração de VM têm suporte? 

18, 4 na Gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Podemos alterar a versão atual do Intel SGX DCAP driver no AKS? 

Não. Para executar instalações personalizadas, recomendamos que você escolha as implantações [de nós de trabalho de computação confidencial do AKS-Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>A qual versão do kubernetes você dá suporte e recomenda? 

Damos suporte e recomendamos o kubernetes versão 1,16 e posterior 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Qual é a limitação atual conhecida ou limitações técnicas do produto em versão prévia? 

- Dá suporte apenas a nós de VM Ubuntu 18, 4 Gen 2 
- Não há suporte para nós do Windows ou para contêineres do Windows
- Não há suporte para dimensionamento automático de Pod horizontal baseado em memória EPC. Há suporte para a CPU e o dimensionamento com base na memória regular.
- Não há suporte para espaços de desenvolvimento em AKS para aplicativos confidenciais no momento

## <a name="next-steps"></a>Próximas etapas
Examine a [página contêineres confidenciais](confidential-containers.md) para obter mais detalhes sobre os contêineres confidenciais.