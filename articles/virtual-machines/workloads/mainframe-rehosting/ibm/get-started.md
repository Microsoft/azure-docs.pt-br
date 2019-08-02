---
title: Cargas de trabalho IBM no Azure | Microsoft Docs
description: Use um emulador de mainframe e outros serviços de parceiros da Microsoft para hospedar novamente suas cargas de trabalho IBM z/OS usando o Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 4acf2fe1bc63061f17f90d6737f586408520dc13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621313"
---
# <a name="ibm-workloads-on-azure"></a>Cargas de trabalho IBM no Azure

Muitas cargas de mainframe IBM com base em z/OS podem ser replicadas no Azure sem perda de funcionalidade e sem que os usuários até mesmo observar que as alterações em seus sistemas subjacentes. Nova hospedagem de aplicativos no Azure fornece os recursos de mainframe como você precisa além de elasticidade, disponibilidade, e a potencial economia nos custos de nuvem.

Soluções híbridas de execução do Azure dá suporte à integração com ambientes de mainframe IBM existentes, permitindo que você migre o applicates que façam sentido, onde necessário e migrar ao longo do tempo. Embora você pode reescrever totalmente os programas existentes baseados em mainframe para o Azure, é mais comum para o novo host-los. Regravação adiciona custo, complexidade e o tempo para projetos de migração. Com a nova hospedagem, você pode:

- Mova aplicativos para um emulador baseado em nuvem.

- Migre o banco de dados para um banco de dados baseado em nuvem.

- Substitua o código usando mecanismos de transformação de código e módulos.

Além disso, o software IBM, incluindo WebSphere e MQ, está agora no Azure Marketplace. Com uma licença para o software IBM, é possível aproveitar a colocação em escala da infraestrutura sob demanda fornecida pelo Azure para criar rapidamente uma máquina virtual.

Um ecossistema de parceria extenso está disponível para ajudá-lo a migrar sistemas de mainframe IBM para o Azure. A maioria segue uma abordagem pragmática da reutilização sempre que possível antes de embarcar em uma implantação em fases da reconfiguração ou substituição de aplicativos. Obtenha mais orientação e ajuda de parceiros na [Centro de migração de Mainframe Azure](https://azure.microsoft.com/migration/mainframe/).

**Próximas etapas**

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instalar o IBM zD & o ambiente de desenvolvimento/teste T no Azure](./install-ibm-z-environment.md)
- [Defina o backup de um aplicativo desenvolvedores controlado por distribuição (ADCD) no IBM zD & T v1](./demo.md)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
