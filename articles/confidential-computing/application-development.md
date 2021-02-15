---
title: Ferramentas de desenvolvimento de computação confidencial do Azure
description: Use ferramentas e bibliotecas para desenvolver aplicativos para computação confidencial
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 6bb3b8dbc7887419f7901a52b56c25f60c869abb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994362"
---
# <a name="application-development-on-intel-sgx"></a>Desenvolvimento de aplicativos no Intel SGX 


A infraestrutura de computação confidencial requer ferramentas e software específicos. Esta página aborda especificamente os conceitos relacionados ao desenvolvimento de aplicativos para máquinas virtuais de computação confidencial do Azure em execução no Intel SGX. Antes de ler esta página, [Leia a introdução de máquinas virtuais de SGX e enclaves da Intel](confidential-computing-enclaves.md). 

Para aproveitar o potencial dos enclaves e dos ambientes isolados, você precisará usar ferramentas compatíveis com a computação confidencial. Há várias ferramentas que compatíveis com o desenvolvimento de aplicativos de enclave. Por exemplo, você pode usar essas estruturas de software livre: 

- [O SDK do OE (Software Development Kit) do Open enclave](#oe-sdk)
- [O CCF (Confidential Consortium Framework)](#ccf)

## <a name="overview"></a>Visão geral

Um aplicativo criado com enclaves é particionado de duas maneiras:

1. Um componente "não confiável" (o host)
1. Um componente "confiável" (o enclave)


![Desenvolvimento de aplicativos](media/application-development/oe-sdk.png)


**O host** é onde o aplicativo enclave está em execução e é um ambiente não confiável. O código de enclave implantado no host não pode ser acessado pelo host. 

**O enclave** é onde o código do aplicativo e seus dados/memória em cache são executados. As computações protegidas devem ocorrer no enclave para garantir que os segredos e os dados confidenciais permaneçam protegidos. 


Durante o design do aplicativo, é importante identificar e determinar qual parte do aplicativo precisa ser executada nos enclaves. O código que você escolhe colocar no componente confiável é isolado do restante do aplicativo. Depois que o enclave é inicializado e o código é carregado na memória, esse código não pode ser lido nem alterado usando os componentes não confiáveis. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>OE SDK (Open Enclave Software Development Kit) <a id="oe-sdk"></a>

Use uma biblioteca ou uma estrutura compatível com seu provedor se quiser escrever um código que seja executado em um enclave. O [OE SDK](https://github.com/openenclave/openenclave) (Open Enclave SDK) é um SDK de software livre que permite a abstração em um hardware habilitado para computação confidencial diferente. 

O OE SDK é criado para ser uma camada de abstração única em qualquer hardware em qualquer CSP. O OE SDK pode ser usado sobre máquinas virtuais de computação confidencial do Azure para criar e executar aplicativos sobre enclaves.

## <a name="confidential-consortium-framework-ccf"></a>CCF (estrutura de consórcio confidencial) <a id="ccf"></a>

O [CCF](https://github.com/Microsoft/CCF) é uma rede distribuída de nós, cada um executando seu próprio enclaves. A rede de nó confiável permite que você execute um razão distribuído. O razão fornece componentes seguros e confiáveis para o protocolo usar. 

![Nós CCF](media/application-development/ccf.png)

Essa estrutura de código-fonte aberto permite uma alta confidencialidade refinada e a governança do consórcio para blockchain. Com cada nó usando TEEs, você pode garantir um consenso seguro e o processamento de transações.


## <a name="next-steps"></a>Próximas etapas 
- [Implantar uma computação confidencial DCsv2-Series máquina virtual](quick-create-portal.md)
- [Baixe e instale o SDK do OE e comece a desenvolver aplicativos](https://github.com/openenclave/openenclave)