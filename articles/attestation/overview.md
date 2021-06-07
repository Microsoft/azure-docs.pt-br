---
title: Visão geral do Atestado do Azure
description: Uma visão geral do Atestado do Microsoft Azure, uma solução usada para atestar TEEs (Ambientes de Execução Confiáveis)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 020ba74948a062d23d61272ee912eb3364180f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617991"
---
# <a name="microsoft-azure-attestation"></a>Atestado do Microsoft Azure 

O Atestado do Microsoft Azure é uma solução unificada para verificar remotamente a confiabilidade de uma plataforma e a integridade dos binários em execução dentro dela. O serviço dá suporte ao atestado das plataformas com suporte de TPMs (Trusted Platform Modules) juntamente com a capacidade de atestar TEEs (Ambientes de Execução Confiável), como enclaves [Intel® SGX](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (Software Guard Extensions) e enclaves [VBS](/windows-hardware/design/device-experiences/oem-vbs) (Segurança baseada em Virtualização). 

O atestado é um processo usado para demonstrar que os binários de software foram instanciados corretamente em uma plataforma confiável. Em seguida, as partes confiáveis remotas podem ter a confiança de que apenas esse software pretendido está sendo executado no hardware confiável. O Atestado do Azure é um serviço unificado voltado ao cliente e uma estrutura para o atestado.

O Atestado do Azure permite paradigmas de segurança de ponta, como a [Computação confidencial do Azure](../confidential-computing/overview.md) e a proteção de borda inteligente. Os clientes estão solicitando a capacidade de verificar de maneira independente a localização de um computador, a postura de uma VM (máquina virtual) nesse computador e o ambiente no qual os enclaves estão em execução nessa VM. O Atestado do Azure capacitará essas e muitas solicitações de clientes adicionais.

O Atestado do Azure recebe evidências de entidades de computação, transforma-as em um conjunto de declarações, valida-as em relação às políticas configuráveis e produz provas de criptografia para aplicativos baseados em declarações (por exemplo, partes confiáveis e autoridades de auditoria).

## <a name="use-cases"></a>Casos de uso

O Atestado do Azure fornece serviços de atestado abrangentes para vários ambientes e casos de uso diferentes.

### <a name="sgx-attestation"></a>Atestado do SGX

O SGX refere-se ao isolamento no nível de hardware, que é compatível com alguns modelos de CPUs Intel. O SGX permite que o código seja executado em compartimentos corrigidos conhecidos como enclaves do SGX. Depois, as permissões de acesso e de memória são gerenciadas pelo hardware para garantir uma superfície de ataque mínima com isolamento adequado.

Os aplicativos cliente podem ser criados para aproveitar os enclaves do SGX, delegando a ocorrência de tarefas de segurança confidenciais nesses enclaves. Em seguida, esses aplicativos podem usar o Atestado do Azure para estabelecer a confiança rotineiramente no enclave e a capacidade de acessar dados confidenciais.

### <a name="open-enclave"></a>Open Enclave
O [OE](https://openenclave.io/sdk/) (Open Enclave) é uma coleção de bibliotecas destinadas à criação de uma abstração unificada de enclaves para que os desenvolvedores criem aplicativos baseados em TEE. Ele oferece um modelo de aplicativo seguro universal que minimiza as especificidades de plataforma. A Microsoft o considera como uma base essencial para a democratização das tecnologias de enclave baseadas em hardware, como o SGX, e para a adoção delas no Azure.

O OE padroniza os requisitos específicos para a verificação de uma evidência de enclave. Isso qualifica o OE como um consumidor de atestado altamente qualificado do Atestado do Azure.

### <a name="tpm-attestation"></a>Atestado de TPM 

O atestado baseado em TPM (Trusted Platform Module) é crítico para fornecer a prova do estado de uma plataforma. O TPM funciona como a raiz de confiança e o coprocessador de segurança para fornecer a validade de criptografia para measurements(evidence). Os dispositivos com um TPM podem contar com o atestado para provar que a integridade da inicialização não está comprometida com o uso das declarações para detectar as habilitações dos estados do recurso durante a inicialização. 

Os aplicativos cliente podem ser criados para aproveitar o atestado TPM, delegando tarefas confidenciais de segurança para ocorrerem apenas depois que uma plataforma for validada para ser segura. Em seguida, esses aplicativos podem usar o Atestado do Azure para estabelecer a confiança rotineiramente na plataforma e a capacidade dela de acessar dados confidenciais.

## <a name="azure-attestation-can-run-in-a-tee"></a>O Atestado do Azure pode ser executado em um TEE

O Atestado do Azure é crítico para cenários de Computação Confidencial, pois ele executa as seguintes ações:

- Verifica se as evidências do enclave são válidas.
- Avalia as evidências do enclave em relação a uma política definida pelo cliente.
- Gerencia e armazena políticas específicas do locatário.
- Gera e assina um token que é usado por terceiras partes confiáveis para interagir com o enclave.

O Atestado do Azure foi criado para ser executado em dois tipos de ambientes:
- O Atestado do Azure em execução em um SGX habilitado para TEE.
- O Atestado do Azure em execução em um não TEE.

Os clientes do Atestado do Azure expressaram um requisito para que a Microsoft fique operacionalmente fora da TCB (base de computação confiável). Isso serve para impedir que entidades da Microsoft, como administradores de VMs, administradores do host e desenvolvedores da Microsoft, modifiquem solicitações de atestado, políticas e tokens emitidos pelo Atestado do Azure. O Atestado do Azure também foi criado para ser executado no TEE, em que os recursos do Atestado do Azure, como validação da cotação e geração e assinatura de token, são movidos para um enclave do SGX.

## <a name="why-use-azure-attestation"></a>Por que usar o Atestado do Azure

O Atestado do Azure é a escolha preferencial para atestar os TEEs, pois oferece os seguintes benefícios: 

- Estrutura unificada para atestar vários ambientes como enclaves TPMs, SGX e VBS 
- Serviço multilocatário que permite a configuração de provedores de atestado personalizados e políticas para restringir a geração de token
- Oferece provedores compartilhados regionais que podem fornecer um atestado sem nenhuma configuração por parte dos usuários
- Protege os dados durante o uso com a implementação em um enclave do SGX
- Serviço altamente disponível 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Suporte de BCDR (continuidade dos negócios e recuperação de desastres)

A [BCDR](../best-practices-availability-paired-regions.md) (continuidade dos negócios e recuperação de desastres) do Atestado do Azure permite reduzir as interrupções de serviço resultantes de problemas de disponibilidade ou eventos de desastres significativos em uma região.

Os clusters implantados em duas regiões funcionarão de maneira independente em circunstâncias normais. No caso de uma falha ou uma interrupção de uma região, ocorrerá o seguinte:

- A BCDR do Atestado do Azure fornecerá um failover contínuo no qual os clientes não precisam realizar nenhuma etapa extra para recuperação
- O [Gerenciador de Tráfego do Azure](../traffic-manager/index.yml) para a região detectará se a investigação de integridade está degradada e alternará o ponto de extremidade para a região emparelhada
- As conexões existentes não funcionarão e receberão problemas de erro interno do servidor ou de tempo limite
- Todas as operações do painel de controle serão bloqueadas. Os clientes não poderão criar provedores de atestado na região primária
- Todas as operações de plano de dados, incluindo chamadas de atestado e configuração de política, serão atendidas pela região secundária. Os clientes podem continuar trabalhando nas operações do plano de dados com o URI original correspondente à região primária

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [conceitos básicos do Atestado do Azure](basic-concepts.md)
- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
