---
title: Backup e restauração do HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como realizar o backup e a restauração do HANA no SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3244cbca3df717d3432670a366147408cf2b2c0e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669108"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Backup e restauração de SAP HANA em instâncias grandes HANA

>[!IMPORTANT]
>Este artigo não substitui a documentação de administração do SAP HANA ou as Notas do SAP. Esperamos que você tenha uma compreensão sólida e experiência em SAP HANA administração e operações, especialmente para backup, restauração, alta disponibilidade e recuperação de desastres. Neste artigo, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

É importante que você execute as etapas e os processos realizados em seu ambiente e com as liberações e versões do HANA. Alguns processos descritos neste artigo são simplificados para um melhor entendimento geral. Eles não devem ser usados como etapas detalhadas para manuais de operação eventual. Se você quiser criar manuais de operação para suas configurações, teste e exerça seus processos e documente os processos relacionados às suas configurações específicas. 

Um dos aspectos mais importantes dos bancos de dados operacionais é protegê-los contra eventos catastróficos. A causa desses eventos pode ser qualquer coisa, desde desastres naturais a simples erros de usuário.

O backup de um banco de dados do, com a capacidade de restaurá-lo para qualquer ponto no tempo, como antes de alguém excluir dados críticos, permite a restauração para um estado o mais próximo possível da forma como era antes da interrupção.

Dois tipos de backups devem ser executados para alcançar a capacidade de restauração:

- Backups de banco de dados: backups completos, incrementais ou diferenciais
- Backups do log de transações

Além de backups completos do banco de dados realizados em um nível de aplicativo, é possível realizar backups com instantâneos de armazenamento. Os instantâneos de armazenamento não substituem os backups de log de transações. Os backups de log de transações continuam sendo importantes para restaurar o banco de dados para um determinado ponto no tempo ou para esvaziar os logs de transações já confirmadas. Os instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de avanço do banco de dados. 

O SAP HANA no Azure (Instâncias Grandes) oferece duas opções de backup e de restauração:

- **DIY (Faça Você Mesmo).** Depois de verificar se há espaço em disco suficiente, execute backups completos de banco de dados e log usando um dos seguintes métodos de backup em disco. Você pode fazer backup diretamente em volumes anexados às unidades de instância grande do HANA ou aos compartilhamentos NFS configurados em uma VM (máquina virtual) do Azure. No último caso, os clientes configuram uma VM do Linux no Azure, anexam o Armazenamento do Azure à VM e compartilham o armazenamento por meio de um servidor NFS configurado na VM. Se você executar o backup em volumes que se anexam diretamente às unidades de instância grande do HANA, copie os backups para uma conta de armazenamento do Azure. Faça isso depois de configurar uma VM do Azure que exporta compartilhamentos NFS baseados no armazenamento do Azure. Você também pode usar um cofre de backup do Azure ou o armazenamento Cold do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles são copiados para uma conta de armazenamento do Azure. A opção de backup DIY também pode ser necessária para os dados que você precisa armazenar por períodos de tempo mais longos para fins de conformidade e auditoria. Em todos os casos, os backups são copiados para compartilhamentos NFS representados por meio de uma VM e do Armazenamento do Azure.

- **Funcionalidade de backup e restauração de infraestrutura.** Você também pode usar a funcionalidade de backup e restauração que a infraestrutura subjacente do SAP HANA no Azure (instâncias grandes) fornece. Essa opção atende a necessidade de backups e restaurações rápidas. O restante desta seção aborda a funcionalidade de backup e de restauração oferecida com Instâncias Grandes HANA. Esta seção também aborda a relação que o backup e a restauração têm para a funcionalidade de recuperação de desastres oferecida pelo HANA em instâncias grandes.

> [!NOTE]
>   A tecnologia de instantâneo usada pela infraestrutura subjacente do HANA em instâncias grandes tem uma dependência em SAP HANA instantâneos. Neste ponto, os instantâneos de SAP HANA não funcionam em conjunto com vários locatários de SAP HANA contêineres de banco de dados multilocatário. Se apenas um locatário for implantado, SAP HANA instantâneos funcionam e você pode usar esse método.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usar instantâneos de armazenamento de SAP HANA no Azure (instâncias grandes)

A infraestrutura de armazenamento subjacente ao SAP HANA no Azure (Instâncias Grandes) dá suporte a instantâneos de armazenamento de volumes. Há suporte para o backup e para a restauração de volumes, com as seguintes considerações:

- Em vez de backups de banco de dados completos, instantâneos de volume de armazenamento são executados com frequência.
- Quando um instantâneo é disparado sobre/Hana/data e/Hana/Shared, que inclui/usr/SAP, volumes, a tecnologia de instantâneo inicia um instantâneo de SAP HANA antes de executar o instantâneo de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento. Para que um instantâneo do HANA seja bem-sucedido, você precisa de uma instância ativa do HANA. Em um cenário de HSR, um instantâneo de armazenamento não tem suporte em um nó secundário atual em que um instantâneo do HANA não pode ser executado.
- Depois que o instantâneo de armazenamento é executado com êxito, o instantâneo de SAP HANA é excluído.
- Backups de log de transações são usados frequentemente e armazenados no volume /hana/logbackups ou no Azure. É possível disparar o volume /hana/logbackups que contém os backups de log de transações para usar um instantâneo separadamente. Nesse caso, você não precisa executar um instantâneo do HANA.
- Se você precisar restaurar um banco de dados para um determinado ponto no tempo, para uma interrupção de produção, solicite que Microsoft Azure suporte ou SAP HANA no Azure Restore para um determinado instantâneo de armazenamento. Um exemplo é uma restauração planejada de um sistema de área restrita para seu estado original.
- O instantâneo de SAP HANA que está incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transações que foram executados e foram armazenados após o instantâneo de armazenamento ter sido tirado.
- Esses backups de log de transações são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

Você pode executar instantâneos de armazenamento direcionados a três classes de volumes:

