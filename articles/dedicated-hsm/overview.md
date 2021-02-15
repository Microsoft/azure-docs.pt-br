---
title: O que é HSM Dedicado? – HSM Dedicado do Azure | Microsoft Docs
description: Saiba como o HSM Dedicado do Azure é um serviço do Azure que fornece armazenamento de chave de criptografia no Azure.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 6eb82400b2f598b4b54da4b238f4bce71d83de6b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90970202"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o HSM dedicado do Azure?

O HSM dedicado do Azure é um serviço do Azure que fornece armazenamento de chave de criptografia no Azure. O HSM dedicado atende aos requisitos de segurança mais rigorosos. É a solução ideal para clientes que precisam de dispositivos validados pelo padrão FIPS 140-2 Nível 3 e de controle total e exclusivo do dispositivo HSM. 

 Os dispositivos HSM são implantados globalmente em várias regiões do Azure. Eles podem ser provisionados com facilidade como um par de dispositivos e configurados para alta disponibilidade. Os dispositivos HSM também podem ser provisionados entre regiões para proteção contra failover em nível regional. A Microsoft fornece o serviço HSM Dedicado usando o dispositivo [SafeNet Luna Network HSM 7 (Modelo A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) da Gemalto. Esse dispositivo oferece os mais altos níveis de desempenho e opções de integração de criptografia. 

Depois de provisionados, os dispositivos HSM são conectados diretamente à rede virtual de um cliente. Eles também podem ser acessados pelas ferramentas de gerenciamento e aplicativo locais quando você configura a conectividade VPN ponto a site ou site a site. Os clientes obtêm o software e a documentação para configurar e gerenciar os dispositivos HSM no portal de suporte da Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Por que usar o HSM Dedicado do Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformidade com o padrão FIPS 140-2 Nível 3

Muitas organizações têm regulamentos rígidos do setor que exigem que o armazenamento de chaves de criptografia atenda aos requisitos do padrão [FIPS 140-2 Nível 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). O Serviço Azure Key Vault multilocatário da Microsoft atualmente fornece certificação apenas para o padrão FIPS 140-2 Nível 2. O HSM Dedicado do Azure atende a uma necessidade real do setor de serviços financeiros, das agências governamentais e de outros que precisam atender aos requisitos do padrão FIPS 140-2 Nível 3.

### <a name="single-tenant-devices"></a>Dispositivos de locatário único

Muitos de nossos clientes são obrigados a ter um único locatário no dispositivo de armazenamento de criptografia. O serviço HSM Dedicado do Azure permite o provisionamento de um dispositivo físico por meio de um dos datacenters da Microsoft distribuídos globalmente. Depois que ele é provisionado para um cliente, somente esse cliente pode acessar o dispositivo.

### <a name="full-administrative-control"></a>Controle administrativo total

Muitos clientes precisam de controle administrativo total e acesso exclusivo aos seus dispositivos para fins administrativos. Depois que um dispositivo é provisionado, apenas o cliente tem acesso ao dispositivo no nível administrativo ou de aplicativo.

 A Microsoft não tem nenhum controle administrativo depois que o cliente acessa o dispositivo pela primeira vez, momento em que o cliente altera a senha. Daí em diante, o cliente é realmente um locatário único com controle administrativo total e capacidade de gerenciamento de aplicativos. A Microsoft mantém o acesso no nível do monitor (não uma função de administrador) para telemetria por meio da conexão de porta serial. Esse acesso abrange monitores de hardware, como temperatura, integridade da alimentação e integridade do ventilador. 
 
 O cliente é livre para desabilitar esse monitoramento necessário. No entanto, se ele desabilitá-lo, ele não receberá alertas proativos de integridade da Microsoft.

### <a name="high-performance"></a>Alto desempenho

O dispositivo da Gemalto foi selecionado para esse serviço por uma variedade de motivos. Ele oferece uma ampla gama de suporte ao algoritmo de criptografia, uma variedade de sistemas operacionais compatíveis e um amplo suporte a APIs. O modelo específico implantado oferece um desempenho excelente, com 10 mil operações por segundo para o RSA-2048. Ele dá suporte a 10 partições que podem ser usadas para instâncias de aplicativo exclusivas. Esse dispositivo é um dispositivo de baixa latência, alta capacidade e alta taxa de transferência.

### <a name="unique-cloud-based-offering"></a>Oferta exclusiva baseada em nuvem

A Microsoft reconheceu uma necessidade específica de um conjunto exclusivo de clientes. Ela é o único provedor de nuvem que oferece aos novos clientes um serviço HSM dedicado validado para o padrão FIPS 140-2 Nível 3 e que oferece uma ampla gama de integração de aplicativos locais e baseados em nuvem.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>O HSM Dedicado do Azure é a opção certa para você?

O HSM Dedicado do Azure é um serviço especializado que atende a requisitos exclusivos de um tipo específico de organização de grande porte. Como resultado, é esperado que a maior parte dos clientes do Azure não se encaixe no perfil de uso desse serviço. Muitos concluirão que o serviço Azure Key Vault é mais apropriado e econômico. Para ajudá-lo a decidir se ele é adequado aos seus requisitos, identificamos os critérios a seguir.

### <a name="best-fit"></a>Mais adequado

O HSM Dedicado do Azure é mais adequado para cenários de “lift-and-shift” que exigem acesso direto e exclusivo a dispositivos HSM. Os exemplos incluem:

- Migração de aplicativos locais para Máquinas Virtuais do Azure
- Migração de aplicativos do Amazon AWS EC2 para máquinas virtuais que usam o serviço AWS Cloud HSM Classic (a Amazon não oferece esse serviço para novos clientes)
- Execução de software compactado, como Apache/Ngnix SSL Offload, Oracle TDE e ADCS, em Máquinas Virtuais do Azure 

### <a name="not-a-fit"></a>Inadequado

O HSM Dedicado do Azure não é uma boa opção para o seguinte tipo de cenário: Serviços em nuvem da Microsoft que dão suporte à criptografia com chaves gerenciadas pelo cliente (como Proteção de Informações do Azure, Azure Disk Encryption, Azure Data Lake Storage, Armazenamento do Azure, Banco de Dados SQL do Azure, Chave de Cliente do Office 365) que não estão integrados ao HSM Dedicado do Azure.

### <a name="it-depends"></a>Isso depende

Se o HSM Dedicado do Azure funcionará para você, isso dependerá de uma combinação potencialmente complexa de requisitos e compromissos que você pode ou não fazer. Um exemplo é o requisito do padrão FIPS 140-2 Nível 3. Esse requisito é comum e o HSM Dedicado atualmente é a única opção que atende a ele. Se esses requisitos obrigatórios não forem relevantes, geralmente, esta será uma escolha entre o Azure Key Vault e o HSM Dedicado. Avalie seus requisitos antes de tomar uma decisão.

As situações em que você precisará ponderar suas opções incluem: 

- Novo código em execução na máquina virtual do Azure de um cliente
- TDE do SQL Server em uma máquina virtual do Azure
- Criptografia do Armazenamento do Azure no lado do cliente
- SQL Server e Banco de Dados SQL do Azure Always Encrypted

## <a name="next-steps"></a>Próximas etapas

Esse é um serviço altamente especializado. Portanto, recomendamos que você entenda os principais conceitos nesta documentação, incluindo preço, suporte e Contratos de Nível de Serviço. 

Os [guias de integração da Gemalto](https://safenet.gemalto.com/partners/microsoft/) ajudam você a facilitar o provisionamento de HSMs em um ambiente de rede virtual existente. Há também guias de instruções para ajudá-lo a determinar como configurar sua arquitetura de implantação.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoring](monitoring.md)
