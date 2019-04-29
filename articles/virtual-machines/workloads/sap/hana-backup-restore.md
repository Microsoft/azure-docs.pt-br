---
title: Backup e restauração do HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como realizar o backup e a restauração do HANA no SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098313"
---
# <a name="backup-and-restore"></a>Backup e restauração

>[!IMPORTANT]
>Esta documentação não substitui a documentação de administração do SAP HANA ou as Notas do SAP. Espera-se que o leitor tenha um sólido reconhecimento e experiência em administração e operações do SAP HANA, especialmente com os tópicos de backup, restauração, alta disponibilidade e recuperação de desastre. Nesta documentação, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

É importante que você execute as etapas e os processos realizados em seu ambiente e com as liberações e versões do HANA. Alguns processos descritos nesta documentação foram simplificados para um melhor entendimento geral e não devem ser usados como etapas detalhadas para eventuais manuais de operação. Se quiser criar manuais de operação para suas configurações, será necessário testar e executar os processos e documentar os processos relacionados às suas configurações específicas. 

Um dos aspectos mais importantes para a operação de bancos de dados é protegê-los contra eventos catastróficos. A causa desses eventos pode ser qualquer coisa, desde desastres naturais a simples erros de usuário.

O backup de um banco de dados, com a capacidade de restaurá-lo para qualquer ponto no tempo (como antes de alguém excluir dados críticos), permite a restauração para um estado o mais próximo possível à forma como estava antes da interrupção.

Dois tipos de backup devem ser executados para alcançar esse recurso para restaurar:

- Backups de banco de dados: backups completos, incrementais ou diferenciais
- Backups do log de transações

Além de backups completos do banco de dados realizados em um nível de aplicativo, é possível realizar backups com instantâneos de armazenamento. Os instantâneos de armazenamento não substituem backups de log de transações. Os backups de log de transações continuam sendo importantes para restaurar o banco de dados para um determinado ponto no tempo ou para esvaziar os logs de transações já confirmadas. No entanto, instantâneos de armazenamento podem acelerar a recuperação fornecendo rapidamente uma imagem de roll forward do banco de dados. 

O SAP HANA no Azure (Instâncias Grandes) oferece duas opções de backup e de restauração:

- DIY (Faça Você Mesmo). Depois que você verifique se há espaço em disco suficiente, realize backups completos de banco de dados e de log usando um dos seguintes métodos de backup de disco. É possível fazer backup diretamente para volumes anexados às unidades do SAP HANA em Instâncias Grandes ou para NFS (Compartilhamentos de Arquivos de Rede) configurados em uma VM (máquina virtual) do Azure. No último caso, os clientes configuram uma VM do Linux no Azure, anexam o Armazenamento do Azure à VM e compartilham o armazenamento por meio de um servidor NFS configurado na VM. Se você realizar o backup em volumes anexados diretamente às unidades de Instância Grande do HANA, será necessário copiar os backups para uma conta de armazenamento do Azure (depois de configurar uma VM do Azure que exporta compartilhamentos NFS com base no Armazenamento do Microsoft Azure). Também é possível usar um cofre de backup do Azure ou um armazenamento frio do Azure. 

   Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles são copiados em uma conta de armazenamento do Azure. A opção de backup DIY também pode ser necessária para dados que precisam ser armazenados por períodos mais longos devido à conformidade e para fins de auditoria. Em todos os casos, os backups são copiados para compartilhamentos NFS representados por meio de uma VM e do Armazenamento do Azure.

- Funcionalidade de backup e restauração de infraestrutura. Também é possível utilizar a funcionalidade de backup e restauração que a infraestrutura subjacente do SAP HANA (Instâncias Grandes) do Azure fornece. Essa opção atende a necessidade de backups e restaurações rápidas. O restante desta seção aborda a funcionalidade de backup e de restauração oferecida com Instâncias Grandes HANA. Esta seção também aborda a restauração e o backup de relação e a restauração para a funcionalidade de recuperação de desastre oferecida pelo SAP HANA em Instâncias Grandes.