- Um instantâneo combinado sobre/Hana/data e/Hana/Shared, que inclui/usr/SAP. Esse instantâneo exige a criação de um instantâneo do SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo de SAP HANA garante que o banco de dados esteja em um estado consistente de um ponto de vista de armazenamento. Para o processo de restauração, esse é um ponto a ser configurado em.
- Um instantâneo separado em /hana/logbackups.
- Uma partição do sistema operacional.

Para obter os scripts de instantâneo e a documentação mais recentes, consulte [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Ao baixar o pacote de script de instantâneo do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), você obtém três arquivos. Um dos arquivos é documentado em um PDF para a funcionalidade fornecida. Depois de baixar o conjunto de ferramentas, siga as instruções em "obter as ferramentas de instantâneo".

## <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Os instantâneos de armazenamento consomem espaço de armazenamento alocado para as unidades de instância grande do HANA. Considere os seguintes aspectos do agendamento de instantâneos de armazenamento e quantos instantâneos de armazenamento devem ser mantidos. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específico no momento em que ele é tomado consome pouco armazenamento.
- À medida que o conteúdo de dados é alterado e o conteúdo em SAP HANA arquivos de dados é alterado no volume de armazenamento, o instantâneo precisa armazenar o conteúdo do bloco original e as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Quanto mais alterações são feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo de armazenamento, maior o consumo de espaço do instantâneo de armazenamento.

O SAP HANA no Azure (Instâncias Grandes) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. A execução de instantâneos desses volumes consome seu espaço de volume. Você precisa:

- Determine quando agendar instantâneos de armazenamento.
- Monitore o consumo de espaço dos volumes de armazenamento. 
- Gerencie o número de instantâneos que você armazena. 

É possível desabilitar os instantâneos de armazenamento quando você importa muitos dados ou realiza outras alterações significativas no banco de dados do HANA. 


As seções a seguir fornecem informações para a execução desses instantâneos e incluem recomendações gerais:

- Embora o hardware possa sustentar 255 instantâneos por volume, você deseja ficar bem abaixo desse número. A recomendação é de 250 ou menos.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. É possível reduzir o número de instantâneos que você mantém ou aumentar os volumes. É possível solicitar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a movimentação de dados em SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou a restauração de bancos de dados do SAP HANA com base em backups, desabilite instantâneos de armazenamento no volume /hana/data. 
- Durante reorganizações maiores de SAP HANA tabelas, evite instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para usar os recursos de recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para usar os instantâneos de armazenamento de autoatendimento

