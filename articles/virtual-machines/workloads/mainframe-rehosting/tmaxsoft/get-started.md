---
title: Introdução ao TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe do IBM z/OS usando o ambiente TmaxSoft OpenFrame em VMs (máquinas virtuais) do Azure.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: workloads
ms.openlocfilehash: a8210a47cdc31b28bd7dd2b0021570c18db89423
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558195"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introdução ao TmaxSoft OpenFrame no Azure

Pegue seus ativos de mainframe existentes e mova-os para Microsoft Azure usando TmaxSoft OpenFrame. Essa solução de rehospedagem popular cria um ambiente de emulação no Azure, permitindo que você migre rapidamente os aplicativos. Nenhuma reformatação é necessária.

## <a name="openframe-rehosting-environment"></a>Ambiente de rehospedagem de OpenFrame

Configure um ambiente OpenFrame no Azure para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Como mostra a figura a seguir, o OpenFrame inclui vários componentes que criam o ambiente de emulação de mainframe no Azure. Por exemplo, OpenFrame serviços online substituir o middleware de mainframe, como o sistema de controle de informações do cliente IBM (CICS). O OpenFrame batch, com seu componente TJES, substitui o JES (subsistema de entrada de trabalho) do IBM Mainframe. 

![Processo de rehospedagem de OpenFrame](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente OpenFrame no Azure, você deve ter uma licença de produto ou licença de avaliação válida do TmaxSoft.

## <a name="openframe-components"></a>Componentes do OpenFrame

Os componentes a seguir fazem parte do ambiente do OpenFrame no Azure:

- **Ferramentas de migração** , incluindo OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.
- **Compiladores**, incluindo OFCOBOL, um compilador que interpreta os programas COBOL do mainframe; OFPLI, que interpreta os programas PL/I do mainframe; e OFASM, um compilador que interpreta os programas de Assembler do mainframe.
- Componentes de **front-end** , incluindo Jeus (Java Enterprise User Solution), um servidor de aplicativos da Web que é certificado para o Java Enterprise Edition 6. OFGW e o componente de gateway OpenFrame que fornece um ouvinte 3270.
- Ambiente de **aplicativo** . OpenFrame base é o middleware que gerencia todo o sistema. O tipo de servidor OpenFrame C (OSC) substitui o middleware do mainframe e o IBM CICS.
- **Banco de dados relacional**, como Tibero (mostrado), Oracle Database, Microsoft SQL Server, IBM DB2 ou MySQL. Os aplicativos OpenFrame usam o protocolo ODBC (Open Database Connectivity) para se comunicar com o banco de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso do usuário a sistemas e recursos. 
- **OFManager** é uma solução que fornece funções de gerenciamento e operação de OpenFrame no ambiente da Web.

![Arquitetura do OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Instalar o TmaxSoft OpenFrame no Azure](./install-openframe-azure.md)
