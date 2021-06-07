---
title: O que é Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece uma introdução para o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869949"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>O que é Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Aplicativo Azure ferramenta de instantâneo consistente (AzAcSnap) é uma ferramenta de linha de comando que permite a proteção de dados para bancos de dado de terceiros ao lidar com toda a orquestração necessária para colocá-los em um estado consistente de aplicativo antes de tirar um instantâneo de armazenamento, após o qual ele os retorna para um estado operacional.

## <a name="supported-platforms-and-os"></a>Plataformas e sistema operacional com suporte

- **Bancos de dados**
  - SAP HANA (consulte [matriz de suporte](azacsnap-get-started.md#snapshot-support-matrix-from-sap) para obter detalhes)

- **Sistemas Operacionais**
  - SUSE Linux Enterprise Server 12 +
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>Benefícios do uso do AzAcSnap

O AzAcSnap aproveita as funcionalidades de replicação e instantâneo de volume do Azure NetApp Files e da Instância Grande do Azure.  Ele oferece os seguintes benefícios:

- **Proteção de dados consistente com o aplicativo** AzAcSnap é uma solução centralizada para fazer backup de arquivos de banco de dados críticos. Ele garante a consistência do banco de dados antes de executar um instantâneo do volume de armazenamento. Como resultado, ele garante que o instantâneo do volume de armazenamento possa ser usado para recuperação do banco de dados.
- **Gerenciamento de catálogo de banco de dados** Quando você usa AzAcSnap com um banco de dados que tem um catálogo de backup interno, os registros no catálogo são mantidos atuais com instantâneos de armazenamento.  Essa funcionalidade permite que um administrador de banco de dados Veja a atividade de backup.
- **Proteção de volume ad hoc** Esse recurso é útil para volumes que não são de banco de dados que não precisam de um desativação do aplicativo antes de tirar um instantâneo de armazenamento.  Os exemplos incluem SAP HANA volumes de backup de log ou volumes SAPTRANS.
- **Clonagem de volumes de armazenamento** Esse recurso fornece clones de volume de armazenamento com economia de espaço para fins de desenvolvimento e teste.
- **Suporte para recuperação de desastre** O AzAcSnap aproveita a replicação do volume de armazenamento para fornecer opções de recuperação de instantâneos consistentes com aplicativos replicados em um site remoto.

AzAcSnap é um único binário.  Ele não precisa de agentes ou plug-ins adicionais para interagir com o banco de dados ou o armazenamento (Azure NetApp Files por meio de Azure Resource Manager e instância grande do Azure via SSH).  O AzAcSnap deve ser instalado em um sistema que tenha conectividade com o banco de dados e o armazenamento.  No entanto, a flexibilidade de instalação e configuração permite uma instalação centralizada única ou uma instalação totalmente distribuída com cópias instaladas em cada instalação de banco de dados.

## <a name="architecture-overview"></a>Visão geral da arquitetura

O AzAcSnap pode ser instalado no mesmo host que o banco de dados (SAP HANA) ou pode ser instalado em um sistema centralizado.  No entanto, deve haver conectividade de rede com os servidores de banco de dados e o back-end de armazenamento (Azure Resource Manager para Azure NetApp Files ou SSH para instância grande do Azure).

AzAcSnap é um aplicativo leve que normalmente é executado a partir de um Agendador externo.  Na maioria dos sistemas Linux, essa operação é `cron` , em que a documentação se concentrará.  Mas o Agendador pode ser uma ferramenta alternativa, desde que possa importar o `azacsnap` perfil de shell do usuário.  A importação das configurações de ambiente do usuário garante que os caminhos e as permissões do arquivo sejam inicializados corretamente.

## <a name="command-synopsis"></a>Sinopse do comando

O formato geral dos comandos é o seguinte: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Opções de comando

As opções de comando são as seguintes com os comandos como marcadores principais e os subcomandos associados como marcadores recuados:

- **`-h`** fornece ajuda de linha de comando estendida com exemplos de uso AzAcSnap.
- **`-c configure`** Este comando fornece um Q interativo&uma interface de estilo para criar ou modificar o `azacsnap` arquivo de configuração (padrão = `azacsnap.json` ).
  - **`--configuration new`** criará um novo arquivo de configuração.
  - **`--configuration edit`** Editará um arquivo de configuração existente.
  - consulte [Configurar referência de comando](azacsnap-cmd-ref-configure.md).
- **`-c test`** valida o arquivo de configuração e a conectividade de teste.
  - **`--test hana`**  testa a conexão com a instância de SAP HANA.
  - **`--test storage`** testa a comunicação com a interface de armazenamento subjacente criando um instantâneo de armazenamento temporário em todos os volumes configurados `data` e, em seguida, removendo-os.
  - **`--test all`** executará os `hana` testes e `storage` em sequência.
  - consulte a [referência do comando de teste](azacsnap-cmd-ref-test.md).
- **`-c backup`** o é o principal comando para executar instantâneos de armazenamento consistentes de banco de dados (SAP HANA volumes de dados) & outros (por exemplo, volumes compartilhados, backups de log ou inicialização).
  - **`--volume data`** para fazer o instantâneo de todos os volumes no `dataVolume` Stanza do arquivo de configuração.
  - **`--volume other`** para fazer o instantâneo de todos os volumes no `otherVolume` Stanza do arquivo de configuração.
  - consulte a [referência de comando de backup](azacsnap-cmd-ref-backup.md).
- **`-c details`** fornece informações sobre instantâneos ou replicação.
  - **`--details snapshots`** Fornece uma lista de detalhes básicos sobre os instantâneos para cada volume que foi configurado.
  - **`--details replication`** Fornece detalhes básicos sobre o status de replicação do site de produção para o local de recuperação de desastre.
  - consulte a [referência de comando de detalhes](azacsnap-cmd-ref-details.md).
- **`-c delete`** Este comando exclui um instantâneo de armazenamento ou um conjunto de instantâneos. Você pode usar a ID de backup SAP HANA conforme encontrada no HANA Studio ou no nome do instantâneo de armazenamento. A ID de backup só está vinculada aos `hana` instantâneos, que são criados para os dados e volumes compartilhados. Caso contrário, se o nome do instantâneo for inserido, ele pesquisará todos os instantâneos que correspondam ao nome do instantâneo inserido.
  - consulte [excluir](azacsnap-cmd-ref-delete.md).
- **`-c restore`** o fornece dois métodos para restaurar um instantâneo para um volume, criando um novo volume com base no instantâneo ou revertendo um volume para um estado de versão prévia.
  - **`--restore snaptovol`** Cria um novo volume com base no instantâneo mais recente no volume de destino.
  - **`-c restore --restore revertvolume`** Reverte o volume de destino para um estado anterior com base no instantâneo mais recente.
  - consulte a [referência do comando Restore](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** O parâmetro de linha de comando opcional para fornecer um nome de arquivo de configuração JSON diferente.  Isso é particularmente útil para criar um arquivo de configuração separado por SID (por exemplo, `--configfile H80.json` ).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Aplicativo Azure ferramenta de instantâneo consistente](azacsnap-get-started.md)