> [!NOTE]
>   A tecnologia de instantâneo que é usada pela infraestrutura subjacente das Instâncias Grandes HANA tem uma dependência de instantâneos do SAP HANA. Neste ponto, os instantâneos do SAP HANA não funcionam junto com os múltiplos locatários dos contêineres de banco de dados multilocatário do SAP HANA. Se apenas um locatário for implantado, os instantâneos do SAP HANA funcionarão e esse método poderá ser utilizado.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usando instantâneos de armazenamento do SAP HANA no Azure (Instâncias Grandes)

A infraestrutura de armazenamento subjacente ao SAP HANA no Azure (Instâncias Grandes) dá suporte a instantâneos de armazenamento de volumes. Há suporte para o backup e para a restauração de volumes, com as seguintes considerações:

- Em vez de backups de banco de dados completos, instantâneos de volume de armazenamento são executados com frequência.
- Ao disparar um instantâneo sobre os volumes /hana/data e /hana/shared (inclui /usr/sap), a tecnologia de instantâneos iniciará um instantâneo do SAP HANA antes de executar o instantâneo de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento. Para o instantâneo do HANA seja bem-sucedida, você precisa de uma instância ativa do HANA.  No cenário de HSR, não há suporte para o instantâneo de armazenamento no nó secundário atual em que o instantâneo do HANA não pode ser executado.
- Após o instantâneo de armazenamento ser executado com êxito, o instantâneo do SAP HANA será excluído.
- Os backups de log de transações são usados frequentemente e armazenados no volume /hana/logbackups ou no Azure. É possível disparar o volume /hana/logbackups que contém os backups de log de transações para usar um instantâneo separadamente. Nesse caso, não é necessário executar um instantâneo do HANA.
- Se você precisar restaurar um banco de dados para um determinado ponto no tempo, solicitar que o suporte do Microsoft Azure (para uma interrupção de produção) ou SAP HANA na restauração do Azure para um determinado instantâneo de armazenamento. Um exemplo é uma restauração planejada de um sistema de área restrita para seu estado original.
- O instantâneo SAP HANA incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transações que foram executados e armazenados após o instantâneo de armazenamento ter sido tirado.
- Esses backups de log de transações são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

É possível executar instantâneos de armazenamento, direcionando três classes de volumes:

- Um instantâneo combinado em /hana/data e /hana/shared (inclui /usr/sap). Esse instantâneo exige a criação de um instantâneo do SAP HANA como preparação para o instantâneo de armazenamento. O instantâneo do SAP HANA garante que o banco de dados esteja em um estado consistente do ponto de vista de armazenamento. Ele garante também que haja um ponto de configuração para o processo de restauração.
- Um instantâneo separado em /hana/logbackups.
- Uma partição do sistema operacional.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Quando você baixa o pacote de script de instantâneo na [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), você obtém três arquivos dos quais um é uma documentação de PDF para a funcionalidade fornecida. Certifique-se de que você prossiga as instruções no capítulo 'Obtendo as ferramentas de instantâneo' quando baixar o conjunto de ferramentas.

## <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Instantâneos de armazenamento consomem o espaço de armazenamento que foi alocado para as unidades de Instância Grande do HANA. É necessário considerar os seguintes aspectos de programação de instantâneos de armazenamento e quantos instantâneos de armazenamento devem ser mantidos. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específico (no ponto no tempo em que é obtido) consome pouco de armazenamento.
- Como as alterações de conteúdo de dados e o conteúdo nos arquivos de dados do SAP HANA mudam no volume de armazenamento, o instantâneo precisa armazenar o conteúdo do bloco original, assim como as alterações de dados.
- Como resultado, o instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Quanto mais alterações são feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo de armazenamento, maior o consumo de espaço do instantâneo de armazenamento.

O SAP HANA no Azure (Instâncias Grandes) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. A execução de instantâneos desses volumes consome seu espaço de volume. Você precisará determinar quando agendar instantâneos de armazenamento. Também é necessário monitorar o consumo de espaço dos volumes de armazenamento, assim como gerenciar o número de instantâneos que você armazena. É possível desabilitar os instantâneos de armazenamento quando você importa muitos dados ou realiza outras alterações significativas no banco de dados do HANA. 


