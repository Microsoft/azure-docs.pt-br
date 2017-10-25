---
title: "Monitorando uma VM Linux com uma extensão de VM | Microsoft Docs"
description: "Saiba como usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico de uma VM do Linux no Azure."
services: virtual-machines-linux
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: b8c6e2e22d8478b6e92e7b7942f15d37a840fed3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico da VM do Linux

Este documento descreve a versão 2.3 da Extensão de Diagnóstico do Linux.

> [!IMPORTANT]
> Esta versão foi preterida e sua publicação pode ser cancelada a qualquer momento após 30 de junho de 2018. Ela foi substituída pela versão 3.0. Para obter mais informações, confira a [documentação da versão 3.0 da Extensão de Diagnóstico do Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Introdução

(**Observação**: a Extensão de Diagnóstico do Linux é de software livre no [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic), onde as informações mais atuais sobre a extensão foram publicadas pela primeira vez. Convém dar uma olhada na [página do GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) primeiro.)

A Extensão de Diagnóstico do Linux ajuda um usuário a monitorar as VMs Linux em execução no Microsoft Azure. Ela oferece os seguintes recursos:

* Coleta e carregamento de informações de desempenho do sistema, da VM Linux para a tabela de armazenamento do usuário, incluindo informações de diagnóstico e syslog.
* Permite que os usuários personalizem as métricas de dados que serão coletadas e carregadas.
* Permite que os usuários carreguem arquivos de log especificados em uma tabela de armazenamento designada.

Na versão 2.3 atual, os dados incluem:

* Todos os logs Rsyslog do Linux, incluindo logs de sistema, de segurança e de aplicativos.
* Todos os dados do sistema especificados [no site do System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders)(Soluções de Plataforma Cruzada do System Center).
* Arquivos de log especificados pelo usuário.

Essa extensão funciona com os modelos de implantação Clássico e do Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Versão atual da extensão e substituição de versões antigas

A versão mais recente da extensão é a **2.3** e **todas as versões antigas (2.0, 2.1 e 2.2) serão preteridas com suas publicações canceladas até o final deste ano (2017)**. Se você instalou a Extensão de Diagnóstico do Linux com a atualização automática de versão secundária desabilitada, é altamente recomendado que você desinstale a extensão e reinstale-a com a atualização automática de versão secundária habilitada. Em VMs clássicas (ASM), você pode conseguir isso especificando '2.*' como a versão, se você estiver instalando a extensão por meio da CLI do Azure XPLAT ou do Powershell. Em VMs ARM, você pode conseguir isso, incluindo ' "autoUpgradeMinorVersion": true' no modelo de implantação da VM. Além disso, todas as novas instalações da extensão devem ter a opção de atualização automática de versão secundária ativada.

## <a name="enable-the-extension"></a>Habilitar a extensão

Você pode habilitar essa extensão usando o [Portal do Azure](https://portal.azure.com/#), o Azure PowerShell ou os scripts da CLI do Azure.

Para exibir e configurar os dados de desempenho e do sistema diretamente do portal do Azure, siga [estas etapas no blog do Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Este artigo se concentra em como habilitar e configurar a extensão usando os comandos da CLI do Azure. Isso permite que você leia e exiba os dados diretamente da tabela de armazenamento.

Observe que os métodos de configuração descritos abaixo não funcionarão para o Portal do Azure. Para exibir e configurar os dados de desempenho e do sistema diretamente do Portal do Azure, a extensão deve ser habilitada no Portal.

## <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.0.6 ou posterior**.

  Observe que a maioria das imagens de galeria da VM Linux do Azure inclui a versão 2.0.6 ou posterior. É possível executar **WAAgent-version** para confirmar a versão que está instalada na VM. Se a VM estiver executando uma versão anterior à 2.0.6, execute [estas instruções no GitHub](https://github.com/Azure/WALinuxAgent "instruções") para atualizá-la.
* **CLI do Azure**. Siga [estas diretrizes de instalação da CLI](../../../cli-install-nodejs.md) para configurar o ambiente da CLI do Azure em seu computador. Depois de instalar a CLI do Azure, você poderá usar o comando **azure** em sua interface de linha de comando (Bash, Terminal ou prompt de comando) para acessar os comandos da CLI do Azure. Por exemplo:

  * Execute **azure vm extension set --help** para obter informações de ajuda detalhadas.
  * Execute **azure login** para entrar no Azure.
  * Execute **azure vm list** para listar todas as máquinas virtuais que você tem no Azure.
* Uma conta de armazenamento para armazenar os dados. Você precisará de um nome de conta de armazenamento criado anteriormente e de uma chave de acesso para carregar os dados no armazenamento.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Usar o comando da CLI do Azure para habilitar a Extensão de Diagnóstico do Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Cenário 1. Habilitar a extensão com o conjunto de dados padrão

Na versão 2.3 ou posterior, os dados padrão que serão coletados incluem:

* Todas as informações de Rsyslog (incluindo os logs de sistema, segurança e aplicativo).  
* Um conjunto principal de dados base do sistema. Observe que o conjunto de dados completo está descrito no [site do System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Se você quiser habilitar dados extras, continue com as etapas nos Cenários 2 e 3.

Etapa 1. Crie um arquivo chamado PrivateConfig.json com o conteúdo a seguir:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Etapa 2. Execute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Cenário 2: Personalizar a métrica do monitor de desempenho

Esta seção descreve como personalizar a tabela de dados de desempenho e diagnóstico.

Etapa 1. Crie um arquivo chamado PrivateConfig.json com o conteúdo descrito no Cenário 1. Além disso, crie um arquivo chamado PublicConfig.json. Especifique os dados específicos que deseja coletar.

Para conhecer todos os provedores e variáveis com suporte, consulte o [site do System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders). Você pode ter várias consultas e armazená-las em várias tabelas, acrescentando mais consultas ao script.

Por padrão, os dados de Rsyslog sempre são coletados.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Etapa 2. Execute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Cenário 3: Carregar os próprios arquivos de log

Esta seção descreve como coletar e carregar arquivos de log específicos em sua conta de armazenamento. Você precisa especificar o caminho até seu arquivo de log e o nome da tabela onde você deseja armazenar seu log. Você pode criar vários arquivos de log adicionando várias entradas de arquivo/tabela ao script.

Etapa 1. Crie um arquivo chamado PrivateConfig.json com o conteúdo descrito no Cenário 1. Em seguida, crie outro arquivo denominado PublicConfig.json com o conteúdo a seguir:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Etapa 2. Execute `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Observe que, com essa configuração nas versões de extensão anteriores a 2.3, todos os logs gravados em `/var/log/mysql.err` também podem ser duplicados para `/var/log/syslog` (ou `/var/log/messages`, dependendo da distribuição Linux). Se você quiser evitar esse registro duplicado, exclua o registro em log dos logs de recursos do `local6` em sua configuração de rsyslog. Isso depende da distribuição do Linux, mas em um sistema Ubuntu 14.04, o arquivo a ser modificado é o `/etc/rsyslog.d/50-default.conf` e você pode substituir a linha `*.*;auth,authpriv.none -/var/log/syslog` por `*.*;auth,authpriv,local6.none -/var/log/syslog`. Esse problema foi corrigido na versão mais recente de hotfix 2.3 (2.3.9007); portanto, se você tiver a versão de extensão 2.3, esse problema não deverá ocorrer. Se o problema ainda persistir mesmo após a reinicialização da VM, entre em contato conosco e nos ajude a descobrir por que a versão mais recente de hotfix não é instalada automaticamente.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Cenário 4: Parar a coleta de todos os logs pela extensão

Esta seção descreve como parar a coleta de logs pela extensão. Observe que o processo do agente de monitoramento ainda estará em execução mesmo com essa reconfiguração. Se quiser parar o completamente processo do agente de monitoramento, você poderá fazer isso desabilitando a extensão. O comando para desabilitar a extensão é `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Etapa 1. Crie um arquivo chamado PrivateConfig.json com o conteúdo descrito no Cenário 1. Crie outro arquivo denominado PublicConfig.json com o conteúdo a seguir:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Etapa 2. Execute **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Examinar os dados

Os dados de desempenho e diagnóstico são armazenados em uma tabela de Armazenamento do Azure. Revise [Como usar o Armazenamento de Tabelas do Azure no Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) para saber como acessar os dados na tabela de armazenamento por meio de scripts da CLI do Azure.

Além disso, você pode usar as ferramentas de interface do usuário a seguir para acessar os dados:

1. Gerenciador de Servidores do Visual Studio. Vá até sua conta de armazenamento. Depois que a VM for executada por aproximadamente cinco minutos, você deverá ver as quatro tabelas padrão: "LinuxCpu", "LinuxDisk", "LinuxMemory" e "Linuxsyslog". Clique duas vezes nos nomes de tabela para exibir os dados.
1. [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/ "Gerenciador de Armazenamento do Azure")(Soluções de Plataforma Cruzada do System Center).

![imagem](./media/diagnostic-extension/no1.png)

Se você tiver habilitado fileCfg ou perfCfg (conforme descrito nos Cenários 2 e 3), você poderá usar o Gerenciador de Servidores do Visual Studio e o Gerenciador de Armazenamento do Azure para exibir dados não padrão.

## <a name="known-issues"></a>Problemas conhecidos

* As informações do Rsyslog e o arquivo de log especificado pelo cliente só podem ser acessados por meio de scripts.
