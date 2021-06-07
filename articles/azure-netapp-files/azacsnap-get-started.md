---
title: Introdução ao Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para instalar o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736355"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Introdução ao Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece um guia para a instalação do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Obtendo as ferramentas de instantâneo

É recomendável que os clientes obtenham a versão mais recente do [instalador do AzAcSnap](https://aka.ms/azacsnapdownload) da Microsoft.

O arquivo de instalação automática tem um [arquivo de assinatura do instalador AzAcSnap](https://aka.ms/azacsnapdownloadsignature) associado que é assinado com a chave pública da Microsoft para permitir a verificação do gpg do instalador baixado.

Depois que esses downloads forem concluídos, siga as etapas neste guia para instalar o.

### <a name="verifying-the-download"></a>Verificando o download

O instalador, que é baixado por acima, tem um arquivo de assinatura PGP associado com uma `.asc` extensão de nome de arquivo. Esse arquivo pode ser usado para garantir que o instalador baixado é um arquivo fornecido da Microsoft verificado. A chave pública PGP da Microsoft usada para assinar pacotes do Linux está disponível aqui ( <https://packages.microsoft.com/keys/microsoft.asc> ) e foi usada para assinar o arquivo de assinatura.

A chave pública PGP da Microsoft pode ser importada para o local de um usuário da seguinte maneira:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Os seguintes comandos confiam na chave pública do PGP da Microsoft:

1. Liste as chaves no repositório.
2. Edite a chave da Microsoft.
3. Verifique a impressão digital com `fpr`
4. Assine a chave para confiar nela.

```bash
gpg --list-keys
```

Chaves listadas:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Saída de assinatura interativa da `gpg` sessão chave pública da Microsoft:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

O arquivo de assinatura do PGP para o instalador pode ser verificado da seguinte maneira:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Para obter mais detalhes sobre como usar o GPG, consulte [o manual de privacidade do GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Cenários com suporte

As ferramentas de instantâneo podem ser usadas nos cenários a seguir.

- SID único
- SID múltiplo
- HSR
- Escalabilidade horizontal
- MDC (apenas um único locatário com suporte)
- Contêiner Único
- Sistema operacional SUSE
- Sistema operacional RHEL
- TIPO DE SKU I
- TIPO DE SKU II

Consulte [cenários com suporte para instâncias grandes do Hana](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Matriz de suporte de instantâneo do SAP

A matriz a seguir é fornecida como uma diretriz sobre quais versões do SAP HANA são suportadas pelo SAP para backups de instantâneo de armazenamento.

| Versões do banco de dados       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Banco de dados de contêiner único| √       | √      | -      | -      | -      | -      |
|Único locatário MDC        | -       | -      | √      | √      | √      | √      |
|Vários locatários MDC     | -       | -      | -      | -      | -      | √      |
> √ = <small>com suporte pelo SAP para instantâneos de armazenamento</small>

## <a name="important-things-to-remember"></a>Itens importantes a lembrar

- Após a configuração das ferramentas de instantâneo, Monitore continuamente o espaço de armazenamento disponível e, se necessário, exclua os instantâneos antigos regularmente para evitar o preenchimento do armazenamento.
- Sempre use as ferramentas de instantâneo mais recentes.
- Use a mesma versão das ferramentas de instantâneo em todo o cenário.
- Teste as ferramentas de instantâneo e fique à vontade com os parâmetros necessários e a saída do comando antes de usar o no sistema de produção.
- Ao configurar o usuário do HANA para backup (detalhes abaixo neste documento), você precisa configurar o usuário para cada instância do HANA. Crie uma conta de usuário SAP HANA para acessar a instância do HANA no SYSTEMDB (e não no banco de dados SID) para MDC. No ambiente de contêiner único, ele pode ser configurado no banco de dados de locatário.
- Os clientes devem fornecer a chave pública SSH para acesso de armazenamento. Essa ação deve ser feita uma vez por nó e para cada usuário sob o qual o comando é executado.
- O número de instantâneos por volume é limitado a 250.
- Se você editar manualmente o arquivo de configuração, sempre use um editor de texto do Linux, como "vi", e não os editores do Windows, como o bloco de notas. Usar o editor do Windows pode corromper o formato do arquivo.
  - Configure `hdbuserstore` para que o usuário SAP Hana se comunique com SAP Hana.
- Para DR: as ferramentas de instantâneo devem ser testadas no nó de recuperação de desastre antes de a recuperação de desastre ser configurada.
- Monitorar o espaço em disco regularmente, a exclusão automatizada de logs é gerenciada com a `--trim` opção do   `azacsnap -c backup` para SAP Hana 2 e versões posteriores.
- **Risco de instantâneos não serem feitos** -as ferramentas de instantâneo só interagem com o nó do sistema SAP Hana especificado no arquivo de configuração.  Se esse nó ficar indisponível, não haverá um mecanismo para iniciar automaticamente a comunicação com outro nó.  
  - Para um **SAP HANA Scale-Out com o cenário de espera** , é comum instalar e configurar as ferramentas de instantâneo no nó mestre. Mas, se o nó mestre ficar indisponível, o nó em espera assumirá a função do nó mestre. Nesse caso, a equipe de implementação deve configurar as ferramentas de instantâneo em ambos os nós (mestre e em espera) para evitar qualquer instantâneo perdido. No estado normal, o nó mestre usará instantâneos do HANA iniciados pelo crontab, mas após o failover do nó mestre, esses instantâneos precisarão ser executados de outro nó, como o novo nó mestre (primeiro em espera). Para atingir esse resultado, o nó em espera precisaria da ferramenta de instantâneo instalada, da comunicação de armazenamento habilitada, do hdbuserstore configurado, `azacsnap.json` configurado e dos comandos do crontab preparados antes do failover.
  - Para um cenário de **HA SAP Hana HSR** , é recomendável instalar, configurar e agendar as ferramentas de instantâneo nos nós (primário e secundário). Em seguida, se o nó primário ficar indisponível, o nó secundário assumirá os instantâneos que estão sendo feitos no secundário. No estado normal, o nó primário usará instantâneos do HANA iniciados pelo crontab e o nó secundário tentaria fazer instantâneos, mas falhará, pois o primário está funcionando corretamente.  Mas, após o failover do nó primário, esses instantâneos serão executados a partir do nó secundário. Para obter esse resultado, o nó secundário precisa da ferramenta de instantâneo instalada, da comunicação de armazenamento habilitada, `hdbuserstore` configurada azacsnap.jsem configurada e o crontab habilitado antes do failover.

## <a name="guidance-provided-in-this-document"></a>Diretrizes fornecidas neste documento

As diretrizes a seguir são fornecidas para ilustrar o uso das ferramentas de instantâneo.

### <a name="taking-snapshot-backups"></a>Fazendo backups de instantâneo

- [Quais são os pré-requisitos para o instantâneo de armazenamento](azacsnap-installation.md#prerequisites-for-installation)
  - [Habilitar a comunicação com o armazenamento](azacsnap-installation.md#enable-communication-with-storage)
  - [Habilitar a comunicação com o SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Como tirar instantâneos manualmente](azacsnap-tips.md#take-snapshots-manually)
- [Como configurar o backup automático de instantâneo](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Como monitorar os instantâneos](azacsnap-tips.md#monitor-the-snapshots)
- [Como excluir um instantâneo?](azacsnap-tips.md#delete-a-snapshot)
- [Como restaurar um instantâneo](azacsnap-tips.md#restore-a-snapshot)
- [Como restaurar um `boot` instantâneo](azacsnap-tips.md#restore-a-boot-snapshot)
- [O que são fatos importantes a saber sobre os instantâneos](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Os instantâneos são testados para SID único e SID múltiplo.

### <a name="performing-disaster-recovery"></a>Executando a recuperação de desastre

- [Quais são os pré-requisitos para a configuração de DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Como configurar uma recuperação de desastre](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Como monitorar a replicação de dados do site primário para DR](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Como executar um failover para um site de DR?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Próximas etapas

- [Instalar Aplicativo Azure ferramenta de instantâneo consistente](azacsnap-installation.md)