As seções a seguir fornecem informações para executar esses instantâneos incluindo recomendações gerais:

- Embora o hardware possa suportar 255 instantâneos por volume, é recomendável que permaneça bem abaixo desse número. A recomendação é 250 ou menos.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. É possível reduzir o número de instantâneos que você mantém ou aumentar os volumes. É possível solicitar armazenamento adicional em unidades de 1 terabyte.
- Durante atividades como a movimentação de dados em SAP HANA com ferramentas de migração da plataforma SAP (R3load) ou a restauração de bancos de dados do SAP HANA com base em backups, desabilite instantâneos de armazenamento no volume /hana/data. 
- Durante maiores reorganizações de tabelas do SAP HANA, deve-se evitar os instantâneos de armazenamento, se possível.
- Os instantâneos de armazenamento são um pré-requisito para usar os recursos de recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Pré-requisitos para usar os instantâneos de armazenamento de autoatendimento

Para garantir que o script do instantâneo execute com êxito, verifique se o Perl está instalado no sistema operacional Linux no servidor do SAP HANA em Instâncias Grandes. O Perl vem pré-instalado em sua unidade do HANA em Instâncias Grandes. Para verificar a versão do Perl, use a linha de comando a seguir:

`perl -v`

![A chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

Para configurar instantâneos de armazenamento com SAP HANA em Instâncias Grandes, siga estas etapas:
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor das Instâncias Grandes HANA.
1. Modifique /etc/ssh/ssh\_config para adicionar a linha _MACs hmac-sha1_.
1. Crie uma conta de usuário de backup do SAP HANA no nó mestre para cada instância do SAP HANA que está em execução, se aplicável.
1. Instale o cliente SAP HANA HDB em todos os servidores de Instâncias Grandes do SAP HANA.
1. No primeiro servidor de Instâncias Grandes do SAP HANA de cada região, crie uma chave pública para acessar a infraestrutura de armazenamento subjacente que controla a criação de instantâneos.
1. Copie os scripts e o arquivo de configuração do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) para o local do **hdbsql** na instalação do SAP HANA.
1. Modifique o arquivo *HANABackupDetails.txt* conforme necessário para as especificações apropriadas do cliente.

