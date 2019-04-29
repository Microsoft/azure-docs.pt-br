---
title: Introdução ao TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Hospede novamente seu IBM z/OS mainframe cargas de trabalho usando o ambiente de TmaxSoft OpenFrame em máquinas virtuais do Azure (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485307"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introdução ao TmaxSoft OpenFrame no Azure

Levar seus ativos existentes do mainframe e movê-los ao Microsoft Azure usando TmaxSoft OpenFrame. Essa solução rehosting popular cria um ambiente de emulação no Azure, permitindo que você migrar rapidamente aplicativos. Não é necessário nenhum reformatação.

## <a name="openframe-rehosting-environment"></a>Ambiente de rehosting OpenFrame

Configure um ambiente de OpenFrame no Azure para desenvolvimento, demonstrações, teste ou cargas de trabalho de produção. Como mostra a figura a seguir, OpenFrame inclui vários componentes que criar o ambiente de emulação de mainframe no Azure. Por exemplo, serviços online da OpenFrame substituem o middleware de mainframe, como IBM Customer informações de controle de sistema (CICS). Lote OpenFrame, com seu componente TJES, substitui o subsistema de entrada de trabalho de (JES da mainframe IBM). 

![Processo de rehosting OpenFrame](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente OpenFrame no Azure, você deve ter uma licença de produto válida ou uma licença de avaliação do TmaxSoft.

## <a name="openframe-components"></a>Componentes de OpenFrame

Os seguintes componentes são parte do ambiente OpenFrame no Azure:

- **As ferramentas de migração** incluindo OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.
- **Compiladores**, incluindo OFCOBOL, um compilador que interpreta os programas de COBOL do mainframe; OFPLI, que interpreta PL do mainframe / eu programas; e OFASM, um compilador que interpreta os programas do assembler do mainframe.
- **Front-end** componentes, incluindo Java Enterprise usuário solução (JEUS), um servidor de aplicativos web que é certificado para 6.OFGW Java Enterprise Edition e o componente de gateway OpenFrame que fornece um ouvinte 3270.
- **Aplicativo** ambiente. OpenFrame Base é o middleware que gerencia todo o sistema. OpenFrame servidor tipo C (OSC) substitui o mainframe middleware e IBM CICS.
- **Banco de dados relacional**, como Tibero (mostrada), banco de dados Oracle, Microsoft SQL Server, IBM Db2 ou MySQL. Os aplicativos de OpenFrame usam o protocolo de conectividade de banco de dados aberto (ODBC) para se comunicar com o banco de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso de usuário aos sistemas e recursos. 
- **OFManager** é uma solução que fornece funções de gerenciamento e operação do OpenFrame no ambiente de web.

![Arquitetura de OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Instalar o TmaxSoft OpenFrame no Azure](./install-openframe-azure.md)
