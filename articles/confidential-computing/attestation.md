---
title: Atestando enclaves no Azure
description: Saiba como você pode usar o atestado para verificar se o seu ambiente confiável de computação confidencial é seguro
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551344"
---
# <a name="attesting-sgx-enclaves"></a>Atestado de enclaves SGX

Computação confidencial no Azure oferece máquinas virtuais baseadas em SGX Intel que podem isolar uma parte do código ou dos dados. Ao trabalhar com esses [enclaves](confidential-computing-enclaves.md), você desejará obter verificação e validação de que seu ambiente confiável é seguro. Essa verificação é o processo de atestado. 

## <a name="overview"></a>Visão geral 

O atestado permite que uma terceira parte confiável tenha maior confiança de que seu software está (1) em execução em um enclave e (2) que o enclave está atualizado e seguro. Por exemplo, um enclave solicita que o hardware subjacente gere uma credencial que inclua uma prova de que o enclave existe na plataforma. O relatório pode ser dado a um segundo enclave que verifica se o relatório foi gerado na mesma plataforma.

![código de atestado no enclave](media/attestation/attestation.png)



O atestado deve ser implementado usando um serviço de atestado seguro que seja compatível com o software do sistema e com o chip. Alguns exemplos de serviços que você pode usar são

- [Atestado de Microsoft Azure (versão prévia)](../attestation/overview.md) ou
- [Serviços de atestado e provisionamento da Intel](https://software.intel.com/sgx/attestation-services)


que são compatíveis com a infraestrutura de computação confidencial do Azure Intel SGX. 

## <a name="next-steps"></a>Próximas etapas
Experimente os [exemplos de atestado de Microsoft Azure para aplicativos com reconhecimento de enclave](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).