Obtenha os scripts de instantâneo e a documentação mais recentes do [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Para obter as etapas detalhadas listadas acima, consulte [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>Consideração para cenários de MCOD
Se estiver executando um [cenário de MCOD](https://launchpad.support.sap.com/#/notes/1681092) com várias instâncias do SAP HANA em uma unidade do SAP HANA em Instâncias Grandes, você terá volumes de armazenamento separados provisionados para cada uma das instâncias do SAP HANA. Para obter detalhes sobre MDC e outras considerações, verifique [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capítulo **'Coisas importantes para lembrar'**.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Etapa 1: Instalar o cliente do SAP HANA HDB

O sistema operacional Linux instalado no SAP HANA (Instâncias Grandes) do Azure inclui as pastas e os scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e de recuperação de desastre. Procure versões mais recentes em [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A versão mais recente dos scripts é 4.0. Scripts diferentes podem ter diferentes versões secundárias dentro da mesma versão principal.

No entanto, é sua responsabilidade instalar o cliente do SAP HANA HDB nas unidades de SAP HANA em Instâncias Grandes durante a instalação do SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Etapa 2: Alterar a /etc/ssh/ssh\_config

Essa etapa é descrita detalhadamente na seleção para versões mais recentes no [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capítulo **'Habilitar a comunicação com o armazenamento'**


### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

Para habilitar o acesso às interfaces de instantâneo de armazenamento do locatário do SAP HANA em Instâncias Grandes, é necessário estabelecer um procedimento de conexão por meio de uma chave pública. No primeiro servidor do SAP HANA (Instâncias Grandes) do Azure no locatário, crie uma chave pública a ser usada para acessar a infraestrutura de armazenamento. A chave pública garante que uma senha não será necessária para entrar nas interfaces do instantâneo de armazenamento. A criação de uma chave pública também significa que não é necessário manter credenciais de senha. Exatamente as etapas de como gerar público chave é descrita em [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capítulo **'Habilitar a comunicação com o armazenamento'**


### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Para iniciar a criação de instantâneos do SAP HANA, é necessário criar uma conta de usuário no SAP HANA que os scripts de instantâneo de armazenamento possam usar. Crie uma conta de usuário do SAP HANA no SAP HANA Studio para essa finalidade. O usuário deve ser criado no SYSTEMDB, e NÃO no banco de dados do SID para MDC. No ambiente de contêiner único, o usuário é criado no banco de dados de locatário. Essa conta deve ter os seguintes privilégios: **Administrador de backup** e **Leitura do catálogo**. Para obter as etapas exatas configurar o usuário e use o usuário, leia [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) capítulo **'Habilitar a comunicação com o SAP HANA'**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, autorize a conta de usuário do SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando `hdbuserstore` do SAP HANA permite criar uma chave de usuário do SAP HANA, armazenada em um ou mais nós do SAP HANA. A chave de usuário permite que ele acesse o SAP HANA sem precisar gerenciar senhas de dentro do processo de script. O processo de script será abordado posteriormente neste artigo.

>[!IMPORTANT]
>Execute estes comandos de configuração com o mesmo contexto de usuário, que os comandos de instantâneo são executados no. Caso contrário, os comandos de instantâneo não funcionará corretamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Etapa 6: Obter os scripts de instantâneo, configurar os instantâneos e testar a configuração e a conectividade

Baixe a versão mais recente dos scripts no [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A maneira como os scripts são vai ser instalado majorly mudou com a versão 4.0 dos scripts. Para obter os detalhes exatos, leia [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capítulo **'Habilitar a comunicação com o SAP HANA'**

Para obter a sequência exata dos comandos, leia o capítulo **'Fácil instalação das ferramentas de instantâneo (padrão)'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Recomendamos o uso da instalação padrão. Se você quiser atualizar da versão 3.x para 4.0, consulte a seção **atualização de uma instalação existente** de [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Para desinstalar o conjunto de 4.0 ferramentas, siga as instruções em **'Desinstalação das ferramentas do instantâneo'** na [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Não se esqueça de executar as etapas descritas em **'Concluir a instalação das ferramentas de instantâneo'** dos [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A finalidade dos diferentes scripts e arquivos que obteve instaladas é listada e detalhada em **'Quais são essas ferramentas de instantâneo'?** o documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Antes de configurar as ferramentas de instantâneo, certifique-se de que você também configurou configurações e os locais de backup do HANA corretamente conforme descrito em **'Configuração do SAP HANA'** do documento [Microsoft snapshot ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A configuração do conjunto de ferramentas de instantâneo é descrita detalhadamente no **'Arquivo Config - Hanabackupcustomerdetails'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="testing-connectivity-with-sap-hana"></a>Testando a conectividade com o SAP HANA

Após colocar todos os dados de configuração no arquivo *HANABackupCustomerDetails.txt* verifique se as configurações estão corretas para os dados da instância do HANA. Use o script `testHANAConnection`, que é independente de uma configuração de expansão ou escala vertical do SAP HANA.

Para obter detalhes, consulte **'Verificar a conectividade com o SAP HANA - testHANAConnection'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>Testando a conectividade de armazenamento

A próxima etapa de teste é verificar a conectividade com o armazenamento com base nos dados que você inseriu no arquivo de configuração *HANABackupCustomerDetails.txt* e, em seguida, executar um instantâneo de teste. Antes de executar o `azure_hana_backup` de comando, você deve executar esse teste. A sequência de comandos para este teste é listada no **'Verificar a conectividade com o armazenamento - testStorageSnapshotConnection'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Após uma conexão com êxito nas interfaces da máquina virtual de armazenamento, o script continuará com a fase 2 e criará um instantâneo de teste. A saída é mostrada aqui para uma configuração de expansão de três nós do SAP HANA:

Se o instantâneo de teste tiver sido executado com êxito com o script, você poderá continuar com a programação de instantâneos de armazenamento real. Se não for executado com êxito, investigue os problemas antes de prosseguir. O instantâneo de teste deve permanecer até que os primeiros instantâneos reais sejam concluídos.


### <a name="step-7-perform-snapshots"></a>Etapa 7: Executar instantâneos

Quando as etapas de preparação estiverem concluídas, você pode começar a configurar e agendar instantâneos de armazenamento real. O script a ser agendado funciona com as configurações de expansão e escalável do SAP HANA. Para execução periódica e regular do script de backup, agende o script usando o utilitário cron. 

Para a sintaxe de comando exata e a funcionalidade, leia **'Executar backup instantâneo - azure_hana_backup'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).  

A execução do script `azure_hana_backup` cria o instantâneo de armazenamento nas três fases a seguir:

1. Executa um instantâneo SAP HANA
1. Executa um instantâneo de armazenamento
1. Remove a captura instantânea do SAP HANA que foi criada antes da execução do instantâneo de armazenamento

Para executar o script, chame-o da pasta executável HDB para a qual ele foi copiado. 

O período de retenção é administrado com o número de instantâneos enviados como parâmetro ao executar o script. A quantidade de tempo coberta pelos instantâneos de armazenamento é uma função do período de execução e do número de instantâneos enviados como um parâmetro ao executar o script. Se o número de instantâneos que é mantido exceder o número identificado como parâmetro na chamada do script, o instantâneo de armazenamento mais antigo do mesmo rótulo será excluído antes de executar um novo instantâneo. O número que você der como o último parâmetro da chamada é o número que você poderá usar para controlar o número de instantâneos mantidos. Com esse número, também é possível controlar, indiretamente, o espaço em disco usado para instantâneos. 


## <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência dos instantâneos para os diferentes tipos depende do fato de você usar a funcionalidade de recuperação de desastre do SAP HANA em Instâncias Grandes. Essa funcionalidade depende de instantâneos de armazenamento, os quais podem requerer recomendações especiais para os períodos de execução e frequência dos instantâneos de armazenamento. 

Nas considerações e recomendações a seguir, vamos supor que você *não* usa a funcionalidade de recuperação de desastre que o SAP HANA em Instâncias Grandes oferece. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Devido às limitações do número de instantâneos e de espaço, os clientes consideraram os seguintes requisitos:

- O tempo de recuperação para uma restauração pontual.
- O espaço usado.
- O ponto de recuperação e os objetivos de tempo de recuperação para possível recuperação de um desastre.
- A execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou a interface **backint** for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups completos do banco de dados sobre instantâneos de armazenamento, verifique se a execução dos instantâneos de armazenamento está desabilitada durante esse momento.
- O número de instantâneos por volume (limitado a 250).

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Para clientes que não usam a funcionalidade de recuperação de desastres de Instâncias Grandes HANA, o período de instantâneo é menos frequente. Nesses casos, os clientes executam os instantâneos combinados em /hana/data e /hana/shared (inclui /usr/sap) em períodos de 12 ou 24 horas e mantêm os instantâneos por um mês. O mesmo acontece com os instantâneos do volume de backup de log de transações. No entanto, a execução de backups de log de transações do SAP HANA no volume de backup de log ocorre em períodos de cinco a 15 minutos.

Os instantâneos de armazenamento agendados executam melhor utilizando o cron. Utilize o mesmo script para todos os backups e necessidades de recuperação de desastre e modifique as entradas de script para corresponder aos vários tempos de backup solicitados. Esses instantâneos são todos agendados de forma diferente no cron dependendo de seu tempo de execução: por hora, 12 horas, diária ou semanal. 

A seguir, um exemplo de uma programação cron em /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
No exemplo anterior, há um instantâneo combinado por hora que aborda os volumes que contêm os dados do Hana e /hana/shared/SID (inclui usr/sap) locais. Use esse tipo de instantâneo para uma recuperação pontual mais rápida nos últimos dois dias. Além disso, há um instantâneo diário nesses volumes. Assim, você tem dois dias de cobertura por instantâneos por hora além de quatro semanas de cobertura por instantâneos diários. Além disso, o backup de log de transações tem seu backup feito diariamente. Esses backups também são mantidos por quatro semanas. Como você pode ver na terceira linha de crontab, o backup de log de transações do HANA está agendado para ser executado a cada 5 minutos. As horas de início dos trabalhos de cron diferentes que executam os instantâneos de armazenamento são alternados, assim, os instantâneos não são executados simultaneamente em um determinado ponto no tempo. 

No exemplo a seguir, você executar um instantâneo combinado que cobre os volumes que contêm os dados do Hana e /hana/shared/SID (incluindo usr/sap) locais por hora. Mantenha esses instantâneos por dois dias. Os instantâneos dos volumes de backup de log de transações são executados em uma base de 5 minutos e são mantidos por 4 horas. Como antes, o backup do arquivo de log de transações do HANA está agendado para ser executado a cada 5 minutos. O instantâneo do volume de backup de log de transações é executado com um atraso de 2 minutos após o backup de log de transações ter sido inciado. Dentro desses 2 minutos, o backup de log de transações do SAP HANA deverá ser concluído sob circunstâncias normais. Como antes, o volume que contém o LUN de inicialização tem seu backup feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

O gráfico a seguir ilustra as sequências do exemplo anterior, excluindo o LUN de inicialização:

![Relação entre os backups e os instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

O SAP HANA executa gravações regulares no volume /hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, o SAP HANA grava um ponto de salvamento para o volume /hana/data. Conforme especificado em crontab, um backup de log de transações do SAP HANA é executado a cada 5 minutos. Você também pode ver que um instantâneo SAP HANA é executado como resultado do acionamento de um instantâneo de armazenamento combinado nos volumes /hana/data e /hana/shared/SID a cada hora. Depois do êxito do instantâneo do HANA, o instantâneo de armazenamento combinado é executado. Conforme instruído em crontab, o instantâneo de armazenamento no volume /hana/logbackup é executado a cada 5 minutos, aproximadamente 2 minutos após o backup de log de transações do HANA.

> 

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log de transações do SAP HANA. Esses backups de log de transações precisam abordar os períodos de tempo entre os instantâneos de armazenamento. 

Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário:

- Em casos extremos, acesse um armazenamento combinado no /hana/data e /hana/shared/SID que tem 30 dias de instantâneo.
- Tenha backups de log de transações contíguos que abrangem o tempo entre todos os instantâneos de armazenamento combinado. Portanto, o instantâneo mais antigo do volume de backup de log de transações deve ser de 30 dias. Esse não será o caso se você copiar os backups de log de transações para outro compartilhamento NFS localizado no armazenamento do Azure. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para beneficiar-se de instantâneos de armazenamento e replicação de armazenamento eventual dos backups de log de transações, você precisa alterar o local para o qual o SAP HANA grava os backups de log de transações. É possível fazer essa alteração no HANA Studio. Embora o SAP HANA faça backup de segmentos de log completos automaticamente, você deve especificar um intervalo de backup de log para ser determinístico. Isso é especialmente verdadeiro quando você usa a opção de recuperação de desastre, porque geralmente convém executar backups de log com um período determinístico. No caso abaixo, usamos 15 minutos como o intervalo de backup de log.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você também pode escolher os backups com frequência maior que 15 minutos. Uma configuração mais frequente é frequentemente usada em conjunto com a funcionalidade de recuperação de desastre do SAP HANA nas Instâncias Grandes. Alguns clientes executam backups de log de transações a cada 5 minutos.  

Se o backup do banco de dados nunca foi feito, a etapa final será executar um backup de banco de dados com base no arquivo para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, o SAP HANA não pode iniciar os backups de log indicados.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após seus primeiros instantâneos de armazenamento bem-sucedido tiverem sido executados, você precisará excluir o instantâneo de teste foi executado na etapa 6. Leia **'Remover teste instantâneos - removeTestStorageSnapshot'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) para obter detalhes. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoramento do número e tamanho dos instantâneos no volume de disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. No entanto, o comando do sistema operacional Linux `du` mostra detalhes sobre esses instantâneos de armazenamento, porque eles são armazenados nos mesmos volumes. O comando pode ser usado com as seguintes opções:

- `du –sh .snapshot`: essa opção fornece um total de todos os instantâneos no diretório de instantâneos.
- `du –sh --max-depth=1`: esta opção lista todos os instantâneos salvos na pasta **.snapshot** e o tamanho de cada um deles.
- `du –hc`: Esta opção fornece o tamanho total usando por todos os instantâneos.

Use estes comandos para verificar se os instantâneos foram obtidos e armazenado não estão consumindo todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos acima.

### <a name="getting-details-of-snapshots"></a>Obter detalhes de instantâneos
Para obter mais detalhes sobre instantâneos, é possível usar o script `azure_hana_snapshot_details`. Esse script poderá ser executado em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece a saída a seguir dividida por cada volume que contém os instantâneos: 
   * O tamanho do total de instantâneos em um volume
   * Os detalhes a seguir em cada instantâneo nesse volume: 
      - Nome do instantâneo 
      - Criar tempo 
      - Tamanho do instantâneo
      - Frequência do instantâneo
      - ID do HANA Backup associada a esse instantâneo, se relevante

Para obter a sintaxe da verificação de comando e saídas **lista de instantâneos - azure_hana_snapshot_details** no documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reduzindo o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número armazenado de certos rótulos de instantâneos. Os dois últimos parâmetros do comando para iniciar um instantâneo são o rótulo e o número de instantâneos que você deseja manter.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

No exemplo anterior, o rótulo de instantâneo é **dailyhana** e o número de instantâneos com este rótulo a ser retido é **28**. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. A maneira fácil de reduzir o número de instantâneos para 15, por exemplo, é executar o script com o último parâmetro definido como **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se você executar o script com essa configuração, o número de instantâneos, incluindo o novo instantâneo de armazenamento, é 15. Os 15 instantâneos mais recentes são mantidos e os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Esse script reduzirá o número de instantâneos apenas se houver instantâneos com mais de 1 hora. O script não exclui instantâneos com menos de 1 hora. Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não deseja mais manter um conjunto de instantâneos com o prefixo de backup **dailyhana** nos exemplos de sintaxe, você pode executar o script com **0** como o número de retenção. Todos os instantâneos que correspondem a esse rótulo são removidos. Entretanto, remover todos os instantâneos pode afetar as funcionalidades de recuperação de desastre do SAP HANA nas Instâncias Grandes.

Uma segunda opção para excluir instantâneos específicos é usar o script `azure_hana_snapshot_delete`. Este script foi desenvolvido para excluir um instantâneo ou conjunto de instantâneos usando a ID de backup do HANA conforme encontrada no HANA Studio ou por meio do próprio nome do instantâneo. No momento, a ID de backup só está vinculada aos instantâneos criados para o tipo de instantâneo **hana**. Backups de instantâneos do tipo **logs** e **boot** não executam um instantâneo do SAP HANA e, portanto, não há ID de backup a ser localizada para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos em volumes diferentes que correspondam ao nome do instantâneo inserido. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Para obter detalhes sobre o script, consulte **'Excluir um instantâneo - azure_hana_snapshot_delete'** do documento [Microsoft as ferramentas de instantâneo do SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Execute o script como usuário **raiz**.

>[!IMPORTANT]
>Se houver dados que somente existam no instantâneo que você está excluindo, depois que o instantâneo for excluído, esses dados serão perdidos para sempre.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Restauração no nível do arquivo de um instantâneo de armazenamento

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Para os tipos de instantâneos **hana** e **logs**, é possível acessar os instantâneos diretamente nos volumes no diretório **.snapshot**. Há um subdiretório para cada um dos instantâneos. É possível copiar cada arquivo no estado em que estava no ponto do instantâneo desse subdiretório para a estrutura de diretório real. Na versão atual do script, há **não** restaurar o script fornecido para a restauração de instantâneo como um autoatendimento (embora a restauração de instantâneo pode ser executada como parte do DR Self-service gera um script no site de recuperação de Desastre durante o failover). Você deve entrar em contato com a equipe de operações do Microsoft através da abertura de uma solicitação de serviço para restaurar um instantâneo desejado dos instantâneos existentes disponíveis.

>[!NOTE]
>A restauração de arquivo único não funciona para instantâneos do LUN de inicialização, independentemente do tipo de unidade do HANA em Instâncias Grandes. O diretório **.snapshot** não é exposto no LUN de inicialização. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Recuperar para o instantâneo mais recente do HANA

Em um cenário de inatividade na produção, o processo de recuperação de um instantâneo de armazenamento poderá ser iniciado como um incidente do cliente com suporte do Microsoft Azure. É um assunto de alta urgência se os dados foram excluídos em um sistema de produção, e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em uma situação diferente, uma recuperação pontual pode ter baixa urgência e ser planejada com dias de antecedência. Você pode planejar essa recuperação com o SAP HANA no Azure em vez de gerar um sinalizador de alta prioridade. Por exemplo, você pode planejar atualizar o software SAP aplicando um novo pacote de melhoria. Em seguida, é necessário reverter para um instantâneo que representa o estado antes da atualização de pacote de melhoria.

Antes de enviar a solicitação, é necessário se preparar. O SAP HANA na equipe do Azure pode manipular a solicitação e fornecer os volumes restaurados. Depois, restaure o banco de dados do HANA com base nos instantâneos.

As possibilidades para obter um instantâneo restaurado com o novo conjunto de ferramentas estão documentadas na seção **'Como restaurar um instantâneo'** do documento [Manual recuperação guia para SAP HANA no Azure por meio do instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

A seguir, é apresentado como preparar a solicitação:

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana é restaurado, a menos que indicado o contrário. 

1. Desligar a instância do HANA.

   ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Desmonte os volumes de dados em cada nó do banco de dados do HANA. Se os volumes de dados ainda estiverem montados para o sistema operacional, a restauração do instantâneo falhará.
   ![Desmontar os volumes de dados em cada nó de banco de dados do HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Abra uma solicitação de suporte do Azure e inclua instruções sobre a restauração de um instantâneo específico.

   - Durante a restauração: SAP HANA no Azure pode solicitar que você participe de uma chamada de conferência para garantir a coordenação, verificação e confirmação de que o instantâneo de armazenamento correto será restaurado. 

   - Depois da restauração: SAP HANA no serviço do Azure notifica você quando o instantâneo de armazenamento tiver sido restaurado.

1. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

   ![Montar novamente todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Outra possibilidade de obter, por exemplo, se recuperou de um instantâneo de armazenamento de arquivos de dados do SAP HANA está documentada na etapa 7 do documento [Manual recuperação guia para SAP HANA no Azure por meio do instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

O documento [Manual recuperação guia para SAP HANA no Azure por meio do instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) ilustra a sequência de restauração de backup de instantâneo. Use essa documentação para a execução de uma restauração. 

>[!Note]
>Etapa 7 não é necessária executar, se você obteve o instantâneo restaurado com o Microsoft operations.


### <a name="recover-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
O documento [Manual recuperação guia para SAP HANA no Azure por meio do instantâneo de armazenamento](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) ilustra a sequência de restauração para um determinado ponto no tempo na seção **'Recuperar o banco de dados para o seguinte ponto no tempo'**. Use essa documentação para a execução de uma restauração para um determinado ponto no tempo. 


## <a name="next-steps"></a>Próximas etapas
- Ver [princípios da recuperação de desastre e preparação](hana-concept-preparation.md).
