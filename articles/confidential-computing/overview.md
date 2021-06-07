---
title: Visão geral sobre a computação confidencial do Azure
description: Visão geral sobre a ACC (computação confidencial do Azure)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554183"
---
# <a name="confidential-computing-on-azure"></a>Computação confidencial no Azure

A computação confidencial do Azure permite isolar seus dados confidenciais enquanto eles estão sendo processados na nuvem. Muitos setores, no intuito de proteger dados, usam a computação confidencial para:

- Proteger dados financeiros
- Proteger informações de pacientes
- Executar processos de machine learning em informações confidenciais
- Executar algoritmos em conjuntos de dados criptografados provenientes de várias fontes


## <a name="overview"></a>Visão geral
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que a proteção de seus dados na nuvem é importante. Ouvimos suas preocupações. Veja algumas perguntas que nossos clientes podem ter ao mover cargas de trabalho confidenciais para a nuvem: 

- Como tenho a certeza de que a Microsoft não acessará dados que não estão criptografados?
- Como faço para impedir ameaças de segurança de administradores privilegiados dentro da minha empresa?
- Quais são outras maneiras de evitar que terceiros acessem dados confidenciais de clientes?

O Microsoft Azure ajuda a minimizar a superfície de ataque para obter uma proteção de dados mais forte. O Azure já oferece muitas ferramentas para proteger [**dados em repouso**](../security/fundamentals/encryption-atrest.md) por meio de modelos como criptografia do lado do cliente e criptografia do lado do servidor. Além disso, o Azure oferece mecanismos para criptografar [**dados em trânsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) por meio de protocolos seguros, como TLS e HTTPS. Esta página apresenta um terceiro segmento de criptografia de dados: a criptografia dos **dados em uso**.

## <a name="introduction-to-confidential-computing"></a>Apresentando a computação confidencial  

Computação confidencial é um termo específico desse setor, definido pelo [CCC](https://confidentialcomputing.io/) (Confidential Computing Consortium), uma fundação dedicada à definição e aceleração da adoção da computação confidencial. O CCC define a computação confidencial como: a proteção dos dados em uso por meio da execução da computação em um TEE (Ambiente de Execução Confiável) baseado em hardware.

Um TEE é um ambiente que impõe a execução somente de código autorizado. Os dados no TEE não podem ser lidos nem adulterados por qualquer código de fora desse ambiente. 

### <a name="lessen-the-need-for-trust"></a>Diminua a necessidade de confiança
A execução de cargas de trabalho na nuvem requer confiança. Você dá essa confiança a vários provedores, habilitando diferentes componentes do seu aplicativo.


**Fornecedores de software de aplicativo**: Confie no software implantando localmente, usando código de software livre ou criando software de aplicativo internamente.

**Fornecedores de hardware**: Confie no hardware usando hardware local ou hardware interno. 

**Provedores de infraestrutura**: Confie em provedores de nuvem ou gerencie os seus data centers locais.


A Computação confidencial do Azure facilita a confiança no provedor de nuvem, reduzindo a necessidade de confiança em vários aspectos da infraestrutura de nuvem de computação. A Computação confidencial do Azure minimiza a confiança ao kernel do sistema operacional do host, ao hipervisor, ao administrador da VM e ao administrador do host.

### <a name="reducing-the-attack-surface"></a>Reduzindo a superfície de ataque
A TCB (base de computação confiável) refere-se a todos os componentes de hardware, de firmware e de software de um sistema que oferecem um ambiente seguro. Os componentes dentro da TCB são considerados "críticos". Se um componente dentro da TCB for comprometido, a segurança de todo o sistema poderá ser prejudicada. 

Uma TCB inferior significa maior segurança. Há menos risco de exposição a várias vulnerabilidades, malwares, ataques e pessoas mal-intencionadas. A Computação confidencial do Azure visa reduzir a TCB das suas cargas de trabalho de nuvem oferecendo TEEs. Os TEEs reduzem sua TCB a binários, códigos e bibliotecas de runtime confiáveis. Ao usar a infraestrutura do Azure e os serviços para computação confidencial, você pode remover tudo o que é da Microsoft da sua TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Usando o Azure para computação confidencial baseada em nuvem <a id="cc-on-azure"></a>

A computação confidencial do Azure permite que você aproveite os recursos de computação confidencial em um ambiente virtualizado. Agora você pode usar ferramentas, software e infraestrutura de nuvem para criar com base em hardware seguro.  

**Impedir o acesso não autorizado**: Executar dados confidenciais na nuvem. Confie que o Azure oferece a melhor proteção de dados possível, com pouca ou nenhuma alteração no que é feito atualmente.

**Conformidade normativa**: Migre para a nuvem e mantenha controle total dos dados para atender às normas governamentais para proteger informações pessoais e proteger o IP organizacional.

**Colaboração segura e não confiável**: Lide com problemas de escala de trabalho de todos os setores, combinando dados entre organizações, até mesmo entre concorrentes, para revelar análises amplas de dados e insights mais detalhados.

**Processamento isolado**: Ofereça uma nova onda de produtos que removem a obrigação em dados privados com o processamento cego. Os dados do usuário não podem ser recuperados nem mesmo pelo provedor de serviços. 

## <a name="get-started"></a>Começar agora
### <a name="azure-compute"></a>Computação do Azure
Crie aplicativos com base nas ofertas de IaaS de computação confidencial no Azure.
- VMs (Máquinas Virtuais): [Série DCsv2](confidential-computing-enclaves.md)
- AKS (Kubernetes do Azure): [Orquestrar contêineres confidenciais](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Segurança do Azure 
Tenha a certeza de que suas cargas de trabalho estão protegidas por meio de métodos de verificação e gerenciamento de chaves vinculados a hardware. 
- Atestado: [Atestado do Microsoft Azure (versão prévia)](../attestation/overview.md)
- Gerenciamento de chaves: HSM Gerenciado (versão prévia)

### <a name="develop"></a>Desenvolver
Comece a usar o desenvolvimento de aplicativos com reconhecimento de enclave e implante algoritmos confidenciais usando a estrutura de inferência confidencial.
- Escreva aplicativos para execução em VMs DCsv2: [SDK do Open-enclave](https://github.com/openenclave/openenclave)
- Modelos de ML confidenciais no runtime do ONNX: [Inferência confidencial (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Próximas etapas

Implante uma máquina virtual da Série DCsv2 e instale o OE SDK nela.

> [!div class="nextstepaction"]
> [Implantar uma VM de computação confidencial no Azure Marketplace](quick-create-marketplace.md)