Para certificar-se de que o script de instantâneo seja executado com êxito, verifique se o Perl está instalado no sistema operacional Linux no servidor HANA em instâncias grandes. O Perl vem pré-instalado em sua unidade de instância grande do HANA. Para verificar a versão do Perl, use a linha de comando a seguir:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com instâncias grandes do HANA, siga estas etapas.
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor das Instâncias Grandes HANA.
1. Modifique /etc/ssh/ssh\_config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de usuário de backup SAP HANA no nó mestre para cada instância de SAP HANA que você executar, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores de Instâncias Grandes do SAP HANA.
1. No primeiro servidor de Instâncias Grandes do SAP HANA de cada região, crie uma chave pública para acessar a infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o arquivo de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) para o local do **hdbsql** na instalação do SAP HANA.
1. Modifique o arquivo de *HANABackupDetails.txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Para as etapas listadas anteriormente, consulte [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários de MCOD
Se você executar um [cenário de MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias de SAP Hana em uma unidade de instância grande do Hana, você terá volumes de armazenamento separados provisionados para cada uma das instâncias de SAP Hana. Para obter mais informações sobre MDC e outras considerações, consulte "coisas importantes a serem lembradas" em [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Etapa 1: Instalar o cliente do SAP HANA HDB

O sistema operacional Linux instalado em SAP HANA no Azure (instâncias grandes) inclui as pastas e os scripts necessários para executar SAP HANA instantâneos de armazenamento para fins de backup e recuperação de desastre. Verifique se há versões mais recentes no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

É sua responsabilidade instalar o SAP HANA cliente HDB nas unidades do SAP HANA em instâncias grandes enquanto você instala o SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Etapa 2: Alterar o /etc/ssh/ssh\_config

Esta etapa é descrita em "habilitar a comunicação com o armazenamento" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

Para habilitar o acesso às interfaces de instantâneo de armazenamento do seu locatário de instância grande do HANA, estabeleça um procedimento de entrada por meio de uma chave pública. 

No primeiro SAP HANA no servidor do Azure (instâncias grandes) em seu locatário, crie uma chave pública para acessar a infraestrutura de armazenamento. Com uma chave pública, uma senha não é necessária para entrar nas interfaces de instantâneo de armazenamento. Você também não precisa manter credenciais de senha com uma chave pública. 

Para gerar uma chave pública, consulte "habilitar a comunicação com o armazenamento" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Para iniciar a criação de instantâneos de SAP HANA, crie uma conta de usuário no SAP HANA que os scripts de instantâneo de armazenamento possam usar. Crie uma conta de usuário do SAP HANA no SAP HANA Studio para essa finalidade. O usuário deve ser criado sob o SYSTEMDB e *não* no banco de dados Sid para MDC. No ambiente de contêiner único, o usuário é criado no banco de dados de locatário. Essa conta deve ter privilégios de **administrador de backup** e de **leitura de catálogo** . 

Para configurar e usar uma conta de usuário, consulte "habilitar a comunicação com o SAP HANA" no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, você autoriza a conta de usuário SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando SAP HANA `hdbuserstore` permite a criação de uma SAP Hana chave de usuário. A chave é armazenada em um ou mais nós de SAP HANA. A chave de usuário permite que ele acesse o SAP HANA sem precisar gerenciar senhas de dentro do processo de script. O processo de script será abordado posteriormente neste artigo.

>[!IMPORTANT]
>Execute esses comandos de configuração com o mesmo contexto de usuário em que os comandos de instantâneo são executados. Caso contrário, os comandos de instantâneo não funcionarão corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Etapa 6: Obter os scripts de instantâneo, configurar os instantâneos e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). A maneira como os scripts são instalados é alterada com a versão 4,1 dos scripts. Para obter mais informações, consulte "habilitar a comunicação com o SAP HANA" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para obter a seqüência exata de comandos, consulte "instalação fácil de ferramentas de instantâneo (padrão)" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Recomendamos o uso da instalação padrão. 

Para atualizar da versão 3. x para 4,1, consulte "atualizar uma instalação existente" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Para desinstalar o conjunto de ferramentas 4,1, consulte "desinstalação das ferramentas de instantâneo" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Não se esqueça de executar as etapas descritas em "concluir a instalação das ferramentas de instantâneo" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A finalidade dos diferentes scripts e arquivos como eles foram instalados é descrita em "o que são essas ferramentas de instantâneo?" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Antes de configurar as ferramentas de instantâneo, certifique-se de que você também configurou locais e configurações de backup do HANA corretamente. Para obter mais informações, consulte "configuração de SAP HANA" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A configuração do conjunto de ferramentas de instantâneo é descrita em "config file-HANABackupCustomerDetails.txt" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testar a conectividade com SAP HANA

Após colocar todos os dados de configuração no arquivo *HANABackupCustomerDetails.txt* verifique se as configurações estão corretas para os dados da instância do HANA. Use o script `testHANAConnection`, que é independente de uma configuração de expansão ou escala vertical do SAP HANA.

Para obter mais informações, consulte "verificar a conectividade com o SAP HANA-testHANAConnection" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testar conectividade de armazenamento

A próxima etapa de teste é verificar a conectividade com o armazenamento com base nos dados que você colocou no arquivo de configuração *HANABackupCustomerDetails.txt* . Em seguida, execute um instantâneo de teste. Antes de executar o `azure_hana_backup` comando, você deve executar este teste. Para obter a sequência de comandos para este teste, consulte "verificar a conectividade com armazenamento-testStorageSnapshotConnection" no [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Após uma conexão com êxito nas interfaces da máquina virtual de armazenamento, o script continuará com a fase 2 e criará um instantâneo de teste. A saída é mostrada aqui para uma configuração de expansão de três nós de SAP HANA.

Se o instantâneo de teste for executado com êxito com o script, você poderá agendar os instantâneos de armazenamento reais. Se ele não for bem-sucedido, investigue os problemas antes de prosseguir. O instantâneo de teste deve permanecer até que os primeiros instantâneos reais sejam concluídos.


### <a name="step-7-perform-snapshots"></a>Etapa 7: Executar instantâneos

Quando as etapas de preparação forem concluídas, você poderá começar a configurar e agendar os instantâneos de armazenamento reais. O script a ser agendado funciona com as configurações de expansão e escalável do SAP HANA. Para execução periódica e regular do script de backup, agende o script usando o utilitário cron. 

Para obter a sintaxe e a funcionalidade exatas do comando, consulte "executar backup de instantâneo-azure_hana_backup" nas [ferramentas de instantâneo da Microsoft para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Quando o script `azure_hana_backup` é executado, ele cria o instantâneo de armazenamento nas três fases a seguir:

1. Ele executa um instantâneo SAP HANA.
1. Ele executa um instantâneo de armazenamento.
1. Ele remove o instantâneo de SAP HANA que foi criado antes da execução do instantâneo de armazenamento.

Para executar o script, chame-o da pasta executável HDB para a qual ele foi copiado. 

O período de retenção é administrado com o número de instantâneos que são enviados como um parâmetro quando você executa o script. A quantidade de tempo coberta pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos enviados como um parâmetro quando o script é executado. 

Se o número de instantâneos que são mantidos exceder o número nomeado como um parâmetro na chamada do script, o instantâneo de armazenamento mais antigo do mesmo rótulo será excluído antes de um novo instantâneo ser executado. O número que você der como o último parâmetro da chamada é o número que você poderá usar para controlar o número de instantâneos mantidos. Com esse número, você também pode controlar, indiretamente, o espaço em disco usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência dos instantâneos para os diferentes tipos depende do fato de você usar a funcionalidade de recuperação de desastre do SAP HANA em Instâncias Grandes. Essa funcionalidade depende de instantâneos de armazenamento, os quais podem requerer recomendações especiais para os períodos de execução e frequência dos instantâneos de armazenamento. 

Nas considerações e recomendações a seguir, vamos supor que você *não* usa a funcionalidade de recuperação de desastre que o SAP HANA em Instâncias Grandes oferece. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Devido às limitações do número de instantâneos e espaço, considere os seguintes requisitos:

- O tempo de recuperação para uma restauração pontual.
- O espaço usado.
- O ponto de recuperação e os objetivos de tempo de recuperação para possível recuperação de um desastre.
- A execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou a interface **backint** for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups completos de bancos de dados sobre instantâneos de armazenamento, certifique-se de que a execução dos instantâneos de armazenamento esteja desabilitada durante esse tempo.
- O número de instantâneos por volume, que é limitado a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se você não usar a funcionalidade de recuperação de desastres do HANA em instâncias grandes, o período de instantâneo será menos frequente. Nesses casos, execute os instantâneos combinados em/Hana/data e/Hana/Shared, que inclui/usr/SAP, em períodos de 12 horas ou 24 horas. Mantenha os instantâneos por um mês. O mesmo é verdadeiro para os instantâneos do volume de backup de log. A execução de SAP HANA backups de log de transações no volume de backup de log ocorre em períodos de 5 minutos a 15 minutos.

Os instantâneos de armazenamento agendados executam melhor utilizando o cron. Use o mesmo script para todos os backups e necessidades de recuperação de desastre. Modifique as entradas de script para corresponder aos vários tempos de backup solicitados. Esses instantâneos são todos agendados de modo diferente em cron, dependendo de seu tempo de execução. Pode ser por hora, a cada 12 horas, diariamente ou semanalmente. 

O exemplo a seguir mostra uma agenda cron em/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, um instantâneo combinado por hora aborda os volumes que contêm o/Hana/data e o/hana/shared/SID, que inclui/usr/SAP, locais. Use esse tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Também há um instantâneo diário nesses volumes. Portanto, você tem dois dias de cobertura por instantâneos por hora, além de quatro semanas de cobertura por instantâneos diários. O backup do volume de log de transações também é feito diariamente. Esses backups são mantidos por quatro semanas. 

Como você vê na terceira linha do crontab, o backup do log de transações do HANA está agendado para ser executado a cada 5 minutos. As horas de início dos trabalhos cron diferentes que executam instantâneos de armazenamento são escalonadas. Dessa forma, os instantâneos não são executados de uma só vez em um determinado ponto no tempo. 

No exemplo a seguir, você executa um instantâneo combinado que abrange os volumes que contêm/Hana/data e/hana/shared/SID, que inclui/usr/SAP, localizações por hora. Mantenha esses instantâneos por dois dias. Os instantâneos dos volumes de backup de log de transações são executados em uma base de 5 minutos e são mantidos por quatro horas. Como antes, o backup do arquivo de log de transações do HANA está agendado para ser executado a cada 5 minutos. 

O instantâneo do volume de backup de log de transações é executado com um atraso de 2 minutos após o backup de log de transações ter sido inciado. Em circunstâncias normais, o backup do log de transações SAP HANA é concluído dentro desses 2 minutos. Como antes, o volume que contém o LUN de inicialização tem seu backup feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico a seguir ilustra as sequências do exemplo anterior. O LUN de inicialização é excluído.

![Relação entre os backups e os instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

O SAP HANA executa gravações regulares no volume /hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, o SAP HANA grava um ponto de salvamento para o volume /hana/data. Conforme especificado no crontab, um backup de log de transações SAP HANA é executado a cada 5 minutos. 

Você também verá que um instantâneo de SAP HANA é executado a cada hora como resultado do acionamento de um instantâneo de armazenamento combinado nos volumes/Hana/data e/hana/shared/SID. Depois que o instantâneo do HANA for bem-sucedidos, o instantâneo de armazenamento combinado será executado. Conforme instruído no crontab, o instantâneo de armazenamento no volume/Hana/logbackup é executado a cada 5 minutos, cerca de 2 minutos após o backup de log de transações do HANA.

> 

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log de transações do SAP HANA. Esses backups de log de transações precisam abordar os períodos de tempo entre os instantâneos de armazenamento. 

Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário:

- Acesse um instantâneo de armazenamento combinado sobre/Hana/data e/hana/shared/SID que é de 30 dias de idade, em casos extremos. 
- Tenha backups de log de transações contíguos que abrangem o tempo entre todos os instantâneos de armazenamento combinado. Portanto, o instantâneo mais antigo do volume de backup de log de transações deve ser de 30 dias. Esse não é o caso se você copiar os backups de log de transações para outro compartilhamento NFS localizado no armazenamento do Azure. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para se beneficiar de instantâneos de armazenamento e replicação eventual de armazenamento de backups de log de transações, altere o local para o qual SAP HANA grava os backups de log de transações. É possível fazer essa alteração no HANA Studio. 

Embora SAP HANA faça backup de segmentos de log completos automaticamente, especifique um intervalo de backup de log para ser determinístico. Isso é especialmente verdadeiro quando você usa a opção de recuperação de desastres porque geralmente deseja executar backups de log com um período determinístico. No caso a seguir, 15 minutos é definido como o intervalo de backup de log.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você também pode escolher backups que são mais frequentes do que a cada 15 minutos. Uma configuração mais frequente é frequentemente usada em conjunto com a funcionalidade de recuperação de desastre do SAP HANA nas Instâncias Grandes. Alguns clientes executam backups de log de transações a cada 5 minutos.

Se o backup do banco de dados nunca foi feito, a etapa final será executar um backup de banco de dados com base no arquivo para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, SAP HANA não poderá iniciar os backups de log especificados.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após a execução de seus primeiros instantâneos de armazenamento com êxito, exclua o instantâneo de teste executado na etapa 6. Para obter mais informações, consulte "remover instantâneos de teste-removeTestStorageSnapshot" em [Microsoft snapshot Tools para SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorar o número e o tamanho dos instantâneos no volume do disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. O comando do sistema operacional Linux `du` mostra detalhes sobre esses instantâneos de armazenamento porque eles são armazenados nos mesmos volumes. Use o comando com as seguintes opções:

- `du –sh .snapshot`: essa opção fornece um total de todos os instantâneos no diretório de instantâneos.
- `du –sh --max-depth=1`: essa opção lista todos os instantâneos salvos na pasta **.snapshot** e o tamanho de cada instantâneo.
- `du –hc`: essa opção fornece o tamanho total usado por todos os instantâneos.

Use esses comandos para certificar-se de que os instantâneos que são criados e armazenados não consumam todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos anteriores.

### <a name="get-details-of-snapshots"></a>Obter detalhes de instantâneos
Para obter mais detalhes sobre instantâneos, use o script `azure_hana_snapshot_details` . Você pode executar esse script em qualquer local se houver um servidor ativo no local de recuperação de desastre. O script fornece a saída a seguir dividida por cada volume que contém os instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os detalhes a seguir em cada instantâneo nesse volume: 
      - Nome do instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID do HANA Backup associada a esse instantâneo, se relevante

Para obter a sintaxe do comando e das saídas, consulte "listar instantâneos-azure_hana_snapshot_details" em [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Reduzir o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número de determinados rótulos de instantâneos que você armazena. Os dois últimos parâmetros do comando para iniciar um instantâneo são o rótulo e o número de instantâneos que você deseja manter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, o rótulo do instantâneo é **dailyhana**. O número de instantâneos com este rótulo a ser mantido é **28**. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. Uma maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se você executar o script com essa configuração, o número de instantâneos, que inclui o novo instantâneo de armazenamento, será 15. Os 15 instantâneos mais recentes são mantidos e os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Esse script reduz o número de instantâneos somente se houver instantâneos mais de uma hora. O script não exclui instantâneos com menos de uma hora de idade. Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não deseja mais manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, execute o script com **0** como o número de retenção. Todos os instantâneos que correspondem a esse rótulo são removidos. A remoção de todos os instantâneos pode afetar os recursos da funcionalidade de recuperação de desastres de instâncias grandes do HANA.

Uma segunda opção para excluir instantâneos específicos é usar o script `azure_hana_snapshot_delete`. Este script foi desenvolvido para excluir um instantâneo ou conjunto de instantâneos usando a ID de backup do HANA conforme encontrada no HANA Studio ou por meio do próprio nome do instantâneo. Atualmente, a ID de backup está vinculada somente aos instantâneos criados para o tipo de instantâneo do **Hana** . Backups de instantâneo do tipo **logs** e **inicialização** não executam um instantâneo SAP Hana, portanto, não há nenhuma ID de backup a ser encontrada para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos em volumes diferentes que correspondam ao nome do instantâneo inserido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter mais informações sobre o script, consulte "excluir um instantâneo-azure_hana_snapshot_delete" em [Microsoft snapshot Tools for SAP Hana no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Execute o script como **raiz** do usuário.

>[!IMPORTANT]
>Se houver dados que existem somente no instantâneo que você planeja excluir, depois que o instantâneo for excluído, esses dados serão perdidos para sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauração no nível do arquivo de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneos **hana** e **logs**, é possível acessar os instantâneos diretamente nos volumes no diretório **.snapshot**. Há um subdiretório para cada um dos instantâneos. Copie cada arquivo no estado em que estava no ponto do instantâneo desse subdiretório na estrutura de diretório real. 

Na versão atual do script, não há *nenhum* script de restauração fornecido para a restauração de instantâneo como autoatendimento. A restauração de instantâneo pode ser executada como parte dos scripts de recuperação de desastre de autoatendimento no site de recuperação de desastre durante o failover. Para restaurar um instantâneo desejado dos instantâneos disponíveis existentes, você deve entrar em contato com a equipe de operações da Microsoft abrindo uma solicitação de serviço.

>[!NOTE]
>A restauração de arquivo único não funciona para instantâneos do LUN de inicialização independentemente do tipo das unidades do SAP HANA em instâncias grandes. O diretório **. snapshot** não é exposto no LUN de inicialização. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Em um cenário de produção, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com suporte a Microsoft Azure. É uma questão de alta urgência se os dados foram excluídos em um sistema de produção e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em uma situação diferente, uma recuperação pontual pode ter baixa urgência e ser planejada com dias de antecedência. Você pode planejar essa recuperação com SAP HANA no Azure em vez de gerar um sinalizador de alta prioridade. Por exemplo, você pode planejar a atualização do software SAP aplicando um novo pacote de aprimoramento. Em seguida, é necessário reverter para um instantâneo que representa o estado antes da atualização de pacote de melhoria.

Antes de enviar a solicitação, é necessário se preparar. A SAP HANA na equipe do Azure pode manipular a solicitação e fornecer os volumes restaurados. Depois, restaure o banco de dados do HANA com base nos instantâneos.

Para as possibilidades de obter um instantâneo restaurado com o novo conjunto de ferramentas, consulte "como restaurar um instantâneo" no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para se preparar para a solicitação, siga estas etapas.

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana é restaurado, a menos que indicado o contrário. 

1. Desligar a instância do HANA.

   ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó do banco de dados do HANA. Se os volumes de dados ainda estiverem montados para o sistema operacional, a restauração do instantâneo falhará.

   ![Desmonte os volumes de dados em cada nó do banco de dados HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra uma solicitação de suporte do Azure e inclua instruções sobre a restauração de um instantâneo específico:

   - Durante a restauração: SAP HANA no serviço do Azure pode pedir que você participe de uma chamada de conferência para coordenar, verificar e confirmar se o instantâneo de armazenamento correto foi restaurado. 

   - Após a restauração: SAP HANA no serviço do Azure notifica você quando o instantâneo de armazenamento é restaurado.

1. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

   ![Montar novamente todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, SAP HANA arquivos de dados recuperados de um instantâneo de armazenamento, está documentada na etapa 7 no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Para restaurar de um backup de instantâneo, consulte [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Se o instantâneo foi restaurado pelas operações da Microsoft, você não precisa fazer a etapa 7.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para restaurar para um determinado ponto no tempo, consulte "recuperar o banco de dados para o seguinte ponto no tempo" no [Guia de recuperação manual para SAP Hana no Azure de um instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>Integração do SnapCenter em instâncias grandes SAP HANA

Esta seção descreve como os clientes podem usar o software SnapCenter da NetApp para fazer um instantâneo, backup e restauração SAP HANA bancos de dados hospedados em Microsoft Azure HANA (instâncias grandes). 

O SnapCenter oferece soluções para cenários, incluindo backup/recuperação, recuperação de desastres (DR) com replicação de armazenamento assíncrona, replicação de sistema e clonagem de sistema. Integrado com o SAP HANA em Instâncias Grandes no Azure, os clientes agora podem usar o SnapCenter para operações de backup e recuperação.

Para obter referências adicionais, consulte NetApp TR-4614 e TR-4646 em SnapCenter.

- [SAP HANA backup/recuperação com SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [SAP HANA recuperação de desastre com a replicação de armazenamento (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR com SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [Clonagem do SAP do SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Requisitos e pré-requisitos do sistema

Para executar o SnapCenter no Azure HLI, os requisitos do sistema incluem:
* SnapCenter Server no Azure Windows 2016 ou mais recente com 4-vCPU, 16 GB de RAM e um mínimo de 650 GB de armazenamento de SSD Premium gerenciado.
* SAP HANA em Instâncias Grandes sistema com 1,5 TB – 24 TB de RAM. É recomendável usar dois SAP HANA sistemas de instância grande para clonar operações e testes.

As etapas para integrar o SnapCenter no SAP HANA são: 

1. Gere uma solicitação de tíquete de suporte para comunicar a chave pública gerada pelo usuário para a equipe do Microsoft Ops. Isso é necessário para configurar o usuário SnapCenter para acessar o sistema de armazenamento.
1. Crie uma VM em sua VNET que tenha acesso ao HLI; essa VM é usada para SnapCenter. 
1. Baixe e instale o SnapCenter. 
1. Operações de backup e recuperação. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Criar um tíquete de suporte para a configuração de armazenamento da função de usuário

1. Abra o portal do Azure e navegue até a página **assinaturas** . Uma vez na página "assinaturas", selecione sua assinatura SAP HANA, descrita em vermelho abaixo.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Criar caso de suporte para a configuração de armazenamento do usuário":::

1. Na página de assinatura do SAP HANA, selecione a subpágina **grupos de recursos** .

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Grupo de recursos de assinatura do laboratório de solução" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Selecione um grupo de recursos apropriado em uma região.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Selecione o grupo de recursos apropriado na região" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Selecione uma entrada de SKU correspondente a SAP HANA no armazenamento do Azure.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Selecione a entrada de SKU correspondente a SAP HANA" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Abra uma **nova** solicitação de tíquete de suporte, descrita em vermelho.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Abrir nova solicitação de tíquete de suporte":::

1. Na guia **noções básicas** , forneça as seguintes informações para o tíquete:

   * **Tipo de problema:** técnico
   * **Assinatura:** Sua assinatura
   * **Serviço:** SAP HANA instância grande
   * **Recurso:** Seu grupo de recursos
   * **Resumo:** Fornecer a chave pública gerada pelo usuário
   * **Tipo de problema:** Configuração e configuração
   * **Subtipo de problema:** Configurar o SnapCenter para HLI


1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça: 
   
   * Configurar o SnapCenter para HLI
   * Sua chave pública para o usuário do SnapCenter (SnapCenter. pem) – consulte o exemplo de criação de chave pública abaixo

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Nova solicitação de suporte-guia detalhes" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Selecione **revisão + criar** para examinar seu tíquete de suporte. 

1. Gere um certificado para o nome de usuário SnapCenter na instância grande do HANA ou em qualquer servidor Linux.

   O SnapCenter requer um nome de usuário e uma senha para acessar a máquina virtual de armazenamento (SVM) e criar instantâneos do banco de dados HANA. A Microsoft usa a chave pública para permitir que você (o cliente) defina a senha para acessar o sistema de armazenamento.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Anexe o arquivo snapcenter. pem ao tíquete de suporte e, em seguida, selecione **criar**

   Depois que o certificado de chave pública é enviado, a Microsoft configura o nome de usuário SnapCenter para seu locatário junto com o endereço IP SVM.   

1. Depois de receber o IP SVM, defina uma senha para acessar SVM, que você controla.

   Veja a seguir um exemplo da chamada REST (documentação) da instância grande do HANA ou da VM na rede virtual, que tem acesso ao ambiente do SAP HANA em instâncias grandes e será usado para definir a senha.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Verifique se não há nenhuma variável de proxy ativa no sistema de BD do HANA.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>Baixar e instalar o SnapCenter
Agora que o nome de usuário está configurado para acesso SnapCenter ao sistema de armazenamento, você usará o nome de usuário SnapCenter para configurar o SnapCenter quando ele estiver instalado. 

Antes de instalar o SnapCenter, examine [SAP Hana backup/recuperação com SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) para definir sua estratégia de backup. 

1. Entre no [NetApp](https://mysupport.netapp.com) para [baixar](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) a versão mais recente do SnapCenter.

1. Instale o SnapCenter na VM do Windows Azure.

   O instalador verifica os pré-requisitos da VM. 

   >[!IMPORTANT]
   >Preste atenção ao tamanho da VM, especialmente em ambientes maiores.

1. Configure as credenciais do usuário para o SnapCenter. Por padrão, ele popula as credenciais de usuário do Windows usadas para instalar o aplicativo. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Diálogo entradas do usuário de instalação"::: 

1. Ao iniciar a sessão, salve a isenção de segurança e a GUI será iniciada.

1. Entre no SnapCenter na VM ( https://snapcenter-vm:8146) usando as credenciais do Windows para configurar o ambiente.


### <a name="set-up-the-storage-system"></a>Configurar o sistema de armazenamento

1. Em SnapCenter, selecione **sistema de armazenamento** e, em seguida, selecione **+ novo**. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="Conexões de armazenamento SnapCenter" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   O padrão é um SVM por locatário. Se um cliente tiver vários locatários ou HLIs em várias regiões, a recomendação será configurar todos os SVMs no SnapCenter

1. Em adicionar sistema de armazenamento, forneça as informações para o sistema de armazenamento que você deseja adicionar, o nome de usuário e a senha do SnapCenter e, em seguida, selecione **Enviar**.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Nova conexão de armazenamento":::

   >[!NOTE]
   >O padrão é um SVM por locatário.  Se houver vários locatários, a recomendação é configurar todos os SVMs aqui em SnapCenter. 

1. Em SnapCenter, selecione **hosts** e selecione **+ Adicionar** para configurar o plug-in do Hana e os hosts do Hana DB.  A versão mais recente do SnapCenter detecta automaticamente o banco de dados HANA no host.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="Em SnapCenter, selecione hosts e, em seguida, selecione Adicionar." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Forneça as informações para o novo host:
   1. Selecione o sistema operacional para o tipo de host.
   1. Insira o nome de host da VM SnapCenter.
   1. Forneça as credenciais que você deseja usar.
   1. Selecione as opções **Microsoft Windows** e **SAP Hana** e, em seguida, selecione **Enviar**.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Informações para o novo host":::

   >[!IMPORTANT]
   >Antes que você possa instalar o primeiro nó, o SnapCenter permite que um usuário não raiz instale plug-ins no banco de dados.  Para obter informações sobre como habilitar um usuário não raiz, consulte [adicionando um usuário não raiz e configurando privilégios sudo](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Examine os detalhes do host e selecione **Enviar** para instalar o plug-in no servidor SnapCenter.

1. Depois que o plug-in estiver instalado, em SnapCenter, selecione **hosts** e, em seguida, selecione **+ Adicionar** para adicionar um nó do Hana.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="Adicionar um nó do HANA" lightbox="media/snapcenter/add-hana-node.png":::

1. Forneça as informações para o nó do HANA:
   1. Selecione o sistema operacional para o tipo de host.
   1. Insira o endereço IP ou o nome de host do HANA DB.
   1. Selecione **+** para adicionar as credenciais configuradas no sistema operacional do host do BD Hana e selecione **OK**.
   1. Selecione **SAP Hana** e, em seguida, selecione **Enviar**.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="Detalhes do nó de SAP HANA":::

1. Confirme a impressão digital e selecione **confirmar e enviar**.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Confirmar e enviar impressão digital":::

1. No nó do Hana, no banco de dados do sistema, selecione **segurança**  >  **usuários**  >  **SNAPCENTER** para criar o usuário SNAPCENTER.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="Criar o usuário SnapCenter no HANA (banco de usuários do sistema)":::



### <a name="auto-discovery"></a>Descoberta automática
O SnapCenter 4,3 habilita a função de descoberta automática por padrão.  A descoberta automática não tem suporte para instâncias do HANA com a HSR (replicação do sistema HANA) configurada. Você deve adicionar manualmente a instância ao servidor SnapCenter.


### <a name="hana-setup-manual"></a>Instalação do HANA (manual)
Se você tiver configurado o HSR, deverá configurar o sistema manualmente.  

1. Em SnapCenter, selecione **recursos** e **San Hana** (na parte superior) e, em seguida, selecione **+ Adicionar SAP Hana banco de dados** (à direita).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="Instalação manual do HANA" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Especifique os detalhes do recurso do usuário administrador do HANA configurado no host do Linux ou no host onde os plug-ins estão instalados. O backup será gerenciado a partir do plug-in no sistema Linux.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Especifique os detalhes do recurso do usuário administrador do HANA configurado no host do Linux.":::

1. Selecione o volume de dados para o qual você precisa fazer instantâneos, selecione **salvar** e **concluir**.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Selecione o volume de dados para o qual você precisa fazer instantâneos, selecione salvar e concluir.":::

### <a name="create-a-snapshot-policy"></a>Criar uma política de instantâneo

Antes de usar o SnapCenter para fazer backup de SAP HANA recursos de banco de dados, você deve criar uma política de backup para o recurso ou grupo de recursos que você deseja fazer backup. Durante o processo de criação de uma política de instantâneo, você terá a opção de configurar comandos Pre/post e chaves SSL especiais. Para obter informações sobre como criar uma política de instantâneo, consulte [criando políticas de backup para bancos de dados SAP Hana](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. Em SnapCenter, selecione **recursos** e, em seguida, selecione um banco de dados.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="Em SnapCenter, selecione recursos e, em seguida, selecione um banco de dados.":::

1. Siga o fluxo de trabalho do assistente de configuração para configurar o Agendador de instantâneos.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Siga o fluxo de trabalho do assistente de configuração para configurar o Agendador de instantâneos." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Forneça as opções para configurar comandos Pre/post e chaves SSL especiais.  Neste exemplo, não estamos usando configurações especiais.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Forneça as opções para configurar comandos Pre-post e chaves SSL especiais." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. Selecione **Adicionar** para criar uma política de instantâneo, que também pode ser usada para outros bancos de dados Hana. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Selecione Adicionar para criar uma política de instantâneo, que também pode ser usada para outros bancos de dados HANA.":::

1. Insira o nome da política e uma descrição.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Insira o nome da política e uma descrição.":::


1. Selecione o tipo e a frequência de backup.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Selecione o tipo e a frequência de backup.":::

1. Defina as **configurações de retenção de backup sob demanda**.  Em nosso exemplo, estamos definindo a retenção para três cópias de instantâneo a serem mantidas.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Defina as configurações de retenção de backup sob demanda.":::

1. Defina as **configurações de retenção por hora**. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Defina as configurações de retenção por hora.":::

1. Se uma configuração do SnapMirror estiver configurada, selecione **Atualizar SnapMirror depois de criar uma cópia de instantâneo local**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Se um SnapMirror for necessário, selecione Atualizar SnapMirror depois de criar uma cópia de instantâneo local.":::

1. Selecione **concluir** para examinar o resumo da nova política de backup. 
1. Em **Configurar agendamento**, selecione **Adicionar**.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Em configurar agendamento, selecione Adicionar.":::

1. Selecione a **data de início**, **expira em** data e a frequência.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Selecione a data de início, expira em data e a frequência.":::

1. Forneça os detalhes do email para notificações.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Forneça os detalhes do email para notificações.":::

1.  Selecione **concluir** para criar a política de backup.

### <a name="disable-ems-message-to-netapp-autosupport"></a>Desabilitar a mensagem do EMS para o suporte da NetApp
Por padrão, a coleta de dados do EMS está habilitada e é executada a cada sete dias após a data de instalação.  Você pode desabilitar a coleta de dados com o cmdlet do PowerShell `Disable-SmDataCollectionEms` .

1. No PowerShell, estabeleça uma sessão com SnapCenter.

   ```powershell
   Open-SmConnection
   ```

1. Entre com suas credenciais.
1. Desabilite a coleção de mensagens do EMS.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Restaurar banco de dados após falha
Você pode usar SnapCenter para restaurar o banco de dados.  Nesta seção, abordaremos as etapas de alto nível, mas para obter mais informações, consulte [SAP Hana backup/recuperação com SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


1. Interrompa o banco de dados e exclua todos os arquivos de banco de dados.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Desmonte o volume do banco de dados.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Restaure os arquivos de banco de dados via SnapCenter.  Selecione o banco de dados e, em seguida, selecione **restaurar**.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Selecione um banco de dados e selecione restaurar." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Selecione o tipo de restauração.  Em nosso exemplo, estamos restaurando o recurso completo. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Selecione o tipo de restauração.":::

   >[!NOTE]
   >Com uma configuração padrão, você não precisa especificar comandos para fazer uma restauração local do instantâneo em disco. 

   >[!TIP]
   >Se você quiser restaurar um LUN específico dentro do volume, selecione **nível de arquivo**.

1. Siga o fluxo de trabalho por meio do assistente de configuração.
   
   O SnapCenter restaura os dados para o local original para que você possa iniciar o processo de restauração no HANA. Além disso, como SnapCenter não é capaz de modificar o catálogo de backup (o banco de dados está inoperante), um aviso é exibido.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Como o SnapCenter não é capaz de modificar o catálogo de backup, um aviso é exibido. ":::

1. Como todos os arquivos de banco de dados são restaurados, inicie o processo de restauração no HANA. No Hana Studio, em **sistemas**, clique com o botão direito do mouse no banco de dados do sistema e selecione **backup e recuperação**  >  **recuperar banco de dados do sistema**.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Inicie o processo de restauração no HANA.":::

1. Selecione um tipo de recuperação.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Selecione o tipo de recuperação.":::

1. Selecione o local do catálogo de backup.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Selecione o local do catálogo de backup.":::

1. Selecione um backup para recuperar o banco de dados de SAP HANA.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Selecione um backup para recuperar o banco de dados de SAP HANA.":::

   Depois que o banco de dados é recuperado, uma mensagem é exibida com uma **recuperação em tempo** e **recuperada no carimbo de posição de log** .

1. Em **sistemas**, clique com o botão direito do mouse no banco de dados do sistema e selecione **backup e recuperação**  >  **recuperar banco de dados de locatário**.
1. Siga o fluxo de trabalho do assistente para concluir a recuperação do banco de dados de locatário. 

Para obter mais informações sobre como restaurar um banco de dados, consulte [SAP Hana backup/recuperação com SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


### <a name="non-database-backups"></a>Backups não relacionados ao banco de dados
Você pode restaurar volumes que não são de dados, por exemplo, um compartilhamento de arquivos de rede (/Hana/Shared) ou um backup do sistema operacional.  Para obter mais informações sobre como restaurar um volume que não é de dados, consulte [SAP Hana backup/recuperação com SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).

### <a name="sap-hana-system-cloning"></a>Clonagem do sistema SAP HANA

Antes de clonar, você deve ter a mesma versão do HANA instalada como o banco de dados de origem. O SID e a ID podem ser diferentes. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="Clonagem do sistema SAP HANA" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. Criar um repositório de usuários do banco de dados do HANA para o banco de dados H34 do/usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Desabilite o firewall.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Instale o SDK do Java.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. No SnapCenter, adicione o host de destino no qual o clone será montado. Para obter mais informações, consulte [adicionando hosts e Instalando pacotes de plug-in em hosts remotos](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).
   1. Forneça as informações para as credenciais executar como que você deseja adicionar. 
   1. Selecione o sistema operacional do host e insira as informações do host.
   1. Em **plug-ins a serem instalados**, selecione a versão, insira o caminho de instalação e selecione **SAP Hana**.
   1. Selecione **validar** para executar as verificações de pré-instalação.

1. Pare o HANA e desmonte o volume de dados antigo.  Você montará o clone de SnapCenter.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Crie os arquivos de script de configuração e de Shell para o destino.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >Você pode copiar os scripts da [clonagem do SAP do SnapCenter](https://www.netapp.com/us/media/tr-4667.pdf).

1. Modifique o arquivo de configuração. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * CHAVE = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * ARMAZENAMENTO = "10.250.101.33"

1. Modifique o arquivo de script do Shell.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOse = não
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, Versal = 4, Hard, Timese = 600, rsize = 1048576, wSize = 1048576, intr, noatime, NOLOCK"

1. Inicie o clone de um processo de backup. Selecione o host para criar o clone. 

   >[!NOTE]
   >Para obter mais informações, consulte [clonar de um backup](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

1. Em **scripts**, forneça o seguinte:

   * **Comando de montagem:** /NetApp/SC-System-Refresh.sh Mount H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Comando post clone:** /NetApp/SC-System-Refresh.sh Recover H34

1. Desabilitar (bloquear) a montagem automática no/etc/fstab, uma vez que o volume de dados do banco de dado pré-instalado não é necessário. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Excluir um clone

Você pode excluir um clone se ele não for mais necessário. Para obter mais informações, consulte [excluindo clones](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

Os comandos usados para executar antes da exclusão de clones são:
* **Pré-clonar exclusão:** /NetApp/SC-System-Refresh.sh desligar H34
* **Desmontar:** /NetApp/SC-System-Refresh.sh umount H34

Esses comandos permitem que o SnapCenter desastrosa o banco de dados, desmonte o volume e exclua a entrada fstab.  Depois disso, o FlexClone é excluído. 

### <a name="cloning-database-logfile"></a>Clonando o arquivo de log

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Desinstalar o pacote de plug-ins do SnapCenter para Linux

Você pode desinstalar o pacote de plug-ins do Linux da linha de comando. Como a implantação automática espera um sistema novo, é fácil desinstalar o plug-in.  

>[!NOTE]
>Talvez seja necessário desinstalar manualmente uma versão mais antiga do plug-in. 

Desinstale os plug-ins.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Agora você pode instalar o plug-in do HANA mais recente no novo nó selecionando **Enviar** em SnapCenter. 




## <a name="next-steps"></a>Próximas etapas
- Consulte [princípios e preparação de recuperação de desastre](hana-concept-preparation.md).
